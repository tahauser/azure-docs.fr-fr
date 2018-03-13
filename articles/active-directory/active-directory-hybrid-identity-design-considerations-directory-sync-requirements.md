---
title: "Conception d’identités hybrides : exigences en matière de synchronisation d’annuaire Azure | Microsoft Docs"
description: "Identifier les exigences nécessaires pour la synchronisation de tous les utilisateurs entre en local et cloud pour l'entreprise."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 475b35c767fcc9c42b828dd2219a14890e05f779
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="determine-directory-synchronization-requirements"></a>Déterminer les exigences de synchronisation de répertoire
La synchronisation consiste à fournir aux utilisateurs une identité dans le cloud basée sur leur identité locale. Qu'ils comptent utiliser le compte synchronisé pour l'authentification ou pour l'authentification fédérée, les utilisateurs doivent toujours avoir une identité dans le cloud.  Cette identité devra être gérée et mise à jour de temps en temps.  Les mises à jour peuvent prendre différentes formes, des modifications de titre aux modifications de mot de passe.  

Commencez par évaluer la solution d'identité locale des organisations et les exigences des utilisateurs. Cette évaluation est importante pour définir les exigences techniques quant à la manière dont les identités d'utilisateur seront créées et gérées dans le cloud.  Pour la plupart des organisations, Active Directory est local et sera l'annuaire local à partir duquel les utilisateurs seront synchronisés, ce qui ne sera cependant pas toujours le cas.  

Veillez à répondre aux questions suivantes :

* Combien avez-vous de forêts AD: une, plusieurs ou aucune ?
  
  * Vers combien d'annuaires Azure AD allez-vous synchroniser ?
    
    1. Utilisez-vous le filtrage ?
    2. Avez-vous prévu plusieurs serveurs Azure AD Connect ?
* Avez-vous actuellement un outil de synchronisation local ?
  
  * Si oui, vos utilisateurs bénéficient-ils d'un annuaire/d'une intégration virtuels d'identités ?
* Avez-vous un autre annuaire local que vous voulez synchroniser (par exemple, annuaire LDAP, base de données des ressources humaines, etc.) ?
  * Procéderez-vous à une synchronisation de la liste d’adresses globale (GALSync) ?
  * Quel est l'état actuel des UPN dans votre organisation ? 
  * Avez-vous un annuaire différent pour l'authentification des utilisateurs ?
  * Votre entreprise utilise-t-elle Microsoft Exchange ?
    * Le déploiement d'Exchange hybride est-il prévu ?

Maintenant que vous avez une idée des conditions de synchronisation, vous devez déterminer quel outil est adapté à ces exigences.  Microsoft propose plusieurs outils pour effectuer la synchronisation et l’intégration d’annuaire.  Pour plus d’informations, consultez [Identité hybride : Comparaison des outils d’intégration d’annuaire](active-directory-hybrid-identity-design-considerations-tools-comparison.md) . 

Maintenant que vous connaissez les exigences de synchronisation et l’outil requis, vous devez évaluer les applications qui utilisent ces services d’annuaire. Cette évaluation est importante pour définir les exigences techniques qui permettront d’intégrer ces applications dans le cloud. Veillez à répondre aux questions suivantes :

* Ces applications migreront-elles vers le cloud et utiliseront-elles l'annuaire ?
* Existe-t-il des attributs spéciaux qui doivent être synchronisées vers le cloud afin que ces applications puissent les utiliser correctement ?
* Ces applications devront-elles être réécrites pour tirer parti de l'authentification par le cloud ?
* Ces applications continueront-elles à résider en local alors que les utilisateurs y accéderont à l'aide de l'identité de cloud ?

Vous devez également déterminer la synchronisation des annuaires d'exigences et de contraintes de sécurité. Cette évaluation est importante pour obtenir une liste des exigences qui seront nécessaires pour créer et gérer des identités d'utilisateurs dans le cloud. Veillez à répondre aux questions suivantes :

* Où se trouvera le serveur de synchronisation ?
* Sera-t-il lié à un domaine ?
* Le serveur se trouvera-t-il sur un réseau restreint derrière un pare-feu, par exemple une zone DMZ ?
  * Pourrez-vous ouvrir les ports de pare-feu requis pour prendre en charge la synchronisation ?
* Avez-vous un plan de récupération d'urgence pour le serveur de synchronisation ?
* Avez-vous un compte disposant des autorisations appropriées pour toutes les forêts avec lesquelles vous souhaitez synchroniser ?
  * Si votre entreprise ne connaît pas la réponse à cette question, consultez la section « Autorisations pour la synchronisation de mot de passe » dans l'article [Installer le Service de synchronisation Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) et déterminez si vous avez déjà un compte disposant de ces autorisations, ou si vous devez en créer un.
* Si vous disposez d'une synchronisation à forêts multiples, le serveur de synchronisation est-il en mesure d'atteindre chaque forêt ?

> [!NOTE]
> Veillez à noter chaque réponse et à comprendre le raisonnement derrière la réponse. [Déterminer les exigences de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) passe en revue les options disponibles. En répondant à chacune de ces questions, vous sélectionnerez l’option correspondant le mieux à vos besoins métier.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les exigences d’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Voir aussi
[Présentation des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

