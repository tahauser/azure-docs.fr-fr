---
title: "Recherche de données à l’aide de recherches de journal dans Azure Log Analytics | Microsoft Docs"
description: "Les recherches de journal vous permettent de combiner et de mettre en corrélation toutes les données de l’ordinateur à partir de plusieurs sources dans votre environnement."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: cef7fc282edc7396a0f26dab98ea7f1087315b23
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Trouver des données avec les recherches de journaux dans Log Analytics

>[!NOTE]
> Cet article détaille les recherches dans les journaux via le langage de requête hérité de Log Analytics.  Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), voir [Comprendre les recherches de journaux dans Log Analytics (nouveau)](log-analytics-log-search-new.md).


La fonctionnalité de recherche de journal se trouve au cœur de Log Analytics et vous permet de combiner et de mettre en corrélation des données machine de plusieurs sources dans votre environnement. Des solutions sont également alimentées par la recherche de journal pour vous proposer des mesures cernant un domaine problématique en particulier.

Sur la page Recherche, vous pouvez créer une requête, puis lorsque vous effectuez une recherche, vous pouvez filtrer les résultats en utilisant des contrôles de facette. Vous pouvez également créer des requêtes avancées pour la transformation, le filtrage et les rapports relatifs aux résultats.

Les requêtes de recherche de journal courantes apparaissent dans la plupart des pages de solutions. Vous pouvez cliquer sur les vignettes ou explorer d’autres éléments dans l’ensemble du portail OMS afin d’afficher les détails de l’élément à l’aide de la recherche dans les journaux.

Dans ce didacticiel, nous examinerons des exemples afin de couvrir toutes les notions de base dont vous avez besoin lorsque vous utilisez la recherche de journal.

Nous allons commencer par des exemples simples et pratiques, puis nous développerons à partir de ceux-ci pour vous permettre d’avoir une compréhension pratique de l’utilisation de la syntaxe pour extraire les informations dont vous avez besoin à partir des données.

