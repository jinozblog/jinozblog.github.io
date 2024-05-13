---

title: python PySide6 Simulator & data to DB
categories: [macOS,python]
tags: [macos,python,venv,virtualenv,pyside,pyside6,simulator]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Python 3.12.2   
    pyside6   
    matplotlib    
    sqlalchemy    
    pymysql   
    
- dbeaver   

---

## Intro

- 먼저 읽어보기....     
  POST: python PySide6 Simulator    
    [python PySide6 Simulator](https://jinozblog.github.io/posts/python-pyside6-a-simulator/)   
  POST: mariadb & dbeaver   
    [DataBase: MariaDB & dbeaver](https://jinozblog.github.io/posts/backend-macOS-mariadb-dbeaver/)   
   
1. pythonSimulator Edit    

2. Code Review

---

## 1. pythonSimulator Edit    
github-link: pythonSimulatorDatabase    
[pythonSimulatorDatabase](https://github.com/jinozblog/pythonSimulatorDatabase)   

사용방법: DB 연결     
/personafun/dbconn/dbengine.py    
```python
  ....    
  
  engine_url = URL.create(
      drivername="mysql+pymysql",
      username="YourID",  # local mariadb user: root or someone
      password="YourPW",  # Passward with YourID
      host="localhost",
      port=3306,
      database="simulator_tempcap"
  )
  
  ....  
```
#### dbeaver: create-database, create-table, privileges-table(ALL)
![db & table 생성 및 권한 설정](/assets/img/mariadb_user_table_privileges.png)
  > Databases: 우클릭 simulator_tempcap 생성      
    Users/YourID@localhost:     
  >>  Catalogs | simulator_tempcap 클릭    
      Tables | %(ALL) 클릭    
      Table Privileges | Check All 클릭   
      -> 우측하단 Save click   


## 2. Code Review    

#### 수정사항 Summary  
- add Library    
  > import csv    
    import datetime   
    from personafun.dbconn.dbapi import apiClean, createTempCap   
- canvas 4ea    
- Button   
- def run_btn(self):    
- def plot_show(self,data_sum):   
- def daq_loop(self):     
  
- save mode: path   
- /dbconn: database connect
- ardconn.py : data 2ea -> 4ea


#### pythonSimulator 대비 추가 구현한 부분    
- Run Button click: daq_loop() 구동   
- daq_loop(): local data-save & insert Data-Base   

DataBase에 data를 저장할 수 있다면 cloud.service에 한 걸음 다가 선 것이다.    
web, app.으로 확장할 수 있다.   

DataBase 연결에 대한 방법은 매우 다양하다.    
engine, model, api-crud 분리해서 구현하는 것이 보통이나 여기서는 2개 module로 분리해서 적용했다.    

DB 연결에 중요한 부분은 /personafun/dbconn/dbengine.py     
engine 생성, DB-model로 부터 Session을 만들어내는 부분이다.   
```python
  from sqlalchemy import create_engine
  from sqlalchemy import Column, Integer, Date, Time, Float
  from sqlalchemy.orm import declarative_base, sessionmaker
  from sqlalchemy.engine.url import URL

  engine_url = URL.create(
      drivername="mysql+pymysql",
      username="YourID", # YourID
      password="YourPW",  # YourPW
      host="localhost",
      port=3306,
      database="simulator_tempcap"
  )

  engine = create_engine(engine_url)

  Base = declarative_base()

  #### DB-model
  class TempCap(Base):
      __tablename__ = 'simulator_tempcap'
      
      id = Column(Integer, primary_key=True)
      date = Column(Date)
      time = Column(Time)
      sampletime = Column(Integer)
      temp = Column(Float)
      adc = Column(Integer)
      capacitance = Column(Float)


  #### Apply metadata
  Base.metadata.create_all(engine)

  #### Create a session
  Session = sessionmaker(bind=engine)
```

personaGuiV2.ui   
personaSimulatorV2.py   
daq_loop() 하단에 else: part에 data-save와 to data-base 부분이 추가로 구현한 중요한 부분이다.   

```python
  ....    
  if self.system_cnt % save_cnt == 0:
      self.timer.stop()
      self.close()
  elif self.system_cnt == finish_cnt:
      self.timer.stop()
      self.close()
  else:
      ### data-save
      with open(save_path, 'a') as fb:
          wr = csv.writer(fb)
          wr.writerow(data_now)
      ### to data-base
      dataset = data_now[1:]
      createTempCap(dataset)
  ....  
```