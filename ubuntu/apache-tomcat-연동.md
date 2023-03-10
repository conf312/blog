## ๐ป Apache2๊ฐ ์ค์น๋ ์๋ฒ์์์ ์์
### ์ฐ๋ ์ปค๋ฅํฐ mod_jk ์ค์น
์ค์น๊ฐ ์๋ฃ๋๋ฉด /etc/apache2/mods-available ๊ฒฝ๋ก์ jk.conf, jk.load ํ์ผ์ด ์์ฑ๋๋ค.
```bash
apt-get install libapache2-mod-jk
```

### workers.properties (/etc/libapache2-mod-jk)
- port : ํฐ์บฃ์ server.xml์ ์ค์  ๋์ด์๋ ajp ํฌํธ
- host : ํฐ์บฃ ์๋ฒ์ ip

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

### jk.conf (/etc/apache2/mods-available)
workers.properties๋ฅผ ์ค์ ํ๋ ๋ถ๋ถ์ด ์ฌ๊ธฐ์ ์๋ค. (JkWorkersFile)
![image](https://user-images.githubusercontent.com/13326651/223328498-1a826390-eddc-43fb-bf89-54900cd7ed1a.png)

### 000-default.conf ์์  (/etc/apache2/sites-available)
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

### ์ํ์น ์ฌ์์
```bash
service apache2 restart
```

## ๐ป Tomcat์ด ์ค์น๋ ์๋ฒ์์์ ์์
### server.xml (ํฐ์บฃ์ค์น๊ฒฝ๋ก/conf)
workers.properties์ ์์ฑํ port์ ๊ฐ์์ผ ํ๋ค.
```bash
<Connector protocol="AJP/1.3"
  address="0.0.0.0"
  port="8009"
  redirectPort="8443" 
  secretRequired="false" />
```

### ํฐ์ผ ์ฌ์์ (ํฐ์บฃ์ค์น๊ฒฝ๋ก/bin)
```bash
./shutdwon.sh
./startup.sh
```
