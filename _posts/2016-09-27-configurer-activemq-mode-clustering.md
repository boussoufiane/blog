---
id: 236
title: Configurer un cluster ActiveMq pour la haute disponiblité
date: 2016-09-27T16:07:28+00:00
author: admin
layout: post
guid: http://boussoufiane.com/?p=236
permalink: /configurer-activemq-mode-clustering/
categories:
  - JAVA
---
### <span style="text-decoration: underline;"><strong>Introduction </strong></span>

ActiveMq est un outil permettant les échanges de messages entre différents systèmes .

Dans cet article on s&rsquo;interessera à mettre en place une architecture distribué d&rsquo;activeMQ afin d&rsquo;assurer la haute disponibilité de l&rsquo;outil .

Active MQ propose 2 types d&rsquo;architectures :

  * Architecture avec des instances indépendantes
  * Architecture en mode Maitre/Esclave

### <span style="text-decoration: underline;"><strong>Architecture avec des instances indépendantes</strong></span>

Cette architecture consiste à installer plusieurs instances d&rsquo;activeMQ qui ne communiquent pas entre eux , quand une instance tombe , le client se reconnecte automatiquement à une autre instance .

Pour mettre en place cette architecture , il suffit que le client qui veut se connecter à activeMQ implémente la configuration suivante : **failover://**protocol

