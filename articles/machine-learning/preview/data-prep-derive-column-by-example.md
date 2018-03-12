---
title: "Transformation Dériver des colonnes par exemple à l’aide d’Azure Machine Learning Workbench"
description: "Document de référence pour la transformation « Dériver des colonnes par exemple »"
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
ms.openlocfilehash: 3eaf42f8d0bdad274ec92f5790fe79878500a1b2
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="derive-column-by-example-transformation"></a>Transformation Dériver des colonnes par exemple

La transformation **Dériver des colonnes par exemple** permet aux utilisateurs de créer un dérivé d’une ou plusieurs colonnes existantes à l’aide d’exemples du résultat dérivé fournis par l’utilisateur. Le dérivé peut être n’importe quelle combinaison des transformations de Chaîne, Date et Nombre prise en charge. 

Les transformations de Chaîne, Date et Nombre suivantes sont prises en charge :

**Transformations de chaîne :** 

Sous-chaîne, notamment l’extraction intelligente de Nombre et de Dates, la concaténation, la manipulation de cas et le mappage de valeurs constantes.

**Transformations de date :** 

Modification du format de date, extraction de parties de date, mappage d’heure à des compartiments de temps.

Les transformations de date sont assez génériques, avec quelques limitations notables :
* Les fuseaux horaires ne sont pas pris en charge.
* Certains formats courants ne sont pas pris en charge :
    * Format de semaine de l’année ISO 8601 (par exemple « 2009-W53-7 ») 
    * Heure d’époque Unix.
* Tous les formats respectent la casse (« 4am » n’est pas reconnu comme une heure, contrairement à « 4AM »).

**Transformations de nombre :** 

Arrondi, plancher, plafond, quantification, remplissage à l’aide de zéros ou d’espaces, division ou multiplication par une puissance de 1000.

**Transformations composites :** 

Toute combinaison de transformations de chaîne, de nombre ou de date.

## <a name="how-to-use-this-transformation"></a>Comment utiliser cette transformation

Pour effectuer cette transformation, exécutez les étapes suivantes :
1. Sélectionnez une ou plusieurs colonnes à partir desquelles vous souhaitez dériver la valeur. 
2. Sélectionnez **Derive Column by Example** (Dériver des colonnes par exemple) dans le menu **Transforms** (Transformations). Vous pouvez aussi cliquer avec le bouton droit sur l’en-tête de la colonne sélectionnée et sélectionner **Derive Column by Example** (Dériver des colonnes par exemple) dans le menu contextuel. L’Éditeur de transformation s’ouvre et une nouvelle colonne est ajoutée en regard de la colonne sélectionnée la plus à droite. Les colonnes sélectionnées peuvent être identifiées par les cases à cocher dans les en-têtes de colonnes. Vous pouvez ajouter des colonnes à la sélection (et en supprimer) à l’aide des cases à cocher présentes dans les en-têtes de colonnes.
3. Tapez un exemple de la *sortie* par rapport à une ligne, puis appuyez sur Entrer. À ce stade, Workbench analyse la colonne d’entrée et la sortie fournie afin de synthétiser un programme capable de transformer les entrées fournies en sortie. Le programme synthétisé est exécuté sur toutes les lignes de la grille de données. Pour les cas ambigus et complexes, plusieurs exemples peuvent être nécessaires. Plusieurs exemples peuvent être fournis de différentes façons, selon que vous êtes en Mode de base ou en Mode avancé.
4. Examinez la sortie et cliquez sur **OK** pour accepter la transformation.

### <a name="transform-editor-basic-mode"></a>Éditeur de transformation : Mode de base

Le Mode de base fournit une expérience d’édition inline dans la grille de données. Vous pouvez fournir des exemples de sortie en accédant à la cellule de votre choix et en tapant la valeur. 

Workbench analyse les données et tente d’identifier les cas ambigus qui doivent être examinés par l’utilisateur. Pendant l’analyse des données, **Analyzing Data** (Analyse des données en cours) est affiché dans l’en-tête de l’Éditeur de transformation. Une fois l’analyse terminée, **No Suggestions** (Aucune suggestion) ou **Review next suggested row** (Examiner la ligne suggérée suivante) s’affiche dans l’en-tête. Vous pouvez naviguer parmi les cas ambigus en cliquant sur **Review next suggested row**. Si la valeur est incorrecte pour une ligne, vous devez taper la valeur correcte comme exemple supplémentaire. 

### <a name="transform-editor-advanced-mode"></a>Éditeur de transformation : Mode avancé

