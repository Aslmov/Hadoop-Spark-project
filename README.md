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

Lien officiel pour installer Docker(https://docs.docker.com/desktop/setup/install/linux/ubuntu/#install-docker-desktop)

Télécharger l'image docker uploadée sur dockerhub:

    docker pull liliasfaxi/hadoop-cluster:latest

![Capture d’écran](https://github.com/user-attachments/assets/f4d74696-20b4-4cb4-ab8c-bd33bd292b15)

2 - Création des trois contenaires à partir de l'image téléchargée 


2.1 - Créer un réseau qui permettra de relier les trois contenaires

    docker network create --driver=bridge hadoop

![Capture d’écran](https://github.com/user-attachments/assets/be13043c-29c4-491e-9b0a-0f5332c3f694)


2.2 - Créer et lancer les trois contenaires 

les instructions -p permettent de faire un mapping (diriger des données ou les références ) entre les ports de la machine hôte et ceux du contenaire

    docker run -itd --net=hadoop -p 9870:9870 -p 8088:8088 -p 7077:7077 -p 16010:16010 --name hadoop-master --hostname hadoop-master liliasfaxi/hadoop-cluster:latest
    
    docker run -itd -p 8040:8042 --net=hadoop --name hadoop-worker1 --hostname hadoop-worker1 liliasfaxi/hadoop-cluster:latest
    
    docker run -itd -p 8041:8042 --net=hadoop --name hadoop-worker2 --hostname hadoop-worker2 liliasfaxi/hadoop-cluster:latest

![Capture d’écran ](https://github.com/user-attachments/assets/4252fa90-1d87-4f3d-8d26-fe27389dce44)


2.3 - Vérifier les trois contenaires 

 Vérifier que les trois contenaires tournent bien en lançant la commande docker ps. Un résultat semblable au suivant devra s'afficher:
 ![Capture d’écran ](https://github.com/user-attachments/assets/95670f79-5bd8-4989-8e69-b8b77c0b8f96)


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

Nous allons utiliser le fichier purchases.txt comme entrée pour le traitement MapReduce. Ce fichier se trouve déjà dans votre container, sous le répertoire principal (il suffit de taper ls et vous le verrez).
À partir du contenaire master, charger le fichier purchases dans le répertoire input (de HDFS) que vous avez créé:

    hdfs dfs -put purchases.txt input

Pour afficher le contenu du répertoire input, la commande est:

    hdfs dfs -ls input

Pour afficher les dernières lignes du fichier purchases:

    hdfs dfs -tail input/purchases.txt

Le résultat suivant va donc s'afficher:

![Capture d’écran](https://github.com/user-attachments/assets/f912ad33-11f1-4cd3-af5c-c51e90b3b909)

## Troisième étape : Interfaces Web pour Hadoop

Hadoop propose plusieurs interfaces web permettant de surveiller le fonctionnement de ses différentes composantes. Il est possible d'accéder à ces pages directement depuis la machine hôte grâce à l'option -p de la commande `docker run`, qui permet de publier un port du conteneur sur l'hôte. Pour exposer tous les ports disponibles, vous pouvez utiliser l'option -P lors du lancement du conteneur.

En examinant la commande `docker run` mentionnée précédemment, vous remarquerez que deux ports ont été exposés sur la machine maître :

- **Le port 9870** : Permet d'afficher les informations du **namenode**.
- **Le port 8088** : Permet d'afficher les informations du **resource manager** de Yarn et de visualiser l'état des différents jobs.

Une fois le cluster démarré et Hadoop prêt à l'emploi, vous pouvez accéder à ces interfaces via votre navigateur préféré en vous rendant sur : `http://localhost:9870`. Vous y trouverez les informations suivantes :

![Capture d’écran ](https://github.com/user-attachments/assets/14f8f5d9-378b-482e-8dc6-476ae79c4462)

Vous pouvez également visualiser l'avancement et les résultats de vos Jobs (Map Reduce ou autre) en allant à l'adresse: http://localhost:8088.

# Map Reduce

Un Job Map-Reduce se compose principalement de deux types de programmes:

    Mappers : permettent d’extraire les données nécessaires sous forme de clef/valeur, pour pouvoir ensuite les trier selon la clef
    Reducers : prennent un ensemble de données triées selon leur clef, et effectuent le traitement nécessaire sur ces données (somme, moyenne, total...)

## Wordcount

Nous allons tester un programme MapReduce grâce à un exemple très simple, le WordCount, l'équivalent du HelloWorld pour les applications de traitement de données. Le Wordcount permet de calculer le nombre de mots dans un fichier donné, en décomposant le calcul en deux étapes:

    L'étape de Mapping, qui permet de découper le texte en mots et de délivrer en sortie un flux textuel, où chaque ligne contient le mot trouvé, suivi de la valeur 1 (pour dire que le mot a été trouvé une fois)
    L'étape de Reducing, qui permet de faire la somme des 1 pour chaque mot, pour trouver le nombre total d'occurrences de ce mot dans le texte.

Commençons par créer un projet Maven dans VSCode. Nous utiliserons dans notre cas JDK 1.8.Version de JDK

### Pour créer un projet Maven dans VSCode:

**Prenez soin d'avoir les extensions Maven for Java et Extension Pack for Java activées.**

Créer un nouveau répertoire dans lequel vous inclurez votre code.
Faites un clic-droit dans la fenêtre Explorer et choisir Create Maven Project.
Choisir No Archetype
Définir les valeurs suivantes pour votre projet:
GroupId: hadoop.mapreduce
ArtifactId: wordcount
Version: 1
Ouvrir le fichier pom.xml automatiquement créé, et ajouter les dépendances suivantes pour Hadoop, HDFS et Map Reduce:
      <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>hadoop.mapreduce</groupId>
        <artifactId>wordcount</artifactId>
        <version>1.0-SNAPSHOT</version>
    
        <properties>
            <maven.compiler.source>8</maven.compiler.source>
            <maven.compiler.target>8</maven.compiler.target>
        </properties>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-common -->
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-common</artifactId>
                <version>3.3.6</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-mapreduce-client-core -->
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-mapreduce-client-core</artifactId>
                <version>3.3.6</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-hdfs -->
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-hdfs</artifactId>
                <version>3.3.6</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-mapreduce-client-common -->
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-mapreduce-client-common</artifactId>
                <version>3.3.6</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-mapreduce-client-jobclient -->
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-mapreduce-client-jobclient</artifactId>
                <version>3.3.6</version>
            </dependency>
        </dependencies>
    </project>

# Référence

Travaux Pratiques Big Data GL4 - INSAT : https://insatunisia.github.io/TP-BigData/
