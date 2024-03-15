---
title: github.io blog chirpy theme
categories: [Posts, github]
tags: [github, github.io, blog, chirpy, theme]
---

## Environment

- HW : MacBookPro 15,4(2019)
- OS : Sonoma 14.3

---

## Intro

1. GitHub Repository 생성
2. git setting
3. chirpy theme 적용: chirpy-starter
4. (options) ruby, rbenv install
5. (options) bundler, jekyll install

---


## 1. GitHub Repository 생성

github.com login tab

Overview | Repositories | Projects | Packages | Starts

Respositories > New

YourGitHubID.github.io


#### Settings

생성된 repo. 선택 후 tab에서 Settings 클릭

- Actions > General
  
  Workflow permissions: Read and write permissions 선택
  
- Pages
  
  Build and deployment > Source: GiHub Actions(Beta) 선택
  
  workflows: GitHub Pages Jekyll Configure

---

## 2. git setting

#### Personal access tokens (classic)
우측 상단 사진 클릭 > Settings 이동

사이드바 하단 Developer Settings 이동

Personal access tokens > Tokens (classic)

새로 만들기 90days : 발급된 토큰 코드 저장, git setting 인증에 사용


> $ mkdir YourGitHubID.github.io
>
> $ cd YourGitHubID.github.io
>
> $ git init
>
> $ git config --global user.name "[username]"
>
> $ git config --global user.email "[email]"
>
> $ touch index.html
>
> $ nano index.html

```html   
  Hello github page
```

> $ git add -A
>
> $ git commit -m "commit test index.html"
>
> $ git status
>
> $ git remote add origin https://[YourToken]@github.com/[YourID]/[YourID].github.io.git
>
> $ git remote -v
>
> $ git push -u origin main

#### browser 접속

YourGitHubID.github.io

index.html message 확인

    Hello github page


## 3. chirpy theme 적용: chirpy-starter
[chirpy theme starter site](https://github.com/cotes2020/chirpy-starter)

zip 파일 다운로드

project 폴더 복사

index.html 파일 삭제

> $ rm -rf index.html


#### _config.yml 수정

```yaml

  lang: ko-KR

  timezone: Asia/Seoul

  title: [YourID].github.io 

  tagline: [Input Anything]

  description: >- # used by seo meta and the atom feed
    [Input Anything]

  url: "https://[YourID].github.io"

  github:
    username: [Input username]

  twitter:
    username: 

  social:
    name: [Input username]
    email: YourEmailID@domain.com
    links:
      - https://github.com/[YourID] # change to your github homepage

```


> $ git add -A
>
> $ git commit -m "chirpy theme test"
>
> $ git status
>
> $ git push -u origin main


## 4. (options) ruby, rbenv install

> $ brew install ruby
>
> $ brew install rbenv

#### ruby version change

> $ rbenv install -l
>
> $ rbenv install 3.3.0
>
> $ rbenv global 3.3.0
>
> $ rbenv versions

    system
    * 3.3.0 (set by /Users/persona/.rbenv/version)

> $ ruby -v

    ruby 2.6.10p210 (2022-04-12 revision 67958) [universal.x86_64-darwin23]

> $ rbenv init
>
> $ nano ~/.zshrc

```bash
  eval "$(rbenv init - zsh)"
```

> $ source ~/.zshrc
>
> $ ruby -v

    ruby 3.3.0 (2023-12-25 revision 5124f9ac75) [x86_64-darwin23]

> $ gem update --system 3.5.6


## 5. (options) bundler, jekyll install

> $ gem install bundler jekyll
>
> $ bundler -v

    Bundler version 2.5.3

> $ jekyll -v

    jekyll 4.3.3

> $ jekyll s

#### browser 접속

localhost:4000
