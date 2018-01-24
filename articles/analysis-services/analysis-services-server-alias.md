---
title: Alias de noms de serveurs Azure Analysis Services | Microsoft Docs
description: "Explique comment créer et utiliser des alias de noms de serveurs."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a947dde1551c653faa54f088c1712c41c7657aa0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="alias-server-names"></a>Alias de noms de serveurs

Grâce aux alias de noms de serveurs, les utilisateurs peuvent se connecter à votre serveur Azure Analysis Services avec un *alias* plus court que le nom du serveur. En cas de connexion à partir d’une application cliente, l’alias est spécifié comme point de terminaison suivant le format de protocole **link://**. Le point de terminaison retourne ensuite le nom réel du serveur pour permettre la connexion.

Les alias de noms de serveurs sont utiles pour :

- migrer des modèles d’un serveur à un autre sans affecter les utilisateurs ; 
- proposer aux utilisateurs des noms conviviaux de serveurs, plus faciles à mémoriser ; 
- rediriger les utilisateurs vers différents serveurs selon les moments de la journée ; 
- rediriger les utilisateurs de différentes régions vers les instances les plus proches géographiquement, comme avec Azure Traffic Manager. 

Tous les points de terminaison HTTP qui renvoient un nom de serveur Azure Analysis Services valide peut servir d’alias.

![Alias au format link](media/analysis-services-alias/aas-alias-browser.png)

En cas de connexion à partir d’un client, l’alias de nom de serveur est entré suivant le format de protocole **link://**. Par exemple, dans Power BI Desktop :

![Connexion Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Créer un alias

Pour créer un alias de point de terminaison, vous pouvez utiliser la méthode de votre choix, à condition qu’elle retourne un nom de serveur Azure Analysis Services valide : par exemple, une référence à un fichier dans le Stockage Blob Azure contenant le nom réel du serveur ; vous pouvez également créer et publier une application Web Forms ASP.NET.

Cet exemple crée une application Web Forms ASP.NET dans Visual Studio. Le contrôle utilisateur et la référence de la page maître sont supprimés de la page Default.aspx. Le contenu de Default.aspx correspond simplement à la directive Page suivante :

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

L’événement Page_Load de Default.aspx.cs utilise la méthode Response.Write() pour retourner le nom du serveur Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Voir aussi

[Bibliothèques clientes](analysis-services-data-providers.md)   
[Se connecter à partir de Power BI Desktop](analysis-services-connect-pbi.md)
