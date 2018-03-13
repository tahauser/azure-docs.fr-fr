---
title: "Conditions d’emplacement dans l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Découvrez comment utiliser la condition d’emplacement pour contrôler l’accès à vos applications cloud basées sur un emplacement réseau de l’utilisateur."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 028a3f4411e6984b70e0f98c5cf3284e5be1c3b2
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Conditions d’emplacement dans l’accès conditionnel Azure Active Directory 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés peuvent accéder à vos applications cloud. La condition d’emplacement d’une stratégie d’accès conditionnel vous permet de lier des paramètres de contrôle d’accès aux emplacements réseau de vos utilisateurs.

Cet article vous fournit les informations dont vous avez besoin pour configurer la condition d’emplacement. 

## <a name="locations"></a>Emplacements

Azure AD active l’authentification unique aux appareils, applications et des services depuis n’importe quel endroit sur l’Internet public. Avec la condition d’emplacement, vous pouvez contrôler l’accès à vos applications cloud basées sur l’emplacement réseau d’un utilisateur. Les cas d’utilisation courants de la condition d’emplacement sont :

- Exiger l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise  

- Bloquer l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques. 

Un emplacement correspond à une étiquette pour un emplacement réseau qu’un représente un emplacement nommé ou des adresses IP approuvées par une authentification multifacteur.


## <a name="named-locations"></a>Emplacements nommés 

Avec les emplacements nommés, vous pouvez créer des regroupements logiques de plages d’adresses IP, pays et régions. 

 Un emplacement nommé se compose des éléments suivants :

![Emplacements](./media/active-directory-conditional-access-locations/42.png)

- **Nom** : nom complet d’un emplacement nommé.

- **Plages d’adresses IP** : une ou plusieurs plages d’adresses IP au format CIDR.

