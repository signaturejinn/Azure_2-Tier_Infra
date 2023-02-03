# Azure_2-Tier_Infra

## 0. 아키텍쳐 및 구성
![image](https://user-images.githubusercontent.com/117608997/216594656-5ddc0a18-7e61-4362-94cc-d379bf632635.png)


</br></br>

## 1. 리소스 그룹 생성
![Resource Group](https://user-images.githubusercontent.com/117608997/215339048-06455906-e075-4eb8-aa88-d55cfb62b586.jpg)
</br></br><br>

## 2. 가상 네트워크 생성
![Vnet1](https://user-images.githubusercontent.com/117608997/215339093-454e1a62-aa1d-436b-99c7-41f4993bee57.jpg)
![Vnet2](https://user-images.githubusercontent.com/117608997/215339101-e1c8ad5e-8095-4869-8ad0-f8cb53e4dee5.jpg)
![Vnet3](https://user-images.githubusercontent.com/117608997/215339103-01c9c6f8-ee51-4fd6-b85c-88db4f7dd3ac.jpg)
</br></br></br>

## 3. 네트워크 보안 그룹 생성
- Bastion Security Group
- AGG Security Group
- Web Security Group

</br></br>

## 4. 웹 서버 이미지 생성 위한 VM 생성 
### - 가상머신 생성
![VM1](https://user-images.githubusercontent.com/117608997/215339123-ea9e5b9f-5201-4f12-a0f6-cb0674a994ca.jpg)
![VM2](https://user-images.githubusercontent.com/117608997/215339124-56df4bcf-0fce-4e84-9814-29369d56738d.jpg)
</br></br></br>

### - 파일 스토리지 계정 생성  스크립트 파일 전달 / 스크립트 파일 전달 후 마운트 및 설치
![Filestorage1](https://user-images.githubusercontent.com/117608997/215339131-3b27b8ba-cc68-4748-ba07-9d0e9f80f02a.jpg)
![Filestorage2](https://user-images.githubusercontent.com/117608997/215339134-1ccc762f-0d46-4788-8e26-fd3a4e9757e4.jpg)
</br></br>

### - 파일 스토리지 공유
![Filestorage3](https://user-images.githubusercontent.com/117608997/215339137-8675092d-34ac-4617-93a7-bc818e974c66.jpg)
![Filestorage4](https://user-images.githubusercontent.com/117608997/215339142-b237ef82-6d1f-4790-922d-e412209a6465.jpg)
![Filestorage5](https://user-images.githubusercontent.com/117608997/215339146-cc4b0e3a-5847-42b0-aa7e-1517e6a1451f.jpg)
</br></br>

  - 연결 </br>
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

  - 스크립트 실행 </br>
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

  - 가상머신(web1) 로그인
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
### ➡️ 실패
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
</br></br></br>

## 5. VMSS 생성
### - 기본사항
![vmss1](https://user-images.githubusercontent.com/117608997/215339217-61359fc9-d49d-4855-8374-d9880cf204f8.jpg)
![vmss2](https://user-images.githubusercontent.com/117608997/215339221-59de7e26-bbe5-4470-95b0-6b585ee09d9b.jpg)
</br>

### - 네트워킹
![vmss3](https://user-images.githubusercontent.com/117608997/215339223-a9f22e23-8d66-4de5-89fd-b47c45a4d936.jpg)
![vmss4](https://user-images.githubusercontent.com/117608997/215339227-f31ed7e6-a4df-4094-8a81-229e780c3085.jpg)
</br></br></br></br>

## 6. AAG 생성
### - 기본 사항
![aag1](https://user-images.githubusercontent.com/117608997/215339239-3c32ba70-b4eb-4ea6-be32-3955d79aafa3.jpg)
</br>

### - 프런트 엔드
![aag2](https://user-images.githubusercontent.com/117608997/215339243-1507a0df-a000-4058-892c-fc3652b060a3.jpg)
</br>

### - 백엔드 풀
![aag3](https://user-images.githubusercontent.com/117608997/215339246-7935b0f3-06e9-4c20-b642-47e7e3df89b9.jpg)
![aag4](https://user-images.githubusercontent.com/117608997/215339253-e230a0e6-679c-4e55-ad80-b7c923879557.jpg)
</br>

### - 구성
![aag5](https://user-images.githubusercontent.com/117608997/215339256-d17e87ce-6adc-4ced-bbe8-5a6119e66448.jpg)
</br>

### - 회람 규칙 추가
- 수신기

![aag6](https://user-images.githubusercontent.com/117608997/215339261-1e97c515-6450-42aa-9f1f-42b63534046a.jpg)
</br></br>

- 백엔드 대상

![aag7](https://user-images.githubusercontent.com/117608997/215339267-3d010c60-4374-4f2f-989d-1358aafb11e9.jpg)
</br></br></br></br>

## 7. 결과 및 Trouble Shooting
### - Trouble Shooting
#### ❗ 502 Bad Gateway 오류 1

![trouble1](https://user-images.githubusercontent.com/117608997/215339282-dcabab1d-d248-4006-8fe2-b2a98e311c71.jpg)
</br>

#### ✅ 해결방안 1 - VMSS 업그레이드 해주기

![trouble2](https://user-images.githubusercontent.com/117608997/215339286-ab086ab6-117a-40eb-a105-2a446c4cb219.jpg)
</br></br></br>

#### ❗ 502 Bad Gateway 오류 2

![trouble4](https://user-images.githubusercontent.com/117608997/215339297-1bdce3c5-bafe-4d6a-9972-fc9f358ed1bb.jpg)
</br>

#### ✅ 해결방안 2 - VMSS 이미지 설정
> VMSS 이미지를 선택할 때 VM 이미지 만들어놓은것으로 설정하지 않고 Ubuntu 그대로 이미지를 선택해서 Web Server가 빈깡통이라서 Bad Gateway가 출력되었다.
</br>

![trouble5](https://user-images.githubusercontent.com/117608997/215339303-59669c64-0f23-47a2-a8f2-2f1728934304.jpg)

###  ➡️ 해결


  
