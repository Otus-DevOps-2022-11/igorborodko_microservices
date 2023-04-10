
#Создадим Docker хост в Yandex Cloud
yc compute instance create \
  --name docker-host \
  --zone ru-central1-a \
  --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
  --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
  --ssh-key /home/house/.ssh/id_rsa.pub
  

# IP 
    address: 10.128.0.14
    one_to_one_nat:
      address: 158.160.49.225
		
# После чего можно инициализировать окружение Docker.

docker-machine create \
  --driver generic \
  --generic-ip-address=158.160.49.225 \
  --generic-ssh-user yc-user \
  --generic-ssh-key /home/house/.ssh/id_rsa \
  docker-host
  
# Были ошибки 
Creating CA: /home/house/.docker/machine/certs/ca.pem
Creating client certificate: /home/house/.docker/machine/certs/cert.pem
Running pre-create checks...
Creating machine...
(docker-host) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Error running SSH command: ssh command error:
command : netstat -tln
err     : exit status 127
output  : bash: netstat: command not found

...
...
...

Error running SSH command: ssh command error:
command : netstat -tln
err     : exit status 127
output  : bash: netstat: command not found

Error creating machine: Error running provisioning: Unable to verify the Docker daemon is listening: Maximum number of retries (10) exceeded

# Выполнил

eval $(docker-machine env docker-host)


# docker-machine создалась
house@Igor:/mnt/c/Users/house$ docker-machine ls
NAME          ACTIVE   DRIVER    STATE     URL                         SWARM   DOCKER    ERRORS
docker-host   *        generic   Running   tcp://158.160.49.225:2376           v23.0.3

# Подключаемся к нашей созданной машине
house@Igor:/mnt/c/Users/house$ ssh yc-user@158.160.49.225

# СТавим прометеус
root@docker-host:/home/yc-user# docker run --rm -p 9090:9090 -d --name prometheus prom/prometheus

root@docker-host:/home/yc-user# docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                       NAMES
8a98ee0a2b65   prom/prometheus   "/bin/prometheus --c…"   4 seconds ago   Up 3 seconds   0.0.0.0:9090->9090/tcp, :::9090->9090/tcp   prometheus

# прометеус доступен 
http://158.160.49.225:9090/


# Получил вывод
prometheus_build_info
prometheus_build_info{branch="HEAD", goarch="amd64", goos="linux", goversion="go1.19.7", instance="localhost:9090", job="prometheus", revision="edfc3bcd025dd6fe296c167a14a216cab1e552ee", tags="netgo,builtinassets", version="2.43.0"}




# ----------------------------

Создаем образ
В директории prometheus собираем Docker образ:
Где USER_NAME - ВАШ логин от DockerHub

$ export USER_NAME=housedegor
$ docker build -t $USER_NAME/prometheus .


# Собрал три образа для
 /src/ui      $ bash docker_build.sh
 /src/post-py $ bash docker_build.sh
 /src/comment $ bash docker_build.sh
 
 или
 
 for i in ui post-py comment; do cd src/$i; bash docker_build.sh; cd -; done


# Одной командой из корня

root@Igor:/home/house/GitHub/igorborodko_microservices# for i in ui post-py comment; do cd src/$i; bash docker_build.sh; cd -; done

