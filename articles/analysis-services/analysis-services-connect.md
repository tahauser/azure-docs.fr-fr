---
title: "Connexion à Azure Analysis Services | Microsoft Docs"
description: "Découvrez comment vous connecter à un serveur Analysis Services dans Azure et en obtenir les données."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: e93544902d7ef435fb1b94e83700ae4fcd6f91f2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-an-azure-analysis-services-server"></a>Se connecter à un serveur Azure Analysis Services

Cet article décrit la connexion à un serveur à l’aide de la modélisation des données et des applications de gestion telles que SQL Server Management Studio (SSMS) ou SQL Server Data Tools (SSDT). La connexion est également possible avec des applications de création de rapports client telles que Microsoft Excel, Power BI Desktop ou des applications personnalisées. Les connexions à Azure Analysis Services utilisent HTTPS.

## <a name="client-libraries"></a>Bibliothèques clientes
[Obtention des bibliothèques clientes les plus récentes](analysis-services-data-providers.md)

Toutes les connexions à un serveur, quel que soit le type, nécessitent des bibliothèques clientes AMO, ADOMD.NET et OLEDB mises à jour pour interagir avec un serveur Analysis Services. Pour SSMS, SSDT, Excel 2016 et Power BI, les bibliothèques clientes les plus récentes sont installées ou mises à jour avec les versions mensuelles. Toutefois, dans certains cas, il est possible qu’une application ne dispose pas de la version la plus récente. Par exemple, lorsque les stratégies retardent les mises à jour, ou lorsque les mises à jour Office 365 se trouvent sur le canal différé.

## <a name="server-name"></a>Nom du serveur

Lorsque vous créez un serveur Analysis Services dans Azure, vous spécifiez un nom unique ainsi que la région dans laquelle le serveur doit être créé. Lorsque vous spécifiez le nom du serveur dans une connexion, le schéma d’affectation de noms du serveur est le suivant :

```
<protocol>://<region>/<servername>
```
 Où protocol est la chaîne **asazure**, region est l’URI où le serveur a été créé (par exemple, westus.asazure.windows.net) et servername est le nom de votre serveur unique au sein de la région.

### <a name="get-the-server-name"></a>Obtenir le nom du serveur
Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur entier. Si d’autres utilisateurs de votre entreprise se connectent également à ce serveur, vous pouvez partager ce nom de serveur avec eux. Lorsque vous spécifiez un nom de serveur, le chemin d’accès complet doit être utilisé.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Chaîne de connexion

Lorsque vous vous connectez à Azure Analysis Services à l’aide du modèle d’objet tabulaire, utilisez les formats de chaîne de connexion suivants :

###### <a name="integrated-azure-active-directory-authentication"></a>Authentification Azure Active Directory intégrée
L’authentification intégrée utilise le cache d’informations d’identification Azure Active Directory s’il est disponible. Si ce n’est pas le cas, la fenêtre de connexion Azure s’affiche.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Authentification Active Directory Azure avec le nom d’utilisateur et le mot de passe

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Authentification Windows (sécurité intégrée)
Utilisez le compte Windows qui exécute le processus en cours.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```



## <a name="connect-using-an-odc-file"></a>Connexion à l’aide d’un fichier .odc
Avec les versions antérieures d’Excel, les utilisateurs peuvent se connecter à un serveur Azure Analysis Services à l’aide d’un fichier Office Data Connection (.odc). Pour en savoir plus, consultez [Créer un fichier Office Data Connection (.odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Étapes suivantes
[Connexion avec Excel](analysis-services-connect-excel.md)    
[Connexion avec Power BI](analysis-services-connect-pbi.md)   
[Gérer votre serveur](analysis-services-manage.md)   

