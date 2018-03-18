---
id: 295
title: 'L&rsquo;ecosystème Hadoop et Big data'
date: 2016-12-30T15:52:23+00:00
author: admin
layout: post
guid: http://boussoufiane.com/?p=295
permalink: /lecosysteme-hadoop/
categories:
  - JAVA
---
## <span style="text-decoration: underline;"><strong>Big Data :</strong></span>

Big Data consiste dans le stockage , le traitement et l&rsquo;analyse de très forte volumétrie de données . Il permet également à tout le monde d’accéder en temps réel à des bases de données géantes . ce qui impossible à faire avec les outils classique de business intelligence .
  
Le concept de big data doit répondre à trois problèmatique dite règles de 3V :
  
&#8211; Volume : Il s’agit notamment d’un Volume de données important
  
&#8211; variété : on doit pouvoir traiter une grande Variété d’informations venant de sources différentes
  
&#8211; vitesse ou vélocité : on doit traiter rapidement les données

Google, Yahoo ou Facebook ont été les premiers à être confrontés à des volumétries de données extrêmement importantes .

Voici quelques exemples de technologies open source utilisées pour la gestion des données massives :

![]({{site.url}}{{site.baseurl}}/assets/img/2016-12-30-lecosysteme-hadoop/1713589-big-data-des-solutions-majoritairement-open-source.jpg)

&nbsp;

## <span style="text-decoration: underline;"><strong>Hadoop</strong></span>

Hadoop est une solution open source de big data qui fonctionne dans le cadre d&rsquo;un système distribué. Hadoop fait partie de la fondation Apache.

En termes d&rsquo;architecture, Hadoop a été pensé pour fonctionner sur plusieurs serveurs simultanément (jusqu&rsquo;à plusieurs milliers si besoin), chacun offrant de la puissance et du stockage. En pratique, les données sont réparties sur les différents serveurs, et Hadoop gère un système de réplication de façon à assurer une très haute disponibilité des données, même lorsqu&rsquo;un ou plusieurs serveurs sont défaillants. La force de Hadoop est donc de pouvoir bénéficier de la puissance de calcul de multiples serveurs banalisés en cluster. Les traitements parallélisés sont gérés par MapReduce, dont la mission est de répartir les traitements sur les différents serveurs, et inversement d&rsquo;agréger les résultats élémentaires en un résultat global.

Plus concrètement Hadoop est constitué de plusieurs composants couvrant le stockage et la répartition des données, les traitements distribués, l&rsquo;entrepôt de données, le workflow, la programmation, sans oublier la coordination de l&rsquo;ensemble des composants.

&nbsp;

![]({{site.url}}{{site.baseurl}}/assets/img/2016-12-30-lecosysteme-hadoop/1713640-hadoop-et-son-ecosysteme.jpg)

&nbsp;

&nbsp;

&nbsp;

**<span style="text-decoration: underline;">Le HDFS :</span>  **c&rsquo;est un système de fichiers distribué, extensible et portable développé par Hadoop à partir du GoogleFS. Écrit en Java, il a été conçu pour stocker de très gros volumes de données sur un grand nombre de machines équipées de disques durs banalisés. Il permet l&rsquo;abstraction de l&rsquo;architecture physique de stockage, afin de manipuler un système de fichiers distribué comme s&rsquo;il s&rsquo;agissait d&rsquo;un disque dur unique.

<span style="text-decoration: underline;"><strong>MapReduce : </strong></span>MapReduce permet de manipuler de grandes quantités de données en les distribuant dans un cluster de machines pour être traitées** ,** c&rsquo;est une plate-forme de programmation conçue pour écrire des applications permettant le traitement rapide et parallélisé de vastes quantités de données réparties sur plusieurs clusters de noeuds de calcul.

<span style="text-decoration: underline;"><b>HBase</b> : </span>est un système de gestion de base de données non-relationnelles distribué (NoSql ), écrit en Java, disposant d&rsquo;un stockage structuré pour les grandes tables.

<span style="text-decoration: underline;"><strong>HCatalog </strong></span>est une couche de métalangage permettant d&rsquo;attaquer les données HDFS via des schémas de type tables de données en lecture/écriture.

<span style="text-decoration: underline;"><strong>Hive </strong></span>est un système d&rsquo;entrepôt de données facilitant l&rsquo;agrégation des données, les requêtes ad hoc, et l&rsquo;analyse de grands ensembles de données stockées dans les systèmes de fichiers compatibles Hadoop. Hive dispose d&rsquo;un langage de type SQL appelé HiveQL.

**<span style="text-decoration: underline;">Pig</span>** est une plate-forme d&rsquo;analyse de vastes ensembles de données. Pig comprend un langage de haut niveau gérant la parallélisation des traitements d&rsquo;analyse.

<span style="text-decoration: underline;"><strong>Oozie </strong></span>est un outil de workflow dont l&rsquo;objectif est de simplifier la coordination et la séquence de différents traitements. Le système permet aux utilisateurs de définir des actions et les dépendances entre ces actions.

<span style="text-decoration: underline;"><strong>ZooKeeper </strong></span>est un service centralisé pour gérer les informations de configuration, de nommage, et assurer la synchronisation des différents serveurs via un cluster. Tous les services pris en charge par ZooKeeper peuvent être utilisés sous une forme ou une autre par les applications distribuées.

<span style="text-decoration: underline;"><strong>Flume</strong></span> : flume fonctionne comme un service distribué pour assurer la collecte de données en temps réel, leur stockage temporaire et leur diffusion vers une cible.Techniquement, un agent Flume permet de créer des routes pour relier une source à une cible via un canal d’échange. elle a pour pour but de récupérer les messages à partir de différentes sources, en particulier des fichiers de logs et de les envoyer vers une cible comme les fichiers HDFS de Hadoop .

<span style="text-decoration: underline;"><strong>Kafka</strong></span> est un système de messagerie distribué, originellement développé chez LinkedIn

**<span style="text-decoration: underline;">Spark</span>** :est un framework de traitements Big Data qui petrmet de faire du temps réél .

C&rsquo;est une plateforme open source construit pour effectuer des analyses sophistiquées et conçu pour la rapidité et la facilité d’utilisation .Spark présente plusieurs avantages par rapport aux autres technologies big data et MapReduce comme Hadoop et Storm.

Spark permet à des applications sur clusters Hadoop d’être exécutées jusqu’à 100 fois plus vite en mémoire, 10 fois plus vite sur disque. Il vous permet d’écrire rapidement des applications en Java, Scala ou Python et inclut un jeu de plus de 80 opérateurs haut-niveau. De plus, il est possible de l’utiliser de façon interactive pour requêter les données depuis un shell.

&nbsp;

&nbsp;

&nbsp;
