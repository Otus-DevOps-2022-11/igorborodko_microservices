# Micorservices

Выполнено основное задние.
Создано приложение с volume.
После рестарта посты были на месте.

------

docker pull mongo:latest
docker build -t housedegor/post:1.0 ./post-py
docker build -t housedegor/comment:1.0 ./comment
docker build -t housedegor/ui:1.0 ./ui

docker run -d --network=reddit --network-alias=post housedegor/post:1.0
docker run -d --network=reddit --network-alias=comment housedegor/comment:1.0
docker run -d --network=reddit -p 9292:9292 housedegor/ui:1.0


docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:4.2-rc
docker run -d --network=reddit --network-alias=post housedegor/post:1.0
docker run -d --network=reddit --network-alias=comment housedegor/comment:1.0
docker run -d --network=reddit -p 9292:9292 housedegor/ui:1.0



docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:4.2-rc
docker run -d --network=reddit --network-alias=post housedegor/post:1.0
docker run -d --network=reddit --network-alias=comment housedegor/comment:1.0
docker run -d --network=reddit -p 9292:9292 housedegor/ui:2.0


docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db -v reddit_db:/data/db mongo:4.2-rc
docker run -d --network=reddit --network-alias=post housedegor/post:1.0
docker run -d --network=reddit --network-alias=comment housedegor/comment:1.0
docker run -d --network=reddit -p 9292:9292 housedegor/ui:2.0