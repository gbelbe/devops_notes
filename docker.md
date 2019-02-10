## Image Docker = Classe (qui peut être instanciée autant de fois que l'on veut)
## Container Docker = instance de l'image, qui est executée (donc utilise un process)
## Registry = les images sont stockées dans une registry (publique kitematic == docker store) ou privée.
            gcr.io sur gcp / nexus artifactory ...


## Lister les images téléchargées sur une machine:
    docker image ls

## Telecharger une image d'une registry:
    docker image pull imagename

## Instancier un container a partir d'une image:
    docker container run imagename echo "hello world"

    Instancier un shell a partir d'un container créé a partir d'une image:
    docker container run -i -t busybox  (-i = stdin input /// -t = tty stdout+stderr )
    on peut faire docker container run --help

Instancier un container en mode détaché à partir d'une image:
    docker container run -d  imagename sh -c 'shell script command'

    lister les containers en train de tourner
    docker container ls

    Afficher les logs d'un container: docker container logs -f container_name_ou_id

    Arreter un container:
    docker container stop container_name_ou_id
    docker container ls -a
    docker container rm container_name_ou_id

    supprimer tous les containers arrêtés:
    docker container prune

    idem pour les images:
    docker image ls
    docker image rm image_name
    docker image prune (supprime toutes les images non utilisées par des containers)

En résumé:
  docker client (docker build / docker pull / docker run)
  docker daemon (s'occuppe des instances containers)
  docker registry (héberge les images)

##Docker images layers:
  Les images docker sont écrites en couches.
  On peut utiliser une image comme base d'une précédente, en surchargeant la précédente.

  Si on se base sur une image précédente, pas d'utilisation de disque supplémentaire, ex from:Ubuntu:14.4
  bon par ex pour définir une image de base commune à tous les projets. Comme ça on ne met a jour que cette image de base qui sera reprise par tous les projets.


Note : on peut puller une image, puis travailler sur un container initié à partir de cette imate, y apporter des modifications, puis "commiter" le résultat en créant une nouvelle image.

[on pull une image alpine ]   docker image pull alpine:3.5
[on run un container a partir de l'image]   docker container run -it --name nodeJScontainer alpine3:5
[on installe des binaires]    apk add --update nodejs
                              exit
[on crée une nouvelle image a partir du container]    docker container commit nodeJScontainer mynodeJS (nom de la nouvelle image)

##Partager des images dans dockerHub:
  docker image tag nodeJScontainer  gbelbe/nodejs:1.0
  docker image ls
  docker login
  docker image push gbelbe/nodejs:1.0

Docker commands =>
    $docker <object> <command>
    $docker image pull
    $docker container prune

##Docker et le binding des ports sur la machine host:
    par défaut les services exposés par des containers ne sont pas accessibles, il faut ajouter des bindings de ports de l'interface physique de l'hôte vers les ports du container.
    Association 1:1 entre un port physique et un port d'un container.
    -p 80:8080 (bind le port 80 du host vers le port 8080 du container)

    si l'hote a plusieurs interfaces, on peut choisir depuis quel interface / IP faire le binding. (par défaut, toutes les interfaces (ip) sont bindées)
    ex: pour le ssh, on veut qu'il ne soit visible que de l'intérieur ou en vpn, on le bind sur l'adresse locale.
    -p 192.xx.xx.1:22:22

Ex: couchbase:
docker image pull couchdb:2.1
docker container run --name couchdb1 -d -p 80:5984 couchdb:2.1
- cree un container en mode détaché, appelé couchdb1 et ouvert sur le port 80 sur toutes les interfaces.

##Docker et les volumes
    Note, les documents créés dans des containers sont détruits lorsque le container est détruit.
    Il faut donc creer un volume, et s'occuper de sa persistance pour que les données restent.

    volume =  managés par docker sur le filesystem du host
    bind mount = specifié un mount sur le filesystem du host a un endroit spécifique (non géré par docker en automatique)
    tmpfs = en memoire

pour supprimer les volumes: docker volume rm volume_hash
docker volume prune (vire tous les volumes usagés)

docker volume create couchdb_vol
docker volume ls
docker container run -it -p 80:4398 -v couchdb_vol:/opt/couchdb/data couchdb_image:latest
    run a container interactive mode binded on port 80 of the machine and to a specific volume path, from image named couchdb_latest
    the path is the path on the container. On the local machine, data is saved on a docker specific foler. (see docker volume inspect volumeName)

##Bind mounts

--> Bind_mounts is another way to save data in a specific local directory on the host, it won't be managed by docker.
--mount type=bind, source=path-to-local-directory [must exist], target=path-inside-container

docker run -it --name myContainerName --mount type=bind,source=c:\data,target=c:\shareddata microsoft/windowsservercore powershell

ou avec -v: (à la place du named volume, on met le path complet sur le host local)
sudo docker run -p 8888:8888 -v /home/gaetan/Documents/ai-learnings/notebooks:/home/jovyan jupyter/minimal-notebook
--> docker run hostport:containerport -v hostdir:containerdir imagename

##Immutable infrastructure:

Images constituées en couches, avec notion d'héritage:

Les immages sont en lectures seules / au file system du container qui est en lecture écriture
Un fichié est modifié par recopie dans le layer du conteneur ("copy on write" strategy)
  
Immutability: 

- copy on write. On récupère de l'éxtérieur un objet que l'on veut modifier. Au lieu de le modifier directement, ce qui pourrait avoir un impact sur les autres utilisateurs potentiels, on en crée une copie avant de modifier la copie. (celà isole notre objet des autres utilisateurs)
- copy on read: si l'on utilise une ressource externe (ex: une API) et que l'on n'a pas confiance que le code ou l'implementation peut changer. On la copie pour l'utiliser en locale (copy on read). De cette manière même si l'API originale change, notre copie de lecture restera immutable.
  


Un fichié est supprimé par un marqueur "suppression" dans le layer du conteneur
Ex: install debian + emacs + apache: les couches sont le résultats des commandes apt-get
    la couche du dessus n'est pas modifiée. 

Ex: si l'on repart a partir d'une image ubuntu pour créer une image docker ==> recrée une image ajoutant juste les données nécessaires à la nouvelle couche.
Perf: mapping des fichiers de l'image stockée sur le disque vers la mémoire vive n'est fait qu'une fois par image ==> un binaire partagé pour plusieurs containers (s'ils partagent la même image)