Le Mode avancé fournit une expérience de dérivation de colonnes par exemple enrichie. Tous les exemples sont affichés à un seul endroit. Vous pouvez également examiner tous les cas ambigus à un seul endroit en cliquant sur **Show suggested examples** (Afficher les exemples suggérés). 

En mode avancé, vous pouvez ajouter n’importe quelle ligne comme exemple de ligne en double-cliquant dessus dans la grille. Une fois qu’une ligne a été copiée en tant qu’exemple de ligne, vous pouvez aussi modifier les données dans les colonnes sources afin de créer un exemple synthétique. En procédant ainsi, vous pouvez ajouter des cas qui ne sont pas présents actuellement dans les exemples de données.

L’utilisateur peut basculer entre le **Mode de base** et le **Mode avancé** en cliquant sur les liens dans l’Éditeur de transformation.

### <a name="transform-editor-send-feedback"></a>Éditeur de transformation : envoyer des commentaires

Cliquez sur le lien **Envoyer des commentaires** pour ouvrir la boîte de dialogue **Commentaires** dans laquelle la zone de commentaires est préremplie avec les exemples fournis par l’utilisateur. L’utilisateur doit vérifier le contenu de la zone de commentaires et fournir plus de détails pour nous aider à comprendre le problème. Si l’utilisateur ne souhaite pas partager de données avec Microsoft, il doit supprimer les données d’exemple préremplies avant de cliquer sur le bouton **Envoyer des commentaires**. 

### <a name="editing-existing-transformation"></a>Modification d’une transformation existante

Un utilisateur peut modifier une transformation **Dériver des colonnes par exemple** existante en sélectionnant l’option **Edit** (Modifier) à l’étape Transformation. Un clic sur **Edit** (Modifier) ouvre l’Éditeur de transformation en **Mode avancé**, et tous les exemples fournis pendant la création de la transformation sont affichés.

## <a name="examples-of-string-transformations-by-example"></a>Exemples de transformations de chaîne par exemple


>[!NOTE] 
>Les valeurs en **gras** représentent les exemples qui ont été fournis pour effectuer la transformation dans le jeu de données indiqué.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extraction de noms de fichiers à partir de chemins de fichiers

Nombre d’exemples qui ont été nécessaires pour ce cas : 2

|Entrée|Sortie|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Manipulation de cas lors de l’extraction de chaîne

Nombre d’exemples qui ont été nécessaires pour ce cas : 3

|Entrée|Sortie|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Manipulation de format de date lors de l’extraction de chaîne

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

|Entrée|Sortie|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 @ 04:11:35;|**12 Nov 2015 4AM**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 @ 01:29:52;|12 Nov 2015 1AM|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 @ 07:29:58;|12 Nov 2015 7AM|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 @ 20:53:30;|12 Oct 2015 8PM|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 @ 23:02:27;|12 Oct 2015 11PM|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 @ 19:25:22;|12 Oct 2015 7PM|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 @ 18:43:07;|12 Oct 2015 6PM|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 @ 20:01:29-Station:STA24;|12 Oct 2015 8PM|

### <a name="s4-concatenating-strings"></a>S4. Concaténation de chaînes

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

>[!NOTE] 
>Dans cet exemple, le caractère spécial · représente des espaces dans la colonne de sortie.

|Prénom|Initiale|Nom|Sortie|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Génération d’initiales

Nombre d’exemples qui ont été nécessaires pour ce cas : 2

|Nom complet|Sortie|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mappage de valeurs constantes

Nombre d’exemples qui ont été nécessaires pour ce cas : 3

|Sexe|Sortie|
|:-----|:-----:|
|Masculin|**0**|
|Féminin|**1**|
|Unknown|**2**|
|Féminin|1|
|Féminin|1|
|Masculin|0|
|Unknown|2|
|Masculin|0|
|Féminin|1|

## <a name="examples-of-number-transformations-by-example"></a>Exemples de transformations de nombre par exemple

>[!NOTE] 
>Les valeurs en **gras** représentent les exemples qui ont été fournis pour effectuer la transformation dans le jeu de données indiqué.


### <a name="n1-rounding-to-nearest-10"></a>N1. Arrondi à la dizaine la plus proche

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

|Entrée|Sortie|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Arrondi à la dizaine inférieure la plus proche

Nombre d’exemples qui ont été nécessaires pour ce cas : 2

|Entrée|Sortie|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Arrondi à 0,05

Nombre d’exemples qui ont été nécessaires pour ce cas : 2

