---
id: 318
title: Installation d&rsquo;un cluster Kafka
date: 2017-10-23
author: admin
layout: post
guid: 'http://boussoufiane.com/?p=318'
permalink: /installation-dun-cluster-kafka/
categories:
  - JAVA
published: true
---
1. Introduction 5

Kafka est un système de message distribué, il permet les échanges de messages asynchrones entre différents systèmes.
  
Kafka utilise Zookeeper pour gérer les différents instances d’un cluster .
  
Dans notre installation, on va utiliser un cluster de 3 instances Kafka et 3 instances Zookeeper installées respectivement dans 3 serveurs différents.
  
Chaque instance de Kafka et Zookeeper a un identifiant pour l’identifier de facon unique dans le cluster .

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/archi_kafka.jpg)

2. Installation
  
3.1. Préparation

D’abord on crée un répertoire de travail qu’on appellera « Kafka » dans lequel on va mettre l’extraction du zip du téléchargement de Kafka

&nbsp;

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_1.jpg)
&nbsp;

Ensuite on crée deux sous répertoires « kafka-logs » et « zookeeper-data» dans le répértoire principale de kafka

Ces deux répertoires contiendront respectivement les données de Kafka et Zookeeper

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kakfa_2.png)

&nbsp;

&nbsp;

3.2. Installation de Zookeeper
  
L’installation de Kafka contient un zookeeper embarqué.
  
D’abord on va créer un fichier « myid » sans extention dans le répertoire « zookeeper-data» qu’on a créé précédemment .
  
Ce fichier va contenir un numéro identifiant chaque instance de zookeeper dans le cluster ( ce numéro doit être compris entre 1 et 255)
  
Dans notre cas, on aura 3 instances de zookeeper ayant 1,2 et 3 comme identifiants.

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_3.png)
![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/id_zookeeper.jpg)

Il faut ensuite modifier le fichier de configuration de chaque instance zookeeper qui se trouve dans le chemin :
  
{répertoire-kafka}\conf\zookeeper.properties .

&#8211; Modifier la propriété « dataDir » pour mettre le chemin des données de zookeeper
  
&#8211; Mettre un port client différent « clientPort » pour chaque zookeper instance : Dans notre cas on attribuera les ports 2186, 2187,2187 aux différentes instances.
  
&#8211; Ajouter les différents serveurs dans chaque configuration de chaque instance zookeeper : 

La configuration sera sous la forme : server.id = host : port : port 

• Id : est l’identifiant de chaque instance de zookeeper configuré dans le fichier myid
  
• Host : l’adresse IP ou le nom de demaine de chaque instance
  
• Port : sont utilisés pour communquer avec les autres instances

La configuration générale de zookeeper doit être comme ci-dessous :

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_5.png)
  

Enfin on installe Zookeeper en tant que service avec l’outil nssm par exemple 

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_6.png)

&#8211; Mettre dans Path le chemin vers « zookeeper-server-start.bat »
  
&#8211; Mettre dans Setup directory : le dossier ou se trouve le « zookeeper-server-start.bat »
  
&#8211; Mettre dans argument le chemin complet vers le fichier de configuration de zookeeper « zookeeper.properties »

Il suffit maintenant de démarrer le service et vérifier que tout fonctionne dans les logs 

Il est préférable de démarrer tous les instances de Zookeeper avant de démarrer les instances de Kafka 

3.3. Installation de Kafka 

On modifier le fichier de configuration de chaque instance de Kafka qui se trouve dans le chemin :
  
{répertoire-kafka}\conf\server.properties .
  
&#8211; On modifie la propriété « log.dirs » pour mettre le chemin vers le répértoires de données de Kafka qu’on a créé précédemment 

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kakfa_7.png)

&#8211; On modifie la propriété « broker.id » pour identifier l’instance du broker de façon unique dans le cluster.
  
Dans notre cas, on mettra 0 pour le premier broker , 1 pour le deuxième et 2 pour le troisième broker .

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_8.png)

&#8211; On met à jour le nombre de réplication par défaut de la topic interne « offsets » à 3.
  
Ce paramètre est très important car cela permet d’avoir des réplications des métadata des différents brokers, si un nœud tombe en panne, le cluster va pouvoir attribuer un nouveau broker aux consommateurs puisqu’il a accès aux réplications des offsets.

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_9.png)

Enfin on installe Zookeeper en tant que service avec l’outil nssm comme on a fait pour zookeeper . 

![]({{site.url}}{{site.baseurl}}/assets/img/installation-dun-cluster-kafka/kafka_10.png)

&#8211; Mettre dans path le chemin vers le « kafka-server-start.bat »
  
&#8211; Mettre dans « setup directory » le chemin vers le repertoire de « kafka-server-start.bat »
  
&#8211; Mettre dans Arguments : le chemin vers le fichier de configuration server.properties
  
Enfin on démarre le service 

3.4. Vérification et monitoring
  
Pour vérifier l’état du cluster il faut consulter les fichiers de logs « server.log » et « controller.log » se trouvant dans le chemin : {kafka-répertoire}\logs.

Pour le monitoring du cluster on utilise le project « Kafka-manager » de Yahoo .

https://github.com/yahoo/kafka-manager

Il fournit une IHM pour monitorer le cluster depuis une interface graphique.

Cependant cet outil ne permet pas de visualiser les messages qui transitent via le cluster.
  
Pour cela il faut utiliser un autre outil ou utiliser les lignes de commandes.

On peut voir les brokers actifs dans le cluster.