<pre class="brush: plain; title: ; notranslate" title="">failover:(tcp://broker1:61616,tcp://broker2:61616 , tcp://broker3:61616)

</pre>

Le problème avec cette architecture , c&rsquo;est qu&rsquo;il n&rsquo;y a pas de réplication de données , c&rsquo;est à dire si le serveur qui tombe contient des messages , les autres instances de ActiveMq ne les traiteront pas .

### <span style="text-decoration: underline;"><strong>Architecture en mode Maitre/Esclave</strong></span>

Cette architecture consiste à avoir un seul ActiveMq qui est considéré comme maitre c&rsquo;est à dire celui qui gèrent les messages des clients  et des activeMQ  esclaves qui sont en mode passives (ils n&rsquo;acceptent pas les requettes des clients )  qui peuvent prendre le relais ou cas ou le ActiveMQ maitre tombe .

On a trois types de cette architecture :

  * **Partage d&rsquo;un repertoire système**

Ce type consiste à configurer les instances actives MQ pour utiliser le même dossier de données , par conséquent les memes données sont visibles par toutes les instances ActiveMq

L&rsquo;inconvénient de cette architecture est qu&rsquo;il n&rsquo;y pas de réplication de données , si le serveur contenant le dossier partagé tombe , les instances n&rsquo;auront pas accès aux données .

Pour mettre en place cette architecture il faut  configurer toutes les instances ActiveMq à utiliser le meme dossier partagé  , il faut changer persistenceAdapter dans le fichier

./conf/activemq.xml :

<pre class="brush: plain; title: ; notranslate" title="">&lt;persistenceAdapter&gt;
  &lt;kahaDB directory="/sharedFileSystem/sharedBrokerData"/&gt;
&lt;/persistenceAdapter&gt;
</pre>

&nbsp;

  * **Base de données JDBC**

Ce type est pareil que le partage de dossier système , sauf qu&rsquo;à la place on utiliseras une base de données pour sauvegarder les données .

On a le même inconvénient que celui du dossier partagé puisque si la base de données ou  le serveur qui contient la base tombe  , alors les instances n&rsquo;ont plus accès aux données .

En terme de performance , le dossier partagé est préféré par rapport à une base de données .

Pour configurer cette architecture :

A l&rsquo;intérieur de la balise broker il faut mettre :

&nbsp;

<pre class="brush: plain; title: ; notranslate" title="">&lt;!-- Oracle DataSource Sample Setup --&gt;
  
  &lt;bean id="oracle-ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close"&gt;
    &lt;property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/&gt;
    &lt;property name="url" value="jdbc:oracle:thin:@localhost:1521:AMQDB"/&gt;
    &lt;property name="username" value="scott"/&gt;
    &lt;property name="password" value="tiger"/&gt;
    &lt;property name="poolPreparedStatements" value="true"/&gt;
  &lt;/bean&gt;
  

</pre>

et ajouter  :

<pre class="brush: plain; title: ; notranslate" title="">&lt;persistenceAdapter&gt;
        &lt;jdbcPersistenceAdapter dataDirectory="activemq-data" dataSource="#oracle-ds"/&gt;
&lt;/persistenceAdapter&gt;

</pre>

J&rsquo;ai rencontré quelques problèmes avec le connecteur JDBC en essayant de tester cette configuration avec SQL Server 2012 .
  
Je n&rsquo;ai pas continué à creuser pour voir la cause du problème .

  * **Architecture avec réplication de données**

C&rsquo;est l&rsquo;architecture la plus intéréssante puisqu&rsquo;il offre la haute disponibilité avec la réplication des données .
  
Le principe de cette architecture est d&rsquo;utiliser un outil qui s&rsquo;appelle Apache ZooKeeper pour gérer le choix du master entre les instances ActiveMQ et la réplication des données .

En effet ZooKeeper choisit une instance maitre ActiveMQ parmi toutes les instances , l&rsquo;instance choisit est celle qui fonctionne et accepte les requêtes clients . les autres instances sont considérées des esclaves et se connectent à l&rsquo;instance Maitre pour synchroniser les données . les instances esclaves sont en mode passives c&rsquo;est à dire ils n&rsquo;acceptent pas les connections des clients . Si l&rsquo;instance maitre tombe , une instance esclave est choisit pour prendre le relais et considéré comme nouveau maitre .

Toute opération est considérée complète si un nombre d&rsquo;instance ActiveMQ sont synchronisées avec l&rsquo;instance maitre . Ce nombre est calculé avec la formule suivante : **(replicas/2) + 1**
  
ou replicas est un paramètre de configuration de ActiveMQ qui correspond au nombre d&rsquo;instance de ActiveMQ du cluster .

Cela veut dire si on a 3 instances de activeMQ (replicas = 3) , donc le quorum pour que l&rsquo;architecture soit opérationnelle est (3/2) + 1 = 2 instances . Donc si jamais une instance tombe les deux autres resteront fonctionnelles

Il est très recommandé d&rsquo;avoir au moins 3 instances d&rsquo;activeMQ , pour que si une tombe le service soit disponible .

Il est recommandé d&rsquo;utiliser aussi au moins 3 instances de ZooKeeper pour une haute disponibilité du service , et plus on a d&rsquo;instances plus on a de garantie sur la disponibilité , car une instance surexploitée , permet d&rsquo;avoir une latente élevée qui peut laisser penser aux instances que l&rsquo;instance maître est tombée .

On va essayer de mettre en place une architecture avec 3 instances de ActiveMq et 3 instances de Zookeeper :

L&rsquo;installation de zookeper est facile , il suffit de telecharger le zip et l&rsquo;extraire dans un repertoire de la machine .
  
il faut créer un ficher zoo.cfg dans le dossier conf et mettre la configuration suivante :

<pre class="brush: plain; title: ; notranslate" title="">tickTime=2000
# ATTENTION : changer dataDir et n’oubliez pas de créer le répertoire tmp dans le repertoire parent
dataDir=../tmp
clientPort=2181
server.1=localhost:2888:3888
server.2=localhost:2889:3889
server.3=localhost:2890:3890
initLimit=5
syncLimit=2

</pre>

  * initLimit : délai max pour se connecter au leader. Facteur multiplicateur du tickTime, donc ici 5 * 2000 = 10 secondes
  * syncLimit : décalage max autorisé entre la date du serveur et celle du leader. Facteur multiplicateur du tickTime, donc ici 2 * 2000 = 4 secondes
  * server.X : configuration du serveur ayant l’index X.
  
    Le format de la ligne est <serveur name>:<port communication avec le leader>:<port pour élection d’un leader>

la configuration proposée ci dessus avec des ports différents est idéal quand on a les 3 instances Zookeeper sur la même machine , sinon sur des machines différentes on peut utiliser le même port .

Pour fixer le numéro de notre serveur Zookeeper, il faut créer un fichier nommé **myid** dans le répertoire configuré dans la clé dataDir et mettre l&rsquo;id du serveur soit 1 pour ce premier serveur et 2 pour le 2 eme et 3 pour le 3 éme .

![]({{site.url}}{{site.baseurl}}/assets/img/2016-09-27-configurer-activemq-mode-clustering/id_zookeeper.jpg)

Pour démarrer l&rsquo;instance Zookeeper il faut cliquer sur le fichier **zkServer.cmd** qui se trouve dans le repertoire ./bin .

Il faut faire pareil pour tous les instances en changeant juste l&rsquo;attribut **clientPort** et mettre un port différent pour chacune des instances .

Il reste maintenant la configuration des instances ActiveMq

les instances ActiveMQ doivent avoir une configuration commune et une configuration différente pour fonctionner en mode cluster :

<pre class="brush: plain; title: ; notranslate" title="">&lt;broker brokerName="broker" ... &gt;
  ...
  &lt;persistenceAdapter&gt;
    &lt;replicatedLevelDB directory="activemq-data" replicas="3" bind="tcp://0.0.0.0:0" zkAddress="zoo1.example.org:2181,zoo2.example.org:2181,zoo3.example.org:2181" zkPassword="password" zkPath="/activemq/leveldb-stores" hostname="broker1.example.org" /&gt;
  &lt;/persistenceAdapter&gt;
  ...
&lt;/broker&gt;

</pre>

Les instances doivent avoir les mêmes valeurs des champs suivants :

  * replicas : Le nombre de noeuds qui existeront dans le cluster. Au moins (replicas/ 2) +1 noeuds doivent être opérationnel pour éviter une interruption du service.
  *  zkAddress : contient la liste des adresses des serveurs Zookeeper séparés par des virgules .
  * zkPassword : Le mot de passe à utiliser lors de la connexion au serveur de ZooKeeper.
  * zkPath : Le chemin vers le répertoire ZooKeeper où les informations électorale seront échangées.

Les instances doivent avoir des valeurs uniques des champs suivants :

  * bind : Lorsque ce noeud devient un maître, il va se lier l&rsquo;adresse configurée et le port au service du protocole de réplication. Utilisation des ports dynamiques est également pris en charge. Il suffit de configurer avec tcp://0.0.0.0:0
  * hostname : Le nom d&rsquo;hôte utilisé pour annoncer le service de réplication lorsque ce noeud devient le maître. Si non défini, il sera déterminé automatiquement.
  * weight : Le noeud de réplication qui a la dernière mise à jour avec le poids le plus élevé sera le maître. Utilisé pour donner la préférence à certains noeuds en vue de devenir maître.

&nbsp;

Et pour la connexion client , c&rsquo;est pareil que toutes les types d&rsquo;architectures :

<pre class="brush: plain; title: ; notranslate" title="">failover:(tcp://broker1:61616,tcp://broker2:61616 , tcp://broker3:61616)

</pre>

Gestion de logs

Zookepeer et MQ utilisent log4j pour la gestion des logs .

Par défaut, Zookeeper n’a pas de fichier de log, il faut mettre à jour le fichier log4j.properties  pour faire le faire apparaitre

&nbsp;

&nbsp;

  * <a name="_Toc475540448"></a> Vérification

&nbsp;

Pour vérifier quel MQ est maitre, Plusieurs moyens possibles :

&nbsp;

  * Essayer d’accéder à la console de chaque instance, celle qui fonctionne est celle qui est maitre

Les URLS :

http://host1:8161/admin/

[http://host2:8161/admin/](http://vporyhtiot7:8161/admin/)

[http://host3:8161/admin/](http://vporyhtiot8:8161/admin/)

&nbsp;

  * Consulter les logs de chaque instance
  * Essayer de vérifier si les machines écoutent bien sur les ports de MQ et Zookeeper :

&nbsp;

  * Pour cela on peut utiliser un utilitaire de réseau comme netcat , qu’il faut télécharger et lancer les commandes suivantes :

&nbsp;

  * <u>Pour MQ : </u>

<u> </u>

**echo stat | nc hostname 61616**

&nbsp;

  * <u>Pour Zookeeper : </u>

<u> </u>

**echo stat |  nc hostname 2181**

&nbsp;

  * <u>Ou bien pour voir directement si l’instance Zookeeper est maitre ou esclave :</u>

<u> </u>

**echo stat |  nc hostname 2181 | find Mode**
