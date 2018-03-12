---
title: "Transformation Fractionner des colonnes par exemple à l’aide d’Azure Machine Learning Workbench"
description: "Document de référence pour la transformation « Fractionner des colonnes par exemple »"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 497c1725fc4554792add11c0ec069d1628a89fbd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="split-column-by-example-transformation"></a>Transformation Fractionner des colonnes par exemple
Cette transformation fractionne de manière prédictive le contenu d’une colonne sur des limites significatives sans nécessiter d’intervention de l’utilisateur. L’algorithme de fractionnement sélectionne les limites après avoir analysé le contenu de la colonne. Ces limites peuvent être définies par
* Un délimiteur fixe
* Plusieurs délimiteurs arbitraires apparaissant dans des contextes particuliers
* Des modèles de données ou certains types d’entités

Les utilisateurs peuvent également contrôler le comportement de fractionnement en mode Avancé, dans lequel ils peuvent spécifier les délimiteurs, ou en fournissent des exemples du fractionnement souhaité.

En théorie, les opérations de fractionnement peuvent également être effectuées dans Workbench à l’aide d’une série de transformations *Dériver des colonnes par exemple*. Toutefois, s’il y a plusieurs colonnes, dériver chacune d’elles individuellement (même en adoptant l’approche « par exemple ») peut prendre beaucoup de temps. Le fractionnement prédictif permet de fractionner facilement sans que l’utilisateur ait à fournir des exemples pour chacune des colonnes. 

## <a name="how-to-perform-this-transformation"></a>Comment effectuer cette transformation

1. Sélectionnez la colonne à fractionner. 
2. Sélectionnez **Split Column by Example** (Fractionner des colonnes par exemple) dans le menu **Transforms** (Transformations). Vous pouvez aussi cliquer avec le bouton droit sur l’en-tête de la colonne sélectionnée et sélectionner **Split Column by Example** (Fractionner des colonnes par exemple) dans le menu contextuel. L’Éditeur de transformation s’ouvre et de nouvelles colonnes sont ajoutées en regard de la colonne sélectionnée. À ce stade, Workbench analyse la colonne d’entrée, détermine les limites de fractionnement et synthétise un programme pour fractionner la colonne telle qu’affichée dans la grille. Le programme synthétisé est exécuté sur toutes les lignes de la colonne. Les délimiteurs, le cas échéant, sont exclus du résultat final.
3. Vous pouvez cliquer sur **Advanced mode** (Mode avancé) afin de contrôler plus précisément la transformation de fractionnement. 
4. Examinez la sortie et cliquez sur **OK** pour accepter la transformation. 

La transformation vise à produire le même nombre de colonnes pour toutes les lignes. Si une ligne ne peut pas être fractionnée sur les limites déterminées, elle produit *null* pour toutes les colonnes par défaut. Ce comportement peut être changé en **Mode avancé**.

### <a name="transform-editor-advanced-mode"></a>Éditeur de transformation : Mode avancé
Le **Mode avancé** fournit une expérience de fractionnement de colonnes enrichie. 

Si vous sélectionnez **Keep Delimiter Columns** (Conserver les colonnes de séparation), les délimiteurs sont inclus dans le résultat final. Les délimiteurs sont exclus par défaut.

Si vous spécifiez **Delimiters** (Délimiteurs), la logique de sélection automatique des délimiteurs est ignorée. Plusieurs délimiteurs, un par ligne, peuvent être spécifiés comme **Délimiteurs**. Tous ces caractères sont utilisés comme délimiteurs pour fractionner la colonne.

Parfois, le fractionnement d’une valeur sur des limites déterminées génère différentes quantités de colonnes. Dans ce cas, **Fill Direction** (Direction de remplissage) est utilisé pour déterminer l’ordre dans lequel les colonnes doivent être remplies.

Un clic sur **Show suggested examples** (Afficher des exemples suggérés) permet d’afficher les lignes représentatives pour lesquelles l’utilisateur doit fournir un exemple de fractionnement. L’utilisateur peut cliquer sur la flèche **Haut** à droite de la ligne suggérée pour la promouvoir comme exemple. 

