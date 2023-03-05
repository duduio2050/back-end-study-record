php.ini 기초 setting

1. window php 공식 사이트에서 php 원하는 버전 파일 다운로드

2. php.ini-development, php.ini-production 둘중 하나의 파일을 복사하여 php.ini 로 이름 변경

3. php.ini 파일에서 ;extension_dir="./" 주석 해제

4. extension_dir = "C:/php8.1/ext" php 파일 위치로 변경

5. short_open_tag = On 변경

6. Dynamic Extensions 주석 해제
    curl, ftp, fileinfo, mbstring, mysqli ( 오라클이나 몽고db 사용 시  oci8_*, pdo 주석해제), openssl, 

7. php 환경 변수 셋팅

8. 윈도우 검색창에 환경 변수 검색

9. 환경 변수 클릭

10. 시스템 변수 > Path 선택 > 편집 클릭

11. 새로 만들기 > C:\php8.1 (PHP 설치한 폴더 경로) > 확인

12. php -v 명령어 입력 후 php 버전 확인

13. 설치 및 환경변수 셋팅 완료

14. 라라벨 설치 시 php.ini 오류 시 extension = php_intl.dll , extension = php_fileinfo.dll 추가

15. 그래도 설치가 안되는 경우 composer create-project laravel/laravel '앱 명' --ignore-platform-reqs