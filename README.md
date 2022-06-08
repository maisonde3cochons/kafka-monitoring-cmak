## [JConsole을 이용한 Apache Kafka Monitoring 방법 - Producer 구성]

<br>

### [Usage & Advantage]

<br>

> #### CMAK은 오픈소스 소프트웨어로 간단한 설치 및 JMX 설정 없이 모니터링 및 Topic 관련 운영 가능
> 
- UI 기반 직관적인 : 기본 제공하는 broker/topic/partition 단위 metric
- 운영 기능 제공 : Topic 생성/삭제, Broker 설정 변경, Partition 재할당
- 멀티 클러서트 관리 : 다수의 Kafka cluster를 하나의 UI에서 관리 가능

<br>

### [환경 구성도]

![image](https://user-images.githubusercontent.com/30817824/172540169-0d085e9a-097f-4a42-b72e-23dd82cefec1.png)

(refer: https://github.com/freepsw/kafka-metrics-monitoring)

<br><br>

----------------------------------------------------

<br>

> ##  STG.01 GCP VM Instance 생성

<br>

###  GCP에 접속하여 VM Instance를 생성한다


<br>

### kafka-monitoring 이라는 이름의 인스턴스를 생성(Seoul Region > e2-standard-4)

![image](https://user-images.githubusercontent.com/30817824/172540467-10c885e3-279c-4d42-8053-8bbd6f54d61a.png)

<br>

### Boot Disk는 Centos로 선택

<br>

![image](https://user-images.githubusercontent.com/30817824/172509380-4ad29e2c-00ba-4ed0-aee9-f5ab8a5f5f1b.png)

<br><br>

> ## STG.02. Install & Configure

<br>

#### 01. Java 11 설치

```
## 설치 가능한 java version 확인
> yum list java*jdk-devel
Available Packages
java-1.6.0-openjdk-devel.x86_64
java-1.7.0-openjdk-devel.x86_64 
java-1.8.0-openjdk-devel.i686 
java-1.8.0-openjdk-devel.x86_64  
java-11-openjdk-devel.i686  
java-11-openjdk-devel.x86_64
java-latest-openjdk-devel.x86_64 

## jdk 11 설치
sudo yum install -y java-11-openjdk-devel.x86_64

## 설치된 java version 확인
> java -version
openjdk version "11.0.13" 2021-10-19 LTS
```

<br>

#### 02. CMAK 설치

```
cd ~
curl -LO https://github.com/yahoo/CMAK/archive/refs/tags/3.0.0.5.tar.gz
tar xvf 3.0.0.5.tar.gz
rm -rf 3.0.0.5.tar.gz
cd CMAK-3.0.0.5/

## 소스코드를 build하여 실행파일 생성
./sbt clean dist

## build를 통해서 생성된 실행파일(cmak-3.0.0.5.zip)을 확인한다.
> ls target/universal/
cmak-3.0.0.5.zip  scripts

## zip파일을 압축해제한다. (현재 디렉토리에 압축 해제)
sudo yum install -y unzip
unzip target/universal/cmak-3.0.0.5.zip
```

<br>

#### 03. CMAK 실행
```
## 현재 디렉토리에 압축해제된 디렉토리(cmak-3.0.0.5) 확인한다. 
> ls ~/CMAK-3.0.0.5/cmak-3.0.0.5
README.md  bin  conf  lib  share

## 모니터링 할 kakfka cluster의 zookeeper IP:PORT를 설정파일에 추가한다. 
> vi ~/CMAK-3.0.0.5/cmak-3.0.0.5/conf/application.conf
cmak.zkhosts="broker-01:2181"

> ~/CMAK-3.0.0.5/cmak-3.0.0.5/bin/cmak
```

<br>

#### 04. GCP VPC Network Firewall 설정 (9000 port 허용)

<br>

![image](https://user-images.githubusercontent.com/30817824/172518925-0a960d8a-8a56-4d92-9af6-bf24231fcf53.png)

![image](https://user-images.githubusercontent.com/30817824/172541307-5011100c-2428-4d12-aa07-93d52feeb89d.png)



#### 05. Web Browser에서 접속 확인

```
http://<External IP>:9000
```

> Cluster Name은 원하는 이름으로 입력하고, zookeeper hostname:port 정보를 입력해준다

![image](https://user-images.githubusercontent.com/30817824/172543054-8decf74f-f6a5-418b-a63b-6e09ad2a14a5.png)