|Entrée|Sortie|
|-----:|-----:|
|-75,5812935|**-75,60**|
|-75,2646799|-75,25|
|-75,3519752|-75,35|
|-75,343513|**-75,35**|
|-75,6033497|-75,60|
|-75,283245|-75,30|

### <a name="n4-binning"></a>N4. Quantification

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

|Entrée|Sortie|
|-----:|:-----:|
|20,16|**20-25**|
|14,32|10-15|
|5,44|5-10|
|3,84|0-5|
|3,73|0-5|
|7,36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Mise à l’échelle vers 1000

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

|Entrée|Sortie|
|-----:|-----:|
|-243|**-243000**|
|-12,5|-12500|
|-2345,23292|-2345232,92|
|-1202,3433|-1202343,3|
|1202,3433|1202343,3|

### <a name="n6-padding"></a>N6. Remplissage

Nombre d’exemples qui ont été nécessaires pour ce cas : 1

|Code|Sortie|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Exemples de transformations de date par exemple

>[!NOTE] 
>Les valeurs en **gras** représentent les exemples qui ont été fournis pour effectuer la transformation dans le jeu de données indiqué.


### <a name="d1-extracting-date-parts"></a>D1. Extraction de parties de date

Ces parties de date ont été extraites à l’aide de différentes transformations par exemple sur le même jeu de données. Les chaînes en gras représentent les exemples qui ont été donnés dans leur transformation respective.

|Datetime|Jour de la semaine|Date|Mois|Year|Hour|Minute|Seconde|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Ven**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Mer|17|Jan|1990|13.|32|01|
|14-Fév-2034 05:36:07|Mars|14|Fév|2034|5.|36|07|
|14-Mars-2002 13:16:16|Jeu|14|Mars|2002|13.|16|16|
|21-Jan-1985 05:44:43|Lun|21|Jan|1985|5.|44|**43**|
|16-Août-1985 01:11:56|Ven|16|Août|1985|1|11|56|
|20-Déc-2033 18:36:29|Mars|20|Déc|2033|18|36|29|
|16-Juil-1984 10:21:59|Lun|16|Juil|1984|10|21|59|
|13-Jan-2038 10:59:36|Mer|13.|Jan|2038|10|59|36|
|14-Août-1982 15:13:54|Sam|14|Août|1982|15|13.|54|
|22-Nov-2030 08:18:08|Ven|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Mars|21|Oct|1997|8|42|58|
|28-Nov-2006 14:19:15|Mars|28|Nov|2006|14|19|15|
|29-Avr-2031 04:59:45|Mars|29|Avr|2031|4|59|45|
|29-Jan-2032 02:38:36|Jeu|29|Jan|2032|2|38|36|
|11-Mai-2028 15:31:52|Jeu|11|Mai|2028|15|31|52|
|15-Juil-1977 12:45:39|Ven|15|Juil|1977|12|45|39|
|27-Jan-2029 05:55:41|Sam|27|Jan|2029|5.|55|41|
|03-Mars-2024 10:17:49|Dim|3|Mars|2024|10|17|49|
|14-Avr-2010 00:23:13|Mer|14|Avr|2010|0|23|13.|

### <a name="d2-formatting-dates"></a>D2. Mise en forme de dates

Ces mises en forme de dates ont été effectuées à l’aide de différentes transformations par exemple sur le même jeu de données. Les chaînes en gras représentent les exemples qui ont été donnés dans leur transformation respective.

|Datetime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Vendredi 31 janvier 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Mercredi 17 janvier 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Fév-2034 05:36:07|2/14/2034|Mardi 14 février 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|14-Mars-2002 13:16:16|3/14/2002|Jeudi 14 mars 2002|03142002 13:16|14/3/2002 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Lundi 21 janvier 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Août-1985 01:11:56|8/16/1985|Vendredi 16 août 1985|08161985 1:11|16/8/1985 1:11 AM|Q3 1985
|20-Déc-2033 18:36:29|12/20/2033|Mardi 20 décembre 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Juil-1984 10:21:59|7/16/1984|Lundi 16 juillet 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Jan-2038 10:59:36|1/13/2038|Mercredi 13 janvier 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14-Août-1982 15:13:54|8/14/1982|Samedi 14 août 1982|08141982 15:13|14/8/1982 3:13 PM|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Vendredi 22 novembre 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Mardi 21 octobre 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|Mardi 28 novembre 2006|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Avr-2031 04:59:45|4/29/2031|Mardi 29 avril 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Jeudi 29 janvier 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11-Mai-2028 15:31:52|5/11/2028|Jeudi 11 mai 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Juil-1977 12:45:39|7/15/1977|Vendredi 15 juillet 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Samedi 27 janvier 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|03-Mars-2024 10:17:49|3/3/2024|Dimanche 3 mars 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|14-Avr-2010 00:23:13|4/14/2010|Mercredi 14 avril 2010|04142010 0:23|14/4/2010 12:23 AM|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mappages d’heure à heure

