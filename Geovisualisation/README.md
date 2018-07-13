# G�ovisualisation 
----
Kim Antunez et Timoth�e Giraud

## Contenu pr�visionnel de la s�ance 

**1. Agr�gation spatiale**

* Agr�ger des bases de donn�es disponibles � un niveau g�ographique � fin � en un ou diff�rents niveaux supra-communaux et illustrer ainsi l�effet MAUP

* R�aliser le m�me type de traitement sur des couches carto et donn�es spatiales (sf)
 
**2. Cartes � classiques � sous R **

*  Cartes en statique avec quelques exemples sur cartography et/ou ggplot2 : choropl�tes, ronds proportionnels (�ventuellement discontinuit�s)

* Cartes interactives, webmapping (leaflet, mapview�)
 
**3. Traitements de cartographie avanc�e**
 
* agr�gation dans des grilles r�guli�res

*  lissages spatiaux 
 
* *[s�lection de flux, dominants / domin�s avec flows : ne s�adapte pas pour le moment � nos donn�es]* 

** *[4. Ouverture : Repr�senter les donn�es g�ographiques au-del� des cartes]* **

## Liens utiles 

*  [Cartographie avec R T. Giraud Tuto@Mate (avril 2018)](https://rcarto.github.io/tuto-mate/exemple.html)
*  [Cartographic Explorations of the OpenStreetMap Database with R T. Giraud (janvier 2018)](https://rgeomatic.hypotheses.org/1244)
*  [Bistrographie avec SIRENE M. Garnier (2017)](https://github.com/mtmx/bistrographie)
 

## Bases de donn�es que l'on peut mobiliser

### A. Donn�es administratives nationales fran�aises : la base SIRENE des entreprises

> Le syst�me informatis� du r�pertoire national des entreprises et des �tablissements (SIRENE) dont la gestion a �t� confi�e � l'Insee enregistre l'�tat civil de toutes les entreprises et leurs �tablissements, quelle que soit leur forme juridique et quel que soit leur secteur d'activit�, situ�s en m�tropole, dans les Dom (Guadeloupe, Guyane, Martinique, La R�union et Mayotte) et � Saint-Pierre et Miquelon. Les entreprises �trang�res qui ont une repr�sentation ou une activit� en France y sont �galement r�pertori�es. L'Insee met depuis un an les donn�es de ce r�pertoire en [open data](https://www.data.gouv.fr/fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret/) et Etalab r�alise un traitement automatique de g�olocalisation de ces fichiers qu'ils mettent en ligne [ici](http://data.cquest.org/geo_sirene/last/).


*Remarque : SIRENE existe d�sormais sous la forme d'une [API](https://github.com/ColinFay/ariel) mais, tout comme la base fournie par l'Insee, ne contient pas (encore) de donn�es de g�olocalisation*

**avantages** : exhaustivit� sur le territoire Fran�ais (10 millions d'�tablissements), donn�es en temps r�el, contient une variable quantitative (tranche d'effectifs salari�s de l'�tablissement)

**inconv�nients** : l'objectif de la base n'est pas initialement de g�olocaliser des commerces mais est un r�pertoire administratif d'entreprises donc peu de pr�cisions sur le type de bar/restaurant, contient une variable de code commune

Il est propos� pour le TP de se focaliser sur le d�partement de la Haute-Garonne (31) dont la pr�fecture est Toulouse (analyser les bars et restaurants en pr�vision de userR2019 !).

Les donn�es utilis�es sont rang�es dans [data/hautegaronne_sirene](lien).


#### Liste des variables utiles

**1. ENSEIGNE : Enseigne ou nom de l'exploitation**

> C'est l'appellation d�signant l'emplacement ou le local dans lequel est exerc�e l'activit�. Elle permet � la client�le d'identifier facilement l'�tablissement (enseigne ou nom de l'exploitation). Lorsqu'elle existe, l'enseigne est reprise dans la seconde ligne de l'adresse g�opostale de l'�tablissement. Exemple :"Coiff Land" est l'enseigne du salon de coiffure d�tenu par l'entrepreneur individuel "Madame Martin Justine".
Si besoin on peut utiliser �galement la variable NOMEN_LONG (Nom ou raison sociale de l'entreprise) qui correspond � la raison sociale pour une personne morale ou le nom pour l'entrepreneur individuel. Cette variable indique pour les entrepreneurs individuels le nom de l'entreprise form� par concat�nation d'une partie des pr�noms et du nom patronymique de l'entrepreneur individuel, du nom de conjoint ou du nom d'usage. Elle indique la d�nomination longue pour les personnes morales. 


**2. APET700 : Activit� principale de l'�tablissement**

> Activit� principale de l'�tablissement
Chacun des �tablissements d'une entreprise se voit attribuer par l'INSEE, lors de son inscription au r�pertoire SIRENE, un code caract�risant son activit� principale, appel� code APE (Activit� Principale Exerc�e). L'APE est codifi�e selon la Nomenclature d'Activit�s Fran�aise (NAF : R�v2, 2008). Les �tablissements d'une m�me entreprise peuvent avoir des activit�s diff�rentes.
Cette variable est syst�matiquement renseign�e. Si une entreprise n'a qu'un seul �tablissement, l'APE de l'�tablissement (APET) est �gal � l'APE de l'entreprise (APEN). Au moment de la d�claration de l'entreprise, il peut arriver que l'INSEE ne soit pas en mesure d'attribuer le bon code APE : la modalit� 0000Z peut alors �tre affect�e provisoirement. Possibilit� de consulter la nomenclature exhaustive [ici](https://www.insee.fr/fr/information/2406147).

| 56     	| Restauration                                   	|
|--------	|------------------------------------------------	|
|        	|                                                	|
| 56.1   	| Restaurants et services de restauration mobile 	|
| 56.10  	| Restaurants et services de restauration mobile 	|
| 56.10A 	| Restauration traditionnelle                    	|
| 56.10B 	| Caf�t�rias et autres libres-services           	|
| 56.10C 	| Restauration de type rapide                    	|
|        	|                                                	|
| 56.2   	| Traiteurs et autres services de restauration   	|
| 56.21  	| Services des traiteurs                         	|
| 56.21Z 	| Services des traiteurs                         	|
| 56.29  	| Autres services de restauration                	|
| 56.29A 	| Restauration collective sous contrat           	|
| 56.29B 	| Autres services de restauration n.c.a.         	|
|        	|                                                	|
| 56.3   	| D�bits de boissons                             	|
| 56.30  	| D�bits de boissons                             	|
| 56.30Z 	| D�bits de boissons                             	|

**3. EFETCENT : Effectif salari� de l'�tablissement � la centaine pr�s**

> Cette variable correspond � l'effectif salari� � la centaine pr�s de l'�tablissement. C'est une variable statistique, mill�sim�e au 31/12 d'une ann�e donn�e, majoritairement l'ann�e n-2. Elle est � rapprocher de sa date de validit�, c'est-�-dire de la date de mise � jour de l'effectif salari� de l'�tablissement. Le code correspond toujours au chiffre inf�rieur de la tranche. A partir de 100, c'est l'effectif salari� approch� � la centaine inf�rieure qui est pr�cis�.

| code  | libell�       |
|---------      |----------------------------------------       |
| NN    | Unit�s non employeuses (pas de salari� au cours de l'ann�e de r�f�rence et pas d'effectif au 31/12). Cette tranche peut contenir quelques effectifs inconnus       |
| 0     | 0 salari� (n'ayant pas d'effectif au 31/12 mais ayant employ� des salari�s au cours de l'ann�e de r�f�rence)    |
| 1     | 1 � 2 salari�s        |
| 3     | 3 � 5 salari�s        |
| 6     | 6 � 9 salari�s        |
| 10    | 10 � 19 salari�s      |
| 20    | 20 � 49 salari�s      |
| 50    | 50 � 99 salari�s      |
| 100   | 100 � 199 salari�s    |
| 200   | 200 � 299 salari�s    |
| .../...       | de 100 en 100 jusqu'� 999 999 salari�s        |


**4. G�ocodage par Etalab**

* longitude (en degr�s d�cimaux, WGS84)
* latitude (en d�gr�s d�cimaux, WGS84) 
* geo_score : indice de similarit� fournit par le moteur de g�ocodage 
* geo_type : "housenumber" = n� trouv�, "interpolation" = n� interpol�, "street" = voie trouv�e, "locality" = lieu-dit (ou position de la mairie), "municipality" = position de la commune car l'adresse n'a pas �t� trouv�e. 
* geo_adresse : libell� de l'adresse trouv�e (exemple : 39 Rue Jean-Jacques Rousseau 75001 Paris) 
* geo_id : id dans le r�f�rentiel BAN, ou BANO (si commence par "BANO_") 
* geo_ligne : ligne d'adresse g�ocod�e (G = g�ographique, N = normalis�e, D = d�clar�e) 
* geo_insee : code INSEE o� l'adresse a �t� g�ocod�e 

### B. Donn�es locales am�ricaines : Louisville's Establishments 

> A [database](https://data.louisvilleky.gov/dataset/establishments) which contains attributes of establishments that are currently inspected by and/or regulated by Louisville Metro Government.  Personal/identifying data has been removed.  EstablishmentID column can be joined to the EstablishmentID column in the Inspections table to show attributes of any inspections of the establishment. Modified Date : 2018-01-16. Data is produced by the department of public health and wellness of Louisville which protects and promotes the health, environment and well being of the people of Louisville, providing health-related programs and health office locations community wide.


**avantages** : exemple de donn�e locale en opendata, donn�es non fran�aises

**inconv�nients** : peu d'information � part qu'il s'agit d'un restaurant et son nom

Les donn�es utilis�es sont rang�es dans [data/louisville](lien).


#### Liste des variables utiles

**1. PremiseName : Nom de l'�tablissement **

Exemple : JERRY'S RESTAURANT

**2. Est-Type : Type d'�tablissement **

Ne s�lectionner que les food service

**3. G�olocalisation**

* longitude (projection ?)
* latitude (projection ? ) 
* premiseStreetNo (num�ro rue)
* premiseStreet (nom rue)

### C. Donn�es de google : les restaurants du centre ville de Toulouse


> The Google Maps Places API gets data from the same database used by Google Maps and Google+ Local. Places features more than 100 million businesses and points of interest that are updated frequently through owner-verified listings and user-moderated contributions.

Ce [lien](https://remibacha.com/api-search-console-r/) explique assez bien comment activer les API sur son compte google. 

Il est par exemple ainsi possible de requ�ter les restaurants � 5 km � la ronde du centre-ville de Toulouse de mani�re assez simple gr�ce au package R [googleway](https://github.com/SymbolixAU/googleway/blob/master/vignettes/googleway-vignette.Rmd). 

    library(googleway)
    data <- google_places(location = c(43.603235,  1.444683),
                          keyword = "Restaurant",
                          radius = 5000,
                          key = key)

L'utilisation gratuite de l'API limite l'utilisation � 1000 requ�te par jour et une requ�te peut contenir au maximum 20 lignes. Si on fait l'hypoth�se qu'on a toujours moins de 20 resto sur un cercle de rayon inf�rieur � 100m, cela veut dire que pour parcourir un espace d'1km x 1km cela demanderait une 100aine de requ�tes (calcul � revoir !)


**avantages** : donn�es en temps r�el, contient des variables quantitatives (note du restaurant, niveau des prix)

**inconv�nients** : requ�tage obligatoire et limit� par google


Les donn�es utilis�es sont rang�es dans [data/toulouse_googleplace](lien).

#### Liste des variables utiles
* geometry.location.lat : latitude
* geometry.location.lng : longitude
* name : nom du restaurant
* vicinity : adresse du restaurant
* price_level : niveau du prix dans le restaurant (peu renseign�)
* rating : note sur 5 du restaurant
* types : permet de voir les autres types d'activit� (cafe,bar,restaurant,food,point of interest,establishment,store...)

### D. OpenStreetMap



<span style="color:red"> **TIMOTHEE**</span>

## Fonds de cartes mobilis�s

### Communes Fran�aises

> Il s'agit du contour des 35 000 communes Fran�aises au 01/01/2017

Les couches cartographiques sont rang�es dans [data/carto](lien).