L’utilisateur peut **supprimer la colonne** ou **insérer de nouvelles colonnes** en cliquant sur l’en-tête de la **table d’exemples**.

L’utilisateur peut copier et coller des valeurs d’une cellule à une autre afin de fournir un exemple de fractionnement.

L’utilisateur peut basculer entre le **Mode de base** et le **Mode avancé** en cliquant sur les liens dans l’Éditeur de transformation.

### <a name="transform-editor-send-feedback"></a>Éditeur de transformation : envoyer des commentaires

Cliquez sur le lien **Envoyer des commentaires** pour ouvrir la boîte de dialogue **Commentaires** dans laquelle la zone de commentaires est préremplie avec les sélections de paramètres et les exemples fournis par l’utilisateur. L’utilisateur doit vérifier le contenu de la zone de commentaires et fournir plus de détails pour nous aider à comprendre le problème. Si l’utilisateur ne souhaite pas partager de données avec Microsoft, il doit supprimer les données d’exemple préremplies avant de cliquer sur le bouton **Envoyer des commentaires**. 


### <a name="editing-an-existing-transformation"></a>Modification d’une transformation existante

Un utilisateur peut modifier une transformation **Fractionner des colonnes par exemple** existante en sélectionnant l’option **Edit** (Modifier) à l’étape Transformation. Un clic sur **Edit** (Modifier) ouvre l’Éditeur de transformation en **Mode avancé**, et tous les exemples fournis pendant la création de la transformation sont affichés.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Exemples de fractionnement sur un délimiteur fixe à caractère unique

Les champs de données sont couramment séparés par un délimiteur fixe unique telle une virgule au format CSV. La transformation de fractionnement tente de déduire ces délimiteurs automatiquement. Par exemple, dans le scénario suivant, elle déduit automatiquement que le « . » est un délimiteur.

### <a name="splitting-ip-addresses"></a>Fractionnement d’adresses IP

Les valeurs de la première colonne sont fractionnées de manière prédictive en quatre colonnes.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Exemples de fractionnement sur plusieurs délimiteurs dans des contextes particuliers

Les données de l’utilisateur peuvent inclure de nombreux délimiteurs différents séparant différents champs. De plus, il est possible que seules certaines occurrences d’une chaîne de délimitation soient un délimiteur. Par exemple, dans le cas suivant, l’ensemble des délimiteurs requis est « - », « , » et « : » pour produire le résultat souhaité. Toutefois, les occurrences de « : » ne doivent pas toutes être un point de fractionnement, car nous ne souhaitons pas fractionner l’heure, mais la conserver dans une seule colonne. La transformation de fractionnement déduit les délimiteurs dans les contextes dans lesquels ils se produisent dans les données d’entrée, plutôt que n’importe quelle occurrence possible du délimiteur. La transformation a également conscience des types de données courants tels que les dates et les heures.   

### <a name="splitting-store-opening-timings"></a>Fractionnement des heures d’ouverture d’un magasin

Les valeurs de la colonne *Horaires* suivante sont fractionnées de manière prédictive en neuf colonnes indiquées dans le tableau ci-dessous.

|Horaires|
|:-----|
|Lundi - Vendredi : 7:00 - 18:00,Samedi : 9:00 - 17:00,Dimanche : fermé|
|Lundi - Vendredi : 9:00 - 18:00,Samedi : 4:00 - 16:00,Dimanche : fermé|
|Lundi - Vendredi : 8:30 - 19:00,Samedi : 3:00 - 14:30,Dimanche : fermé|
|Lundi - Vendredi : 8:00 - 18:00,Samedi : 2:00 - 15:00,Dimanche : fermé|
|Lundi - Vendredi : 4:00 - 19:00,Samedi : 9:00 - 16:00,Dimanche : fermé|
|Lundi - Vendredi : 8:30 - 16:30,Samedi : 9:00 - 17:00,Dimanche : fermé|
|Lundi - Vendredi : 5:30 - 18:30,Samedi : 5:00 - 16:00,Dimanche : fermé|
|Lundi - Vendredi : 8:30 - 20:30,Samedi : 6:00 - 17:00,Dimanche : fermé|
|Lundi - Vendredi : 8:00 - 21:00,Samedi : 9:00 - 20:00,Dimanche : fermé|
|Lundi - Vendredi : 10:00 - 21:30,Samedi : 9:30 - 15:00,Dimanche : fermé|