Ces mappages entre des dates/heures et des périodes ont été effectués à l’aide de différentes transformations par exemple sur le même jeu de données. Les chaînes en gras représentent les exemples qui ont été donnés dans leur transformation respective.

|Datetime|Période(secondes)|Période(minutes)|Période(deux heures)|Période(30 minutes)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Fév-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-Mars-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Août-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Déc-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Juil-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13-Jan-2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Août-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Avr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11-Mai-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Juil-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mars-2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14-Avr-2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Exemples de transformations composites par exemple

|Durée du trajet|Heure de départ|ID de station de départ|Latitude de station de départ|Longitude de station de départ|Type d’utilisateur|Colonne|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42,3625|-71,08822|Abonné|**Un Abonné a choisi un vélo à la station 107, lat/long (42,363,-71,088), le 8 janvier 2016 à environ 16h00. La durée du trajet a été de 61 minutes**|
|61|2016-01-17 09:28:10|74|42,373268|-71,118579|Client|Un Client a choisi un vélo à la station 74, lat/long (42,373,-71,119), le 17 janvier 2016 à environ 9h00. La durée du trajet a été de 61 minutes|
|62|2016-01-25 08:10:26|176|42,386748020450561|-71,119018793106079|Abonné|Un Abonné a choisi un vélo à la station 176, lat/long (42,387,-71,119), le 25 janvier 2016 à environ 8h00. La durée du trajet a été de 62 minutes|
|63|2016-01-08 10:10:29|107|42,3625|-71,08822|Abonné|Un Abonné a choisi un vélo à la station 107, lat/long (42,363,-71,088), le 8 janvier 2016 à environ 10h00. La durée du trajet a été de 63 minutes|
|64|2016-01-15 19:42:08|68|42,36507|-71,1031|Abonné|Un Abonné a choisi un vélo à la station 68, lat/long (42,365,-71,103), le 15 janvier 2016 à environ 19h00. La durée du trajet a été de 64 minutes|
|64|2016-01-22 18:16:13|115|42,387995|-71,119084|Abonné|Un Abonné a choisi un vélo à la station 115, lat/long (42,388,-71,119), le 22 janvier 2016 à environ 18h00. La durée du trajet a été de 64 minutes|
|68|2016-01-18 09:51:52|178|42,359573201090441|-71,101294755935669|Abonné|Un Abonné a choisi un vélo à la station 178, lat/long (42,360,-71,101), le 18 janvier 2016 à environ 9h00. La durée du trajet a été de 68 minutes|
|69|2016-01-14 08:57:55|176|42,386748020450561|-71,119018793106079|Abonné|Un Abonné a choisi un vélo à la station 176, lat/long (42,387,-71,119), le 14 janvier 2016 à environ 8h00. La durée du trajet a été de 69 minutes|
|69|2016-01-13 22:12:55|141|42,363560158429884|-71,08216792345047|Abonné|Un Abonné a choisi un vélo à la station 141, lat/long (42,364,-71,082), le 13 janvier 2016 à environ 22h00. La durée du trajet a été de 69 minutes|
|69|2016-01-15 08:13:09|176|42,386748020450561|-71,119018793106079|Abonné|Un Abonné a choisi un vélo à la station 176, lat/long (42,387,-71,119), le 15 janvier 2016 à environ 8h00. La durée du trajet a été de 69 minutes|


## <a name="technical-notes"></a>Notes techniques

### <a name="conditional-transformations"></a>Transformations conditionnelles
Dans certains cas, il est impossible de trouver une seule transformation qui satisfait aux exemples donnés. Dans ce cas, la transformation Dériver une colonne par exemple tente de grouper les entrées en fonction d’un modèle et d’apprendre une transformation distincte pour chaque groupe. Nous appelons cela la **transformation conditionnelle**. Une tentative de **transformation conditionnelle** est effectuée uniquement pour les transformations avec une seule colonne d’entrée. 

### <a name="reference"></a>Informations de référence
Vous trouverez plus d’informations sur la technologie de transformation de chaîne par exemple dans [cette publication](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
