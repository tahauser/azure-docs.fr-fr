---
title: "Contrôles de page Gestion des API Azure | Microsoft Docs"
description: "Découvrez les contrôles de page disponibles pour une utilisation dans les modèles du portail des développeurs dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: 4fd91ae079ff054932f4572874001dd69dd848e7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="azure-api-management-page-controls"></a>Contrôles de page Gestion des API Azure
Gestion des API Azure fournit les contrôles suivants à utiliser dans les modèles du portail des développeurs.  
  
Pour utiliser un contrôle, placez-le à l’emplacement souhaité dans le modèle du portail des développeurs. Certains contrôles, tels que [app-actions](#app-actions), ont des paramètres, comme illustré dans l’exemple suivant :  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Les valeurs des paramètres sont transmises comme partie du modèle de données pour le modèle. Dans la plupart des cas, vous pouvez simplement coller l’exemple fourni pour chaque contrôle afin qu’il fonctionne correctement. Pour plus d’informations sur les valeurs de paramètre, vous pouvez voir la section de modèle de données pour chaque modèle dans lequel un contrôle peut être utilisé.  
  
 Pour plus d’informations sur l’utilisation de modèles, consultez [Comment personnaliser le portail des développeurs Gestion des API Azure à l’aide de modèles](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Contrôles de page du modèle dans le portail des développeurs  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [fournisseurs](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 Le contrôle `app-actions` fournit une interface utilisateur pour interagir avec les applications sur la page de profil utilisateur dans le portail des développeurs.  
  
 ![contrôle app-actions](./media/api-management-page-controls/APIM-app-actions-control.png "contrôle app-actions APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>parameters  
  
|Paramètre|Description|  
|---------------|-----------------|  
|appId|ID de l’application.|  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `app-actions` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Applications](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 Le contrôle `basic-signin` fournit un contrôle pour la collecte des informations de connexion utilisateur dans la page de connexion du portail des développeurs.  
  
 ![contrôle basic-signin](./media/api-management-page-controls/APIM-basic-signin-control.png "contrôle basic-signin APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `basic-signin` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Connexion](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 Le contrôle `paging-control` fournit des fonctionnalités de pagination sur les pages du portail des développeurs qui affichent une liste d’éléments.  
  
 ![paging-control](./media/api-management-page-controls/APIM-paging-control.png "paging-control APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `paging-control` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Liste d’API](api-management-api-templates.md#APIList)  
  
-   [Liste des problèmes](api-management-issue-templates.md#IssueList)  
  
-   [Liste de produits](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> providers  
 Le contrôle `providers` fournit un contrôle pour la sélection des fournisseurs d’authentification sur la page de connexion du portail des développeurs.  
  
 ![contrôle providers](./media/api-management-page-controls/APIM-providers-control.png "contrôle providers APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `providers` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Connexion](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 Le contrôle `search-control` fournit des fonctionnalités de recherche sur les pages du portail des développeurs affichant une liste d’éléments.  
  
 ![search-control](./media/api-management-page-controls/APIM-search-control.png "search-control APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `search-control` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Liste d’API](api-management-api-templates.md#APIList)  
  
-   [Liste de produits](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 Le contrôle `sign-up` fournit un contrôle pour la collecte des informations de profil utilisateur sur la page d’inscription du portail des développeurs.  
  
 ![sign-up](./media/api-management-page-controls/APIM-sign-up-control.png "sign-up APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `sign-up` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Inscription](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` fournit un contrôle pour abonner un utilisateur à un produit.  
  
 ![contrôle subscribe-button](./media/api-management-page-controls/APIM-subscribe-button-control.png "contrôle subscribe-button APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>parameters  
 Aucune.  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `subscribe-button` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Produit](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 Le contrôle `subscription-cancel` fournit un contrôle pour annuler l’abonnement à un produit sur la page de profil utilisateur du portail des développeurs.  
  
 ![contrôle subscription-cancel](./media/api-management-page-controls/APIM-subscription-cancel-control.png "contrôle subscription-cancel APIM")  
  
### <a name="usage"></a>Usage  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>parameters  
  
|Paramètre|Description|  
|---------------|-----------------|  
|subscriptionId|ID de l’abonnement à annuler.|  
|cancelUrl|URL permettant d’annuler l’abonnement.|  
  
### <a name="developer-portal-templates"></a>Modèles du portail des développeurs  
 Le contrôle `subscription-cancel` peut être utilisé dans les modèles suivants du portail des développeurs :  
  
-   [Produit](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de modèles, consultez [Comment personnaliser le portail des développeurs Gestion des API Azure à l’aide de modèles](api-management-developer-portal-templates.md).