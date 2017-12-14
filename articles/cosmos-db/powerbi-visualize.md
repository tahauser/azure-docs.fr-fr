---
title: Didacticiel Power BI pour le connecteur Azure Cosmos DB | Microsoft Docs
description: "Utilisez ce didacticiel Power BI pour importer JSON, créer des rapports et visualiser les données à l’aide du connecteur Power BI et Azure Cosmos DB."
keywords: "didacticiel power BI, visualiser les données, connecteur Power BI"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: mimig
ms.openlocfilehash: 6414cdc942c43f6eb13ca8f050d6503bdd3e0b42
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Didacticiel Power BI pour Azure Cosmos DB : visualiser les données à l’aide du connecteur Power BI
[PowerBI.com](https://powerbi.microsoft.com/) est un service en ligne qui vous permet de créer et partager des tableaux de bord et des rapports à partir de données véritablement significatives pour vous et votre organisation.  Power BI Desktop est un outil de création de rapports dédié qui vous permet de récupérer des données à partir de diverses sources, de les fusionner et de les transformer, de créer de puissants rapports et visualisations, puis de publier ces rapports sur Power BI.  Grâce à la dernière version de Power BI Desktop, vous pouvez maintenant vous connecter à votre compte Cosmos DB via le connecteur Cosmos DB pour Power BI.   

Dans ce didacticiel Power BI, nous allons passer en revue les différentes étapes qui vous permettront de vous connecter à un compte Cosmos DB dans Power BI Desktop, d’accéder à une collection à partir de laquelle extraire des données à l’aide du Navigateur, de convertir les données JSON en tableau en utilisant l’éditeur de requête de Power BI Desktop et de générer et publier un rapport sur PowerBI.com.

Après avoir terminé ce didacticiel pour Power BI, vous serez en mesure de répondre aux questions suivantes :  

* Comment générer des rapports avec des données issues de Cosmos DB à l’aide de Power BI Desktop ?
* Comment me connecter à un compte Cosmos DB dans Power BI Desktop ?
* Comment récupérer des données d’une collection dans Power BI Desktop ?
* Comment transformer des données JSON imbriquées dans Power BI Desktop ?
* Comment publier et partager mes rapports dans PowerBI.com ?

> [!NOTE]
> Le connecteur Power BI pour Azure Cosmos DB se connecte à Power BI Desktop pour extraire et transformer les données. Les rapports créés dans Power BI Desktop peuvent ensuite être publiés sur PowerBI.com. L’extraction et la transformation directes des données Azure Cosmos DB ne peuvent pas être effectuées sur PowerBI.com. 

> [!NOTE]
> Pour connecter Azure Cosmos DB à Power BI à l’aide de l’API MongoDB, vous devez utiliser le [pilote ODBC Simba MongoDB](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Composants requis
Avant de suivre les instructions de ce didacticiel Power BI, assurez-vous de disposer des ressources suivantes :

* [La dernière version de Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Un accès à notre compte de démonstration ou aux données de votre compte Cosmos DB.
  * Le compte de démonstration comprend les données relatives au volcan présentées dans ce didacticiel. Ce compte de démonstration n’est lié à aucun contrat de niveau de service et est réservé uniquement à des fins de démonstration.  Nous nous réservons le droit d’apporter à tout moment des modifications à ce compte de démonstration sans préavis et sans raison, y compris sans toutefois s’y limiter, à résilier le compte, à modifier la clé d’accès, à restreindre l’accès, à modifier et supprimer les données.
    * URL : https://analytics.documents.azure.com
    * Clé en lecture seule : MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR + YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw ==
  * Sinon, créez votre propre compte ; pour cela, consultez [Azure Cosmos DB : Développer une application web API DocumentDB avec .NET et le portail Azure](https://azure.microsoft.com/documentation/articles/create-account/). Ensuite, pour obtenir des exemples de données relatives aux volcans semblables à celles utilisées dans ce didacticiel (mais qui ne contiennent pas les blocs GeoJSON), consultez le [site NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) puis importez les données à l’aide de [l’outil de migration de données Azure Cosmos DB](import-data.md).

Pour partager vos rapports dans PowerBI.com, vous devez disposer d’un compte sous PowerBI.com.  Pour en savoir plus sur la version gratuite de Power BI et sur Power BI Pro, visitez le site [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Prise en main
Dans ce didacticiel, imaginons que vous êtes géologue et que vous devez étudier les volcans du monde entier.  Les données relatives aux volcans sont stockées dans un compte Cosmos DB et les documents JSON sont semblables au document en exemple suivant.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Vous souhaitez récupérer les données relatives aux volcans du compte Cosmos DB et visualiser ces données dans un rapport Power BI similaire à ce qui suit.

![En terminant ce didacticiel Power BI avec le connecteur Power BI, vous serez en mesure de visualiser les données du rapport sur les volcans Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Prêt à vous lancer ? Allons-y.

1. Exécutez Power BI Desktop sur votre station de travail.
2. Après le démarrage de Power BI Desktop, un écran d’ *accueil* s’affiche.
   
    ![Power BI Desktop - Écran d’accueil - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Vous pouvez **obtenir des données**, consulter les **Sources récentes** ou **Ouvrir d’autres rapports** directement à partir de cet écran *d’accueil*.  Cliquez sur le symbole X en haut à droite pour fermer l’écran. La vue **Rapport** de Power BI Desktop s’affiche.
   
    ![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Sélectionnez le ruban **Accueil**, puis cliquez sur **Obtenir des données**.  La fenêtre **Obtenir des données** doit alors s’afficher.
5. Cliquez sur **Azure**, sélectionnez **Microsoft Azure DocumentDB (Beta)**, puis cliquez sur **Se connecter**. 

    ![Power BI Desktop - Obtenir des données - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Sur la page **Aperçu connecteur**, cliquez sur **Continuer**. La fenêtre **Connexion à Microsoft Azure DocumentDB** s’affiche.
7. Spécifiez l’URL du point de terminaison du compte Cosmos DB dont vous souhaitez récupérer les données (voir ci-dessous), puis cliquez sur **OK**. Vous pouvez récupérer l’URL dans la zone URI du panneau **[Clés](manage-account.md#keys)** du portail Azure pour utiliser votre propre compte. Pour utiliser le compte de démonstration, entrez `https://analytics.documents.azure.com` pour l’URL. 
   
    Laissez les champs facultatifs du nom de la base de données, du nom de la collection et de l’instruction vides.  Nous allons utiliser le Navigateur pour sélectionner la base de données et la collection afin d’identifier la provenance des données.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Fenêtre de connexion](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Si vous vous connectez pour la première fois à ce point de terminaison, le système vous demandera la clé du compte. Vous pouvez récupérer la clé dans la zone **Clé primaire** du panneau **[Clés en lecture seule](manage-account.md#keys)** du portail Azure pour votre propre compte. Pour le compte de démonstration, la clé est `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Entrez la clé appropriée, puis cliquez sur **Connecter**.
   
    nous vous recommandons d’utiliser la clé en lecture seule lorsque vous créez des rapports, ce afin de ne pas exposer inutilement la clé principale à des risques de sécurité potentiels.  ce afin de ne pas exposer inutilement la clé principale à des risques de sécurité potentiels. La clé en lecture seule est disponible à partir du panneau [Clés](manage-account.md#keys) du portail Azure. Sinon, vous pouvez utiliser les informations du compte de démonstration fournies ci-dessus.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Clé du compte](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Si vous obtenez une erreur indiquant « La base de données spécifiée est introuvable. » consultez les étapes de la solution de contournement dans ce [problème Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Une fois le compte connecté, le **Navigateur** s’affiche.  Le **Navigateur** affiche une liste des bases de données attachées au compte.
10. Sélectionnez puis développez la base de données d’où proviennent les données du rapport. Si vous utilisez le compte de démonstration, sélectionnez **volcanodb**.   
11. À présent, sélectionnez une collection d’où vous allez récupérer les données. Si vous utilisez le compte de démonstration, sélectionnez **volcano1**.
    
    le volet d’aperçu affiche une liste des éléments d’ **enregistrement** .  Dans Power BI, un Document est représenté sous la forme d’un type d’ **enregistrement** . De même, un bloc JSON imbriqué à l’intérieur d’un document est également considéré comme un **enregistrement**.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Fenêtre de Navigateur](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Cliquez sur **Modifier** pour lancer l’éditeur de requête dans une nouvelle fenêtre et transformer les données.

## <a name="flattening-and-transforming-json-documents"></a>Mise à plat et transformation de documents JSON
1. Basculez dans la fenêtre de l’éditeur de requête de Power BI, où une colonne **Document** s’affiche dans le volet central.
   ![Power BI Desktop - Éditeur de requête](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Cliquez sur l’icône de développement à droite de l’en-tête de colonne **Document** .  Un menu contextuel s’affiche avec une liste de champs.  Sélectionnez les champs dont vous avez besoin pour votre rapport (par exemple, nom du volcan, pays, région, emplacement, altitude, type, état et dernière éruption connue), puis cliquez sur **OK**.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Développement des documents](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Le volet central affiche un aperçu du résultat avec les champs sélectionnés.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Résultats aplatis](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Dans notre exemple, la propriété Emplacement est un bloc GeoJSON contenu dans un document.  Comme vous pouvez le constater, l’emplacement est représenté en tant que type d’ **enregistrement** dans Power BI Desktop.  
5. Cliquez sur l’icône de développement à droite de l’en-tête de colonne Emplacement.  Un menu contextuel s’affiche avec les champs « type » et « coordonnées ».  Sélectionnez le champ de coordonnées et cliquez sur **OK**.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Enregistrement emplacement](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Le volet central affiche maintenant une colonne de coordonnées de type **Liste** .  Comme indiqué au début de ce didacticiel, les données GeoJSON utilisées dans ce didacticiel sont de type « Point », avec des valeurs de latitude et longitude enregistrées dans le tableau de coordonnées.
   
    L’élément coordinates[0] représente la longitude et l’élément coordinates[1] la latitude.
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Liste des coordonnées](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Pour mettre à plat le tableau de coordonnées, nous allons créer une **colonne personnalisée** appelée LatLong.  Sélectionnez le ruban **Add column** (Ajouter une colonne), puis cliquez sur **Add Custom Column** (Ajouter une colonne personnalisée).  La fenêtre **Add Custom Column** (Ajouter une colonne personnalisée) s’affiche.
8. Indiquez un nom pour la nouvelle colonne, par exemple LatLong.
9. Spécifiez ensuite la formule personnalisée à appliquer à la nouvelle colonne.  Dans notre exemple, nous allons concaténer les valeurs de latitude et longitude séparées par une virgule, comme indiqué ci-dessous, à l’aide de la formule suivante : `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Cliquez sur **OK**.
   
    Pour plus d’informations sur le langage DAX (Data Analysis Expressions) et notamment sur les fonctions DAX, consultez la page [DAX Basic in Power BI Desktop (Dax de base dans Power BI Desktop)](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Ajout d’une colonne personnalisée](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Le volet central affiche maintenant la nouvelle colonne LatLong contenant les valeurs de latitude et de longitude, séparées par une virgule.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Colonne personnalisée LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Si vous recevez une erreur dans la nouvelle colonne, assurez-vous que les étapes appliquées sous les Paramètres de requête correspondent à la figure suivante :
    
    ![Les étapes appliquées doivent être Source, Navigation, Expanded Document, Expanded Document.Location, Added Custom](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Si vos étapes sont différentes, supprimez les étapes supplémentaires et essayez d’ajouter de nouveau la colonne personnalisée. 
11. Nous avons terminé de convertir les données au format tabulaire.  Vous pouvez tirer parti de toutes les fonctionnalités disponibles dans l’éditeur de requête pour modeler et transformer des données dans Cosmos DB.  Si vous utilisez l’exemple, modifiez le type de données pour le champ Altitude en **Nombre entier**, en modifiant le **Type de données** sur le ruban **Accueil**.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Modification du type de colonne](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Cliquez sur **Fermer et appliquer** pour enregistrer le modèle de données.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Fermer et appliquer](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Création de rapports
La vue Rapport de Power BI Desktop vous permet de créer des rapports pour visualiser des données.  Pour créer des rapports, vous pouvez simplement faire glisser des champs et les déposer dans la zone de dessin **Rapport** .

![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Dans la vue Rapport, vous devriez trouver les éléments suivants :

1. Le volet **Champs** , qui contient une liste de modèles de données avec des champs que vous pouvez utiliser pour vos rapports.
2. Le volet **Visualisations** . Un rapport peut contenir une ou plusieurs visualisations.  Dans le volet **Visualisations** , sélectionnez les types visuels adaptés à vos besoins.
3. La zone de dessin **Rapport** , où vous allez créer les éléments visuels de votre rapport.
4. La page **Rapport** . Vous pouvez ajouter plusieurs pages de rapport dans Power BI Desktop.

Vous trouverez ci-dessous les principales étapes de création d’un rapport de vue cartographique interactif simple.

1. Dans notre exemple, nous allons créer une vue cartographique indiquant l’emplacement de chaque volcan.  Dans le volet **Visualisations** , cliquez sur le type d’élément visuel « carte », comme illustré dans la capture d’écran ci-dessus.  Le type d’élément visuel « carte » devrait s’afficher dans la zone de dessin **Rapport** .  Le volet **Visualisations** doit également afficher un ensemble de propriétés associées au type d’élément visuel « carte ».
2. Maintenant, faites glissez et déplacez le champ LatLong du volet **Champs** vers la propriété **Emplacement** du volet **Visualisations**.
3. Faites glissez le champ Nom du volcan vers la propriété **Légende** .  
4. Ensuite, faites glisser le champ Altitude sur la propriété **Taille** .  
5. Vous devriez maintenant voir une carte contenant un ensemble de bulles qui indiquent l’emplacement de chaque volcan, la taille de la bulle étant proportionnelle à l’altitude des volcans.
6. Vous avez maintenant créé un rapport de base.  Vous pouvez continuer à personnaliser votre rapport en ajoutant davantage de visualisations.  Dans notre cas, nous avons ajouté un segment Type de volcan pour obtenir un rapport interactif.  
   
    ![Capture d’écran du rapport final Power BI Desktop après avoir terminé le didacticiel Power BI pour Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publier et partager votre rapport
Pour partager votre rapport, vous devez disposer d’un compte dans PowerBI.com.

1. Dans Power BI Desktop, cliquez sur le ruban **Accueil** .
2. Cliquez sur **Publier**.  Vous devrez entrer le nom d’utilisateur et le mot de passe associés à votre compte PowerBI.com.
3. Une fois les informations d’identification authentifiées, le rapport est publié sur la destination sélectionnée.
4. Cliquez sur **Open 'PowerBITutorial.pbix' in Power BI (Ouvrir PowerBITutorial.pbix dans Power BI)** pour consulter et partager votre rapport sur PowerBI.com.
   
    ![Publication réussie sur Power BI ! Ouvrir le didacticiel dans Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Créer un tableau de bord dans PowerBI.com
Maintenant que vous disposez d’un rapport, partageons-le dans PowerBI.com.

Lorsque vous publiez votre rapport à partir de Power BI Desktop sur PowerBI.com, des éléments **Rapport** et **Jeu de données** sont générés dans votre client PowerBI.com. Par exemple, après la publication d’un rapport nommé **PowerBITutorial** sur PowerBI.com, un élément PowerBITutorial s’affiche dans les sections **Rapports** et **Jeux de données** de PowerBI.com.

   ![Capture d’écran des nouveaux rapport et jeu de données dans PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Pour créer un tableau de bord partageable, cliquez sur le bouton **Épingler une page dynamique** de votre rapport PowerBI.com.

   ![Capture d’écran des nouveau rapport et jeu de données dans PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Ensuite, suivez les instructions de [Pin a tile from a report (Épingler une vignette d’un rapport)](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) afin de créer un tableau de bord. 

Vous pouvez également apporter des modifications ad hoc au rapport avant de créer un tableau de bord. Toutefois, il est recommandé d’utiliser Power BI Desktop pour effectuer les modifications et republier le rapport sur PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Actualiser les données dans PowerBI.com
Il existe deux manières d’actualiser les données, le modèle ad hoc et la planification.

Pour une actualisation ad hoc, cliquez sur les ellipses (…) en regard du **Jeu de données**, par exemple PowerBITutorial. Une liste d’actions doit s’afficher, comprenant notamment **Actualiser maintenant**. Cliquez sur **Actualiser maintenant** pour actualiser les données.

![Capture d’écran de la fenêtre Actualiser maintenant dans PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

Pour procéder à une actualisation planifiée, procédez comme suit.

1. Cliquez sur **Planifier l’actualisation** dans la liste d’actions. 

    ![Capture d’écran de la fenêtre Planifier l’actualisation de PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. Sur la page **Paramètres**, développez **Informations d’identification de la source de données**. 
3. Cliquez sur **Modifier les informations d’identification**. 
   
    La fenêtre contextuelle de configuration apparaît. 
4. Entrez la clé de connexion au compte Cosmos DB pour ce jeu de données, puis cliquez sur **Se connecter**. 
5. Développez **Planifier l’actualisation** , puis définissez le calendrier que vous souhaitez actualiser pour le jeu de données. 
6. Cliquez sur **Appliquer** ; vous avez terminé la configuration de l’actualisation planifiée.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur Power BI, consultez la section [Get started with Power BI (Prise en main de Power BI)](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Pour en savoir plus sur Cosmos DB, consultez la [page d’accueil de la documentation relative à Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