- **Marquer comme emplacement approuvé** : indicateur que vous pouvez définir pour un emplacement nommé afin d’indiquer un emplacement approuvé. Les emplacements approuvés sont généralement des zones de réseau qui sont contrôlées par votre service informatique. En plus de l’accès conditionnel, les emplacements nommés approuvés sont également utilisés par les rapports de sécurité Azure Identity Protection et Azure AD afin de réduire les [faux positifs](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Pays/régions** : cette option vous permet de sélectionner un ou plusieurs pays ou régions pour définir un emplacement nommé. 

- **Inclure les zones inconnues** : certaines adresses IP ne sont pas mappées à un pays spécifique. Cette option vous permet de choisir si ces adresses IP doivent être incluses dans l’emplacement nommé. Elles peuvent être vérifiées lorsque la stratégie utilisant l’emplacement nommé doit s’appliquer à des emplacements inconnus.

Le nombre d’emplacements nommés que vous pouvez configurer est limité par la taille de l’objet associé dans Azure AD. Vous pouvez configurer les éléments suivants :

- Un emplacement nommé avec 1200 plages d’adresses IP maximum.

- Un maximum de 90 emplacements nommés avec une plage d’adresses IP assignée à chacun d’eux.




## <a name="trusted-ips"></a>Adresses IP approuvées

Vous pouvez également configurer des plages d’adresses IP représentant l’intranet local de votre organisation dans les [paramètres du service d’authentification multifacteur](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Cette fonctionnalité vous permet de configurer jusqu’à 50 plages d’adresses IP. Les plages d’adresses IP sont au format CIDR. Pour plus d’informations, consultez [Adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Si vous avez approuvé des adresses IP configurées, elles apparaissent en tant qu’**adresses IP approuvées MFA** dans la liste des emplacements de la condition d’emplacement.   

### <a name="skipping-multi-factor-authentication"></a>Ignorer l’authentification multifacteur

Sur la page des paramètres du service d’authentification multifacteur, vous pouvez identifier les utilisateurs de l’intranet d’entreprise en sélectionnant **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet**. Ce paramètre indique que la revendication de réseau d’entreprise interne, qui est émise par AD FS, doit être approuvée et utilisée pour identifier l’utilisateur comme étant sur le réseau d’entreprise. Pour plus d’informations, consultez [Activer la fonctionnalité Adresses IP approuvées à l’aide de l’accès conditionnel](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Après avoir activé cette option, qui inclut l’emplacement nommé, les **Adresses IP approuvées MFA** s’appliqueront à toutes les stratégies contenant celle sélectionnée.

Pour les applications mobiles et de bureau avec des durées de session active longues, l’accès conditionnel est réévalué régulièrement. La valeur par défaut est une fois une heure. Lorsque la revendication de réseau d’entreprise interne n’est émise qu’au moment de l’authentification initiale, Azure AD peut ne pas contenir de liste de plages d’adresses IP approuvées. Dans ce cas, il est plus difficile de déterminer si l’utilisateur est toujours sur le réseau d’entreprise :

1. Vérifiez si l’adresse IP de l’utilisateur se trouve dans l’une des plages d’adresses IP approuvées.

2. Vérifiez si les trois premiers octets de l’adresse IP de l’utilisateur correspondent aux 3 premiers octets de l’adresse IP de l’authentification initiale. L’adresse IP est comparée à l’authentification initiale car c’est là que la revendication de réseau d’entreprise interne a été émise à l’origine et que l’emplacement de l’utilisateur a été validé.

Si les deux étapes échouent, un utilisateur est considéré comme n’étant plus sur une adresse IP approuvée.



## <a name="location-condition-configuration"></a>Configuration de la condition d’emplacement

Lorsque vous configurez la condition d’emplacement, vous avez la possibilité de faire la distinction entre :

- N’importe quel emplacement 
- Tous les emplacements approuvés
- Des emplacements sélectionnés

![Emplacements](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>N’importe quel emplacement

Par défaut, la sélection de **N’importe quel emplacement** entraîne l’application d’une stratégie à toutes les adresses IP, à savoir n’importe quelle adresse sur Internet. Ce paramètre ne se pas limite pas aux adresses IP que vous avez configurées en tant qu’emplacement nommé. Lorsque vous sélectionnez **N’importe quel emplacement**, vous pouvez toujours exclure des emplacements spécifiques d’une stratégie. Par exemple, vous pouvez appliquer une stratégie à tous les emplacements, sauf des emplacements approuvés, afin de définir l’étendue à tous les emplacements, à l’exception du réseau d’entreprise.

### <a name="all-trusted-locations"></a>Tous les emplacements approuvés

Cette option s’applique à :

- Tous les emplacements qui ont été marqués comme emplacement approuvé
- **Adresses IP approuvées MFA** (si configurées)


### <a name="selected-locations"></a>Des emplacements sélectionnés

Avec cette option, vous pouvez sélectionner un ou plusieurs emplacements nommés. Pour une stratégie avec ce paramètre à appliquer, un utilisateur doit se connecter à partir de l’un des emplacements sélectionnés. Lorsqu’il vous cliquez sur **Sélectionner**, le contrôle de sélection du réseau nommé qui affiche la liste des réseaux nommés s’ouvre. La liste indique également si l’emplacement réseau a été marqué comme approuvé. L’emplacement nommé appelé **Adresses IP approuvées MFA** est utilisé pour inclure les paramètres IP pouvant être configurés dans la page des paramètres du service d’authentification multifacteur.

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="when-is-a-location-evaluated"></a>Quand un emplacement est-il évalué ?

Les stratégies d’accès conditionnel sont évaluées lorsque : 

- Un utilisateur se connecte pour la première fois 

- Azure AD émet un jeton pour l’application cloud sur laquelle la stratégie d’accès conditionnel a été définie. 

Par défaut, Azure AD émet un jeton toutes les heures. Après une sortie du réseau d’entreprise, la stratégie est appliquée dans l’heure qui suit pour les applications utilisant l’authentification moderne.


### <a name="user-ip-address"></a>Adresse IP utilisateur

L’adresse IP qui est utilisée dans l’évaluation de la stratégie correspond à l’adresse IP publique de l’utilisateur. Pour les appareils sur un réseau privé, il ne s’agit pas de l’adresse IP cliente de l’appareil de l’utilisateur sur l’intranet ; il s’agit de l’adresse utilisée par le réseau pour se connecter à l’Internet public. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Chargement et téléchargement en bloc d’emplacements nommés

Lorsque vous créez ou mettez à jour des emplacements nommés, pour effectuer des mises à jour en bloc, vous pouvez charger ou télécharger un fichier CSV contenant les plages d’adresses IP. Un chargement remplace les plages d’adresses IP de la liste par celles du fichier. Chaque ligne du fichier contient une plage d’adresses IP au format CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxies cloud et réseaux privés virtuels 

Lorsque vous utilisez un proxy hébergé dans le cloud ou une solution VPN, l’adresse IP Azure AD utilisée pendant l’évaluation d’une stratégie correspond à l’adresse IP du proxy. L’en-tête X-Forwarded-For (XFF) qui contient l’adresse IP publique de l’utilisateur n’est pas utilisé car il n’existe aucune validation provenant d’une source approuvée, il pourrait donc permettre de falsifier une adresse IP. 

Lorsqu’un proxy cloud est disponible, une stratégie utilisée pour exiger un appareil joint à un domaine peut être utilisée, ou la revendication de réseau d’entreprise interne à partir d’AD FS.



### <a name="api-support-and-powershell"></a>Prise en charge des API et PowerShell 

Les API et PowerShell ne sont pas encore pris en charge pour les emplacements nommés, ou pour les stratégies d’accès conditionnel.





## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
