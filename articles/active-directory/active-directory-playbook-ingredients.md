---
title: "Ingrédients du manuel de PoC Azure Active Directory | Microsoft Docs"
description: "Explorer et implémenter rapidement des scénarios de gestion de l'identité et de l'accès"
services: active-directory
keywords: azure active directory, manuel, preuve de concept, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Ingrédients du manuel de preuve de concept Azure Active Directory 

## <a name="theme"></a>Thème
Azure AD fournit des solutions d’identité et des accès dans plusieurs domaines de l’entreprise. Nous classons les scénarios dans les domaines suivants : 

* [Un grand nombre d’applications, une seule identité](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Augmenter la sécurité](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Mettre à l’échelle avec le libre-service](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

La définition d’un thème pour cadrer la preuve de concept vous aide à concentrer les efforts qui correspondent aux attentes l’entreprise, et qui souvent les déclencheurs de l’intérêt dans une preuve de concept en premier lieu. 

## <a name="environment"></a>Environnement

Il est important de déterminer les détails de l’environnement dans lequel vous fournirez la preuve de concept. Idéalement, vous pouvez vous baser dessus une fois la preuve de concept terminée. L’environnement cible est crucial, et vous devez trouver le juste équilibre entre une solution aussi réelle que possible et la charge des contraintes ou considérations supplémentaires. Les environnements typiques pour les preuves de concept sont :
* **Production :** Les scénarios seront implémentés dans votre environnement de production et les services cloud Microsoft déjà déployés (AD de production, Office 365, solution SSO/client Azure AD). 
* **Test d’acceptation utilisateur (UAT)/Environnement de développement :** Vous disposez d’une infrastructure de test (Active Directory et éventuellement une solution SSO/client Azure AD en parallèle) avec des données de test qui ressemblent à celles de production. En règle générale, l’environnement de test est partagé entre plusieurs projets dans l’entreprise.

La plupart des scénarios de ce guide sont additifs par nature. Par conséquent, ils peuvent être déployés dans le client de production sans affecter les utilisateurs en dehors de la preuve de concept. Dans ce document, nous évoquerons les scénarios qui auraient un impact sur l’ensemble du client. Dans ce cas, vous souhaiterez envisager un environnement hors production. 


## <a name="target-users"></a>Utilisateurs cible

Il est important de déterminer le jeu cible d’utilisateurs qui testeront les scénarios, en particulier lorsque l’environnement est en production ou en test. Les catégories d’utilisateurs cibles pour les preuves de concept sont :
* **Utilisateurs du pilote :** Les utilisateurs réels dans l’environnement qui utiliseront la solution avec le compte qu’ils utilisent pour leurs fonctions au quotidien
* **Utilisateurs de test :** Les comptes de test créés dans l’environnement 

La plupart des scénarios de ce guide peuvent être réalisés par des utilisateurs du pilote. Dans ce document, nous évoquerons les considérations relatives aux utilisateurs cible si nécessaire.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]