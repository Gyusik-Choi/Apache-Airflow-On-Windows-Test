# airflow

### Windows 환경에서 시도 (실패)

- apache-airflow를 설치할 폴더에서 vs code를 실행시킨다



- ```bash
  python -m venv venv (파이썬 가상환경을 만드는 명령)
  ```



- ```bash
  source venv/Scripts/activate (파이썬 가상환경 실행)
  ```

  

- ```bash
  export AIRFLOW_HOME=~/airflow (경로 설정)(실행할 터미널의 주소로 세팅하면 추후 해당하는 폴더에 파일들이 생성된다.)

  ex>
  export AIRFLOW_HOME=/mnt/c/Users/kuidoli/airflow_tutorial
  ```



- ```bash
  airflow db init ( db 초기화 - 기본은 sqlite )
  ```

  - 에러 발생
  - no module named 'termios'
    - windows 에서 지원하지 않는 파이썬 모듈
      - 해결방법은 두 가지였다
        - WSL 설치 (우분투)
        - Docker 설치 (가상 컨테이너 환경)

<br>

### WSL2 설치 후 시도 (성공)

WSL의 업데이트 버전인 WSL2를 작년에 마이크로소프트에서 공개했다. 차이는 [이 글](http://melonicedlatte.com/2020/07/05/200400.html)에 자세하게 설명이 되어 있다.

다만 WSL2이 모든 컴퓨터에서 설치되는 것은 아니다. 일정 버전 이상의 컴퓨터에서 설치가 가능하고 다행히 이 컴퓨터(회사 노트북)로는 가능했다. 가능한 버전 확인도 위에 링크된 글에서 확인할 수 있다.

설치에 대한 설명은 꽤 자세하게 여러 글에서 다루고 있어서 생각보다 수월하게 설치할 수 있었다.

<br>

### WSL2 설치 방법

- windows의 powershell을 관리자 권한으로 실행

- 실행 후에 아래의 두 명령어 입력

  - ```powershell
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```

    - WSL feature Enable 시키는 명령어

  - ```powershell
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

    - Virtual Machine Platform feature Enable 시키는 명령어



- 명령어들 입력하고 재부팅

- 재부팅하고 powershell (관리자권한 실행)에서 wsl 명령어 입력

  - 명령어 인식을 못하는 경우

    - ```powershell
      Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
      ```

      

- Windows store에서 ubuntu를 검색하고 원하는 ubuntu를 설치한다. 

  - 버전별로 여러개가 존재합니다. 저는 ubuntu 20.04 LTS를 설치했습니다



- 설치파일의 설치가 완료되면 ubuntu 터미널이 뜨고 터미널에서 추가 설치가 진행된다

- 완료되면 터미널에서 username과 password를 설정하라고 뜬다

  - password는 추후에 ubuntu에서 무언가를 설치할때 비밀번호 확인용으로 사용하게 된다.

- 현재 wsl 버전 확인

  - ```bash
    wsl -l -v
    ```

    - Version 항목이 1로 나오면 2로 변경 가능하다

      - ```bash
        wsl --set-version Ubuntu 2
        ```

      - ```bash
        wsl --set-default-version 2
        ```

<br>

### WSL2 환경에서 apache-airflow 실행

- apache-airflow를 설치할 폴더에서 vs code를 실행시킨다
- vs code의 extensions에서 'remote - wsl'를 설치
- vs code의 터미널을 ubuntu로 변경하고 실행
  - 터미널 변경법은
    - ctrl + shift + p
    - terminal 검색
    - select default Profile 선택
    - ubuntu 선택
    - 터미널 실행해서 확인
      - 터미널 실행 단축키 ctrl + `(백틱 버튼 - 키보드의 숫자 1왼쪽에 있는 ~와 함께있음)



- ```bash
  python -m venv venv
  ```



- ```bash
  source venv/bin/activate
  ```



- ```bash
  export AIRFLOW_HOME=~/airflow (경로 설정)(실행할 터미널의 주소로 세팅하면 추후 해당하는 폴더에 파일들이 생성된다.)

  ex>
  export AIRFLOW_HOME=/mnt/c/Users/kuidoli/airflow_tutorial
  ```



- ```bash
  export SLUGIFY_USES_TEXT_UNIDECODE=yes
  ```

  - 이 명령은 왜 쳐야하는지 모르겠다
  - 해도 빨간색 에러 메시지가 발생하고
  - 안해도 마찬가지로 에러 메시지 발생한다.



- ```bash
  pip3 install apache-airflow
  ```



- ```bash
  airflow db init
  ```



- ```bash
  airflow users create \    		
  --role Admin \    		
  --username (원하는 계정명 - 이거로 나중에 로그인시 사용한다) \    		
  --firstname (이름) \    		
  --lastname (성) \    		
  --email (이메일) \    		
  --password (비밀번호)    
  
  ex> 
  airflow users create \    		
  --role Admin \    		
  --username abcde \    		
  --firstname a \    		
  --lastname b \    		
  --email c \    		
  --password 123
  ```



- ```bash
  airflow webserver --port 8080
  ```



- 브라우저에서
  - http://localhost:8080
    - 위에서 설정한 username, password 입력하고 로그인

<br>

##### Ubuntu 에러

- ~~are you root?
  - 파이썬 가상환경을 실행하기 위해
    - (우분투는 python3 -m venv venv)
    - 위와 같은 명령어를 입력했으나 에러 발생
      - apt-get install python3-venv 를 통해 venv 패키지를 설치하라는 내용이 나온다
      - 그대로 실행시켰으나 에러 발생
        - 앞에 sudo를 붙여서 sudo apt-get install python3-venv 를 실행시켜서 성공
  - 결론
    - 맨앞에 sudo 를 붙일 것!

<br>

참고

https://velog.io/@hamdoe/Airflow-%EC%A1%B0%EA%B7%B8%EB%A7%A3%EA%B2%8C-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Quick-start

https://dhznsdl.tistory.com/19

https://m.blog.naver.com/occidere/221773113221

http://melonicedlatte.com/2020/07/05/200400.html

https://keepdev.tistory.com/90

https://www.44bits.io/ko/post/wsl2-install-and-basic-usage

https://www.lesstif.com/software-architect/wsl-2-windows-subsystem-for-linux-2-89555812.html

https://docs.microsoft.com/ko-kr/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package
