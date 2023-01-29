# Azure_2-Tier_Infra

## 0. 아키텍쳐 및 구성
![archi](https://user-images.githubusercontent.com/117608997/215339332-e862383c-69a5-4234-8555-8ab551142137.jpg)
■ Wordpress – MySQL 연동
■ Jumpbox를 통해서 서버 관리
■ Application Gateway를 이용해 클라이언트 접속 허용
■ Web
- subnet : ubuntu 18.04LTS + wordpress 가상머신 운영
■ DB Subnet: MySQL database
■ Web tier에 Autoscale 구성
■ Azure DNS 구성을 통해 도메인 기반 통신
</br><hr/>

## 1. 리소스 그룹 생성
![Resource Group](https://user-images.githubusercontent.com/117608997/215339048-06455906-e075-4eb8-aa88-d55cfb62b586.jpg)
</br><hr/>

## 2.가상 네트워크 생성
![Vnet1](https://user-images.githubusercontent.com/117608997/215339093-454e1a62-aa1d-436b-99c7-41f4993bee57.jpg)
![Vnet2](https://user-images.githubusercontent.com/117608997/215339101-e1c8ad5e-8095-4869-8ad0-f8cb53e4dee5.jpg)
![Vnet3](https://user-images.githubusercontent.com/117608997/215339103-01c9c6f8-ee51-4fd6-b85c-88db4f7dd3ac.jpg)
</br><hr/>

## 3. 네트워크 보안 그룹 생성
- Bastion Security Group
- AGG Security Group
- Web Security Group
</br><hr/>

## 4. 웹 서버 이미지 생성 위한 VM 생성 
- 가상머신 생성
![VM1](https://user-images.githubusercontent.com/117608997/215339123-ea9e5b9f-5201-4f12-a0f6-cb0674a994ca.jpg)
![VM2](https://user-images.githubusercontent.com/117608997/215339124-56df4bcf-0fce-4e84-9814-29369d56738d.jpg)
</br>

- 파일 스토리지 계정 생성  스크립트 파일 전달 / 스크립트 파일 전달 후 마운트 및 설치
![Filestorage1](https://user-images.githubusercontent.com/117608997/215339131-3b27b8ba-cc68-4748-ba07-9d0e9f80f02a.jpg)
![Filestorage2](https://user-images.githubusercontent.com/117608997/215339134-1ccc762f-0d46-4788-8e26-fd3a4e9757e4.jpg)
</br>

- 파일 스토리지 공유
![Filestorage3](https://user-images.githubusercontent.com/117608997/215339137-8675092d-34ac-4617-93a7-bc818e974c66.jpg)
![Filestorage4](https://user-images.githubusercontent.com/117608997/215339142-b237ef82-6d1f-4790-922d-e412209a6465.jpg)
![Filestorage5](https://user-images.githubusercontent.com/117608997/215339146-cc4b0e3a-5847-42b0-aa7e-1517e6a1451f.jpg)
</br>
  - 연결
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
  
