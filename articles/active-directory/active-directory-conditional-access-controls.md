---
title: "Contrôles d’accès dans l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Découvrez comment fonctionnent les contrôles d’accès dans l’accès conditionnel Azure Active Directory."
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
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c5a2ab3b8d2fe55eee1f67e4b697a1e5b0dd2daf
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="access-controls-in-azure-active-directory-conditional-access"></a>Contrôles d’accès dans l’accès conditionnel Azure Active Directory 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Dans une stratégie d’accès conditionnel, vous définissez la réponse (« faire ») sur la raison du déclenchement de votre stratégie (« quand cela se produit »). 

![Contrôle](./media/active-directory-conditional-access-controls/10.png)


Dans le contexte de l’accès conditionnel, 

- « **Quand cela se produit** » est une **condition**

- « **Faire** » est un **contrôle d’accès**


Une stratégie d’accès conditionnel combine une instruction de condition à des contrôles.

![Contrôle](./media/active-directory-conditional-access-controls/61.png)

Chaque contrôle est soit une exigence qui doit être remplie par la personne ou le système qui se connecte, soit une restriction sur ce que l’utilisateur peut faire après la connexion. 

Il existe deux types de contrôles : 

- **Contrôle d’octroi** - pour réguler l’accès

- **Contrôles de session** - pour restreindre l’accès au sein d’une session

Cette rubrique explique les divers contrôles qui sont disponibles dans l’accès conditionnel Azure AD. 

## <a name="grant-controls"></a>Contrôles d’octroi

Avec les contrôles d’octroi, vous pouvez bloquer l’accès complètement ou autoriser l’accès avec des exigences supplémentaires en sélectionnant les contrôles de votre choix. Pour de nombreux contrôles, vous pouvez exiger ce qui suit :

- Tous les contrôles sélectionnés doivent être satisfaits (*AND*). 
- Un seul contrôle sélectionné doit être satisfait (*OR*).

