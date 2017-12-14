---
title: "Expériences de connexion avec Azure AD Identity Protection | Microsoft Docs"
description: "Fournit une vue d’ensemble de l’expérience utilisateur lorsque Identity Protection a atténué ou corrigé la connexion d’un utilisateur ou lorsque l’authentification multifacteur est requise par une stratégie."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f7abbab27a6794c934344c78ba1776c8dbb97088
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Expériences de connexion avec Azure AD Identity Protection
Avec Azure Active Directory Identity Protection, vous pouvez :

* exiger que les utilisateurs s’inscrivent à l’authentification multifacteur
* gérer les connexions risquées et les utilisateurs compromis

La réponse du système à ces problèmes a un impact sur l’expérience de connexion d’un utilisateur, car la connexion à l’aide d’un nom d’utilisateur et d’un mot de passe seulement ne sera plus possible. Des étapes supplémentaires sont nécessaires pour rétablir l’accès d’un utilisateur en toute sécurité.

Cette rubrique donne une vue d’ensemble de l’expérience de connexion d’un utilisateur pour tous les cas qui peuvent se produire.

**Authentification multifacteur**

* Inscription à l’authentification multifacteur

**Connexion risquée**

* Récupération de connexion à risque
* Connexion à risque bloquée
* Inscription à l’authentification multifacteur au cours d’une connexion à risque

**Utilisateur à risque**

* Récupération de compte compromis
* Compte compromis bloqué

## <a name="multi-factor-authentication-registration"></a>Inscription à l’authentification multifacteur
L’utilisateur bénéficie d’une expérience optimale pour le flux de récupération de compte compromis et de connexion à risque lorsqu’il peut effectuer lui-même l’opération de récupération. Si des utilisateurs sont inscrits à l’authentification multifacteur, ils ont déjà un numéro de téléphone associé à leur compte qu’ils peuvent utiliser pour répondre aux questions de sécurité. La récupération d’un compte suite à sa compromission ne nécessite pas l’intervention du support technique ou d’un administrateur. Par conséquent, nous vous recommandons vivement de demander à vos utilisateurs de s’inscrire à l’authentification multifacteur. 

Les administrateurs peuvent :

* définir une stratégie qui impose aux utilisateurs d’ajouter une vérification de sécurité supplémentaire à leur compte ; 
* autoriser les utilisateurs à ignorer l’inscription à l’authentification multifacteur pendant 30 jours maximum, s’ils souhaitent leur accorder un délai de grâce avant l’inscription.

**L’inscription à l’authentification multifacteur comporte trois étapes :**

1. Dans la première étape, l’utilisateur reçoit une notification concernant la nécessité d’inscrire le compte à l’authentification multifacteur. 
   
    ![Correction](./media/active-directory-identityprotection-flows/140.png "Correction")
2. Pour configurer l’authentification multifacteur, vous devez indiquer au système comment vous souhaitez être contacté.
   
    ![Correction](./media/active-directory-identityprotection-flows/141.png "Correction")
3. Le système vous envoie un défi et vous devez y répondre.
   
    ![Correction](./media/active-directory-identityprotection-flows/142.png "Correction")

## <a name="risky-sign-in-recovery"></a>Récupération de connexion à risque
Lorsqu’un administrateur a configuré une stratégie pour les risques à la connexion, les utilisateurs affectés sont avertis quand ils tentent de se connecter. 

**Le flux de connexion à risque comporte deux étapes :** 

1. L’utilisateur est informé que quelque chose d’inhabituel a été détecté concernant sa connexion, par exemple en cas de connexion depuis un nouvel emplacement, un nouvel appareil ou une nouvelle application. 
   
    ![Correction](./media/active-directory-identityprotection-flows/120.png "Correction")
2. L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il doit saisir un code de sécurité envoyé sur son téléphone. Comme il s’agit simplement d’une connexion à risque et non pas d’un compte compromis, l’utilisateur ne doit pas changer le mot de passe dans ce flux. 
   
    ![Correction](./media/active-directory-identityprotection-flows/121.png "Correction")

## <a name="risky-sign-in-blocked"></a>Connexion à risque bloquée
Les administrateurs peuvent également choisir de définir une stratégie en matière de risque à la connexion pour bloquer les utilisateurs lors de la connexion selon le niveau de risque. Pour débloquer leur accès, les utilisateurs finaux doivent contacter un administrateur ou leur support technique, ou ils peuvent essayer de se connecter depuis un emplacement ou un appareil connu. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis.

![Correction](./media/active-directory-identityprotection-flows/200.png "Correction")

## <a name="compromised-account-recovery"></a>Récupération de compte compromis
Lorsqu’une stratégie de sécurité en matière de risque des utilisateurs a été configurée, les utilisateurs dont le niveau de risque correspond à celui spécifié dans la stratégie (et qui sont donc considérés comme compromis) doivent passer par le flux de récupération de compte compromis avant de pouvoir se connecter. 

**Le flux de récupération de compte compromis comporte trois étapes :**

1. L’utilisateur est informé que la sécurité de son compte est menacée en raison d’activités suspectes ou de la divulgation de ses informations d’identification.
   
    ![Correction](./media/active-directory-identityprotection-flows/101.png "Correction")
2. L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il peut récupérer lui-même son compte compromis. Il devra saisir un code de sécurité envoyé sur son téléphone. 
   
   ![Correction](./media/active-directory-identityprotection-flows/110.png "Correction")
3. Enfin, l’utilisateur est obligé de changer son mot de passe, car il se peut que quelqu’un d’autre ait eu accès à son compte. 
   Vous trouverez ci-dessous des captures d’écran de cette expérience.
   
   ![Correction](./media/active-directory-identityprotection-flows/111.png "Correction")

## <a name="compromised-account-blocked"></a>Compte compromis bloqué
Pour débloquer un compte bloqué par une stratégie de sécurité en matière de risque des utilisateurs, l’utilisateur doit contacter un administrateur ou son support technique. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis.

![Correction](./media/active-directory-identityprotection-flows/104.png "Correction")

## <a name="reset-password"></a>Réinitialiser le mot de passe
Si des utilisateurs compromis voient leur connexion bloquée, un administrateur peut générer un mot de passe temporaire pour eux. Les utilisateurs devront changer leur mot de passe la prochaine fois qu’ils se connecteront.

![Correction](./media/active-directory-identityprotection-flows/160.png "Correction")

## <a name="see-also"></a>Voir aussi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

