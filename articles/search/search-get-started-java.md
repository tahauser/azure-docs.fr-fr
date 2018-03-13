---
title: "Prise en main d’Azure Search dans Java | Microsoft Docs"
description: "Comment créer une application de recherche hébergée dans le cloud sur Azure en utilisant le langage de programmation Java."
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 8b4df3c9-3ae5-4e3a-b4bb-74b516a91c8e
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 07/14/2016
ms.author: evboyle
ms.openlocfilehash: 8b253ef1eccfbc6ca703657b124d17d1482c2eed
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-search-in-java"></a>Prise en main d'Azure Search dans Java
> [!div class="op_single_selector"]
> * [Portail](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Apprenez à créer une application Java personnalisée, qui utilise Azure Search pour ses fonctionnalités de recherche. Ce didacticiel utilise l’ [API REST du service Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) pour créer les objets et opérations utilisés dans cet exercice.

Pour exécuter cet exemple, vous devez disposer d’un service Azure Search auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com). Consultez [Création d’un service Azure Search dans le portail](search-create-service-portal.md) pour obtenir des instructions pas-à-pas.

Nous avons utilisé les logiciels suivants pour générer et tester cet exemple :

* [Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Veillez à télécharger la version EE. Une des étapes de vérification nécessite une fonctionnalité présente uniquement dans cette édition.
* [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>À propos des données
Cet exemple d'application utilise des données de l’ [USGS (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments importants, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **SearchServlet.java** crée et charge l'index à l'aide d'une construction de type [Index](https://msdn.microsoft.com/library/azure/dn798918.aspx) , en récupérant le jeu de données USGS filtré à partir d’une base de données SQL Azure publique. Les informations d’identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Pour accéder aux données, aucune configuration supplémentaire n'est nécessaire.

> [!NOTE]
> Nous avons filtré ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas et vous pouvez modifier ce code pour utiliser un jeu de données plus important. Pour plus d'informations sur la capacité de chaque niveau de tarification, consultez la section [Limites et contraintes](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>À propos des fichiers de programme
La liste suivante décrit les fichiers qui sont pertinents pour cet exemple.

* Search.jsp : fournit l'interface utilisateur.
* SearchServlet.java : fournit des méthodes (semblables à un contrôleur dans MVC).
* SearchServiceClient.java : gère les descripteurs HTTP.
* SearchServiceHelper.java : classe d’utilitaire qui fournit des méthodes statiques.
* Document.Java : fournit le modèle de données.
* config.properties : définit la clé API et l'URL du service de recherche.
* Pom.XML : dépendance Maven.

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Rechercher le nom et la clé API de votre service Azure Search
Tous les appels d’API REST dans Azure Search exigent que vous fournissiez l’URL du service et une clé d’api. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre d’index, cliquez sur **Service de recherche** pour obtenir la liste des services Azure Search approvisionnés pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.
   
      ![][3]
5. Copiez l'URL du service et une clé d’administration. Vous en aurez besoin plus tard, pour les ajouter au fichier **config.properties** .

## <a name="download-the-sample-files"></a>Télécharger les fichiers exemples
1. Accédez à [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) sur GitHub.
2. Cliquez sur **Download ZIP**, enregistrez le fichier ZIP sur le disque, puis extrayez tous les fichiers qu'il contient. Si vous le souhaitez, vous pouvez extraire ces fichiers dans votre espace de travail Java pour faciliter la recherche du projet ultérieurement.
3. Les fichiers exemples sont en lecture seule. Avec le bouton droit de la souris, cliquez sur le dossier et désactivez l'attribut de lecture seule.

Toutes les modifications et instructions d'exécution ultérieures seront effectuées sur les fichiers de ce dossier.  

## <a name="import-project"></a>Importer le projet
1. Dans Eclipse, choisissez **File** > **Import** > **General** > **Existing Projects into Workspace**.
   
    ![][4]
2. Dans **Select root directory**, accédez au dossier contenant les fichiers exemples. Sélectionnez le dossier qui contient le dossier .project. Le projet doit s'afficher sélectionné dans la liste **Projects** .
   
    ![][12]
3. Cliquez sur **Terminer**.
4. Utilisez **Project Explorer** pour afficher et modifier les fichiers. Si l’explorateur n’est pas ouvert, cliquez sur **Window** > **Show View** > **Project Explorer** ou utilisez le raccourci.

## <a name="configure-the-service-url-and-api-key"></a>Configurer l'URL et la clé API du service
1. Dans **Project Explorer**, double-cliquez sur **config.properties** pour modifier les paramètres de configuration qui contient le nom du serveur et la clé API.
2. Reportez-vous aux étapes précédentes de cet article. Vous y trouverez l’URL de service et la clé d’API du service dans le [portail Azure](https://portal.azure.com) afin d’obtenir les valeurs que vous allez saisir dans **config.properties**.
3. Dans **config.properties**, remplacez « Api Key » par la clé API de votre service. Ensuite, le nom du service (le premier composant de l’URL http://servicename.search.windows.net) remplace « ServiceName » dans le même fichier.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configuration des environnements de projet, de génération et d'exécution
1. Dans Project Explorer dans Eclipse, cliquez avec le bouton droit sur le projet, puis cliquez sur **Properties** > **Project Facets**.
2. Sélectionnez **Dynamic Web Module**, **Java** et **JavaScript**.
   
    ![][6]
3. Cliquez sur **Appliquer**.
4. Sélectionnez **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add..**.
5. Développez Apache et sélectionnez la version du serveur Apache Tomcat que vous avez précédemment installée. Sur notre système, nous avons installé la version 8.
   
    ![][7]
6. Dans la page suivante, spécifiez le répertoire d'installation de Tomcat. Sur un ordinateur Windows, il s’agit très probablement du répertoire C:\Program Files\Apache Software Foundation\Tomcat *version*.
7. Cliquez sur **Terminer**.
8. Sélectionnez **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**.
9. Dans **Add JRE**, sélectionnez **Standard VM**.
10. Cliquez sur **Suivant**.
11. Dans JRE Definition de la page d’accueil de JRE, cliquez sur **Directory**.
12. Accédez à **Program Files** > **Java** et sélectionnez le JDK que vous avez installé précédemment. Il est important de sélectionner le JDK comme JRE.
13. Dans Installed JREs, sélectionnez le **JDK**. Vos paramètres doivent être similaires au contenu de la capture d'écran suivante.
    
    ![][9]
14. Si vous le souhaitez, sélectionnez **Window** > **Web Browser** > **Internet Explorer** pour ouvrir l’application dans un navigateur externe. Un navigateur externe vous donne une meilleure expérience de l’application Web.
    
    ![][8]

La phase de configuration est maintenant terminée. Vous allez maintenant créer et exécuter le projet.

## <a name="build-the-project"></a>Créer le projet
1. Dans Project Explorer, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Run As** > **Maven build...** pour configurer le projet.
   
    ![][10]
2. Dans le champ Goals de Edit Configuration, saisissez « clean install », puis cliquez sur **Run**.

Des messages d'état s’affichent dans la fenêtre de la console. Le message BUILD SUCCESS indique que le projet a été généré sans erreur.

## <a name="run-the-app"></a>Exécution de l'application
Dans cette dernière étape, vous allez exécuter l'application dans un environnement d'exécution de serveur local.

Si ce n’est déjà fait, vous devez spécifier un environnement d'exécution de serveur dans Eclipse.

1. Dans Project Explorer, développez **WebContent**.
2. Cliquez avec le bouton droit sur **Search.jsp** > **Run As** > **Run on Server**. Sélectionnez le serveur Apache Tomcat, puis cliquez sur **Run**.

> [!TIP]
> Si vous avez stocké votre projet dans un espace de travail personnalisé, vous devrez désigner cet emplacement dans **Run Configuration** pour éviter une erreur lors du démarrage du serveur. Dans Project Explorer, cliquez avec le bouton droit sur **Search.jsp** > **Run As** > **Run Configurations**. Sélectionnez le serveur Apache Tomcat. Cliquez sur **Arguments**. Cliquez sur **Workspace** ou **File System** pour définir le dossier contenant le projet.
> 
> 

Lorsque vous exécutez l'application, une fenêtre de navigateur affiche un champ de recherche  permettant d'entrer des termes.

Attendez environ une minute avant de cliquer sur **Search** , le temps que le service crée et charge l'index. Si vous obtenez une erreur HTTP 404, Patientez encore un peu avant de réessayer.

## <a name="search-on-usgs-data"></a>Exécution d'une recherche sur les données USGS
Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous cliquez sur **Search** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer le nom d’une figure locale. « Roger Williams » fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![][11]

Vous pouvez également essayer les termes suivants :

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Étapes suivantes
Ceci est le premier didacticiel Azure Search basé sur Java et le jeu de données USGS. Au fil du temps, nous le compléterons avec des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous avez déjà une certaine maîtrise d’Azure Search, vous pouvez utiliser cet exemple comme un tremplin pour d’autres expérimentations, notamment pour améliorer la [page de recherche](search-pagination-page-layout.md) ou mettre en place la [navigation par facettes](search-faceted-navigation.md). Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) . Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](search-video-demo-tutorial-list.md) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
