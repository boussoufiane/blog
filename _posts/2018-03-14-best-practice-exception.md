---
published: false
---
title: 'Bonne pratique pour la gestion des exception '
date: 2018-03-14T22:31:12+00:00
author: admin
layout: post
permalink: /bonne-pratique-exception/
categories:
  - JAVA
  
  
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
  
  
 