Une fois que vous êtes familiarisé avec les techniques de recherche, vous pouvez consulter les [informations de référence sur la recherche de journal Log Analytics](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Utilisation de filtres de base
La première chose à savoir est que la première partie d’une requête de recherche avant un caractère de barre verticale « | » est toujours un *filtre*. Considérez-la comme une clause WHERE dans TSQL : elle détermine *quel* sous-ensemble de données doit être extrait de l’espace de travail Log Analytics. La recherche dans un magasin de données consiste principalement à préciser les caractéristiques de données que vous souhaitez extraire ; il est donc naturel qu’une requête commence par la clause WHERE.

Les filtres les plus simples que vous pouvez utiliser sont des *mots clés*tels que « error » ou « timeout » ou un nom d'ordinateur. Ces types de requêtes simples retournent généralement différentes formes de données dans le même jeu de résultats. Cela est dû au fait que Log Analytics dispose de différents *types* de données dans le système.

### <a name="to-conduct-a-simple-search"></a>Pour effectuer une recherche simple
1. Dans le portail OMS, cliquez sur **Recherche de journal**.  
    ![Vignette Rechercher](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. Dans le champ de requête, tapez `error` puis cliquez sur **Rechercher**.  
    ![Recherche d’erreur](./media/log-analytics-log-searches/oms-search-error.png)  
    Par exemple, la requête pour `error` dans l’image suivante a retourné 100 000 enregistrements **Événement** (collectés par la gestion des journaux), 18 enregistrements **ConfigurationAlert** (générés par l’évaluation de la configuration), et 12 enregistrements **ConfigurationChange** (capturés par le suivi des modifications).    
    ![Recherche de résultats](./media/log-analytics-log-searches/oms-search-results01.png)  

Ces filtres ne sont pas vraiment des classes/types d'objet. *Type* est simplement une balise, une propriété ou une chaîne/un nom/une catégorie associés à un élément de données. Certains documents dans le système sont balisés en tant que **Type:ConfigurationAlert**, d’autres en tant que **Type:Perf** ou **Type:Event**, et ainsi de suite. Chaque résultat de recherche, document, enregistrement ou entrée affiche toutes les propriétés brutes et leurs valeurs pour chacun de ces éléments de données. Vous pouvez utiliser ces noms de champs pour préciser dans le filtre lorsque vous souhaitez récupérer uniquement les enregistrements dont le champ a cette valeur donnée.

Le *type* est simplement un champ que tous les enregistrements ont. Il n'est pas différent des autres champs. Cela a été établi suivant la valeur du champ Type. Cet enregistrement aura une autre forme. À ce propos, **Type=Perf** ou **Type=Event** constituent également la syntaxe dont vous avez besoin pour apprendre à interroger des données ou événements de performances.

Vous pouvez utiliser les deux-points (:) ou le signe égal (=) après le nom du champ et avant la valeur. Les notations **Type:Event** et **Type=Event** ont la même signification. Vous pouvez donc choisir le style qui vous convient.

Ainsi, si les enregistrements Type=Perf ont un champ appelé « CounterName », vous pouvez alors écrire une requête qui ressemble à `Type=Perf CounterName="% Processor Time"`.

Vous obtiendrez uniquement les données de performances où le nom du compteur de performance est « % Processor Time ».

### <a name="to-search-for-processor-time-performance-data"></a>Pour rechercher des données de performance de temps du processeur
* Dans le champ de requête de recherche, tapez `Type=Perf CounterName="% Processor Time"`

Vous pouvez également être plus précis et utiliser **InstanceName=_’Total’** dans la requête, qui est un compteur de performances Windows. Vous pouvez également sélectionner une facette et une autre valeur **field:value**. Ce filtre est automatiquement ajouté à votre filtre dans la barre de requête. Vous pouvez le voir dans l'image suivante. Elle indique où cliquer pour ajouter **InstanceName:’_Total’** à la requête sans devoir taper quoi que ce soit.

![Recherche de facette](./media/log-analytics-log-searches/oms-search-facet.png)

Votre requête devient alors `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

Dans cet exemple, vous n’êtes pas obligé de spécifier **Type=Perf** pour obtenir ce résultat. Étant donné que les champs CounterName et InstanceName existent uniquement pour les enregistrements de Type=Perf, la requête est suffisamment spécifique pour retourner les mêmes résultats que le résultat précédent plus long :

```
CounterName="% Processor Time" InstanceName="_Total"
```

Cela est dû au fait que tous les filtres dans la requête sont évalués comme étant dans chacun d’eux *ET* entre eux. En effet, plus vous ajoutez des champs aux critères, moins vous obtenez des résultats plus spécifiques et affinés.

Par exemple, la requête `Type=Event EventLog="Windows PowerShell"` est identique à `Type=Event AND EventLog="Windows PowerShell"`. Elle retourne tous les événements qui ont été enregistrés dans et collectés à partir du journal des événements Windows PowerShell. Si vous ajoutez un filtre plusieurs fois en sélectionnant de manière répétée la même facette, le problème est alors purement esthétique : il peut surcharger la barre de recherche, mais il retourne toujours les mêmes résultats, car l'opérateur AND implicite est toujours présent.

Vous pouvez facilement inverser l'opérateur AND implicite à l'aide d'un opérateur NOT explicite. Par exemple : 

`Type:Event NOT(EventLog:"Windows PowerShell")` ou son équivalent `Type=Event EventLog!="Windows PowerShell"` retournent tous les événements de tous les autres journaux qui ne sont PAS le journal Windows PowerShell.

Vous pouvez également utiliser un autre opérateur booléen tel que « OR ». La requête suivante retourne les enregistrements pour lesquels le journal des événements est une application OU un système.

```
EventLog=Application OR EventLog=System
```

À l'aide de la requête ci-dessus, vous obtiendrez les entrées pour les journaux dans le même jeu de résultats.

Cependant, si vous supprimez l'opérateur OR en laissant l’opérateur implicite AND en place, la requête suivante ne produira pas de résultats car aucune entrée du journal des événements n’appartient aux DEUX journaux. Chaque entrée de journal des événements a été écrite pour seulement un des deux journaux.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Utilisation de filtres supplémentaires
La requête suivante retourne les entrées pour deux journaux des événements pour tous les ordinateurs qui ont envoyé des données.

```
EventLog=Application OR EventLog=System
```

![Recherche de résultats](./media/log-analytics-log-searches/oms-search-results03.png)

La sélection de l'un des champs ou des filtres restreindra la requête pour un ordinateur spécifique, en excluant tous les autres. La requête obtenue peut se présenter comme suit.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Ce qui revient à la commande suivante, en raison de l'opérateur AND implicite.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Chaque requête est évaluée dans l'ordre explicite suivant. Notez la parenthèse.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Comme le champ du journal des événements, vous pouvez récupérer des données uniquement pour un ensemble d'ordinateurs spécifiques en ajoutant OR. Par exemple : 

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

De même, cette requête suivante retourne le **pourcentage de temps processeur** uniquement pour les deux ordinateurs sélectionnés.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Types de champ
Lorsque vous créez des filtres, vous devez comprendre les différences entre les types de champs retournés par les recherches dans les journaux.

Les **champs interrogeables** s’affichent en bleu dans les résultats de la recherche.  Vous pouvez les utiliser dans des conditions de recherche propres au champ, par exemple :

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

Les **champs interrogeables en texte libre** s’affichent en gris dans les résultats de la recherche.  Ils ne peuvent pas être utilisés avec des conditions de recherche propres au champ, à la différence des champs interrogeables.  Ils ne sont interrogeables que dans le cadre de requêtes portant sur tous les champs, comme ci-dessous.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Opérateurs booléens
Avec les champs numériques et DateHeure, vous pouvez rechercher des valeurs à l’aide d’opérateurs *supérieur à*, *inférieur à* et *inférieur ou égal à*. Vous pouvez utiliser des opérateurs simples tels que >, <, >=, <=, != dans la barre de recherche de requête.

Vous pouvez interroger un journal des événements spécifique pour une période spécifique. Par exemple, l’expression mnémonique suivante permet d’exprimer les dernières 24 heures.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Pour effectuer une recherche à l'aide d'un opérateur booléen
* Dans le champ de requête de recherche, tapez `EventLog=System TimeGenerated>NOW-24HOURS`  
    ![Recherche avec des opérateurs boléens](./media/log-analytics-log-searches/oms-search-boolean.png)

Bien que vous puissiez contrôler graphiquement l'intervalle de temps, et nous vous invitons à faire cela la plupart du temps, l’ajout d’un filtre de temps directement dans la requête présente certains avantages. Par exemple, cela fonctionne très bien avec les tableaux de bord qui vous permettent de remplacer le temps pour chaque vignette, quel que soit le sélecteur de temps *global* sur la page du tableau de bord. Pour plus d'informations, consultez [Questions relatives au temps dans le tableau de bord](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Lors du filtrage par heure, n’oubliez pas que vous obtenez des résultats pour l’ *intersection* des deux périodes : celle indiquée dans le portail OMS (S1) et celle indiquée dans la requête (S2).

![intersection](./media/log-analytics-log-searches/oms-search-intersection.png)

Cela signifie que, si les périodes ne présentent pas d’intersection, par exemple, dans le portail OMS où vous choisissez **Cette semaine** et dans la requête où vous précisez la **semaine dernière**, il n’y a pas d’intersection et vous ne recevez aucun résultat.

Les opérateurs de comparaison utilisés pour le champ TimeGenerated sont également utiles dans d'autres situations. Par exemple, avec des champs numériques.

Par exemple, étant donné que les alertes d'évaluation de configuration ont les valeurs de gravité suivantes :

* 0 = Information
* 1 = Avertissement
* 2 = Critique

Vous pouvez interroger pour les alertes d’avertissements et les alertes critiques et exclure également les alertes d'information avec la requête suivante :

```
Type=ConfigurationAlert  Severity>=1
```


Vous pouvez aussi utiliser des requêtes de plage de données. Cela signifie que vous pouvez fournir la plage de valeurs de début et de fin dans une séquence. Par exemple, si vous souhaitez obtenir des événements du journal des événements Operations Manager où l'ID de l’événement est supérieur ou égal à 2100 mais inférieur ou égal à 2199, la requête suivante va alors les retourner.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> La syntaxe de plage de données que vous devez utiliser est le séparateur field:value avec les deux-points (:) field:value et *non* le signe égal (=). Ajoutez l’extrémité supérieure et inférieure de la plage de données entre crochets et séparez-les par deux points (..).
>
>

## <a name="manipulate-search-results"></a>Manipulation des résultats de la recherche
Lorsque vous recherchez des données, vous devez affiner votre requête de recherche et avoir un bon niveau de contrôle sur les résultats. Lorsque les résultats sont récupérés, vous pouvez appliquer des commandes pour les transformer.

Les commandes dans les recherches de Log Analytics *doivent* se trouver après la barre verticale (|). La première partie d'une chaîne de requête doit toujours être un filtre. Celui-ci définit le jeu de données avec lequel vous travaillez, puis il envoie ces résultats dans une commande. Vous pouvez ensuite utiliser ce pipe pour ajouter des commandes supplémentaires. Cela est relativement similaire au pipeline Windows PowerShell.

En général, le langage de recherche de Log Analytics tente de respecter le style et les instructions PowerShell de manière à ce qu’ils soient similaires à ceux des professionnels de l’informatique et pour faciliter cette étape de l’apprentissage.

Le nom des commandes est un verbe, ce qui vous permet de connaître facilement leur fonction.  

### <a name="sort"></a>Trier
La commande de tri vous permet de définir l'ordre de tri avec un ou plusieurs champs. Même si vous ne l'utilisez pas, un ordre de temps décroissant est appliqué par défaut. Les résultats les plus récents sont toujours en haut des résultats de recherche. Cela signifie que lorsque vous exécutez une recherche avec `Type=Event EventID=1234` , ce qui est réellement exécuté pour vous est :

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

C’est parce qu’il s’agit du type d'expérience que vous connaissez avec les journaux. Par exemple, dans l'observateur d'événements Windows.

Vous pouvez utiliser la fonction Sort pour modifier la façon dont les résultats sont retournés. Les exemples suivants illustrent son fonctionnement.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Ces simples exemples ci-dessus vous montrent le fonctionnement des commandes : elles modifient la forme des résultats que le filtre a retournés.

### <a name="limit-and-top"></a>Limit et Top
Une autre commande moins connue est LIMIT. Limit est un verbe similaire à PowerShell. Limit a la même fonction que la commande TOP. Les requêtes suivantes retournent les mêmes résultats.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Pour effectuer une recherche à l'aide de Top
* Dans le champ de requête de recherche, tapez `Type=Event EventID=600 | Top 1` .  
    ![Recherche top](./media/log-analytics-log-searches/oms-search-top.png)

Dans l’image ci-dessus, il existe des 358 000 enregistrements avec l’EventID=600. Les champs, les facettes et les filtres sur la gauche affichent toujours des informations sur les résultats retournés *par la partie du filtre* de la requête, qui est la partie située avant une barre verticale. Le panneau **Résultats** retourne uniquement le résultat le plus récent car l’exemple de commande a formé et transformé les résultats.

### <a name="select"></a>Sélectionnez
La commande SELECT agit comme Select-Object dans PowerShell. Elle retourne des résultats filtrés qui n'ont pas toutes leurs propriétés d'origine. Au lieu de cela, elle sélectionne uniquement les propriétés que vous avez spécifiées.

#### <a name="to-run-a-search-using-the-select-command"></a>Pour exécuter une recherche à l'aide de la commande select
1. Dans Rechercher, tapez `Type=Event` puis cliquez sur **Rechercher**.
2. Cliquez sur **+ Afficher plus** dans un des résultats pour afficher toutes les propriétés dont disposent les résultats.
3. Sélectionnez certains d’entre eux de façon explicite ; la requête devient alors `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![Recherche select](./media/log-analytics-log-searches/oms-search-select.png)

Il s’agit d’une commande particulièrement utile pour contrôler les résultats de recherche et choisir uniquement des portions de données qui importent vraiment pour l’exploration et qui, bien souvent, ne correspondent pas à la totalité de l’enregistrement. Elle est également utile lorsque des enregistrements de différents types présentent *certaines* propriétés communes, mais que leurs propriétés ne sont pas *toutes* communes. Vous pouvez générer des résultats qui ressemblent plus naturellement à une table ou fonctionnent bien lorsqu’ils sont exportés vers un fichier CSV puis envoyés dans Excel.

## <a name="use-the-measure-command"></a>Utilisation de la commande measure
MEASURE est une des commandes les plus polyvalentes dans les recherches de Log Analytics. Elle vous permet d'appliquer des *fonctions* statistiques à vos données et de regrouper des résultats par champ donné. Il existe plusieurs fonctions statistiques qui prennent en charge Measure.

### <a name="measure-count"></a>La fonction count() de Measure
La première fonction statistique à utiliser et la plus simple à comprendre est la fonction *count()* .

Les résultats d'une requête de recherche telle que `Type=Event`, affichent des filtres, également appelés facettes, sur le côté gauche des résultats de la recherche. Les filtres montrent une distribution de valeurs pour un champ donné dans les résultats de la recherche effectuée.

![Recherche measure count](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Par exemple, dans l’image ci-dessus, vous voyez le champ **Computer**. L’image montre que, parmi les près de 739 000 événements figurant dans les résultats, 68 contiennent des valeurs uniques et distinctes dans le champ **Computer**. La vignette affiche uniquement les 5 premières, qui sont les 5 valeurs les plus courantes écrites dans le champ **Ordinateur** , triées par nombre de documents contenant cette valeur spécifique dans ce champ. L’image montre que, parmi ces près de 369 000 événements, 90 000 proviennent de l’ordinateur OpsInsights04.contoso.com, 83 0000 de l’ordinateur DB03.contoso.com, et ainsi de suite.

Comment faire si vous souhaitez voir toutes les valeurs, étant donné que la vignette ne montre que les 5 premières ?

C'est ce que la commande Measure permet de faire avec la fonction count(). Cette fonction n'utilise aucun paramètre. Vous spécifiez simplement le champ par lequel vous voulez regrouper – le champ **Ordinateur** dans ce cas :

`Type=Event | Measure count() by Computer`

![Recherche measure count](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Le champ **Computer** est toutefois simplement un champ utilisé *dans* chaque élément de données : aucune base de données relationnelle n’est impliquée, et il n’existe aucun objet **Computer**distinct nulle part. Simplement, les valeurs *dans* les données peuvent décrire l’entité qui les a générées, ainsi qu’un certain nombre d’autres caractéristiques et aspects des données, d’où le terme *facette*. Toutefois, vous pouvez également les regrouper par d'autres champs. Étant donné que les résultats d’origine des près de 739 000 événements canalisés dans la commande Measure ont également un champ nommé **EventID**, vous pouvez appliquer la même technique pour regrouper par ce champ et obtenir un nombre d’événements par EventID :

```
Type=Event | Measure count() by EventID
```

Si vous n'êtes pas intéressé par le nombre d'enregistrements réels qui contiennent une valeur spécifique, mais que vous souhaitez seulement une liste de valeurs elles-mêmes, vous pouvez ajouter une commande *Sélect* à la fin de celle-ci et simplement sélectionner la première colonne :

```
Type=Event | Measure count() by EventID | Select EventID
```

Vous pouvez ensuite obtenir des résultats de requête plus complexes et les trier, ou vous pouvez aussi simplement cliquer sur les colonnes dans la grille.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Pour effectuer une recherche à l'aide de measure count
* Dans le champ de requête de recherche, tapez `Type=Event | Measure count() by EventID`
* Ajoutez `| Select EventID` à la fin de la requête.
* Enfin, ajoutez `| Sort EventID asc` à la fin de la requête.

Il est important de comprendre et de mettre en évidence certains points essentiels :

Tout d'abord, les résultats que vous voyez ne sont plus les résultats bruts d'origine. Il s’agit en fait de résultats agrégés : autrement dit, des groupes de résultats. Cela n'est pas un problème, mais vous devez comprendre que vous interagissez avec une toute autre forme de données qui diffère de la forme brute d'origine créée en cours de route lorsque la fonction d'agrégation ou statistique est utilisée.

Ensuite, la fonction **Measure count** ne retourne pour le moment que les 100 premiers résultats distincts. Cette limite ne s'applique pas aux autres fonctions statistiques. Par conséquent, vous devez généralement utiliser d’abord un filtre plus précis pour rechercher des éléments spécifiques avant d'appliquer measure count().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Utilisation des fonctions min et max avec la commande measure
Il existe plusieurs situations où les commandes **Measure Max()** et **Measure Min()** sont utiles. Toutefois, étant donné que chaque fonction est l'opposé de l'autre, nous démontrerons la fonction Max() et vous pourrez ensuite tester vous-même la fonction Min().

Si vous interrogez des événements de sécurité, ceux-ci ont une propriété **Level** qui peut varier. Par exemple : 

```
Type=SecurityEvent
```

![Recherche du début de la fonction measure count](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Si vous souhaitez afficher la valeur la plus élevée de toutes les alertes de sécurité pour un ordinateur commun donné, puis les grouper par champ, vous pouvez utiliser la syntaxe suivante :

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![Recherche l’ordinateur de la fonction measure max](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Elle indique que, pour les ordinateurs ayant des enregistrements **Level**, la plupart ont au moins le niveau 8, et un grand nombre ont le niveau 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Recherche l’ordinateur qui génère la valeur horaire measure max](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Cette fonction fonctionne bien avec les nombres, mais elle fonctionne également avec les champs DateHeure. Il est utile de vérifier l'horodatage de la dernière heure ou de l’heure la plus récente pour tout type de données indexé pour chaque ordinateur. Par exemple : quand l’événement de sécurité plus récent a-t-il été signalé pour chaque ordinateur ?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Utilisation de la fonction avg avec la commande measure
La fonction statistique Avg() utilisée avec measure vous permet de calculer la valeur moyenne pour un champ et de grouper les résultats selon ce même champ ou un autre. Cela est utile dans de nombreux cas, pour les données de performances par exemple.

Nous allons commencer par les données de performances. Remarque : Log Analytics collecte actuellement les compteurs de performances pour les ordinateurs Windows et Linux.

Pour rechercher *toutes* les données de performances, la requête la plus simple est :

```
Type=Perf
```

![Recherche du début de la fonction avg](./media/log-analytics-log-searches/oms-search-avg01.png)

La première chose que vous pouvez remarquer est que Log Analytics présente trois perspectives : Liste, qui montre les enregistrements réels sous-jacents aux graphiques ; Table, qui montre une vue tabulaire des données d’un compteur de performances ; et Mesures, qui montre des graphiques pour les compteurs de performances.

Dans l'image ci-dessus, il existe deux ensembles de champs marqués qui indiquent les éléments suivants :

* Le premier jeu identifie le nom du compteur de performances Windows, le nom de l’objet et le nom de l’instance dans le filtre de la requête. Il s’agit des champs que vous utiliserez probablement le plus souvent en tant que facettes ou filtres.
* **CounterValue** est la valeur réelle du compteur. Dans cet exemple, la valeur est *75*.
* **TimeGenerated** a la valeur 12:51, au format 24 heures.

Voici une vue des mesures dans un graphique.

![Recherche du début de la fonction avg](./media/log-analytics-log-searches/oms-search-avg02.png)

Après avoir lu ce qui concerne la forme d’enregistrement Perf et ce qui concerne d’autres techniques de recherche, vous pouvez utiliser measure Avg() pour agréger ce type de données numériques.

Voici un exemple simple :

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![Recherche de l’exemple de valeur de la fonction avg](./media/log-analytics-log-searches/oms-search-avg03.png)

Dans cet exemple, vous sélectionnez le compteur de performance du temps processeur total et la moyenne par ordinateur. Si vous souhaitez restreindre les résultats aux 6 dernières heures, vous pouvez utiliser le contrôle de filtre de temps ou ajouter une mention spéciale à votre requête, comme suit :

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Rechercher à l’aide de la fonction avg avec la commande measure
* Dans le champ de requête de recherche, tapez `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Vous pouvez agréger et corréler les données *entre* plusieurs ordinateurs. Par exemple, imaginez que vous disposez d'un ensemble d'hôtes dans une sorte de batterie de serveurs où chaque nœud est égal à n'importe quel autre et ils effectuent simplement le même type de travail et la charge doit être à peu près équilibrée. Vous pouvez obtenir leurs compteurs en une seule fois avec la requête suivante et obtenir des moyennes pour l’ensemble de la batterie. Vous pouvez commencer en choisissant les ordinateurs avec l'exemple suivant :

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Maintenant que vous avez les ordinateurs, vous ne devez sélectionner que deux indicateurs de performance clés (KPI) : le pourcentage d’utilisation du processeur et le pourcentage d’espace disque disponible. Par conséquent, cette partie de la requête devient :

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Vous pouvez maintenant ajouter des ordinateurs et des compteurs avec l'exemple suivant :

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Étant donné que vous avez une sélection très spécifique, la commande **measure Avg()** peut retourner la moyenne non par ordinateur mais pour l’ensemble de la batterie de serveurs, simplement en effectuant un regroupement par CounterName. Par exemple : 

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Cela vous donne une vue compacte utile de quelques indicateurs de performance clés de votre environnement.

![Recherche du groupement de la fonction avg](./media/log-analytics-log-searches/oms-search-avg04.png)

Vous pouvez facilement utiliser la requête de recherche dans un tableau de bord. Par exemple, vous pouvez enregistrer la requête de recherche, puis créer un tableau de bord à partir de celle-ci, nommé *Indicateurs de performance clés (KPI) de batterie de serveurs web*. Pour en savoir plus sur l’utilisation de tableaux de bord, consultez [Créer un tableau de bord personnalisé dans Log Analytics](log-analytics-dashboards.md).

![Tableau de bord de recherche avg](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Utilisation de la fonction sum avec la commande measure
La fonction sum est similaire à d'autres fonctions de la commande measure. Vous pouvez voir un exemple sur la façon d'utiliser la fonction sum dans [Recherche de journaux IIS W3C dans Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Vous pouvez utiliser les fonctions Max() et Min() avec des chaînes de nombres, de dates et heures, et de texte. Avec des chaînes de texte, elles sont triées par ordre alphabétique et vous pouvez obtenir la première et la dernière.

Toutefois, vous ne pouvez pas utiliser Sum() uniquement avec des champs numériques. Cela vaut également pour la fonction Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Utilisation de la fonction percentile avec la commande measure
La fonction percentile est similaire à Avg() et Sum() dans la mesure où vous pouvez l’utiliser uniquement pour les champs numériques. Vous pouvez utiliser n’importe quel pourcentage entre 1 et 99 sur un champ numérique. Vous pouvez également utiliser les deux commandes **percentile** et **pct**. Voici quelques exemples :  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Utilisation de la commande where
La commande where fonctionne comme un filtre, mais elle peut être appliquée dans le pipeline pour filtrer davantage les résultats agrégés résultant d’une commande Measure – au lieu des résultats bruts qui sont filtrés au début d'une requête.

Par exemple : 

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Vous pouvez ajouter une autre barre verticale « | » et la commande Where pour obtenir uniquement les ordinateurs dont la moyenne du processeur est supérieure à 80 %, avec l’exemple suivant :

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Si vous êtes familiarisé avec Microsoft System Center - Operations Manager, vous pouvez considérer la commande where comme un pack d’administration. S'il l'exemple était une règle, la première partie de la requête serait la source de données et la commande where serait la détection de condition.

Vous pouvez utiliser la requête sous forme de vignette dans **Mon tableau de bord**, comme un moniteur de tri, afin de voir à quel moment les processeurs de l'ordinateur sont surexploités. Pour en savoir plus sur les tableaux de bord, consultez [Créer un tableau de bord personnalisé dans Log Analytics](log-analytics-dashboards.md). Vous pouvez également créer et utiliser des tableaux de bord à l'aide de l'application mobile. Pour plus d’informations, voir [Applications mobiles OMS](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). En bas des deux vignettes de l'image suivante, vous pouvez voir que le moniteur a affiché une liste et un nombre. Essentiellement, le nombre doit toujours être zéro et la liste doit toujours être vide. Dans le cas contraire, il indique une condition d'alerte. Au besoin, vous pouvez l'utiliser pour voir quels ordinateurs sont sous pression.

![Tableau de bord mobile](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Utilisation de l’opérateur in
Les opérateurs *IN* et *NOT IN* vous permettent d’effectuer des sous-recherches, c’est-à-dire des recherches incluant une autre recherche en tant qu’argument. Elles sont contenues entre accolades {} à l’intérieur d’une autre recherche *principale* ou *externe*. Le résultat d’une sous-recherche, souvent une liste de résultats distincts, sert ensuite d’argument pour la recherche principale.

Vous pouvez utiliser les sous-recherches pour faire correspondre des sous-ensembles de données que vous ne pouvez pas décrire directement dans une expression de recherche, mais qui peuvent être générés à partir d’une recherche. Par exemple, si vous souhaitez utiliser une recherche pour trouver tous les événements survenus sur des *ordinateurs auxquels manquent des mises à jour de sécurité*, vous devez concevoir une sous-recherche qui identifie d’abord les *ordinateurs auxquels manquent des mises à jour de sécurité*, avant de rechercher des événements associés à ces hôtes.

Par conséquent, vous pouvez définir les *ordinateurs ayant actuellement des mises à jour de sécurité obligatoires manquantes* avec la requête suivante :

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![Exemple de recherche avec l’opérateur IN](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Une fois la liste en votre possession, vous pouvez utiliser la recherche comme recherche interne pour alimenter la liste des ordinateurs dans une recherche externe (principale) qui recherchera les événements correspondant à ces ordinateurs. Pour cela, incluez la recherche interne dans des accolades et indiquez ses résultats en tant que valeurs possibles pour un filtre/champ dans la recherche externe à l’aide de l’opérateur IN. La requête se présente comme suit :

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![Exemple de recherche avec l’opérateur IN](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Notez également le filtre de temps utilisé dans la recherche interne, car l’évaluation des mises à jour du système prend un instantané de tous les ordinateurs toutes les 24 heures. Vous pouvez rendre la requête interne plus légère et plus précise en recherchant uniquement une journée. La recherche externe utilise pour sa part la sélection de temps de l’interface utilisateur, en récupérant les événements des 7 derniers jours. Consultez la page [Opérateurs booléens](#boolean-operators) pour plus d’informations sur les opérateurs de temps.

Étant donné que vous utilisez uniquement les résultats de la recherche interne comme valeurs de filtre pour la recherche externe, vous pouvez toujours appliquer des commandes à la recherche externe. Par exemple, vous pouvez toujours regrouper les événements ci-dessus avec une autre commande measure :

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![Exemple de recherche avec l’opérateur IN](./media/log-analytics-log-searches/oms-search-in03-revised.png)

En règle générale, vous souhaitez que la requête interne s’exécute rapidement, car elle nécessite des délais d’attente côté service dans Log Analytics, et qu’elle renvoie un nombre de résultats limité. Si la requête interne renvoie trop de résultats, la liste des résultats est tronquée, ce qui peut provoquer un renvoi de résultats incorrects de la part de la recherche externe.

Une autre règle est que la recherche interne doit actuellement fournir des résultats *agrégés* . En d’autres termes, elle doit contenir une commande *measure* ; vous ne pouvez pour le moment pas alimenter les résultats bruts dans une recherche externe.

En outre, il ne peut y avoir qu’un seul opérateur IN et il doit être le dernier filtre de la requête. Il est impossible de relier plusieurs opérateurs IN par un opérateur OR, dans la mesure où cela empêche l’exécution de plusieurs sous-recherches : il est important de noter qu’une seule sous-recherche/recherche interne est possible pour chaque recherche externe.

Malgré ces limites, l’opérateur IN permet de nombreux types de recherches corrélées et vous permet de définir des éléments similaires aux groupes tels que les ordinateurs, les utilisateurs ou les fichiers, quels que soient les champs contenus dans vos données. Voici des exemples supplémentaires :

**Toutes les mises à jour manquantes sur des ordinateurs sur lesquels le paramètre de mise à jour automatique est désactivé**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Tous les événements d’erreur sur des ordinateurs exécutant SQL Server (où l’évaluation de SQL a été exécutée)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Tous les événements de sécurité des ordinateurs qui sont des contrôleurs de domaine (où l’évaluation d’Active Directory a été exécutée)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Quels autres comptes ont ouvert une session sur les ordinateurs auxquels le compte BACONLAND\jochan s’est connecté ?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Utilisation de la commande distinct
Comme son nom l’indique, cette commande fournit une liste de valeurs distinctes pour un champ. Son utilisation est étonnamment simple mais cette commande s’avère très utile. Vous pouvez obtenir le même résultat avec la commande measure count(), comme indiqué ci-dessous.

```
Type=Event | Measure count() by Computer
```

![Exemple de commande de recherche DISTINCT](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Toutefois, si vous souhaitez simplement obtenir une liste de valeurs distinctes sans le nombre de documents qui ont ces valeurs, la commande DISTINCT vous permet de bénéficier d’un résultat plus clair et plus facile à lire, ainsi que d’une syntaxe plus courte, comme indiqué ci-dessous.

```
Type=Event | Distinct Computer
```
![Exemple de commande de recherche DISTINCT](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Utilisation de la fonction countdistinct avec la commande measure
La fonction countdistinct compte le nombre de valeurs distinctes dans chaque groupe. Par exemple, elle peut servir à compter le nombre d’ordinateurs uniques correspondant à chaque Type :

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Utilisation de la commande measure interval
Grâce à la récupération des données de performance en quasi-temps réel, vous pouvez récupérer et visualiser des compteurs de performance dans Log Analytics. En saisissant simplement la requête **Type:Perf** , vous obtiendrez des milliers de graphiques de mesure basés sur le nombre de compteurs et de serveurs de votre environnement Log Analytics. Grâce à l’agrégation des mesures à la demande, vous pouvez consulter les mesures globales dans votre environnement à un niveau élevé, et analyser en profondeur des données plus granulaires lorsque c’est nécessaire.

Supposons que vous souhaitiez connaître l’utilisation moyenne du processeur sur tous vos ordinateurs. Examiner l’utilisation moyenne du processeur pour chaque ordinateur peut ne pas être représentatif car les résultats risquent d’être lissés. Pour une analyse plus détaillée, vous pouvez regrouper vos résultats dans des segments temporels plus courts, puis analysez une série chronologique dans différentes dimensions. Par exemple, vous pouvez effectuer la moyenne horaire de l’utilisation du processeur sur tous vos ordinateurs comme suit :

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![intervalle moyen de mesure](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Par défaut, ces résultats s’affichent dans un graphique interactif en courbes contenant plusieurs séries.  Ce graphique prend en charge le basculement entre séries (avec la remise à l’échelle de l’axe y), le zoom et le survol.  L’option d’affichage de tableau reste disponible pour l’affichage des données brutes si nécessaire.

Vous pouvez également effectuer des regroupements sur d’autres champs. Dans cet exemple, j’examine tous les compteurs en % pour un ordinateur spécifique, et je souhaite connaître le 70e centile horaire de chaque compteur :

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Il est à noter que ces requêtes ne sont pas limitées aux compteurs de performance. Vous pouvez les appliquer à toute mesure. Dans cet exemple, j’examine les journaux IIS W3C. Je voudrais savoir quel est le temps maximal nécessaire, sur un intervalle de 5 minutes, pour traiter chaque demande :

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Utilisation de plusieurs agrégats dans une même requête
Vous pouvez spécifier plusieurs clauses d’agrégation dans une commande measure.  Un alias indépendant peut être attribué à chacune d’entre elles.  Si aucun alias n’est donné, le nom du champ résultant correspondra à la fonction d’agrégation utilisée (par exemple, « avg(CounterValue) » pour avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Voici un autre exemple :

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les recherches de journal, consultez les ressources suivantes :

* Utilisez [Champs personnalisés dans Log Analytics](log-analytics-custom-fields.md) pour étendre les recherches de journal.
* Pour connaître tous les champs de recherche et facettes disponibles dans Log Analytics, consultez les [informations de référence sur la recherche de journal avec Log Analytics](log-analytics-search-reference.md) .
