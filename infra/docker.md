# Docker

https://docker-curriculum.com/#hello-world

- 순차적으로 따라함

- 설치
  - sudo apt-get remove docker docker-engine docker.io
  - *Images* -  `docker pull` 
  - *Containers* -  `docker run` `docker ps` 
  - *Docker Daemon* - The background service running on the host that manages building, running and distributing Docker containers. The daemon is the process that runs in the operating system which clients talk to.
  - *Docker Client* - The command line tool that allows the user to interact with the daemon. - - 
  - *Docker Hub* - A [registry](https://hub.docker.com/explore/) of Docker images. 
- hello world
  - Foodtruck + elastic search
  - network
    - docker network create foodtrucks-net
    - docker network ls
  - use network
    - docker run -d --name es --net foodtrucks-net -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.2
- docker compose
  - 아직 compose까지 들어가는건 어려운듯, 설정값이 너무 많음
  - image 받고 수정한 후, 다시 배포할때 설정하기 손쉬움