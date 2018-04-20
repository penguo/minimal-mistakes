---
title: "no 'Pintos booting' message"
categories:
  - pintos
tags:
  - troubleshooting
---
## 1. 문제 상황

make check 를 할 때, 일부 test들이

>"Run didn't start up properly: no "Pintos booting" message"

를 출력하며 FAIL된다.

![error_message]({{ site.url }}{{ site.baseurl }}/img/pintos/nob1.png)

&nbsp;

## 2. 해결 방법

### 2.1. bochs 대신 qemu 를 사용하여 make check 을 한다

1. (qemu가 설치되어 있지 않을 경우) qemu 설치를 한다

    ```default
    cd
    sudo apt-get install qemu
    sudo ln -s /usr/bin/qemu-system-i386 /usr/bin/qemu
    ```

2. make check 할 폴더에 이동하여 시뮬레이터 옵션을 바꿔준다.

    폴더에 이동하여 Make.vars 파일에의 옵션을 바꾼다.

    (아래는 userprog의 경우.)

    ```default
    cd pintos/src/userprog
    vim Make.vars
    ```

    ![make.vars]({{ site.url }}{{ site.baseurl }}/img/pintos/nob5.png)

3. make check 한다.

    ![pass_message]({{ site.url }}{{ site.baseurl }}/img/pintos/nob2.png)

&nbsp;

### 2.2. utils에서 make를 해준다

1. util 폴더에 이동하여 make 해준다.

    ```default
    cd pintos/src/utils
    make
    ```

2. make check 할 폴더에 이동하여 make check한다.

&nbsp;

## 3. 원인 분석

make clean 을 했을 경우 발생하며, pintos utils의 make내역도 함께 삭제되기 때문에 발생하는 듯 하다.

*utils 에서 make를 하기 전*
![utils_no_make]({{ site.url }}{{ site.baseurl }}/img/pintos/nob3.png)

*utils 에서 make를 한 뒤*
![utils_make]({{ site.url }}{{ site.baseurl }}/img/pintos/nob4.png)

&nbsp;

## 4. 마무리

현재 진행중인 pintos project 상에서는

qemu 로 make check 를 실행하면 정상적으로 76 pass가 나타나는데,

*qemu에서 make check 결과*
![passed_on_qemu]({{ site.url }}{{ site.baseurl }}/img/pintos/nob7.png)

bochs 위에서 make check 를 실행하면 2 fail이 나타난다.

*bochs에서 make check 결과*
![failed_on_bochs]({{ site.url }}{{ site.baseurl }}/img/pintos/nob6.png)

동일한 코드 상에서 에뮬레이터의 차이로 fail이 뜰 수도 있는 듯 하다. qemu 짱짱!