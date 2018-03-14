---
title: 'Bonne pratique pour la gestion des exception '
date: 2018-03-14T22:31:12+00:00
author: admin
layout: post
permalink: /bonne-pratique-exception/
categories:
  - JAVA
---
  
  
  Voici les bonnes pratiques pour la gestion des exception en JAVA : 
  
  1. Il faut fermer libérer les ressources utilisées avec un bloc Finally
  
  Quand on utilise des ressources comme par exemple **InputStream** ou **OutputStream** ou une connexion à une base de données  , il ne faut pas oublier de les libérer dans un bloc Finally de try catch .
 Si on libère pas la ressource , il sera bloqué et aucun autre processus ne peut y accéder .
 
 <pre class="brush: java; title: ; notranslate" title="">
 public void doNotCloseResourceInTry() {
	FileInputStream inputStream = null;
	try {
		File file = new File("./tmp.txt");
		inputStream = new FileInputStream(file);
		
		// use the inputStream to read a file
		
		// do NOT do this
		inputStream.close();
	} catch (FileNotFoundException e) {
		log.error(e);
	} catch (IOException e) {
		log.error(e);
	}
}
 </pre>
 
 Le problème avec cette approche c'est que si il y a une exception , la libération de la ressource ne sera jamais appelé .
 
 Il y a un autre problème c'est que parfois la méthode qui permet de libérer la ressource , peut lever elle meme l'exception : 
 
  <pre class="brush: java; title: ; notranslate" title="">
  public void closeResourceInFinally() {
	FileInputStream inputStream = null;
	try {
		File file = new File("./tmp.txt");
		inputStream = new FileInputStream(file);
		
		// use the inputStream to read a file
		
	} catch (FileNotFoundException e) {
		log.error(e);
	} finally {
		if (inputStream != null) {
			try {
				inputStream.close();
			} catch (IOException e) {
				log.error(e);
			}
		}
	}
}
</pre>

2. Utiliser le Try-With-Resource disponible depuis Java 7

On peut utiliser cette option si la ressource implémente l'interface **AutoCloseable** , ce qui est généralement le cas pour la plupart des ressources java .
Quand on crée la ressource dans le bloc try , elle est automatiquement libérée après la fin d'excécution du bloc try .
  <pre class="brush: java; title: ; notranslate" title="">
  public void automaticallyCloseResource() {
	File file = new File("./tmp.txt");
	try (FileInputStream inputStream = new FileInputStream(file);) {
		// use the inputStream to read a file
		
	} catch (FileNotFoundException e) {
		log.error(e);
	} catch (IOException e) {
		log.error(e);
	}
}
  </pre>
  
  3. Prévilier les exceptions plus spécifique que général
  
  Il faut utiliser les exceptions spécifique au lieu des exceptions global , il vaut mieux par exemple utiliser NumberFormatException que llegalArgumentException. que Exception tout court .
  Les exceptions spécifiques  permet d'avoir plus d'informations utile sur la nature de l'exception .
  Cela facilite aussi la compréhension et l'utilisation des API développés .
  
  
  <pre class="brush: java; title: ; notranslate" title="">
  public void generalException() throws Exception { ... }
  public void specificException() throws NumberFormatException { ... }
  </pre>
  
  4. Gérer les exceptions spécifique en premier 
  
  <pre class="brush: java; title: ; notranslate" title="">
  public void catchMostSpecificExceptionFirst() {
	try {
		doSomething("A message");
	} catch (NumberFormatException e) {
		log.error(e);
	} catch (IllegalArgumentException e) {
		log.error(e)
	}
}
 
  </pre>
  
  
 4. il faut logger les information exceptions gérées 
 
 Le fait de logger l'exception permet d'avoir plus d'informations de l'erreur .
 Si la cause de l'excption loggé n'est pas assez claire , il est préférable d' ajouter une description .

 
  <pre class="brush: java; title: ; notranslate" title="">
 try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
}
</pre>


 5 . Il ne faut pas catcher Throowable 
 
 Throwable est la super class de tous les exceptions et les errors . On peut la catcher mais ce n'est pas recommandé . car elle intercepteras tous les exceptions et tous les erreurs . 
 Les erreurs sont levés par la JVM et cela concerne généralement des problèmes graves qui doivent pas etre générées par l'application comme OutOfMemoryError ou  StackOverflowError
 
 6. Il ne faut jamais ignorer une exception
 
 <pre class="brush: java; title: ; notranslate" title="">
 public void doNotIgnoreExceptions() {
	try {
		// do something
	} catch (NumberFormatException e) {
		// this will never happen
	}
}
</pre>

comme dit précédemment , il faut logger les exception 

 <pre class="brush: java; title: ; notranslate" title="">
  public void logAnException() {
	try {
		// do something
	} catch (NumberFormatException e) {
		log.error("This should never happen: " + e);
	}
}
</pre>

7. Il faut pas logger une exception et la lever en meme temps 

Cela sert à rien car ca va écrire l'exception deux fois dans les logs . et cela ne rajoute aucune information utilie .
Si on veut ajouter des informations dans l'exception , il faut mieux l'envelopper et l'ajouter dans une exception customisé 

 <pre class="brush: java; title: ; notranslate" title="">
public void wrapException(String input) throws MyBusinessException {
	try {
		// do something
	} catch (NumberFormatException e) {
		throw new MyBusinessException("A message that describes the error.", e);
	}
}
</pre>

8.Eviter d'utiliser e.printStackTrace()  pour gérer les exceptions 

Il est préferable d'éviter  cette instruction car elle affiche les logs juste dans la console en utilisant System.err . Ca peut etre utilie en cas de développement maias pas en production car l'exception n'est pas loggée .
Il vaut mieux utiliser des loggers à la place de e.printStackTrace() .
