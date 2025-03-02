# Hadoop-Spark-project

Projet universitaire sur le traitement Batch avec Hadoop HDFS et Map Reduce

Ce projet est basé sur : Travaux Pratiques Big Data GL4 - INSAT (Référence plus bas)

## Aperçu du Projet

Ce projet universitaire est centré sur l'exploration et l'implémentation des technologies de traitement de données massives, spécifiquement en utilisant l'écosystème Hadoop. L'objectif principal est de se familiariser avec le framework Hadoop et le modèle de programmation MapReduce tout en mettant en œuvre une architecture distribuée à l'aide de Docker.

Notre déploiement du framework Hadoop repose sur la technologie des conteneurs Docker. Cette approche assure une parfaite cohérence entre les différents environnements de développement tout en simplifiant significativement le processus de configuration, comparé à une installation native. De plus, les conteneurs offrent une solution beaucoup plus légère et performante que les machines virtuelles traditionnelles, éliminant ainsi la surcharge d'exécution associée à la virtualisation complète.

## Objectifs Projet

Initiation au framework hadoop et au patron MapReduce, utilisation de docker pour lancer un cluster hadoop de 3 noeuds.

## Outils et Versions

    Apache Hadoop Version: 3.3.6.
    Docker Version latest
    Visual Studio Code Version 1.85.1 (ou tout autre IDE de votre choix)
    Java Version 1.8.
    Unix-like ou Unix-based Systems (Divers Linux et MacOS)

La version Linux utilisé est : Ubuntu 24.10 sur machine virtuelle

## Première étape : Installation de Docker et Hadoop

1 - Installer Docker

Télécharger l'image docker uploadée sur dockerhub:

    docker pull liliasfaxi/hadoop-cluster:latest

2 - Création des trois contenaires à partir de l'image téléchargée 


2.1 - Créer un réseau qui permettra de relier les trois contenaires

    docker network create --driver=bridge hadoop


2.2 - Créer et lancer les trois contenaires 

les instructions -p permettent de faire un mapping (diriger des données ou les références ) entre les ports de la machine hôte et ceux du contenaire

    docker run -itd --net=hadoop -p 9870:9870 -p 8088:8088 -p 7077:7077 -p 16010:16010 --name hadoop-master --hostname hadoop-master liliasfaxi/hadoop-cluster:latest
    
    docker run -itd -p 8040:8042 --net=hadoop --name hadoop-worker1 --hostname hadoop-worker1 liliasfaxi/hadoop-cluster:latest
    
    docker run -itd -p 8041:8042 --net=hadoop --name hadoop-worker2 --hostname hadoop-worker2 liliasfaxi/hadoop-cluster:latest


2.3 - Vérifier que les trois contenaires tournent bien en lançant la commande docker ps. Un résultat semblable au suivant devra s'afficher


3 - Entrer dans le contenaire master pour commencer à l'utiliser.

    docker exec -it hadoop-master bash

Le résultat de cette exécution sera le suivant:

    root@hadoop-master:~#
    
Une fois connecté, vous accéderez directement à l'interface shell du namenode, vous permettant de contrôler l'ensemble du cluster. Votre première action devra être d'initialiser les services Hadoop et Yarn. Pour simplifier cette opération, exécutez le script préconfiguré `start-hadoop.sh` qui s'occupera de démarrer tous les composants nécessaires.

Le résultat devra ressembler à ce qui suit:

## Deuxième étape : Premiers pas avec Hadoop

Toutes les commandes interagissant avec le système HDFS commencent par hdfs dfs. Ensuite, les options rajoutées sont très largement inspirées des commandes Unix standard.

Créer un répertoire dans HDFS, appelé input. Pour cela, taper:

    hdfs dfs -mkdir -p input

# Référence

Travaux Pratiques Big Data GL4 - INSAT : https://insatunisia.github.io/TP-BigData/
