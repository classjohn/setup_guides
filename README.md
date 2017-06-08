## AWS 서버세팅

### 1. AWS EC2 instance 생성
---

### 2. AWS EC2에 SSH로 접속
---


### 3. 개발환경설정
Ubuntu 패키지 업그레이드
```
sudo apt-get update
```
Ruby on Rails에 필요한 여러 프로그램 설치
```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev nodejs
```
rbenv를 활용한 ruby -v 2.3.0 설치
```
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.4.0
rbenv global 2.4.0
ruby -v
```
bundler 설치
```
gem install bundler
```
기본 gem을 업데이트 할 때 마다 해줘야함
```
rbenv rehash
```
Nginx & Passenger 설치
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates

# Add Passenger APT repository
sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list'
sudo apt-get update

# Install Passenger & Nginx
sudo apt-get install -y nginx-extras passenger
```
Nginx 서버 실행
```
sudo service nginx start
```
Nginx 서버 설정(passenger를 사용하겠다)
```
sudo vi /etc/nginx/nginx.conf
```

```
##
# Phusion Passenger
##
# Uncomment it if you installed ruby-passenger or ruby-passenger-enterprise
##

include /etc/nginx/passenger.conf;
```
Passenger 설정(아래쪽 코드 붙여넣기)
```
sudo vi /etc/nginx/passenger.conf
```

```
passenger_ruby /home/ubuntu/.rbenv/shims/ruby; # If you use rbenv
```
다시 서버 켜기
```
sudo service nginx restart
```
최종 서버 설정
```
sudo vi /etc/nginx/sites-enabled/default
```

```
server {
        listen 80;
        listen [::]:80 ipv6only=on;

        server_name my_domain.com;
        passenger_enabled on;
        rails_env    production;
        root         /home/ubuntu/my_app_name/current/public;

        # redirect server error pages to the static page /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
}
```
Passenger로 Rails 실행하기
