---
title: "Nouveautés d’Azure Data Catalog | Microsoft Docs"
description: "Cet article offre une vue d’ensemble des nouvelles fonctionnalités ajoutées à Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 9fb7814a8412200f6d31cfb9dcaee4663d7cea97
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="whats-new-in-azure-data-catalog"></a>Nouveautés d'Azure Data Catalog
Des mises à jour **d’Azure Data Catalog** sont publiées régulièrement. Comme certaines versions portent sur les fonctionnalités du service principal, toutes les versions ne contiennent pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Cette page présente les nouvelles fonctionnalités orientées utilisateur ajoutées au service Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Nouveautés de novembre 2017 
Depuis novembre 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de la liaison directement aux termes du glossaire métier spécifique dans le portail Data Catalog. Les utilisateurs peuvent copier des liens à partir du glossaire métier et les incorporer dans des documents, des e-mails, des rapports ou d'autres emplacements à lier directement à la définition du terme de glossaire.
* Prise en charge des principaux du service Azure Active Directory. Les administrateurs de Data Catalog peuvent utiliser des principaux de service pour autoriser les applications clientes à accéder au catalogue, et accorder des autorisations spécifiques à ces applications, comme ils le feraient pour des utilisateurs et des groupes de sécurité. Pour plus d'informations, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Prise en charge de l’authentification Azure Active Directory lors de la connexion aux sources de données Azure SQL Database et Azure SQL Data Warehouse à l’aide de l’outil d'inscription des sources de données Data Catalog. Pour plus d’informations, consultez [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou de SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Nouveautés de septembre 2017 
À compter de septembre 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de l’extraction de métadonnées de relation de jointure à partir de sources de données DB2 durant l’inscription des tables associées à l’aide de l’outil d’inscription des sources de données.
* Prise en charge de l’inscription des sources de données MongoDB version 3.4 à l’aide de l’outil d’inscription des source de données.
* Prise en charge de la suppression de toutes les métadonnées pour les objets contenus dans une seule opération durant la suppression d’une base de données ou d’un autre conteneur de Data Catalog.
* Prise en charge de l’affichage de 1 000 balises au maximum, des termes de glossaire métier, ou d’autres facettes de recherche, quand vous affinez une recherche dans le portail Data Catalog.


## <a name="whats-new-for-august-2017"></a>Nouveautés d’août 2017 
Depuis août 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

*   Un nouvel échantillon de développeur est disponible pour la création et la gestion de métadonnées de relation à l’aide de l’API REST de Data Catalog. L’échantillon d’*importation des informations de relation dans Data Catalog* est disponible sur la [page d’exemples de code Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Prise en charge de l’extraction des métadonnées de relation jointes depuis des sources de données Teradata lors de l’inscription des tables associées à l’aide de l’outil d’inscription des sources de données.
* Prise en charge des objets de fonction table SQL Server (TVF) lors de l’inscription des sources de données SQL Server à l’aide de l’outil d’inscription des sources de données.
* Diverses mises à jour et améliorations permettant d’améliorer les performances et la convivialité du portail Data Catalog.

## <a name="whats-new-for-july-2017"></a>Nouveautés de juillet 2017 
Depuis juillet 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Prise en charge d’un contrôle plus précis des opérations de métadonnées autorisées, notamment :
    - Les administrateurs du catalogue peuvent limiter la capacité des utilisateurs à insérer des balises et métadonnées associées dans ce dernier, en leur permettant uniquement d’y accéder en lecture seule.
    - Les administrateurs du catalogue peuvent limiter la capacité des utilisateurs à inscrire des sources de données dans le catalogue.
    - Les administrateurs du catalogue peuvent limiter la capacité des utilisateurs à prendre possession des métadonnées des ressources de données dans le catalogue.
    - Les autorisations peuvent être accordées aux utilisateurs et groupes de sécurité Azure Active Directory dans le but de faciliter la gestion des autorisations.
* Prise en charge des relations entre les ressources de données inscrites et la détection de ressources de données associées dans le portail Data Catalog, notamment :
    - Extraction de métadonnées de relation depuis SQL Server (y compris Azure SQL Database), Oracle et les sources de données MySQL lors de l’utilisation de l’outil d’inscription des sources de données Data Catalog.
    - Détection des ressources de données associées lors de l’affichage des métadonnées des ressources dans le portail Data Catalog.
    - Opérations pour définir, détecter et gérer les relations entre les ressources de données à l’aide de l’API REST de Data Catalog.

Pour plus d’informations sur la gestion des autorisations dans Data Catalog, consultez [Guide pratique pour sécuriser l’accès à un catalogue de données et des ressources de données](data-catalog-how-to-secure-catalog.md).
Pour plus d’informations sur les relations dans Data Catalog, consultez [Comment afficher les ressources de données associées dans Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Nouveautés de juin 2017 
Depuis juin 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Prise en charge des sources de données Sybase, Apache Cassandra et MongoDB. Les utilisateurs peuvent désormais inscrire et découvrir les bases de données et tables Cassandra et MongoDB, ainsi que les bases de données, tables et vues Sybase.

> [!NOTE]
> Lorsque vous inscrivez les tables MongoDB et Cassandra qui incluent des colonnes avec des types de données complexes tels que des enregistrements et des tableaux, ces colonnes ne sont pas inclues dans les métadonnées ajoutées à Data Catalog.

## <a name="whats-new-for-may-2017"></a>Nouveautés de mai 2017 
Depuis mai 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Un nouvel échantillon de développeur est disponible pour l’importation en bloc de termes de glossaire métier. L’échantillon d’importation en bloc du glossaire est disponible sur la [page d’échantillons du développeur de Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Prise en charge de la modification des informations de connexion ODBC dans le portail Azure Data Catalog. Les propriétaires de la ressource de données et les administrateurs de Data Catalog peuvent désormais modifier les informations de connexion par des sources de données ODBC sans avoir à réinscrire les sources de données.
*   Prise en charge des URL interactives dans les définitions et les descriptions du glossaire métier. Lorsque les URL sont inclues dans les propriétés de description et de définition de termes de glossaire métier, elles sont affichées en tant que liens hypertextes dans le portail Data Catalog.
*   Prise en charge de l’affichage des noms d’experts en plus des adresses e-mail d’experts. Lorsque vous affichez des experts dans les propriétés d’une ressource de données dans le portail Data Catalog, le nom complet de l’expert Azure Active Directory s’affiche dans l’info-bulle.

## <a name="whats-new-for-april-2017"></a>Nouveautés d’avril 2017 
Depuis avril 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Prise en charge des sources de données ODBC. Les utilisateurs peuvent désormais inscrire et découvrir les bases de données, tables et vues ODBC à l’aide de l’outil d’enregistrement des sources de données Data Catalog.

## <a name="whats-new-for-march-2017"></a>Nouveautés de mars 2017 
Depuis mars 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Prise en charge de l’utilisation des groupes de sécurité AAD pour les administrateurs de Data Catalog.
*   Azure Data Catalog est maintenant disponible dans une nouvelle région Azure. Les clients peuvent approvisionner Azure Data Catalog dans la région centre ouest des États-Unis, en plus de l’est des États-Unis, l’ouest des États-Unis, l’Europe de l’ouest et l’est de l’Australie, l’Europe du nord et l’Asie du sud-est. Pour plus d’informations, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Nouveautés de février 2017 
Depuis février 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Prise en charge des paramètres avancés dans l’outil d’enregistrement des sources de données Data Catalog. Les utilisateurs peuvent spécifier des valeurs de délai d’expiration des commandes lors de l’inscription de sources de données volumineuses.
*   Prise en charge des sources de données FTP et PostgreSQL. Les utilisateurs peuvent désormais inscrire et découvrir des fichiers et des répertoires FT, ainsi que les bases de données, tables et vues PostgreSQL.

## <a name="whats-new-for-january-2017"></a>Nouveautés de janvier 2017 
Depuis janvier 2017, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Azure Data Catalog est désormais conforme à [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification).
*   Intégration à [Obtenir et transformer dans Excel 2016 et Power Query pour Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Les utilisateurs Excel peuvent partager et détecter des requêtes à l’aide d’Azure Data Catalog à partir d’Excel. Cette fonctionnalité est disponible pour les utilisateurs ayant des licences Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Nouveautés de décembre 2016
Depuis décembre 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Azure Data Catalog est désormais conforme à [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) et aux [Clauses modèles de l’UE](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses).
*   Prise en charge de la modification des informations de connexion des sources de données. Les propriétaires de la ressource de données et les administrateurs de Data Catalog peuvent désormais modifier les informations de connexion par des sources de données enregistrées sans avoir à réinscrire les sources de données.
*   Prise en charge des sources de données Salesforce.com. Les utilisateurs peuvent désormais inscrire et découvrir les objets Salesforce.


## <a name="whats-new-for-november-2016"></a>Nouveautés de novembre 2016
Depuis novembre 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :
*   Azure Data Catalog est désormais conforme aux normes [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) et [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018).
*   Prise en charge de l’inscription manuelle des sources de données ODBC à l’aide du portail Data Catalog et de l’API REST.

## <a name="whats-new-for-september-2016"></a>Nouveautés de septembre 2016
Depuis septembre 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge des sources de données IBM DB2. Les utilisateurs peuvent désormais inscrire et découvrir les bases de données, tables et vues DB2.
* Prise en charge des sources de données Azure Cosmos DB. Les utilisateurs peuvent désormais inscrire et découvrir les bases de données et collections Cosmos DB.
* Prise en charge de la personnalisation du nom du catalogue dans le portail Data Catalog. Les administrateurs de Catalog peuvent désormais saisir du texte qui s’affiche dans le titre du portail (nom de l’organisation, par exemple).

## <a name="whats-new-for-august-2016"></a>Nouveautés d’août 2016
Depuis août 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Améliorations de l’inscription des sources de données SQL Server Master Data Services (MDS). Les utilisateurs peuvent désormais inclure des aperçus et des profils de données lors de l’inscription des entités MDS à l’aide de l’outil d’inscription des sources de données Data Catalog.
* Prise en charge des recherches enregistrées de l’organisation définies par l’administrateur. Lorsqu’ils enregistrent une recherche dans le portail Data Catalog, les administrateurs Data Catalog peuvent désormais choisir d’enregistrer les recherches pour leur usage personnel ou pour tous les utilisateurs du catalogue. Les recherches enregistrées de l’organisation sont partagées avec tous les utilisateurs du catalogue et peuvent servir de points de départ standardisés pour la détection des sources de données.
* Mises à jour de l’affichage des propriétés dans le portail Data Catalog. Toutes les propriétés des ressources de données sont désormais affichées et gérées dans un volet unique et redimensionnable pour offrir une expérience plus cohérente et accessible.

## <a name="whats-new-for-july-2016"></a>Nouveautés de juillet 2016
Depuis juillet 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge des sources de données SQL Server Master Data Services (MDS). Les utilisateurs peuvent désormais inscrire et découvrir les entités et les modèles MDS.
* Prise en charge des procédures SQL Server stockées. Les utilisateurs peuvent désormais inscrire et découvrir les objets des procédures stockées dans les sources de données SQL Server.
* Prise en charge de langues supplémentaires dans le portail Azure Data Catalog et l’outil d’inscription des sources de données, soit un total de 18 langues prises en charge. L’expérience utilisateur Azure Data Catalog est localisée en fonction des préférences de langue définies dans Windows ou dans le navigateur web.
* Mise à jour et optimisation de la page d’accueil du portail Data Catalog, y compris améliorations des performances et rationalisation de l’expérience utilisateur.

## <a name="whats-new-for-june-2016"></a>Nouveautés de juin 2016
Depuis juin 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge du redimensionnement des colonnes dans l’affichage de liste lors de la découverte des ressources de données dans le portail Data Catalog. Les utilisateurs peuvent désormais redimensionner les colonnes pour lire plus facilement les métadonnées des ressources longues telles que les balises et les descriptions.
* Power Query pour Excel a été ajouté au menu « Ouvrir dans » dans le portail Data Catalog. Les utilisateurs peuvent désormais ouvrir les sources de données prises en charge dans Excel 2016 ou dans Excel 2010 et Excel 2013 avec le complément [Power Query pour Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) installé.
* Prise en charge des sources de données de stockage de tables Azure. Les utilisateurs peuvent désormais inscrire et découvrir des objets de table dans les sources de données Stockage Azure.

## <a name="whats-new-for-may-2016"></a>Nouveautés de mai 2016
Depuis mai 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Un glossaire métier qui permet aux administrateurs de catalogue de définir des termes métier et des hiérarchies pour créer un vocabulaire métier commun. Les utilisateurs peuvent baliser les ressources de données inscrites avec des termes de glossaire de façon à faciliter la découverte et la compréhension du contenu du catalogue. Pour plus d’informations, consultez l’article [Comment configurer le glossaire métier pour un balisage géré](data-catalog-how-to-business-glossary.md)  
* Améliorations du glossaire métier Data Catalog qui permet de mettre à jour plusieurs termes du glossaire en une seule opération. Les utilisateurs peuvent sélectionner plusieurs termes pour modifier les champs suivants :
  * Terme parent : l’utilisateur peut sélectionner un nouveau terme parent, et tous les termes sélectionnés sont mis à jour pour devenir les enfants du terme parent sélectionné. Si les termes sélectionnés ont tous le même parent, alors ce dernier s’affichera dans la zone de texte. Sinon, le champ parent du terme est vide.   
  * Balises et experts : les utilisateurs peuvent ajouter et supprimer des balises et des experts pour plusieurs termes du glossaire, en utilisant la même méthode que pour baliser plusieurs ressources de données.

> [!NOTE]
> Le glossaire métier est disponible uniquement dans l’édition Standard d’Azure Data Catalog. L’édition gratuite ne propose pas de fonctionnalités de balisage géré ni de glossaire métier.

## <a name="whats-new-for-march-2016"></a>Nouveautés de mars 2016
Depuis mars 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Un point de terminaison d’API REST consolidé pour accéder par programmation aux fonctionnalités de recherche et de gestion des ressources de catalogue du service Azure Data Catalog. Ce point de terminaison d’API de recherche et ce point de terminaison d’API de catalogue étaient déconseillés et ne sont plus disponibles depuis le 21 mars 2016. Aucune modification n’est apportée à la sémantique de l’API. Seul l’URI du point de terminaison a été modifié. Pour plus d’informations, consultez [Informations de référence sur l’API REST Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267595.aspx). Pour des exemples d’API, consultez [Exemples de développement Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Nouveautés de février 2016
Depuis février 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Une nouvelle expérience de sélection de sources de données dans l’outil d’inscription de sources de données Azure Data Catalog. L’outil d’inscription de sources de données a été mis à jour pour faciliter la localisation et la sélection à partir des sources de données prises en charge par Azure Data Catalog.
* Prise en charge de 10 langues supplémentaires sur le Portail Azure Data Catalog et l’outil d’inscription de sources de données. Outre l’anglais, l’expérience Azure Data Catalog est désormais disponible en allemand, chinois simplifié, chinois traditionnel, coréen, espagnol, français, italien, japonais, portugais (Brésil) et russe. L’expérience utilisateur Azure Data Catalog est localisée en fonction des préférences de langue définies dans Windows ou dans le navigateur web de l’utilisateur.
* Prise en charge de la géo-réplication des données Azure Data Catalog pour garantir la continuité de l’activité et la récupération d’urgence. Tous les contenus Azure Data Catalog, notamment les métadonnées de source de données et les annotations de crowdsourcing, sont désormais répliqués entre deux régions Azure sans frais supplémentaires pour les clients. Les régions Azure sont préalablement jumelées et distantes d’au moins 800 kilomètres, et suivent le mappage décrit dans [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure](../best-practices-availability-paired-regions.md).
* Prise en charge du changement de l’abonnement Azure utilisé par Azure Data Catalog. Les administrateurs Azure Data Catalog peuvent utiliser la page Paramètres du portail Azure Data Catalog pour sélectionner un autre abonnement Azure à des fins de facturation.

## <a name="whats-new-for-january-2016"></a>Nouveautés de janvier 2016
Depuis janvier 2016, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de l’inscription manuelle de sources de données supplémentaires. Vous pouvez désormais utiliser « Créer une entrée manuelle » sur le Portail Azure Data Catalog ou l’API REST Azure Data Catalog pour inscrire les sources de données suivantes :
  * OData : fonction, jeu d’entités et conteneur d’entités
  * HTTP : fichier, point de terminaison, rapport et site
  * Système de fichiers : fichier
  * SharePoint : liste
  * FTP : fichier et répertoire
  * Salesforce.com : objet
  * DB2 : table, vue et base de données
  * PostgreSQL : table, vue et base de données
* Prise en charge de la fonction « Ouvrir dans SQL Server Data Tools » pour les sources de données SQL Server (y compris Base de données SQL Azure et Azure SQL Data Warehouse).  
* Prise en charge de l’inscription et de la découverte des vues et packages SAP HANA. Vous pouvez inscrire des sources de données SAP HANA à l’aide de l’outil d’inscription de sources de données Azure Data Catalog, ainsi qu’annoter et découvrir les sources de données SAP HANA inscrites à l’aide du Portail Azure Data Catalog.
* Possibilité d’épingler et de désépingler des ressources de données dans le portail Azure Data Catalog. Vous pouvez choisir d’épingler des ressources de données pour faciliter leur redécouverte et leur réutilisation.
* Une page d’accueil revue récemment dans le portail Azure Data Catalog. Cette nouvelle page d’accueil fournit des informations sur l’activité actuelle des utilisateurs, notamment les ressources publiées, les ressources épinglées et les recherches enregistrées dernièrement, ainsi que des informations sur l’activité à l’échelle de Catalog.
* Prise en charge des paramètres utilisateur persistants dans le portail Azure Data Catalog. Les paramètres d’expérience utilisateur, notamment la vue grille ou mosaïque, le nombre de résultats par page, l’activation ou la désactivation de la mise en surbrillance des correspondances, sont conservés entre les sessions utilisateur.
* Azure Data Catalog est maintenant disponible dans deux nouvelles régions Azure. Les clients peuvent approvisionner Azure Data Catalog dans les régions Europe du Nord et Sud-Est asiatique, outre l’Est des États-Unis, l’Ouest des États-Unis, l’Europe de l’Ouest et l’Est de l’Australie. Pour plus d’informations, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> L’installation de la fonction « Ouvrir dans SQL Server Data Tools » requiert Visual Studio 2013 avec Update 4 et les outils SQL Server. Pour installer la dernière version de SQL Server Data Tools, visitez [Télécharger SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Nouveautés de décembre 2015
Depuis décembre 2015, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de profils de données pour les sources de données Azure SQL Data Warehouse. Lors de l'enregistrement des tables et vues Azure SQL Data Warehouse, les utilisateurs peuvent choisir d'inclure les mesures de profil de données avec les métadonnées extraites de la source de données.
* Prise en charge de l’inscription et de la découverte des bases de données et des objets MySQL. Les utilisateurs peuvent inscrire des sources de données MySQL à l’aide de l’outil d’inscription de sources de données Azure Data Catalog, ainsi qu’annoter et découvrir les sources de données MySQL inscrites à l’aide du portail Azure Data Catalog.
* Prise en charge de l’authentification SPNEGO et Windows pour les sources de données Teradata. Lors de l’inscription de tables et vues Teradata, les utilisateurs peuvent choisir de se connecter à Teradata à l’aide de SPNEGO et de Windows, ainsi que de l’authentification LDAP et TD2.
* Prise en charge des sources de données Azure Data Lake Store. Les utilisateurs peuvent désormais inscrire et découvrir des sources de données Azure Data Lake Store à l’aide d'Azure Data Catalog.
* Prise en charge de la spécification manuelle des paramètres de proxy réseau dans l'outil d'inscription de sources de données Azure Data Catalog. Les utilisateurs peuvent sélectionner « Modifier les paramètres de proxy » à partir de la page d'accueil de l'outil et spécifier l'adresse du proxy et le port qui doivent être utilisés par l'outil.

## <a name="whats-new-for-november-2015"></a>Nouveautés de novembre 2015
Depuis novembre 2015, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Possibilité d’afficher et de copier des chaînes de connexion depuis le portail Azure Data Catalog pour des sources de données SQL Server (notamment Base de données SQL Azure) et Oracle. Les utilisateurs peuvent cliquer sur le lien « Afficher les chaînes de connexion » dans les informations de connexion d’une table, vue ou base de données SQL Server ou Oracle afin de voir les chaînes de connexion utilisées pour se connecter à la source de données. Les chaînes de connexion ADO.NET, ODBC, OLEDB et JDBC sont fournies pour les sources de données SQL Server. Les chaînes de connexion ODBC et OLEDB sont fournies pour les sources de données Oracle.
* Prise en charge de l’inclusion des profils de données lors de l’inscription des tables et vues Teradata.
* Prise en charge de la fonction « Ouvrir dans Power BI Desktop » pour les sources SQL Server (notamment Azure SQL Database et Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage et HDFS.  
* Prise en charge de l’authentification LDAP pour les sources de données Teradata. Lors de l’inscription de tables et vues Teradata, les utilisateurs peuvent choisir de se connecter à Teradata à l’aide de LDAP et de l’authentification TD2.
* Prise en charge de « Ouvrir dans Excel » pour les sources de données Teradata.
* Prise en charge des termes de recherche récents dans le portail Azure Data Catalog. Lors de la recherche dans le portail, les utilisateurs peuvent sélectionner les termes de recherche récemment utilisés pour accélérer l’expérience de découverte.
* Prise en charge de l’aperçu des sources de données Teradata. Lors de l'enregistrement des tables et vues Teradata, les utilisateurs peuvent choisir d'inclure les enregistrements d’instantané avec les métadonnées extraites de la source de données.
* Prise en charge de la commande « Ouvrir avec Excel » pour les sources de données Azure SQL Data Warehouse.
* Prise en charge de la définition et la modification des schémas de niveau colonne pour les ressources de données inscrites. Après avoir créé manuellement une ressource de données à l’aide du portail Azure Data Catalog, les utilisateurs peuvent ajouter des définitions de colonne dans les propriétés des ressources de données.
* Prise en charge des requêtes « has » lors des recherches dans Azure Data Catalog, autorisant ainsi la découverte de ressources de données inscrites qui possèdent des métadonnées spécifiques. La syntaxe des requêtes Azure Data Catalog comprend à présent les éléments suivants :

| Syntaxe de requête | Objectif |
| --- | --- |
| `has:previews` |Recherche les ressources de données qui comprennent une version préliminaire |
| `has:documentation` |Recherche les ressources de données pour lesquelles une documentation a été fournie |
| `has:tableDataProfiles` |Recherche des ressources de données avec des informations de profil des données de niveau table |
| `has:columnsDataProfiles` |Recherche des ressources de données avec des informations de profil des données de niveau colonne |


> [!NOTE]
> « Ouvrir dans Power BI Desktop » requiert l’installation d’une version actuelle de l’application Power BI Desktop. Si vous rencontrez des problèmes ou des erreurs en utilisant cette fonctionnalité, vérifiez que vous disposez de la dernière version de Power BI Desktop à la page [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Nouveautés d’octobre 2015
Depuis octobre 2015, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de chiffrement au repos d'aperçus et de profils de données pour des sources de données enregistrées. Azure Data Catalog chiffre de façon transparente les sources de données d’enregistrements d’aperçu et de profils de données inscrits auprès du service, sans gestion de clés par des administrateurs du catalogue.
* Prise en charge des sources de données Teradata. Les utilisateurs peuvent désormais inscrire et découvrir des tables et vues Teradata.
* Prise en charge de sources de données Hive locales. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données locales.
* Prise en charge des recherches enregistrées dans le portail Azure Data Catalog. Les utilisateurs peuvent enregistrer des termes de recherche et filtrer les sélections pour répéter facilement des recherches précédentes et définir des vues utiles du contenu du catalogue. L’utilisateur peut également définir une recherche enregistrée comme recherche par défaut. Quand un utilisateur clique sur l’icône de recherche en forme de loupe sur la page d’accueil du Portail Azure Data Catalog ou sur la page de démarrage, il accède directement à la recherche enregistrée définie comme recherche par défaut.
* Prise en charge de la documentation complète pour les données et les conteneurs inscrits dans Azure Data Catalog. Les utilisateurs peuvent désormais fournir une documentation pour les données telles que les tableaux, les vues et les rapports, ainsi que pour les conteneurs tels que les bases de données et les modèles, pour les scénarios où les balises et les descriptions ne sont pas suffisantes.
* Prise en charge de l'enregistrement manuel de types de sources de données connus. Les utilisateurs peuvent entrer manuellement les informations d’une source de données à l’aide du portail Azure Data Catalog pour tous les types de source de données pris en charge par Azure Data Catalog.
* Prise en charge de l’autorisation des  groupes de sécurité Azure Active Directory. Les administrateurs du catalogue peuvent autoriser l’accès au catalogue à des groupes de sécurité et à des comptes d’utilisateurs, ce qui facilite la gestion de l’accès à Azure Data Catalog.
* Prise en charge de l’ouverture de sources de données Hive dans Excel à partir du portail Azure Data Catalog.

> [!NOTE]
> Pour la version actuelle, seule l’authentification Teradata TD2 est pris en charge. D’autres mécanismes d’authentification seront pris en charge dans les futures versions.

> [!NOTE]
> Pour pouvoir utiliser la fonctionnalité « Ouvrir dans Excel » avec des sources de données Hive, les utilisateurs doivent avoir installé le pilote ODBC pour Hive.

## <a name="whats-new-for-september-2015"></a>Nouveautés de septembre 2015
Depuis septembre 2015, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge de l’inclusion des profils de données lors de l’inscription des sources de données Hive.
* Prise en charge de la découverte par programmation de l’API Catalog, ce qui facilite l’intégration des applications à Azure Data Catalog.
* Une nouvelle expérience d’initiation à la découverte de source de données dans le portail Azure Data Catalog. Quand les utilisateurs accèdent à la page de découverte du Portail Azure Data Catalog sans entrer de terme de recherche, une vue d’ensemble du contenu du catalogue s’affiche, notamment les balises fréquemment utilisées, les experts, les types de sources de données et les types d’objets.
* Prise en charge de l’inscription et de la découverte des bases de données et des objets Azure SQL Data Warehouse. Pour plus d’informations sur Azure SQL Data Warehouse, consultez [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Prise en charge de l’inscription et de la découverte des modèles SQL Server Analysis Services et des serveurs SQL Server Reporting Services en tant que conteneurs. Au moment de l’inscription d’objets SSAS et SSRS, Azure Data Catalog crée une entrée pour le modèle SSAS et le serveur SSRS, ainsi que pour les rapports et d’autres objets. Les conteneurs peuvent être découverts et annotés à l’aide du portail Azure Data Catalog. Les utilisateurs peuvent également rechercher et filtrer le contenu d’un modèle ou serveur en plus de la recherche et du filtrage du contenu du catalogue.
* Prise en charge de l’inscription et de la détection d’objets SQL Server Analysis Services via HTTP/HTTPS. Les utilisateurs peuvent désormais se connecter à des serveurs SSAS à l’aide d’une URL (telle que https://nom_serveur/olap/msmdpump.dll) au lieu d’un nom de serveur, et peuvent utiliser une authentification de base et des connexions anonymes en plus de l’authentification Windows. Pour plus d’informations sur les connexions HTTP/HTTPS à SSAS, consultez [Configuration de l’accès HTTP à Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Prise en charge de sources de données Hive sur HDInsight. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données HDInsight. Pour plus d'informations sur Hive dans HDInsight, consultez le [Centre de documentation HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Prise en charge de l’inscription et de la détection de bases de données Oracle et de clusters HDFS en tant que conteneurs. Pendant l’inscription de tables et de vues Oracle ou de HDFS, Azure Data Catalog crée une entrée pour la base de données, les tables et les vues. La base de données peut être découverte et annotée à l’aide du portail Azure Data Catalog. Les utilisateurs peuvent également rechercher et filtrer le contenu d’une base de données ou d’un cluster, en plus de la recherche et du filtrage du contenu du catalogue.
* Prise en charge de l'enregistrement manuel de types de sources de données inconnus. Les utilisateurs peuvent entrer manuellement des informations sur les sources de données à l’aide du portail Azure Data Catalog, si bien que les sources de données non prises en charge explicitement par l’outil d’inscription de sources de données peuvent être annotées et découvertes.
* Prise en charge de l'enregistrement et de la découverte des bases de données SQL Server en tant que conteneurs. Pendant l’inscription de tables et de vues SQL Server, Azure Data Catalog crée une entrée pour la base de données, les tables et les vues. La base de données peut être découverte et annotée à l’aide du portail Azure Data Catalog. Les utilisateurs peuvent également rechercher et filtrer le contenu d'une base de données en plus de la recherche et du filtrage du contenu du catalogue.

> [!NOTE]
> Les objets SSAS et SSRS qui ont été inscrits avant la version du 18 septembre doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données pour que l’entrée de modèle ou de serveur soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail Azure Data Catalog.

> [!NOTE]
> Les tables et vues Oracle, ainsi que les fichiers et répertoires HDFS, qui ont été inscrits avant la version du 11 septembre, doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données pour que l’entrée de base de données ou de cluster soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail Azure Data Catalog.

> [!NOTE]
> Les tables et vues SQL Server qui ont été inscrites avant la version du 4 septembre doivent être inscrites de nouveau à l’aide de l’outil d’inscription de sources de données pour que l’entrée de base de données soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail Azure Data Catalog.

## <a name="whats-new-for-august-2015"></a>Nouveautés d’août 2015
Depuis août 2015, les fonctionnalités suivantes ont été ajoutées à Azure Data Catalog :

* Prise en charge du profilage des données pour les sources de données SQL Server et Oracle. Lors de l'enregistrement des vues et des tables SQL Server et Oracle, les utilisateurs peuvent choisir d'inclure des informations de profil des données pour les objets en cours d'enregistrement. Le profil des données inclut des statistiques au niveau de l'objet et au niveau des colonnes.
* Prise en charge des sources de données HDFS Hadoop. Les utilisateurs peuvent désormais s'inscrire et découvrir les fichiers et répertoires HFDFS.
* Prise en charge de la fourniture d’informations de demande d’accès pour les sources de données inscrites. Pour toute ressource de données inscrite, les utilisateurs peuvent maintenant fournir des instructions de demande d’accès, y compris des liens de messagerie ou des URL, en vue d’une intégration aisée aux outils et processus existants.
* Info-bulles pour balises et experts, pour savoir facilement quels utilisateurs ont fourni quelles métadonnées pour les ressources de données inscrites.
* Nous avons ajouté un nouveau bouton « Utilisateur » et un menu à notre barre de navigation supérieure. Ce menu indique à l’utilisateur le compte avec lequel il se connecte à Azure Data Catalog (et éventuellement s’en déconnecte). Ce menu affiche aussi le nom du catalogue, qui est une information utile pour les développeurs qui utilisent l’API REST Azure Data Catalog.
* Édition Standard uniquement : quand vous ajoutez des propriétaires à des ressources de données, Azure Data Catalog prend désormais en charge les comptes d’utilisateurs et les groupes de sécurité en tant que propriétaires. Pour ajouter un groupe de sécurité en tant que propriétaire pour des ressources de données sélectionnées, vous pouvez entrer le nom d’affichage du groupe ou l’adresse de messagerie UPN du groupe, le cas échéant.
* Prise en charge des sources de données de stockage d’objets blob Azure. Les utilisateurs peuvent désormais s’inscrire et découvrir des objets blob Azure Storage et des répertoires.