![Contrôle](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Authentification multifacteur

Vous pouvez utiliser ce contrôle pour exiger une authentification multifacteur pour accéder à l’application cloud spécifiée. Ce contrôle prend en charge les fournisseurs multifacteur suivants : 

- Azure Multi-Factor Authentication 

- Un fournisseur d’authentification multifacteur local combiné avec les services de fédération Active Directory (AD FS).
 
L’authentification multifacteur contribue à empêcher tout accès à vos ressources par un utilisateur non autorisé qui peut avoir obtenu l’accès aux informations d’identification principales d’un utilisateur valide.



### <a name="compliant-device"></a>Conformité de l’appareil

Vous pouvez configurer des stratégies d’accès conditionnel au niveau de l’appareil. L’objectif d’une stratégie d’accès conditionnel appareil par appareil est de n’accorder l’accès aux ressources configurées qu’aux appareils de confiance. Vous pouvez par exemple exiger un appareil conforme, ce qui implique de définir ce qu’est un appareil de confiance. Pour plus d’informations, consultez [Configurer des stratégies d’accès conditionnel au niveau de l’appareil Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Appareil joint à un domaine

Pour configurer des stratégies d’accès conditionnel au niveau de l’appareil, vous pouvez également exiger un appareil joint à un domaine. Cette exigence fait référence aux tablettes professionnelles, ordinateurs portables et postes de travail Windows joints à une instance sur site d’Active Directory. Pour plus d’informations, consultez [Configurer des stratégies d’accès conditionnel au niveau de l’appareil Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Application cliente approuvée

Étant donné que vos employés utilisent des appareils mobiles pour les tâches personnelles et professionnelles, vous pouvez souhaiter avoir la possibilité de protéger les données d’entreprise accessibles à l’aide d’appareils même si ce n’est pas vous qui les gérez.
Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise, quelle que soit votre solution de gestion des appareils mobiles (MDM).


Avec les applications clientes approuvées, vous pouvez exiger qu’une application cliente qui tente d’accéder à vos applications cloud prenne en charge des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy). Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook. Une stratégie d’accès conditionnel qui requiert des applications clientes approuvées est aussi appelée [stratégie d’accès conditionnel basé sur les applications](active-directory-conditional-access-mam.md). Pour obtenir la liste des applications clientes approuvées prises en charge, consultez [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Conditions d’utilisation

Vous pouvez demander à un utilisateur dans votre locataire de donner son consentement pour les conditions d’utilisation avant de lui accorder l’accès à une ressource. En tant qu’administrateur, vous pouvez configurer et personnaliser les conditions d’utilisation en chargeant un document PDF. Si un utilisateur est soumis à ce contrôle, il est autorisé à accéder à une application uniquement s’il a accepté les conditions d’utilisation. 


### <a name="custom-controls"></a>Contrôles personnalisés 

Vous pouvez créer des contrôles personnalisés dans l’accès conditionnel qui redirigent les utilisateurs vers un service compatible pour satisfaire à d’autres exigences en dehors d’Azure Active Directory. Cela vous permet d’utiliser certains fournisseurs de vérification et d’authentification multifacteur externes pour appliquer des règles d’accès conditionnel ou pour créer votre propre service personnalisé. Pour satisfaire à ce contrôle, le navigateur de l’utilisateur est redirigé vers le service externe, effectue les opérations d’authentification et de vérification requises, puis est redirigé vers Azure Active Directory. Si l’utilisateur a été correctement authentifié ou vérifié, il continue dans le flux d’accès conditionnel. 

## <a name="custom-controls"></a>Contrôles personnalisés

Les contrôles personnalisés sont une fonctionnalité de l’édition Premium P2 d’Azure Active Directory. Quand vous utilisez les contrôles personnalisés, les utilisateurs sont redirigés vers un service compatible pour satisfaire à d’autres exigences en dehors d’Azure Active Directory. Pour satisfaire à ce contrôle, le navigateur de l’utilisateur est redirigé vers le service externe, effectue les opérations d’authentification et de vérification requises, puis est redirigé vers Azure Active Directory. Azure Active Directory vérifie la réponse. Si l’utilisateur a été correctement authentifié ou vérifié, il continue dans le flux d’accès conditionnel.

Ces contrôles permettent l’utilisation de certains services externes ou personnalisés comme contrôles d’accès conditionnel. Ils étendent généralement les fonctionnalités de l’accès conditionnel.

Les fournisseurs suivants offrent un service compatible :

- [Duo Security](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Pour plus d’informations sur ces services, contactez directement les fournisseurs.

### <a name="creating-custom-controls"></a>Création de contrôles personnalisés

Pour créer un contrôle personnalisé, commencez par contacter le fournisseur que vous souhaitez utiliser. Les fournisseurs autres que Microsoft ont chacun leurs propres processus et exigences que vous devez suivre pour vous inscrire, vous abonner ou devenir membre du service, et pour demander à intégrer l’accès conditionnel. Le fournisseur vous fournit alors un bloc de données au format JSON. Ces données permettent au fournisseur et à l’accès conditionnel de fonctionner ensemble pour votre locataire, créent le contrôle et définissent de quelle façon l’accès conditionnel indique si vos utilisateurs ont réussi la vérification auprès du fournisseur.

Copiez les données JSON et collez-les dans la zone de texte correspondante. Ne modifiez pas les données JSON, à moins d’être parfaitement sûr de la modification que vous apportez. Une modification inappropriée peut rompre la liaison entre le fournisseur et Microsoft, et potentiellement verrouiller l’accès à votre compte et aux comptes de vos utilisateurs.

L’option pour créer un contrôle personnalisé se trouve dans la section **Gérer** de la page **Accès conditionnel**.

![Contrôle](./media/active-directory-conditional-access-controls/82.png)

Cliquez sur **Nouveau contrôle personnalisé** pour ouvrir un panneau contenant une zone de texte réservée aux données JSON de votre contrôle.  


![Contrôle](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Suppression de contrôles personnalisés

Pour supprimer un contrôle personnalisé, vous devez d’abord vous assurer qu’il n’est pas utilisé dans une stratégie d’accès conditionnel. Quand vous avez terminé :

1. Accédez à la liste de contrôles personnalisés

2. Cliquez sur...  

3. Sélectionnez **Supprimer**.

### <a name="editing-custom-controls"></a>Modification des contrôles personnalisés

Pour modifier un contrôle personnalisé, vous devez supprimer le contrôle existant et créer un autre contrôle avec les nouvelles informations.




## <a name="session-controls"></a>Contrôles de session

Les contrôles de session permettent de limiter l’expérience dans une application cloud. Les contrôles de session sont appliqués par les applications cloud et s’appuient sur des informations supplémentaires fournies par Azure AD à l’application concernant la session.

![Contrôle](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Utiliser les restrictions appliquées par l’application

Vous pouvez utiliser ce contrôle pour exiger qu’Azure AD transmette les informations d’appareil à l’application cloud. Cela permet à l’application cloud de savoir si l’utilisateur utilise un appareil conforme ou un appareil joint à un domaine. Ce contrôle est actuellement pris en charge uniquement avec SharePoint en comme application cloud. SharePoint utilise les informations d’appareil pour fournir aux utilisateurs une expérience limitée ou complète en fonction de l’état de l’appareil.
Pour savoir plus en détails comment exiger un accès limité avec SharePoint, consultez la page [Contrôler l’accès à partir d’appareils non gérés](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
