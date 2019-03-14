# Virtualisation 

## Vagrant

Récuperer une box vagrant debian puis se connecter dedans:
    
    vagrant init debian/stretch64
    vagrant up
    vagrant ssh

Note: Parfois il faut executer les commandes en root

Pour éteindre la machine virtuelle et la supprimer:
    
    vagrant halt
    vagrant destroy

## Docker

    docker-compose up -d

    docker-compose scale repo=3

    docker-compose down

    docker ps -a
