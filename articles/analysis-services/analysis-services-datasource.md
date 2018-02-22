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
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 33115ee35670407c3b046f70a5fbebc47284b4b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Sources de données prises en charge dans Azure Analysis Services
Les serveurs Azure Analysis Services prennent en charge la connexion aux données sources dans le cloud et en local au sein de votre organisation. Des sources de données supplémentaires prises en charge sont ajoutées en permanence. Vérifiez ces données régulièrement. 

Les sources de données actuellement prises en charge sont les suivantes :

| Cloud  |
|---|
| Stockage Blob Azure*  |
| Base de données SQL Azure  |
| Azure Data Warehouse |


| Local  |   |   |   |
|---|---|---|---|
| Base de données Access  | Dossier* | Oracle Database  | Base de données Teradata |
| Active Directory*  | Document JSON*  | Base de données PostgreSQL*  |Table XML* |
| Analysis Services  | Lignes issues d’un fichier binaire*  | SAP HANA*  |
| Système de plateforme d’analyse  | MySQL Database  | SAP Business Warehouse*  | |
| Dynamics CRM*  | Flux OData*  | SharePoint*  |
| Classeur Excel  | Requête ODBC  | Base de données SQL  |
| Exchange*  | OLE DB  | Base de données Sybase  |

\* Modèles Tabular 1400 uniquement. 

> [!IMPORTANT]
> La connexion aux sources de données sur site nécessite une [passerelle de données locale](analysis-services-gateway.md) installée sur un ordinateur dans votre environnement.

## <a name="data-providers"></a>Fournisseurs de données

Les modèles de données dans Azure Analysis Services peuvent nécessiter différents fournisseurs de données lors de la connexion à certaines sources de données. Dans certains cas, les modèles tabulaires de connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur.

Pour les modèles de données qui se connectent à une source de données cloud comme Azure SQL Database, si vous utilisez des fournisseurs natifs autres que SQLOLEDB, vous pouvez voir le message d’erreur : **« Le fournisseur 'SQLNCLI11.1' n’est pas inscrit »**. Ou bien, si vous disposez d’un modèle de requête directe (DirectQuery) de connexion à des sources de données locales et que vous utilisez des fournisseurs natifs, vous pouvez voir le message d’erreur : **« Erreur lors de la création du jeu de lignes OLE DB. Syntaxe incorrecte à proximité de 'LIMITE' »**.

Les fournisseurs de source de données suivants sont pris en charge pour les modèles de données en mémoire ou DirectQuery lors de la connexion à des sources de données locales ou dans le cloud :

### <a name="cloud"></a>Cloud
| **Source de données** | **En mémoire** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |Fournisseur de données .NET Framework pour SQL Server |Fournisseur de données .NET Framework pour SQL Server |
| Base de données SQL Azure |Fournisseur de données .NET Framework pour SQL Server |Fournisseur de données .NET Framework pour SQL Server | |

### <a name="on-premises-via-gateway"></a>Local (via une passerelle)
|**Source de données** | **En mémoire** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Fournisseur de données .NET Framework pour SQL Server |
| SQL Server |Fournisseur Microsoft OLE DB pour SQL Server |Fournisseur de données .NET Framework pour SQL Server | |
| SQL Server |Fournisseur de données .NET Framework pour SQL Server |Fournisseur de données .NET Framework pour SQL Server | |
| Oracle |Fournisseur Microsoft OLE DB pour Oracle |Fournisseur de données Oracle pour .NET | |
| Oracle |Fournisseur de données Oracle pour .NET |Fournisseur de données Oracle pour .NET | |
| Teradata |Fournisseur OLE DB pour Teradata |Fournisseur de données Teradata pour .NET | |
| Teradata |Fournisseur de données Teradata pour .NET |Fournisseur de données Teradata pour .NET | |
| Système de plateforme d’analyse |Fournisseur de données .NET Framework pour SQL Server |Fournisseur de données .NET Framework pour SQL Server | |

> [!NOTE]
> Vérifiez que des fournisseurs 64 bits sont installés lors de l’utilisation d’une passerelle locale.
> 
> 

Lorsque vous migrez un modèle tabulaire SQL Server Analysis Services local vers Azure Analysis Services, il peut être nécessaire de modifier le fournisseur.

**Pour spécifier un fournisseur de source de données**

1. Dans SSDT > **Explorateur de modèles tabulaires** > **Sources de données**, cliquez sur une connexion de source de données, puis cliquez sur **Modifier la source de données**.
2. Dans **Modifier la connexion**, cliquez sur **Avancé** pour ouvrir la fenêtre de propriétés avancées.
3. Dans **Définir les propriétés avancées** > **Fournisseurs**, sélectionnez le fournisseur approprié.

## <a name="impersonation"></a>Emprunt d’identité
Dans certains cas, il peut être nécessaire de spécifier un autre compte d’emprunt d’identité. Le compte d’emprunt d’identité peut être spécifié dans SSDT ou SSMS.

Pour les sources de données locales :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.
* Si vous utilisez l’authentification Windows, définissez l’utilisateur/le mot de passe Windows. Pour SQL Server, l’authentification Windows avec un compte d’emprunt d’identité spécifique est pris en charge pour les modèles de données en mémoire uniquement.

Pour les sources de données cloud :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.

## <a name="next-steps"></a>étapes suivantes
Si vous disposez de sources de données locales, veillez à installer la [Passerelle locale](analysis-services-gateway.md).   
Pour en savoir plus sur la gestion de votre serveur dans SSDT ou SSMS, consultez [Gérer votre serveur](analysis-services-manage.md).

