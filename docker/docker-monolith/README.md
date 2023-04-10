# igorborodko_infra
igorborodko Infra repository


...
root@Igor:/home/house/GitHub/igorborodko_infra/docker-monolith# docker run --name reddit -d -p 9292:9292 reddit:latest
b2ce5ce9ac57b4a33460c807e168a41971146e9dd017071b7ba6605e248dda3a



http://127.0.0.1:9292/login

# Залогинился в докерхаб
root@Igor:/mnt/c/Users/house# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: housedegor
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
root@Igor:/mnt/c/Users/house#


# Навесил тэг
root@Igor:/mnt/c/Users/house# docker tag reddit:latest housedegor/otus-reddit:1.0


# Запушил образ
root@Igor:/mnt/c/Users/house#  docker push housedegor/otus-reddit:1.0
The push refers to repository [docker.io/housedegor/otus-reddit]
25ce16cc0260: Pushed
b8b50c58732d: Pushed
c4f2dc6116e8: Pushed
0f5d706aac8c: Pushed
81a4035c722c: Pushed
d04296420155: Pushed
f5a66746e1e2: Pushed
66999a39c767: Pushed
1e1783e6960a: Pushed
d543b8cad89e: Mounted from library/ubuntu
1.0: digest: sha256:9589eef8ed120f3ced17cbef83749cbc4758f73f8b2d5a40de36b315985c6414 size: 2413

# Запусстил из докер хаба образ
root@Igor:/mnt/c/Users/house# docker run --name reddit -d -p 9292:9292 housedegor/otus-reddit:1.0
2314c467c92aee1069194cf3017eda29d9c1569ed0903ec95db3ab33063ba944


# Проверил логи
root@Igor:/mnt/c/Users/house# docker logs reddit -f
about to fork child process, waiting until server is ready for connections.
forked process: 9
child process started successfully, parent exiting
Puma starting in single mode...
* Puma version: 5.6.5 (ruby 2.7.0-p0) ("Birdie's Version")
*  Min threads: 0
*  Max threads: 5
*  Environment: development
*          PID: 32
/reddit/helpers.rb:4: warning: redefining `object_id' may cause serious problems
* Listening on http://0.0.0.0:9292
Use Ctrl-C to stop
172.17.0.1 - - [28/Feb/2023:14:17:33 +0000] "GET / HTTP/1.1" 200 1861 0.0462
172.17.0.1 - - [28/Feb/2023:14:17:35 +0000] "GET / HTTP/1.1" 200 1861 0.0336


# ПРоверил процессы
root@Igor:/mnt/c/Users/house# docker exec -it reddit bash
root@2314c467c92a:/# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   3980  2748 ?        Ss   14:17   0:00 /bin/bash /start.sh
root         9  0.7  2.1 980720 64500 ?        Sl   14:17   0:01 /usr/bin/mongod --fork --logpath /var/log/mongod.log --config /etc/mongodb.conf
root        32  0.4  1.8 710016 55496 ?        Sl   14:17   0:01 puma 5.6.5 (tcp://0.0.0.0:9292) [reddit]
root        44  0.3  0.1   4244  3388 pts/0    Ss   14:21   0:00 bash
root        57  0.0  0.0   5896  2860 pts/0    R+   14:21   0:00 ps aux


