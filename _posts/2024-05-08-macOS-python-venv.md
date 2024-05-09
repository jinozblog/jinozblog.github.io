---
title: macOS python venv & pyside setting
categories: [macOS,python]
tags: [macos,python,venv,virtualenv,pyside,pyside6]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- Python 3.12.2

---

## Intro

1. macOS python venv(virsutalenv, 가상환경) setting    
    python version 및 개발 환경(library) 관리   
    pyside6(python-gui) 개발 시 필요    

2. pyside(PySide6) Install

python package management에 대한 개인적인 생각      
  > web 개발에서는 poetry 추천    
  > PySide(or PyQt)에서는 poetry 사용하지 않고 venv 가상환경에서 pip로 패키지 관리   

---

## 1. macOS python venv(virsutalenv, 가상환경) setting

macOS Sonoma 14.x version에는 python 3.12.2가 기본으로 설치되어있다.    

```zsh
  python -V
```
    
macOS or Linux에서 venv 관리에 대한 개인적인 생각   
home dir에서 .venvs dir을 만들고 zshrc alias로 관리하는 방법을 추천한다.  
  > macOS는 기본 터미널이 bash에서 zsh로 바뀌었으나 Linux(Ubuntu)에서는 bash를 사용한다.    
  > oh-my-zsh는 zsh 사용환경에 편의를 제공한다. 다음 링크의 3.oh-my-zsh를 참고(options)   
[MacBook 설정](https://jinozblog.github.io/posts/MacBook-설정/)


#### zshrc alias 생성: pip, venv

zshrc alias 생성 및 적용 순서   
1) nano editor open    
```zsh
  nano ~/.zshrc
```

2) .zshrc 아래 하단에 pip, venv alias 추가   
```zsh 
  alias pip="python3 -m pip"
  alias venv="python3 -m venv"
```

3) nano editor 저장 및 종료    
  > control + O > Enter   
  > control + X   

4) .zshrc alias 적용    
```zsh
  source ~/.zshrc
```


#### venv 만들기  
persona는 필자 가상환경 이름, 원하는 이름으로 생성    
```zsh
  mkdir ~/.venvs
  cd ~/.venvs
  venv persona
```

#### zshrc alias 생성: persona    
.zshrc 아래 하단에 persona alias 추가   
[YourMacID]에 사용중인 macOSID를 넣으면 된다.   

```zsh
  alias persona="source /Users/[YourMacID]/.venvs/persona/bin/activate"
```

#### venv 진입하기    
persona는 필자 가상환경 이름, 생성했던 venv 이름으로....    

```zsh
  persona
```
다음과 같이 터미널 콘솔 앞에 (yourvenv) 형태로 생성됨을 알려줌    
(persona)$

다음 명령어로 생성된 venv 상태를 확인   
```zsh
  python -V
  pip list
```

## 2. pyside(PySide6) Install

```
  brew install pyside
```

#### zshrc alias 생성: designer  

.zshrc 아래 하단에 persona alias 추가   
```zsh
  alias designer="open -a designer"
```

Open Designer: GUI 환경
```zsh
  designer
```
