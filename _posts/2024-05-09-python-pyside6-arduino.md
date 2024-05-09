---
title: python PySide6 Arduino Real-Time Serial Communication
categories: [macOS,python,Arduino]
tags: [macos,python,arduino,venv,virtualenv,pyside,pyside6,ldr,photocell]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Arduino Uno R4 WiFi   
    Sensor : LDR or PhotoCell   
    Resistor : 10kohm   
- Python 3.12.2   
    pyside6   
    matplotlib    
    pyserial    
    
---

## Intro

- 먼저 읽어보기....     
  venv & pyside setting    
    [macOS python venv & pyside setting](https://jinozblog.github.io/posts/macOS-python-venv/)    
  PySide6 Simulator    
    [python PySide6 Simulator](https://jinozblog.github.io/posts/python-pyside6-simulator/)   
  Arduino & LDR Serial Monitoring    
    [Arduino R4 LDR Serial Monitor...](https://jinozblog.github.io/posts/ArduinoR4-LDR/)
  
1. pythonArduino        
  
2. Code Review    

---

## 1. pythonArduino    
pythonSimulator를 통해 PySide6과 Signal Real-Time Data-I/O를 이했다면 실물 Arudino를 이용해서 통신해보자.   
github link Code를 모두 다운받아 pythonArduino 프로젝트 폴더에 압축 파일을 풀어준다.   
[pythonArduino](https://github.com/jinozblog/pythonArduino)   
<>Code > Download Zip   

pythonSimulator와 다른 점   
- ardconn.py: signal generator가 아닌 arduino 통신 data   
- /ard_photocell/ard_photocell.ino: ArduinoIDE or vscode로 Arduino UNO R4에 upload 해준다.(먼저 읽어보기 3번)      
- personaDev.py: run button save mode, save_cnt = 500, 500개 data 저장 후 종료

pythonArduino   
  >  /ard_photocell  
  >> ard_photocell.ino   
  
  >  /img    
  >>  logo_persona_450_150.png    
  >>  python_logo.svg   
  
  >  /personafun   
  >>  ardconn.py    
  >>  figurediagram.py    
  
  >  personaGui.ui   
  >  personaDev.py   


#### library(package) Install & vscode 실행   
Simulator는 실물 HW가 필요 없지만 Arduino 실물은 PC에 연결되어있어야 한다.    
ardconn.py에서 class BoardConn: code를 보면 macOS와 ubuntu serial port를 자동으로 찾도록 설계되어있다.    
ubuntu의 경우 raspberry-pi:4B/ubuntu-desktop:22.04LTS 환경에서만 실행을 확인했다.   

venv 진입
(yourvenv)$

```zsh
  pip install PySide6 matplotlib pyserial
```

```zsh
  cd pythonArduino
  code .
```

python file: personaDev.py   
python 실행: vscode 우측 상단 Run Code    
photoCell에 빛이 많이 들어오면 ADC가 크고 빛을 차단하면 ADC가 작아짐    

![pythonArduino_Qt-GUI](/assets/img/pythonArduino_photocell_gui.png)    

## 2. Code Review:    

#### personafun/ardconn.py review
  > serial: pip install pyserial 이지만 import에서는 serial   
  > class BoardConn: init(self,baudrate), get_data(), close_ser()   
  > 초기 init function Arduino-baudrate 변수를 받아 serial_port를 자동으로 찾아 self._ard_ser 생성    
  > main code에서 BoardConn(baudrate).get_data()으로 arduino-data를 가져옴     

```python
import os
import serial
import serial.tools.list_ports as serial_port
import platform


class BoardConn:
    def __init__(self, baud_rate):
        self._baud_rate = baud_rate
        self._platform_type = platform.platform()[0:5]
        
        _port_list = serial_port.comports()
        _get_port_ser = None
        if self._platform_type == 'macOS':
            _port_ser = '/dev/cu.usbmodem'
            for _port in _port_list:
                if _port.device[0:16] == _port_ser:
                    _get_port_ser = _port.device
                else:
                    pass
        else:
            _port_ser = '/dev/ttyACM'
            for _port in _port_list:
                if _port.device[0:11] == _port_ser:
                    _get_port_ser = _port.device
                else:
                    pass
            if _get_port_ser == None:
                os.system("sudo chmod a+rw {}".format(_get_port_ser))
            else:
                pass
        self._ard_ser = serial.Serial(_get_port_ser, self._baud_rate)
    
    def get_data_frame(self):
        return self._ard_ser.readline().decode()
    
    def get_data(self):
        _get_data = self._ard_ser.readline().decode()
        data = [0,0]
        try :
            if _get_data[0] == 'i':
                _sampletime  = _get_data[_get_data.index('a')+1:_get_data.index('b')]
                _aivalue = _get_data[_get_data.index('b')+1:_get_data.index('f')]
                data = [_sampletime, _aivalue]
            else:
                pass
        except:
            pass
        
        return data
    
    def close_ser(self):
        self._ard_ser.close()
```

#### personaDev.py review   
pythonSimulator에서 다른 점은 initial variable 설정과 save mode가 추가됨   
  > baud_rate = 9600 : Arduino-SourceCode baudrate에 맞춤   
  > save base path set 추가: run-btn daq_loop() 실행 시 data-set 저장    
  
  
```python
  ....
    ## run loop after push Run
    def daq_loop(self):
        .... start_loop와 유사한 form ....
        
        if self.system_cnt % save_cnt == 0:
            self.timer.stop()
            BoardConn(baud_rate).close_ser()
            self.close()
        else:
            with open(save_path, 'a') as fb:
                wr = csv.writer(fb)
                wr.writerow(data_now)
  
  #### Start Code ####
  ## initial variable
  baud_rate = 9600
  sample_time = 100
  save_cnt = 500
  data_sum = []

  ## check form
  platform_check = platform.platform()

  ## define: data header
  # data_header = ['id','date','time','sampletime','aivalue']

  ## define: save path
  #### save base path set
  base_path = Path(__file__).resolve().parent
  save_dir_name = 'savedata'
  save_base = base_path.joinpath(save_dir_name)
  #### save path from time: year-month-day_hour:minute:second
  datetime_now = datetime.datetime.now()
  save_sub_name_from_time = datetime_now.strftime("%y%m%d_%H%M%S")
  save_path = save_base.joinpath(save_sub_name_from_time)

  if save_base.is_dir() == False:
      save_base.mkdir()
  #### End Code ####
```
