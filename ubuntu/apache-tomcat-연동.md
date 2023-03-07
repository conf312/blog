### 🌈 Apache2, Tomcat이 각 서버에 설치되어 있는 환경에서 작성되었습니다.

## 💻 Apache2가 설치된 서버에서의 작업
### 연동 커넥터 mod_jk 설치
```bash
apt-get install libapache2-mod-jk
```
설치가 완료되면 /etc/apache2/mods-available 경로에 jk.conf, jk.load 파일이 생성된다.

### workers.properties 수정 (/etc/libapache2-mod-jk)
- port : 톰캣의 server.xml에 설정되어있는 ajp 포트
- host : 톰캣 서버의 ip

```bash
workers.tomcat_home=/usr/share/tomcat8
workers.java_home=/usr/lib/jvm/default-java
ps=/
worker.list=ajp13_worker
 
worker.ajp13_worker.port=8009
worker.ajp13_worker.host=localhost
worker.ajp13_worker.type=ajp13
worker.ajp13_worker.lbfactor=1
 
worker.loadbalancer.type=lb
worker.loadbalancer.balance_workers=ajp13_worker
```

#### workers.properties를 만들어서 사용하려면 /etc/apache2/mods-available/jk.conf 에 설정 되어있는 JkWorkersFile /etc/libapache2-mod-jk/workers.properties 부분을 경로와 파일 이름에 맞게 변경한다.
![image](https://user-images.githubusercontent.com/13326651/223328498-1a826390-eddc-43fb-bf89-54900cd7ed1a.png)

### VirtualHost 수정 (/etc/apache2/sites-available/000-default.conf)
```bash
<VirtualHost *:80>
  //ServerName localhost
  ServerAdmin root@localhost
  DocumentRoot /home/project/sample

  JkMount / ajp13_worker
  JkMount /* ajp13_worker
  JkUnmount /common/* ajp13_worker

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
 
  <Directory /home/project/sample > 
     Options FollowSymLinks
     AllowOverride None
     Order Deny,Allow
     Allow from all
     Require all granted 
  </Directory>
</VirtualHost>
```

### 아파치 재시작
```bash
service apache2 restart
```

## 💻 Tomcat이 설치된 서버에서의 작업
### server.xml
```bash
<Connector protocol="AJP/1.3"
  address="0.0.0.0"
  port="8009"
  redirectPort="8443" 
  secretRequired="false" />
```

### 톰켓 재시작
```bash
./shutdwon.sh
./startup.sh
```
