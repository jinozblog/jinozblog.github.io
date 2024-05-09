---
title: python PySide6 Simulator
categories: [macOS,python]
tags: [macos,python,venv,virtualenv,pyside,pyside6,simulator]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Python 3.12.2   
    pyside6   
    matplotlib    
    

---

## Intro

- 환경설정: 사전 요구사항    
  venv & pyside setting    
   [macOS python venv & pyside setting](https://jinozblog.github.io/posts/macOS-python-venv/)   
   
1. Qt Designer    

2. Code Review: pythonSimulator


---

## 1. Qt Designer    
Qt Designer를 통해 직접 UI를 작성해야 .ui 파일이 생성된다.    
초심자 경험을 위해서 아래 github link Code를 모두 다운받아 pythonSimlator 프로젝트 폴더에 압축 파일을 풀어준다.   

[pythonSimulator](https://github.com/jinozblog/pythonSimulator)   
<>Code > Download Zip   


pythonSimulator   
  >  /img    
  >>  logo_persona_450_150.png    
  >>  python_logo.svg   
  
  >  /personafun   
  >>  ardconn.py    
  >>  figurediagram.py    
  
  >  personaGui.ui   
  >  personaSimulator.py   
  >  requirements.txt    

#### library(package) Install

venv 진입
(yourvenv)$

```zsh
  pip install PySide6 matplotlib
```


#### vscode 설치 및 실행

cask
```zsh
  brew install cask
```
vscode
```zsh
  brew install –cask visual-studio-code
```
Run Visual Studio Code    
terminal에서 project dir(pythonSimulator)로 이동    

```zsh
  cd pythonSimulator
  code .
```

Simulator 실행 python file: personaSimulator.py   
python 실행: vscode 우측 상단 Run Code    

#### python 실행 안될 때: vscode extensions    
vscode left-tab Extensions에서 "Python Extension Pack" 검색하여 설치   
추가 확장팩 설치는 링크 참고 [vscode-extensions](https://jinozblog.tistory.com/176)   



## 2. Code Review: pythonSimulator    

#### project file review
/img ==> GUI-logo images    
/img/logo_persona_450_150.png ==> Top Logo   
/img/python_logo.svg ==> Sub Logo    
/personafun   
/personafun/ardconn.py ==> Signal Generator    
/personafun/figurediagram.py ==> matplotlib graph figure    
personaGui.ui ==> Qt Desginer UI   
personaSimulator.py ==> Simulator Run main python-file   
requirements.txt ==> 실행에 필요한 패키지, 사용법 pip install requirements.txt    


#### personaSimulator.py review

##### library(package) 선언부     
  > built-in function: sys,pathlib,platform   
  > PySide6 back-bone: QtWidgets, QtUiTools, QtCore   
  > personafun: Arduino Signal Generator, matplotlib figure   

```python
  import sys
  from pathlib import Path
  import platform

  from PySide6.QtWidgets import QMainWindow, QApplication
  from PySide6.QtUiTools import loadUiType
  from PySide6.QtCore import QTimer, QDateTime

  from personafun.ardconn import ArdSimulator
  from personafun.figurediagram import MplCanvas
```

##### PySide6 Real-Time Loop Form     
  > 만들어진 .ui를 PySide6 QMainWindow로 불러와 셋팅하는 형식   
  > def initUI(self): 원하는 로고를 /img dir에 저장하여 사용    
  > def initCanvas(self): 원하는 Canvas 갯수를 늘릴 수 있음   
  > 
  > == Start Code ==   
  > 필요한 초기값 셋팅하는 곳   
  > == End Code ==     
  > 
  > if __name__=="__main__": 초기 ui window 실행에 필요함    

  
```python
  #### Resource Path
  def resource_path(relative_path):
      """ Get absolute path to resource, works for dev and for PyInstaller """
      base_path = getattr(sys, '_MEIPASS', Path(__file__).resolve().parent)
      return str(Path.joinpath(base_path,relative_path))

  #### ui connect
  ui_form = resource_path("personaGui.ui")
  form_class = loadUiType(ui_form)[0]

  #### Window class
  class BaseWindow(QMainWindow, form_class):

      def __init__(self, *args, **kwargs):
          super(BaseWindow, self).__init__(*args, **kwargs)
          self.setupUi(self)
          self.setWindowTitle('Persona Simulator')
          self.initUI()
          self.initCanvas()
          self.initStart()
          self.pushBtn_run.setEnabled(True)
          self.system_cnt = 0
      
      #### initial UI contents : Logo
      def initUI(self):
          # Top Logo
          self.labelTopLogo.setStyleSheet('border-image:url(./img/python_logo.svg);border:0px;')
          # Sub Logo
          self.labelpersonaLogo.setStyleSheet('border-image:url(./img/logo_persona_450_150.png);border:0px;')
          
      def initCanvas(self):
          self.canvas1 = MplCanvas()
          self.graph1_verticalLayout.addWidget(self.canvas1)
      
      def initStart(self):
          self.text_display("Start Loop")
          dateTimeVar = QDateTime.currentDateTime()
          self.text_display(dateTimeVar.toString("yyyy-MM-dd | hh:mm:ss"))
          self.text_display("Platform Check : {}".format(platform_check[0:5]))
          
          self.timer = QTimer()
          self.timer.setInterval(sample_time)
          self.timer.timeout.connect(self.start_loop)
          self.timer.start()

          ## Button connect
          self.pushBtn_run.clicked.connect(self.run_btn)
          self.pushBtn_quit.clicked.connect(self.close)
      
      ########== Button Fun ==###############################################    
      def run_btn(self):
          self.system_cnt = 0
          self.text_display("Daq Run....")
          self.pushBtn_run.setDisabled(True)
          
          self.timer = QTimer()
          self.timer.setInterval(sample_time)
          self.timer.timeout.connect(self.daq_loop)
          self.timer.start()
  ....
  
  ....

  #### Start Code ####
  ## initial variable
  sample_time = 1000
  save_cnt = 10
  data_sum = []

  ## check form
  platform_check = platform.platform()
  #### End Code ####

  if __name__=="__main__":
      app = QApplication(sys.argv)
      window = BaseWindow()
      window.show()
      app.exec()
```

##### UI Display     
  > lcd.display & plot mode

```python
  ########== UI Display ==###############################################    
  def text_display(self,to_text):
      self.textBrowser.append(to_text)
  
  def lcd_display_update(self, loop_cnt, data):
      dateTimeVar = QDateTime.currentDateTime()
      self.lcdID.display(loop_cnt)
      self.lcdID.setDigitCount(4)
      self.lcdTime.display(dateTimeVar.toString("yy-MM-dd,hh:mm:ss"))
      self.lcdSampleTime.display(data[3])
      self.lcdSampleTime.setDigitCount(5)
      self.lcdTime.setDigitCount(17)
      self.lcdTemp.display(data[4])
      self.lcdTemp.setDigitCount(5)
      
          
  def plot_show(self,data_sum):
      x = []
      y = []
      for data in data_sum:
          x.append(int(data[0]))
          y.append(round(float(data[4]),1))
      self.canvas1.axes.cla()
      self.canvas1.axes.set_facecolor("black")
      self.canvas1.axes.plot(x,y,'ro-',label="temp")
      self.canvas1.axes.xaxis.set_tick_params(color='white',labelcolor='white')
      self.canvas1.axes.yaxis.set_tick_params(color='white',labelcolor='white',)
      self.canvas1.axes.set_title("Temp. vs Time",color='white')
      self.canvas1.axes.set_xlabel('Time[sec]',color='white')
      self.canvas1.axes.set_ylabel('Temp.[oC]',color='white')
      self.canvas1.axes.legend(loc="lower right")
      self.canvas1.axes.grid()
      self.canvas1.draw()    
```


##### start_loop & daq_loop      
  > real-time main loop function    
  > start_loop: 초기 실행 시 나타나는 real-time ui    
  > daq_loop: run-button click 후 real-time ui, data-save or transfer database... 활용    

```python
  ## start loop
  def start_loop(self):
      global data_sum
      test_loop = 10
      self.system_cnt += 1
      datetime_now = QDateTime.currentDateTime()
      date_now = datetime_now.toString("yyMMdd")
      time_now = datetime_now.toString("hhmmss")
      
      datetime_list = [self.system_cnt, date_now, time_now]
      ### Dev. Simulation Mode Open
      get_sim_data = ArdSimulator(sample_time).get_data()
      data_now = datetime_list + get_sim_data
      self.lcd_display_update(self.system_cnt, data_now)
      
      data_sum.append(data_now)
      self.plot_show(data_sum)
      
      if self.system_cnt % test_loop == 0:
          data_sum = []
          self.system_cnt = 0
  
  ## run loop after push Run
  def daq_loop(self):
      pass
```