|Horaires_1|Horaires_2|Horaires_3|Horaires_4|Horaires_5|Horaires_6|Horaires_7|Horaires_8|Horaires_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Lundi|Vendredi|7:00|18:00|Samedi|9:00|17:00|Dimanche|Fermés|
|Lundi|Vendredi|9:00|18:00|Samedi|4:00|16:00|Dimanche|Fermés|
|Lundi|Vendredi|8:30|19:00|Samedi|3:00|14:30|Dimanche|Fermés|
|Lundi|Vendredi|8:00|18:00|Samedi|2:00|15:00|Dimanche|Fermés|
|Lundi|Vendredi|4:00|19:00|Samedi|9:00|16:00|Dimanche|Fermés|
|Lundi|Vendredi|8:30|16:30|Samedi|9:00|17:00|Dimanche|Fermés|
|Lundi|Vendredi|5:30|18:30|Samedi|5:00|16:00|Dimanche|Fermés|
|Lundi|Vendredi|8:30|20:30|Samedi|6:00|17:00|Dimanche|Fermés|
|Lundi|Vendredi|8:00|21:00|Samedi|9:00|20:00|Dimanche|Fermés|
|Lundi|Vendredi|10:00|21:30|Samedi|9:30|15:00|Dimanche|Fermés|

### <a name="splitting-iis-log"></a>Fractionnement de journal IIS

Voici un autre exemple de plusieurs délimiteurs arbitraires. Cet exemple inclut également un délimiteur contextuel « / », qui ne doit pas être fractionné à l’intérieur des URL ou des chemins de fichiers. Il est fastidieux d’effectuer ce fractionnement à l’aide de plusieurs transformations *Dériver des colonnes par exemple* et en donnant des exemples pour chaque champ. Grâce à la transformation de fractionnement, nous pouvons effectuer le fractionnement prédictif sans donner d’exemples.

|textejournal|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

est fractionné en :

|textejournal_1|textejournal_2|textejournal_3|textejournal_4|textejournal_5|textejournal_6|textejournal_7|textejournal_8|textejournal_9|textejournal_10|textejournal_11|textejournal_12|textejournal_13|textejournal_14|textejournal_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/Oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|compatible; MSIE 4|
|10.128.72.213|17/Oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/Nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/Nov/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/Mar/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Exemples de fractionnement sans délimiteurs
Dans certains cas il n’y a aucun délimiteur réel, et les champs de données peuvent être contigus. Dans ce cas, la transformation de fractionnement détecte automatiquement les modèles dans les données afin de déduire les points de fractionnement probables. Par exemple, dans le scénario suivant, nous souhaitons séparer le montant du type de devise, et la transformation de fractionnement déduit automatiquement la limite entre les données numériques et non numériques ainsi que le point de fractionnement.

### <a name="splitting-amount-with-currency-symbol"></a>Fragmentation de montant avec symbole monétaire

|Montant|Montant_1|Montant_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9.|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

Dans l’exemple suivant, nous souhaitons séparer les valeurs de poids des unités de mesure. Là encore, l’inférence de fractionnement détecte automatiquement la limite significative et la préfère par rapport aux autres délimiteurs possibles tels que le caractère « . ». 

### <a name="splitting-weights-with-units"></a>Fractionnement de poids avec des unités

|Poids|Poids_1|Poids_2|
|:-----|:-----|:-----|
|2,27KG|2,27|KG|
|1L|1|L|
|2,5KG|2.5|KG|
|2KG|2|KG|
|1,7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1,5KGA|1.5|KGA|

## <a name="technical-notes"></a>Notes techniques

La fonctionnalité de transformation de fractionnement est basée sur la technique de **synthèse de programme prédictive**. Avec cette technique, les programmes de transformation de données sont appris automatiquement, en fonction des données d’entrée. Les programmes sont synthétisés dans un langage propre à un domaine. Ce langage est basé sur les délimiteurs et les champs qui se produisent dans des contextes d’expression régulière particuliers. Vous trouverez plus d’informations sur cette technologie dans une [publication récente](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
