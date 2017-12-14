---
title: "Bibliothèques clientes requises pour la connexion à Azure Analysis Services | Microsoft Docs"
description: "Décrit les bibliothèques clientes requises pour que les applications clientes et les outils se connectent à Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/04/2017
ms.author: owend
ms.openlocfilehash: 9cf5bcc9f7e089bd919c1a46934b64383b88c0ab
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliothèques clientes pour la connexion à Azure Analysis Services

Les bibliothèques clientes sont nécessaires pour que les applications clientes et les outils se connectent aux serveurs Analysis Services. 

## <a name="download-the-latest-client-libraries"></a>Télécharger les dernières bibliothèques clientes  

|Télécharger  |Version  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.1000.397      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.1000.397      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.218      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.1000.397      |

## <a name="understanding-client-libraries"></a>Présentation des bibliothèques clientes

Analysis Services utilise trois bibliothèques clientes, également appelées fournisseurs de données. ADOMD.NET et Analysis Services Management Objects (AMO) sont des bibliothèques clientes managées. Le fournisseur Analysis Services OLE DB (MSOLAP DLL) est une bibliothèque cliente native. En général, les trois bibliothèques sont installées en même temps. **Azure Analysis Services nécessite les dernières versions des trois bibliothèques**. 

Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes et les mettent à jour quand de nouvelles versions sont disponibles. Selon la version ou la fréquence des mises à jour, certaines bibliothèques clientes peuvent ne pas être les dernières versions requises par Azure Analysis Services. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent l’installation manuelle ou par programmation des bibliothèques. Les bibliothèques clientes pour l’installation manuelle sont incluses dans les packs de fonctionnalités SQL Server sous forme de packages distribuables. Toutefois, elles sont liées à la version de SQL Server et il se peut qu’elles ne soient pas à jour.  

Les bibliothèques clientes pour les connexions clientes sont différentes des fournisseurs de données requis pour connecter un serveur Azure Analysis Services à une source de données. Pour plus d’informations sur les connexions aux sources de données, consultez [Connexions de source de données](analysis-services-datasource.md).

## <a name="client-library-types"></a>Types de bibliothèques clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>Fournisseur Analysis Services OLE DB (MSOLAP) 

 Le fournisseur Analysis Services OLE DB (MSOLAP) est la bibliothèque cliente native pour les connexions de base de données Analysis Services. Il est utilisé indirectement par ADOMD.NET et AMO, les demandes de connexion étant déléguées au fournisseur de données. Vous pouvez également appeler le fournisseur OLE DB directement à partir de code d’application.  
  
 Le fournisseur Analysis Services OLE DB est installé automatiquement par la plupart des outils et des applications clientes utilisés pour accéder aux bases de données Analysis Services. Il doit être installé sur les ordinateurs utilisés pour accéder aux données Analysis Services.  
  
 Les fournisseurs OLE DB sont souvent spécifiés dans les chaînes de connexion. Une chaîne de connexion Analysis Services utilise une nomenclature différente pour faire référence au fournisseur OLE DB : MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO est une bibliothèque cliente managée utilisée pour l’administration serveur et la définition de données. Elle est installée et utilisée par des outils et des applications clientes. Par exemple, SQL Server Management Studio (SSMS) utilise AMO pour se connecter à Analysis Services.  
  
 Une connexion utilisant AMO se résume généralement à `“data source=\<servername>”`. Une fois une connexion établie, vous utilisez l’API pour recourir à des collections de bases de données et à des objets principaux. SSDT et SSMS utilisent AMO pour se connecter à une instance Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET est une bibliothèque cliente de données managée utilisée pour interroger des données Analysis Services. Elle est installée et utilisée par des outils et des applications clientes. 
  
 Durant la connexion à une base de données, les propriétés de chaîne de connexion pour les trois bibliothèques sont similaires. Pratiquement toute chaîne de connexion que vous définissez pour ADOMD.NET à l’aide de [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) fonctionne également pour AMO et le fournisseur OLE DB Analysis Services (MSOLAP). Pour plus d’informations, consultez [Propriétés des chaînes de connexion &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Comment déterminer la version de la bibliothèque cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Accédez à `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140` Si vous avez plusieurs dossiers, choisissez le numéro le plus élevé.
  
2.  Cliquez avec le bouton droit sur **msolap140.dll** > **Propriétés** > **Détails**.  
    
    ![Détails de la bibliothèque cliente](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. Accédez à `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`
2. Cliquez avec le bouton droit sur **Microsoft.AnalysisServices** > **Propriétés** > **Détails**.  

### <a name="adomd"></a>ADOMD

1. Accédez à `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`
2. Cliquez avec le bouton droit sur **Microsoft.AnalysisServices.AdomdClient** > **Propriétés** > **Détails**.  


## <a name="next-steps"></a>Étapes suivantes
[Connexion avec Excel](analysis-services-connect-excel.md)    
[Connexion avec Power BI](analysis-services-connect-pbi.md)
