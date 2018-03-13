---
title: "Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager pour l’équilibrage de charge."
description: "Utilisation d’un nom de domaine personnalisé pour une application web dans Azure App Service intégrant Traffic Manager pour équilibrer la charge."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l’utilisation d’un nom de domaine personnalisé avec une application [App Service](app-service-web-overview.md) intégrée à [Traffic Manager](../traffic-manager/traffic-manager-overview.md) pour l’équilibrage de la charge.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Présentation des enregistrements DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configuration de vos applications web en mode Standard
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Ajout d'un enregistrement DNS pour votre domaine personnalisé
> [!NOTE]
> Si vous avez acheté un domaine via Azure App Service Web Apps, ignorez les étapes suivantes et reportez-vous à l'étape finale de l'article [Acheter un domaine pour Web Apps](custom-dns-web-site-buydomains-web-app.md) .
> 
> 

Pour associer votre domaine personnalisé à une application web dans Azure App Service, vous devez ajouter une nouvelle entrée dans la table DNS pour ce domaine. Pour cela, utilisez les outils de gestion de votre fournisseur de domaine.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Bien que les spécificités de chaque fournisseur de domaine varient, vous mappez généralement *depuis* votre nom de domaine personnalisé (comme **contoso.com**) *vers* le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) qui est intégré à votre application web.
   
> [!NOTE]
> Si un enregistrement est déjà utilisé et que vous devez y lier préventivement vos applications, vous pouvez créer un enregistrement CNAME supplémentaire. Par exemple, pour lier **www.contoso.com** à votre application web à titre préventif, créez un enregistrement CNAME entre **awverify.www** et **contoso.trafficmanager.net**. Vous pouvez ensuite ajouter « www.contoso.com » à votre application web sans modifier l’enregistrement CNAME « www ». Pour plus d’informations, consultez [Créer des enregistrements DNS pour une application web dans un domaine personnalisé][CREATEDNS].
> 
> 

Quand vous avez terminé l’ajout ou la modification des enregistrements DNS auprès de votre fournisseur de domaine, enregistrez les modifications.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Activation de Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
