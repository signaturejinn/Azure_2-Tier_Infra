# Azure_2-Tier_Infra

## 0. 아키텍쳐 및 구성
![archi](https://user-images.githubusercontent.com/117608997/215339332-e862383c-69a5-4234-8555-8ab551142137.jpg)
- Wordpress – MySQL 연동
- Jumpbox를 통해서 서버 관리
- Application Gateway를 이용해 클라이언트 접속 허용
- Web
  - subnet : ubuntu 18.04LTS + wordpress 가상머신 운영
- DB Subnet: MySQL database 
- Web tier에 Autoscale 구성
- Azure DNS 구성을 통해 도메인 기반 통신

</br></br>

## 1. 리소스 그룹 생성
![Resource Group](https://user-images.githubusercontent.com/117608997/215339048-06455906-e075-4eb8-aa88-d55cfb62b586.jpg)
</br></br>

## 2.가상 네트워크 생성
![Vnet1](https://user-images.githubusercontent.com/117608997/215339093-454e1a62-aa1d-436b-99c7-41f4993bee57.jpg)
![Vnet2](https://user-images.githubusercontent.com/117608997/215339101-e1c8ad5e-8095-4869-8ad0-f8cb53e4dee5.jpg)
![Vnet3](https://user-images.githubusercontent.com/117608997/215339103-01c9c6f8-ee51-4fd6-b85c-88db4f7dd3ac.jpg)
</br></br>

## 3. 네트워크 보안 그룹 생성
- Bastion Security Group
- AGG Security Group
- Web Security Group

</br></br>

## 4. 웹 서버 이미지 생성 위한 VM 생성 
### - 가상머신 생성
![VM1](https://user-images.githubusercontent.com/117608997/215339123-ea9e5b9f-5201-4f12-a0f6-cb0674a994ca.jpg)
![VM2](https://user-images.githubusercontent.com/117608997/215339124-56df4bcf-0fce-4e84-9814-29369d56738d.jpg)
</br></br>

### - 파일 스토리지 계정 생성  스크립트 파일 전달 / 스크립트 파일 전달 후 마운트 및 설치
![Filestorage1](https://user-images.githubusercontent.com/117608997/215339131-3b27b8ba-cc68-4748-ba07-9d0e9f80f02a.jpg)
![Filestorage2](https://user-images.githubusercontent.com/117608997/215339134-1ccc762f-0d46-4788-8e26-fd3a4e9757e4.jpg)
</br></br>

### - 파일 스토리지 공유
![Filestorage3](https://user-images.githubusercontent.com/117608997/215339137-8675092d-34ac-4617-93a7-bc818e974c66.jpg)
![Filestorage4](https://user-images.githubusercontent.com/117608997/215339142-b237ef82-6d1f-4790-922d-e412209a6465.jpg)
![Filestorage5](https://user-images.githubusercontent.com/117608997/215339146-cc4b0e3a-5847-42b0-aa7e-1517e6a1451f.jpg)
</br></br>

#### - 연결 </br>
```
  sudo mkdir /mnt/wordpressdata
  if [ ! -d "/etc/smbcredentials" ]; then
  sudo mkdir /etc/smbcredentials
  fi
  if [ ! -f "/etc/smbcredentials/wordpressa.cred" ]; then
    sudo bash -c 'echo "username=wordpressa" >> /etc/smbcredentials/wordpressa.cred'
    sudo bash -c 'echo "password=uTzpYobU55xU9xYROrEBSWUCePbEY86MqtM9NLnjGgfzbLJ9gbx4irnKjd8PLH2IDl93YJFcd8uL+ASt4HiTGw==" >> /etc/smbcredentials/wordpressa.cred'
  fi
  sudo chmod 600 /etc/smbcredentials/wordpressa.cred

  sudo bash -c 'echo "//wordpressa.file.core.windows.net/wordpressdata /mnt/wordpressdata cifs        nofail,credentials=/etc/smbcredentials/wordpressa.cred,dir_mode=0777,file_mode=0777,serverino,nosharesock,actimeo=30" >> /etc/fstab'
  sudo mount -t cifs //wordpressa.file.core.windows.net/wordpressdata /mnt/wordpressdata -o credentials=/etc/smbcredentials/wordpressa.cred,dir_mode=0777,file_mode=0777,serverino,nosharesock,actimeo=30
```
#### - 스크립트 실행 </br>
```
  cd mnt/wordpressdata
  . wordpress.sh
  ```
  ```
  # 웹 서버 설치
  nano wordpress.sh
  . wordpress.sh
```
</br>

### - Azure Database for MySQL 만들기
![db1](https://user-images.githubusercontent.com/117608997/215339165-d2c1e4e1-b6db-402c-97b4-27f3ab532294.jpg)
![db2](https://user-images.githubusercontent.com/117608997/215339167-e204a93b-5d9c-4e31-8ecc-345bdcd06fa5.jpg)
</br>

### - wordpress 환경구성
  - wordpress 구성파일에 앞서 생성한 mysql hostname, usernmae, password를 설정
    - wp-config.php 편집
</br>

#### - 가상머신(web1) 로그인

```
root@Web1:~# ls /var/www/html/
index.php             wp-config-sample.php  wp-login.php
license.txt           wp-config.php         wp-mail.php
readme.html           wp-content            wp-settings.php
wp-activate.php       wp-cron.php           wp-signup.php
wp-admin              wp-includes           wp-trackback.php
wp-blog-header.php    wp-links-opml.php     xmlrpc.php
wp-comments-post.php  wp-load.php
```
</br>

### - mysql 클라이언트 프로그램 설치 후 구성 정보 수정

```
클라이언트 프로그램 설치
# apt install mysql-client-core-5.7 -y

Wordpress 설치 여부 확인
# cd /var/www/html/
# nano wp-config.php
/** Database username */
define( 'DB_USER', 'btcuser@wordpress1-mysqldb' );

/** Database password */
define( 'DB_PASSWORD', '<password>' );

/** Database hostname */
define( 'DB_HOST', 'wordpress1-mysqldb.mysql.database.azure.com' );
```
</br>

### - mysql 접속 TEST
```
# mysql -h wordpress1-mysqldb.mysql.database.azure.com -u btcuser@wordpress1-mysqldb -p
mysql: [Warning] Using a password on the command line interface can be insecure.
Enter password: 
ERROR 9000 (HY000): Client with IP address '20.196.206.171' is not allowed to connect to this MySQL server.
```
</br>

➡️ 실패
</br>

### - Connection 허용 설정
  - MySQL -> [연결보안] 메뉴 실행
     - Azure 서비스 방문 허용 : '아니오 ➡️ 예'로 설정
![db3](https://user-images.githubusercontent.com/117608997/215339172-faebba69-f564-4ea3-93cd-5ef13f901474.jpg)
</br>

### - 다시 접속 TEST 후 연결되면 wordpress 데이터베이스 생성
```
# mysql -h wordpress1-mysqldb.mysql.database.azure.com -u btcuser@wordpress1-mysqldb -p
password: <password>
mysql: [Warning] Using a password on the command line interface can be insecure.
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64548
Server version: 5.6.47.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


mysql> CREATE DATABASE wordpressdb DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
Query OK, 1 row affected (0.15 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.10 sec)

mysql> show databases;
mysql> exit;
```

### - wordpress 실행
  - 가상머신의 공인 IP를 이용해 접속 TEST
![wordpress1](https://user-images.githubusercontent.com/117608997/215339192-0d250c41-446c-4ffc-af15-c1dbffef6ad8.jpg)
![wordpress2](https://user-images.githubusercontent.com/117608997/215339196-46e4aea9-a1ef-4d56-a26d-78f21feb827c.jpg)
</br>

  - 관리자 권한 설정
  - 로그인 후 워드프레스 동작
![wordpress3](https://user-images.githubusercontent.com/117608997/215339206-adb8ae78-866e-47e7-a3ad-0e1039f22b0b.jpg)
</br>
    
    
  
