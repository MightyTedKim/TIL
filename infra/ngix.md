# NginX

- 기간 : 2020.4.15 ~

- 공부는 갓 생활코딩 활용

- 2013년 강의라서 경로가 다르지만, 기본적인 것은 동일해서 좋은 공부가 됐음

  

## 기본

- 더 적은 자원으로, 더빠르게 서비스하기 위한 경량화된 웹서버
- Apache가 독보적, NginX는 3위
  - apache는 오래됨
  - NginX는 개발의 모든 목적이 높은 성능에 맞춰져 있다. 그리고 잘 사용하지 않는 기능은 과감하게 제외함
- NGINX는 차세대 웹서버로 불린다. 

![Nginx](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/384/1394.gif)

### 설치

```
ps aux | grep apache2
```

If Apache is already installed on the server, stop it and disable it from starting on server boot:

```
systemctl stop apache2.service
sudo systemctl disable apache2.service
```

Install Nginx:

```
sudo apt-get install nginx
```

Start the Nginx service and enable it to start on boot:

```
sudo systemctl start nginx.service
sudo systemctl enable nginx.service
```

https://linuxhostsupport.com/blog/how-to-install-mediawiki-on-ubuntu-18-04/

### Hello world

- /var/www/html > index.nginx-debian.html
- http://20.41.80.77/index.nginx-debian.html

### 설정 파일

- /etc/nginx/sites-available/



- 더 자세한 공부는 나중에