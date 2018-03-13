---
title: "Sources de données prises en charge par Azure Analysis Services | Microsoft Docs"
description: "Décrit les sources de données prises en charge pour les modèles de données dans Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: e2f7e356b260c0e5af67d28811bd88a63a601312
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Sources de données prises en charge dans Azure Analysis Services

Les sources de données et connecteurs affichés dans Obtenir des données ou l’Assistant Importation dans Visual Studio concernent à la fois Azure Analysis Services et SQL Server Analysis Services. Cependant, toutes les sources de données et tous les connecteurs indiqués ne sont pas pris en charge dans Azure Analysis Services. Les types de sources de données auxquelles vous pouvez vous connecter dépendent de nombreux facteurs tels que le niveau de compatibilité du modèle, les connecteurs de données disponibles, le type d’authentification, les fournisseurs et la prise en charge de la passerelle de données locale. 

## <a name="azure-data-sources"></a>Sources de données Azure

|Source de données  |En mémoire  |DirectQuery  |
|---------|---------|---------|
|Base de données SQL Azure     |   OUI      |    OUI      |
|Azure SQL Data Warehouse     |   OUI      |   OUI       |
|Stockage Blob Azure*     |   OUI       |    Non       |
|Stockage de tables Azure*    |   OUI       |    Non       |
|Azure Cosmos DB (Bêta)*     |  OUI        |  Non         |
|Azure Data Lake Store*     |   OUI       |    Non       |
|Azure HDInsight HDFS*     |     OUI     |   Non        |
|Azure HDInsight Spark (Bêta)*     |   OUI       |   Non        |
|Azure Database pour MySQL (Version préliminaire)*     |   OUI       |   Non       |
|Azure Database pour PostgreSQL (Version préliminaire)*     | OUI         |  Non        |
||||

\* Modèles Tabular 1400 uniquement.

**Fournisseur**   
Les modèles en mémoire et DirectQuery qui se connectent aux sources de données Azure utilisent le fournisseur de données .NET Framework pour SQL Server.

## <a name="on-premises-data-sources"></a>Sources de données locales

La connexion aux sources de données locales requiert une passerelle locale. Lorsque vous utilisez une passerelle, vérifiez que des fournisseurs 64 bits sont installés.

### <a name="in-memory-and-directquery"></a>En mémoire et DirectQuery

|Source de données | Fournisseur en mémoire | Fournisseur DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Fournisseur Microsoft OLE DB pour SQL Server, Fournisseur de données .NET Framework pour SQL Server | Fournisseur de données .NET Framework pour SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, Fournisseur Microsoft OLE DB pour SQL Server, Fournisseur de données .NET Framework pour SQL Server | Fournisseur de données .NET Framework pour SQL Server |
| Oracle |Fournisseur Microsoft OLE DB pour Oracle, Fournisseur de données Oracle pour .NET |Fournisseur de données Oracle pour .NET | |
| Teradata |Fournisseur OLE DB pour Teradata, Fournisseur de données Teradata pour .NET |Fournisseur de données Teradata pour .NET | |
| | | |

\* Modèles Tabular 1400 uniquement.

### <a name="in-memory-only"></a>En mémoire uniquement

> [!IMPORTANT]
> Le test des fournisseurs pour les sources de données suivantes est en cours. 

|Source de données  |  
|---------|---------|
|Base de données Access     |  
|Active Directory*     |  
|Analysis Services     | 
|Système de plateforme d’analyse     |  
|Dynamics CRM*     |  
|Classeur Excel     | 
|Exchange*     |  
|Dossier*     | 
|Document JSON*     |  
|Lignes issues d’un fichier binaire*     | 
|MySQL Database     | 
|Flux OData*     | 
|Requête ODBC     | 
|OLE DB     |  
|Base de données PostgreSQL*    | 
|SAP HANA*    |   
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Base de données Sybase     |  
|Table XML*    |  
|||
 
\* Modèles Tabular 1400 uniquement.

## <a name="specifying-a-different-provider"></a>Spécifier un autre fournisseur

Les modèles de données dans Azure Analysis Services peuvent nécessiter différents fournisseurs de données lors de la connexion à certaines sources de données. Dans certains cas, les modèles tabulaires de connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur. Si vous utilisez des fournisseurs natifs autres que SQLOLEDB, vous pouvez avoir le message d’erreur suivant : **Le fournisseur 'SQLNCLI11.1' n’est pas inscrit**. Ou bien, si vous disposez d’un modèle de requête directe (DirectQuery) de connexion à des sources de données locales et si vous utilisez des fournisseurs natifs, vous pouvez avoir le message d’erreur suivant : **Erreur lors de la création du jeu de lignes OLE DB. Syntaxe incorrecte à proximité de 'LIMIT'**.

Lorsque vous migrez un modèle tabulaire SQL Server Analysis Services local vers Azure Analysis Services, il peut être nécessaire de modifier le fournisseur.

**Pour spécifier un fournisseur**

1. Dans SSDT > **Explorateur de modèles tabulaires** > **Sources de données**, cliquez sur une connexion de source de données, puis cliquez sur **Modifier la source de données**.
2. Dans **Modifier la connexion**, cliquez sur **Avancé** pour ouvrir la fenêtre de propriétés avancées.
3. Dans **Définir les propriétés avancées** > **Fournisseurs**, sélectionnez le fournisseur approprié.

## <a name="impersonation"></a>Emprunt d’identité
Dans certains cas, il peut être nécessaire de spécifier un autre compte d’emprunt d’identité. Le compte d’emprunt d’identité peut être spécifié dans Visual Studio SSDT ou SSMS.

Pour les sources de données locales :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.
* Si vous utilisez l’authentification Windows, définissez l’utilisateur/le mot de passe Windows. Pour SQL Server, l’authentification Windows avec un compte d’emprunt d’identité spécifique est pris en charge pour les modèles de données en mémoire uniquement.

Pour les sources de données cloud :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.

## <a name="next-steps"></a>Étapes suivantes
[Passerelle locale](analysis-services-gateway.md)   
[Gérer votre serveur](analysis-services-manage.md)   

