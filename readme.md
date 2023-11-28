# Spring PetClinic Sample Application

[![Build Status](https://travis-ci.org/spring-petclinic/spring-framework-petclinic.svg?branch=master)](https://travis-ci.org/spring-petclinic/spring-framework-petclinic/) 
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=spring-petclinic_spring-framework-petclinic&metric=alert_status)](https://sonarcloud.io/dashboard?id=spring-petclinic_spring-framework-petclinic)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=spring-petclinic_spring-framework-petclinic&metric=coverage)](https://sonarcloud.io/dashboard?id=spring-petclinic_spring-framework-petclinic)

GCP Cloud 과제 수행을 위한 Java Spring 으로 개발된 Sample Application 입니다.
AWS 

**3-layer architecture** (i.e. presentation --> service --> repository) 로 Tomcat 에 배포하여 2 Tier 로 구성하거나
또는 nginx 등의 Web 서버를 통해서 Tomcat 을 연결하는 3 Tier 구성을 테스트할 수 있습니다. 

## Understanding the Spring Petclinic application with a few diagrams

[See the presentation here](http://fr.slideshare.net/AntoineRey/spring-framework-petclinic-sample-application) (2017 update)

## Running petclinic locally

### Java, Git 설치
```
sudo yum update -y
sudo yum install java -y
sudo alternatives --config java // java버전 선택
sudo yum install git -y
```

### Tomcat 설치 및 Start
Tomcat 설치 가이드를 참조하여 Tomcat 설치 후, tomcat-users.xml 에 User 및 Role 추가

[ Amazon Linux2 : Tomcat 9 설치하는 방법 ](https://progtrend.blogspot.com/2018/07/aws-amazon-linux-2-tomcat-9.html)

Tomcat9 설치

```
# Tomcat 컴파일 디렉토리로 이동
cd /usr/local/lib
# tomcat 컴파일 설치
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.78/bin/apache-tomcat-9.0.78.tar.gz
tar xvf apache-tomcat-9.0.78.tar.gz 
mv apache-tomcat-9.0.78 ./tomcat9 
```

Tomcat9 시작 및 작동 테스트

```
cd /usr/local/lib/tomcat9/bin 
./start.sh 
./shutdwon.sh
```

권한 문제가 발생할 경우 유저:그룹 체크
```
# 재귀적으로 대상의 유저와 그룹을 변경
sudo chown -R <유저>:<그룹> <대상> 
```
톰캣 매니저 HTML출력으로 정상 작동 확인 시 종료
<br>
<img src="https://github.com/tthingbini/petclinic_3Tier/assets/137377076/1c7ec0ed-4656-409a-8360-cc265dcf3c28">

### petclinic 빌드 & Tomcat 저장
```
cd /usr/local/lib
git clone https://github.com/SteveKimbespin/petclinic_btc.git //petclinic 소스파일 인스톨
cd /usr/local/lib/petclinic_btc
./mvnw package //메이븐 월페이퍼 빌드 시작.
sudo cp ./target/petclinic.war /usr/local/lib/tomcat9/webapps //빌드된 war파일 -> tomcat 기본 배포 위치로 복사
```

### petclinic 배포 (in 외장 Tomcat9)
```
cd /usr/local/lib/tomcat9/bin
./start.sh //톰캣 배포 디렉토리에 war가 있어서 자동 배포.
```

You can then access petclinic here: [http://localhost:8080/petclinic](http://localhost:8080/petclinic)

<img width="1042" alt="petclinic-screenshot" src="https://cloud.githubusercontent.com/assets/838318/19727082/2aee6d6c-9b8e-11e6-81fe-e889a5ddfded.png">

### 외부에 구성한 MySQL Database 연결 방법

MySQL 을 각 CSP 의 DB Service 로 구성
  - database 명 : petclinic  
  - db user 및 password 설정

MySQL database 접속 설정을 하기 위해, pom.xml 파일에 정의 된 'MySQL' profile 을 아래와 같이 수정후, 재배포(redeploy)한다.
  - jdbc.url 부분에 정의되어 있는 DNS 또는 IP Address 를 연결하고자 하는 MySQL IP 로 변경한다. ( 필요시 database 도 수정)
  - db 접속 User ID 및 Password 수정

```
<properties>
    <jpa.database>MYSQL</jpa.database>
    <jdbc.driverClassName>com.mysql.cj.jdbc.Driver</jdbc.driverClassName>
    <jdbc.url>jdbc:mysql://localhost:3306/petclinic?useUnicode=true</jdbc.url>
    <jdbc.username>petclinic</jdbc.username>
    <jdbc.password>petclinic</jdbc.password>
</properties>
```      

Tomcat 에 재배포

```
# 기존에 배포되어 있는 환경에 MySQL 연결 설정 수정후, 재배포 하는 경우
./mvnw tomcat7:redeploy -P MySQL

# 최초 배포하는 경우
./mvnw tomcat7:deploy -P MySQL
```


You can then access petclinic here: [http://localhost:8080/petclinic](http://localhost:8080/petclinic)





