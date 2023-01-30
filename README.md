# Azure를 활용한 2-Tier 인프라 구축
### Azure를 활용해 Web 서비스를 위한 인프라를 구축하고 Wordpress 가상머신과 데이터 베이스 운용

</br>

## 🗓️ 진행 기간
- 2022.08.01 ~ 2022.08.05

</br>

## 👥 팀 구성
- Infra 구축 1명

</br>

## ⚙️ 사용 기술
#### CSP
<img src="https://img.shields.io/badge/Microsoft Azure-0078D4?style=for-the-badge&logo=Microsoft Azure&logoColor=white"> <!--azure-->

### OS
<img src="https://img.shields.io/badge/Ubuntu 18.04LTS-E95420?style=for-the-badge&logo=Ubuntu&logoColor=white"> <!--Ubuntu-->

### DB
<img src="https://img.shields.io/badge/mysql-4479A1?style=for-the-badge&logo=mysql&logoColor=white">  <!--mysql-->

#### Team Collabolation Tool
<img src="https://img.shields.io/badge/Notion-000000?style=for-the-badge&logo=Notion&logoColor=white"> <!--Notion-->

</br>

## 📝 상세 내용 
### 📌 Infra Architecture
![archi](https://user-images.githubusercontent.com/117608997/215339332-e862383c-69a5-4234-8555-8ab551142137.jpg)

</br>

## ⛓️ 구축 과정
### 🔗 Notion Link
#### - [Azure 2-Tier Infra](https://glen-party-257.notion.site/Azure-2-Tier-Infra-ba12623f0fde4f709fb7636f8b2f28f6)

### 🔗 Git Link
#### - [Azure 2-Tier](https://github.com/signaturejinn/Azure_2-Tier_Infra/tree/main/Azure_2-Tier)


</br>

## 🙋🏻‍♂️ 담당 업무
- Wordpress – MySQL 연동
- Jumpbox를 통해서 서버 관리
- Application Gateway를 이용해 클라이언트 접속 허용
- Web
    - subnet : ubuntu 18.04LTS + wordpress 가상머신 운영
- DB Subnet: MySQL database
- Web tier에 Autoscale 구성
