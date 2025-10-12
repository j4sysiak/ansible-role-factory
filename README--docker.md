# source ~/ansible-venv/bin/activate

tworzymy image i kontainer
# docker run \
-d \
--name icinga-master \
--hostname icinga-master \
-p 8080:80 \
ubuntu:22.04 \
sleep infinity


uruchamiamy kontener z bazą
#  docker start icinga-master
#  docker exec -it icinga-master bash

sprawdzamy czy kontener dziala
#  docker ps
i dopiero teraz laczymy dockera z ansible
#  ansible-playbook bootstrap_docker.yml