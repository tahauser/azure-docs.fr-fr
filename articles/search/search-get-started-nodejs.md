---
title: "Prise en main d’Azure Search dans Node.js | Microsoft Docs"
description: "Guide de création d’une application de recherche sur un service de recherche Azure hébergé dans le cloud en utilisant le langage de programmation Node.js."
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 0625dc1b-9db6-40d5-ba9a-4738b75cbe19
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: evboyle
ms.openlocfilehash: 511f3327972672deaa321fb34bc5f91d355370a2
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Prise en main d’Azure Search dans Node.js
> [!div class="op_single_selector"]
> * [Portail](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Apprenez à créer une application de recherche Node.js personnalisée, qui utilise Azure Search pour ses fonctionnalités de recherche. Ce didacticiel utilise l’ [API REST du service Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) pour créer les objets et opérations utilisés dans cet exercice.

Nous avons utilisé [Node.js](https://Nodejs.org) et NPM, [Sublime Text 3](http://www.sublimetext.com/3) et Windows PowerShell sur Windows 8.1 pour développer et tester ce code.

Pour exécuter cet exemple, vous devez disposer d’un service Azure Search auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com). Consultez [Création d’un service Azure Search dans le portail](search-create-service-portal.md) pour obtenir des instructions pas-à-pas.

## <a name="about-the-data"></a>À propos des données
Cet exemple d'application utilise des données de l’ [USGS (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments importants, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **DataIndexer** crée et charge l'index à l'aide d'une construction de type [Index](https://msdn.microsoft.com/library/azure/dn798918.aspx) , en récupérant le jeu de données USGS filtré à partir d'une base de données SQL Azure publique. Les informations d'identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Aucune configuration supplémentaire n'est nécessaire.

> [!NOTE]
> Nous avons appliqué un filtre à ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas. Pour plus d’informations sur la capacité de chaque niveau de tarification, consultez la page [Limites de service d’Azure Search](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Rechercher le nom et la clé API de votre service Azure Search
Une fois le service créé, revenez au portail pour obtenir l'URL ou `api-key`. Pour vous connecter à votre service de recherche, vous devez saisir l'URL et une `api-key` afin d’authentifier l'appel.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre d’index, cliquez sur **Service de recherche** pour obtenir la liste des services Azure Search approvisionnés pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l’icône de clé permettant d’accéder aux clés administrateur.
5. Copiez l'URL du service, une clé d’administration et une clé de requête. Vous en aurez besoin plus tard, pour les ajouter au fichier config.js.

## <a name="download-the-sample-files"></a>Télécharger les fichiers exemples
Utilisez l'une des approches suivantes pour télécharger l'exemple.

1. Accédez à [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Cliquez sur **Download ZIP**, enregistrez le fichier ZIP sur le disque, puis extrayez tous les fichiers qu'il contient.

Toutes les modifications et instructions d’exécution ultérieures seront effectuées sur les fichiers de ce dossier.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Mettre à jour le fichier config.js. avec l’URL et la clé API du service de recherche
À l'aide de l'URL et des clés API que vous avez copiées précédemment, spécifiez l'URL, la clé d’administration et la clé de requête dans le fichier de configuration.

Les clés d’administration octroient un contrôle total sur les opérations du service, notamment la création ou la suppression d'un index et le chargement de documents. En revanche, les clés de requête sont réservées aux opérations en lecture seule, généralement utilisées par les applications clientes qui se connectent à Azure Search.

Dans cet exemple, nous incluons la clé de requête pour renforcer la bonne pratique consistant à utiliser la clé de requête dans les applications clientes.

La capture d'écran suivante montre le fichier **config.js** ouvert dans un éditeur de texte, avec les entrées appropriées encadrées en rouge pour que vous puissiez voir où mettre à jour le fichier avec les valeurs correspondant à votre service de recherche.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Héberger un environnement d’exécution pour l’exemple
Cet exemple nécessite un serveur HTTP, que vous pouvez installer globalement à l'aide de npm.

Exécutez les commandes suivantes dans une fenêtre PowerShell :

1. Accédez au dossier qui contient le fichier **package.json** .
2. Saisissez `npm install`.
3. Saisissez `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Générer l’index et exécuter l'application
1. Saisissez `npm run indexDocuments`.
2. Saisissez `npm run build`.
3. Saisissez `npm run start_server`.
4. Dans votre navigateur, accédez à `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Exécuter une recherche sur les données USGS
Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous cliquez sur **Recherche** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer le nom d’une figure locale. « Roger Williams » fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![][9]

Vous pouvez également essayer les termes suivants :

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Étapes suivantes
Ceci est le premier didacticiel Azure Search basé sur Node.js et le jeu de données USGS. Au fil du temps, nous le compléterons avec des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous connaissez déjà Azure Search, vous pouvez utiliser cet exemple comme tremplin pour tester des générateurs de suggestions (requêtes prédictives ou à saisie semi-automatique), des filtres et la navigation à facettes. Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) . Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](search-video-demo-tutorial-list.md) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
