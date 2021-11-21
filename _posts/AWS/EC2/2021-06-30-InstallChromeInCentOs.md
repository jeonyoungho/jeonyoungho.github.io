---
title: "[개발자 블로그] Ec2에 Chrome 및 ChromeDriver 설치" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [AWS, EC2] # categories는 최대 2개까지 가능
tags: [aws, ec2, chrome, chromedriver] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Chrome 설치
1) yum 저장소 생성<br>

~~~
sudo vi /etc/yum.repos.d/google-chrome.repo
~~~

<img width="844" alt="스크린샷 2020-05-07 오후 4 36 04" src="https://user-images.githubusercontent.com/44339530/81267116-e32fdd80-9080-11ea-929e-8c1b503f291c.png"><br>

1-2) 아래와 같이 파일 내용작성<br>

~~~
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/<br>stable/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/<br>linux_signing_key.pub
~~~

<img width="844" alt="스크린샷 2020-05-07 오후 4 37 14" src="https://user-images.githubusercontent.com/44339530/81267188-035f9c80-9081-11ea-9ee2-e653460728ef.png"><br>

2) 설치<br>

~~~
sudo yum install google-chrome-stable
~~~

<img width="844" alt="스크린샷 2020-05-07 오후 4 40 08" src="https://user-images.githubusercontent.com/44339530/81267420-6b15e780-9081-11ea-8e44-c81545489a18.png"><br>


3) 확인<br>

~~~
google-chrome --version
~~~

<img width="844" alt="스크린샷 2020-05-07 오후 4 41 05" src="https://user-images.githubusercontent.com/44339530/81267497-8c76d380-9081-11ea-8244-db02636c6a04.png"><br>


# Chrome Driver 설치
1) 아래 코드 실행(chrome 버전에 따라 78.0.3904.70 이부분은 수정해야함)<br>
~~~
wget -N http://chromedriver.storage.googleapis.com/78.0.3904.70/chromedriver_linux64.zip -P ~/Downloads
unzip ~/Downloads/chromedriver_linux64.zip
sudo mv /usr/local/bin/chromedriver
~~~

<img width="844" alt="스크린샷 2020-05-07 오후 4 48 39" src="https://user-images.githubusercontent.com/44339530/81268204-9c42e780-9082-11ea-99a8-fada3eada9fa.png"><br>


2) unzip 설치<br>
~~~
rpm -qa | grep unzip
yum list unzip
sudo mv ~/Downloads/chromedriver /usr/local/bin/
~~~