[+] Building 82.5s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                                                                 0.0s
 => => transferring dockerfile: 375B                                                                                                 0.0s
 => [internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 2B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/ruby:2.3                                                                          1.8s
 => [auth] library/ruby:pull token for registry-1.docker.io                                                                          0.0s
 => [internal] load build context                                                                                                    0.0s
 => => transferring context: 846B                                                                                                    0.0s
 => [1/7] FROM docker.io/library/ruby:2.3@sha256:78cc821d95c48621e577b6b0d44c9d509f0f2a4e089b9fd0ca2ae86f274773a8                   50.2s
 => => resolve docker.io/library/ruby:2.3@sha256:78cc821d95c48621e577b6b0d44c9d509f0f2a4e089b9fd0ca2ae86f274773a8                    0.0s
 => => sha256:78cc821d95c48621e577b6b0d44c9d509f0f2a4e089b9fd0ca2ae86f274773a8 2.37kB / 2.37kB                                       0.0s
 => => sha256:e79bb959ec00faf01da52437df4fad4537ec669f60455a38ad583ec2b8f00498 45.34MB / 45.34MB                                    10.6s
 => => sha256:d4b7902036fe0cefdfe9ccf0404fe13322ecbd552f132be73d3e840f95538838 10.78MB / 10.78MB                                     2.4s
 => => sha256:1b2a72d4e03052566e99130108071fc4eca4942c62923e3e5cf19666a23088ef 4.34MB / 4.34MB                                       2.0s
 => => sha256:79c9f9a430a66c7e307573c8220d0a83a834fdcf4f5034a91730b7015742c75b 2.00kB / 2.00kB                                       0.0s
 => => sha256:c0d23ebb3ed63af928b7c4a954de87a5d3c1fe98a2dfcb2a44e685a3e65c3c61 7.50kB / 7.50kB                                       0.0s
 => => sha256:d54db43011fd116b8cb6d9e49e268cee1fa6212f152b30cbfa7f3c4c684427c3 50.07MB / 50.07MB                                    12.9s
 => => sha256:69d473365bb390367b7a54a3e890ca28c4640a56dfe4f53a0036130c964a1e52 215.05MB / 215.05MB                                  31.4s
 => => sha256:84ed2a0dc034bbdc8a28dfb592e5176338052ada31aa748ce78170876a8a0a84 204B / 204B                                          11.0s
 => => extracting sha256:e79bb959ec00faf01da52437df4fad4537ec669f60455a38ad583ec2b8f00498                                            6.8s
 => => sha256:8952ca0665c56358d62b13871b6a1cb62be5ea173bfa53bfc3a21efa96ca10d8 33.62MB / 33.62MB                                    19.6s
 => => sha256:ef485f36c62492911d22d57cdb485fc295f58e37451fef4a7e8208ca56c36401 149B / 149B                                          13.3s
 => => extracting sha256:d4b7902036fe0cefdfe9ccf0404fe13322ecbd552f132be73d3e840f95538838                                            1.2s
 => => extracting sha256:1b2a72d4e03052566e99130108071fc4eca4942c62923e3e5cf19666a23088ef                                            0.5s
 => => extracting sha256:d54db43011fd116b8cb6d9e49e268cee1fa6212f152b30cbfa7f3c4c684427c3                                            6.5s
 => => extracting sha256:69d473365bb390367b7a54a3e890ca28c4640a56dfe4f53a0036130c964a1e52                                           15.0s
 => => extracting sha256:84ed2a0dc034bbdc8a28dfb592e5176338052ada31aa748ce78170876a8a0a84                                            0.0s
 => => extracting sha256:8952ca0665c56358d62b13871b6a1cb62be5ea173bfa53bfc3a21efa96ca10d8                                            2.7s
 => => extracting sha256:ef485f36c62492911d22d57cdb485fc295f58e37451fef4a7e8208ca56c36401                                            0.0s
 => [2/7] RUN apt-get update -qq && apt-get install -y build-essential --force-yes                                                   8.3s
 => [3/7] RUN mkdir /app                                                                                                             0.5s
 => [4/7] WORKDIR /app                                                                                                               0.0s
 => [5/7] ADD Gemfile* /app/                                                                                                         0.0s
 => [6/7] RUN bundle install                                                                                                        20.5s
 => [7/7] ADD . /app                                                                                                                 0.0s
 => exporting to image                                                                                                               0.8s
 => => exporting layers                                                                                                              0.8s
 => => writing image sha256:0ffb9b41c2eda9b1b77e371931c75eeca65a888208e0c1be5b300400c27982a9                                         0.0s
 => => naming to docker.io/housedegor/ui                                                                                             0.0s
/home/house/GitHub/igorborodko_microservices
[+] Building 41.6s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                                                 0.1s
 => => transferring dockerfile: 32B                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 2B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/python:3.6.0-alpine                                                               1.0s
 => [auth] library/python:pull token for registry-1.docker.io                                                                        0.0s
 => [1/4] FROM docker.io/library/python:3.6.0-alpine@sha256:142fc3f338b10569d08c3f3855c492c2a176b0c45af099f9ebe87f0fededb210         0.0s
 => [internal] load build context                                                                                                    0.0s
 => => transferring context: 249B                                                                                                    0.0s
 => CACHED [2/4] WORKDIR /app                                                                                                        0.0s
 => [3/4] ADD . /app                                                                                                                 0.1s
 => [4/4] RUN apk --no-cache --update add build-base &&     pip install -r /app/requirements.txt &&     apk del build-base          39.9s
 => exporting to image                                                                                                               0.5s
 => => exporting layers                                                                                                              0.4s
 => => writing image sha256:a75aefcc1cd71b405072fac944a30eec391ac1bf89afcee834b13249fd2e20e4                                         0.0s
 => => naming to docker.io/housedegor/post                                                                                           0.0s
/home/house/GitHub/igorborodko_microservices
[+] Building 14.1s (12/12) FINISHED
 => [internal] load build definition from Dockerfile                                                                                 0.1s
 => => transferring dockerfile: 325B                                                                                                 0.0s
 => [internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 2B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/ruby:2.3                                                                          0.5s
 => [1/7] FROM docker.io/library/ruby:2.3@sha256:78cc821d95c48621e577b6b0d44c9d509f0f2a4e089b9fd0ca2ae86f274773a8                    0.0s
 => [internal] load build context                                                                                                    0.0s
 => => transferring context: 598B                                                                                                    0.0s
 => CACHED [2/7] RUN apt-get update -qq && apt-get install -y build-essential --force-yes                                            0.0s
 => CACHED [3/7] RUN mkdir /app                                                                                                      0.0s
 => CACHED [4/7] WORKDIR /app                                                                                                        0.0s
 => [5/7] ADD Gemfile* /app/                                                                                                         0.0s
 => [6/7] RUN bundle install                                                                                                        12.7s
 => [7/7] ADD . /app                                                                                                                 0.0s
 => exporting to image                                                                                                               0.7s
 => => exporting layers                                                                                                              0.6s
 => => writing image sha256:c3a90ea28d4b191e3c2d6750793fe2d6555fb141999c12451819864694d28a2b                                         0.0s
 => => naming to docker.io/housedegor/comment   
 
 
# Проверяем созданные образы
root@Igor:/home/house/GitHub/igorborodko_microservices# docker images
REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
housedegor/comment            latest    c3a90ea28d4b   3 minutes ago   1.01GB
housedegor/post               latest    a75aefcc1cd7   3 minutes ago   111MB
housedegor/ui                 latest    0ffb9b41c2ed   3 minutes ago   1.01GB


# Добавим новый сервис

root@Igor:/home/house/GitHub/igorborodko_microservices/docker# cat docker-compose.yml
version: '3.3'
services:
  post_db:
    image: mongo:${MONGO_VER}
    volumes:
      - post_db:/data/db
    networks:
      - back_net
  ui:
    build: ./ui
    image: ${USERNAME}/ui:${SERVICE_VER}
    ports:
      - 9292:9292/tcp
    networks:
      - front_net
  post:
    build: ./post-py
    image: ${USERNAME}/post:${SERVICE_VER}
    networks:
      - front_net
      - back_net
  comment:
    build: ./comment
    image: ${USERNAME}/comment:${SERVICE_VER}
    networks:
      - back_net
      - front_net
   prometheus:
    image: ${USERNAME}/prometheus
    ports:
      - '9090:9090'

   volumes:
      - prometheus_data:/prometheus
   command: # Передаем доп параметры в командной строке
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention=1d' # Задаем время хранения метрик в 1 день

volumes:
  prometheus_data:

networks:
  back_net:
  front_net:
  
  22 стр.
  
  
# При запуске ошибка
root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker-compose up -d
ERROR: build path /home/house/GitHub/igorborodko_microservices/docker/ui either does not exist, is not accessible, or is not a valid URL.
  
  
# Поправил - добавил волюмс, убрал пути к билдам.

root@Igor:/home/house/GitHub/igorborodko_microservices/docker# cat docker-compose.yml
version: '3.3'
services:
  post_db:
    image: mongo:${MONGO_VER}
    volumes:
      - post_db:/data/db
    networks:
      - back_net
  ui:
    image: ${USERNAME}/ui:${SERVICE_VER}
    ports:
      - 9292:9292/tcp
    networks:
      - front_net
  post:
    image: ${USERNAME}/post:${SERVICE_VER}
    networks:
      - front_net
      - back_net
  comment:
    image: ${USERNAME}/comment:${SERVICE_VER}
    networks:
      - back_net
      - front_net
  prometheus:
    image: ${USERNAME}/prometheus
    ports:
      - '9090:9090'

    volumes:
      - prometheus_data:/prometheus
    command: # Передаем доп параметры в командной строке
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention=1d' # Задаем время хранения метрик в 1 день
    networks:
      - back_net
      - front_net

volumes:
  prometheus_data:
  post_db:

networks:
  back_net:
  front_net:
  
# Запустилось
root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker-compose up -d
Creating network "docker_back_net" with the default driver
Creating network "docker_front_net" with the default driver
Creating volume "docker_prometheus_data" with default driver
Creating volume "docker_post_db" with default driver
Creating docker_ui_1         ... done
Creating docker_post_db_1    ... done
Creating docker_comment_1    ... done
Creating docker_prometheus_1 ... done
Creating docker_post_1       ... done
root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                    NAMES
ceb40c113228   mongo:3.2                "docker-entrypoint.s…"   44 seconds ago   Up 35 seconds   27017/tcp                docker_post_db_1
e69df41017ec   housedegor/ui:1.0        "puma"                   44 seconds ago   Up 36 seconds   0.0.0.0:9292->9292/tcp   docker_ui_1
f12341b03e5f   housedegor/prometheus    "/bin/prometheus --c…"   44 seconds ago   Up 35 seconds   0.0.0.0:9090->9090/tcp   docker_prometheus_1
2d0aa84cb462   housedegor/comment:1.0   "puma"                   44 seconds ago   Up 35 seconds                            docker_comment_1
99a16837939a   housedegor/post:1.0      "python3 post_app.py"    44 seconds ago   Up 35 seconds                            docker_post_1
  
  
  
 # ----приложение доступно по http://localhost:9292/	прометеус по http://localhost:9090/graph
 
 
 46 стр.
 Ошибка 
 root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker-compose up -d
ERROR: yaml.parser.ParserError: while parsing a block mapping
  in "./docker-compose.yml", line 3, column 3
expected <block end>, but found '<block mapping start>'
  in "./docker-compose.yml", line 40, column 4
  

# Поправил

root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker-compose up -d
Creating network "docker_back_net" with the default driver
Creating network "docker_front_net" with the default driver
Creating docker_comment_1       ... done
Creating docker_post_1          ... done
Creating docker_prometheus_1    ... done
Creating docker_ui_1            ... done
Creating docker_post_db_1       ... done
Creating docker_node-exporter_1 ... done
root@Igor:/home/house/GitHub/igorborodko_microservices/docker# docker-compose ps
         Name                       Command               State           Ports
----------------------------------------------------------------------------------------
docker_comment_1         puma                             Up
docker_node-exporter_1   /bin/node_exporter --path. ...   Up      9100/tcp
docker_post_1            python3 post_app.py              Up
docker_post_db_1         docker-entrypoint.sh mongod      Up      27017/tcp
docker_prometheus_1      /bin/prometheus --config.f ...   Up      0.0.0.0:9090->9090/tcp
docker_ui_1              puma                             Up      0.0.0.0:9292->9292/tcp
root@Igor:/home/house/GitHub/igorborodko_microservices/docker# cat docker-compose.yml
version: '3.3'
services:
  post_db:
    image: mongo:${MONGO_VER}
    volumes:
      - post_db:/data/db
    networks:
      - back_net
  ui:
    image: ${USERNAME}/ui:${SERVICE_VER}
    ports:
      - 9292:9292/tcp
    networks:
      - front_net
  post:
    image: ${USERNAME}/post:${SERVICE_VER}
    networks:
      - front_net
      - back_net
  comment:
    image: ${USERNAME}/comment:${SERVICE_VER}
    networks:
      - back_net
      - front_net
  prometheus:
    image: ${USERNAME}/prometheus
    ports:
      - '9090:9090'

    volumes:
      - prometheus_data:/prometheus
    command: # Передаем доп параметры в командной строке
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention=1d' # Задаем время хранения метрик в 1 день
    networks:
      - back_net
      - front_net

  node-exporter:
    image: prom/node-exporter:v0.15.2
    user: root
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.ignored-mount-points="^/(sys|proc|dev|host|etc)($$|/)"'
    networks:
      - back_net

volumes:
  prometheus_data:
  post_db:

networks:
  back_net:
  front_net: