---
title: "Conditions d’utilisation d’Azure Active Directory | Microsoft Docs"
description: "Les conditions d’utilisation d’Azure AD vous permettent, ainsi qu’à votre entreprise, de définir les conditions d’utilisation que doivent accepter les utilisateurs des services Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2018
ms.author: billmath
ms.openlocfilehash: e29e842a54d286e19d23c1c91c08d55933a0eada
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Fonctionnalité Conditions d’utilisation d’Azure Active Directory (préversion)
La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux.  Cela permet de garantir qu’ils se voient présenter les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Le contenu proposé par la fonctionnalité Conditions d’utilisation d’Azure AD est au format PDF.   Libre à vous de choisir le contenu de ce PDF. Vous pouvez par exemple utiliser le document d’un contrat existant, ce qui vous permet de collecter les contrats des utilisateurs finaux quand ils se connectent.  Vous pouvez utiliser les conditions d’utilisation pour des applications ou des groupes d’utilisateurs. Vous pouvez aussi avoir plusieurs conditions d’utilisation pour des finalités différentes.

Le reste de ce document explique comment utiliser les conditions d’utilisation d’Azure AD.  

## <a name="why-use-azure-ad-terms-of-use"></a>À quoi servent les conditions d’utilisation d’Azure AD ?
Vous avez des difficultés à faire accepter vos conditions d’utilisation à vos employés ou à vos invités avant de leur octroyer l’accès à vos applications ? Vous avez besoin d’aide pour savoir qui a accepté les conditions d’utilisation de l’entreprise et qui les a refusées ?  La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux.  Cela permet de garantir qu’ils se voient présenter les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Vous pouvez utiliser la fonctionnalité Conditions d’utilisation d’Azure Active Directory dans les scénarios suivants :
-   Conditions d’utilisation générales pour tous les utilisateurs de votre organisation.
-   Conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des [groupes dynamiques](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Conditions d’utilisation spécifiques en fonction de l’accès aux applications à fort impact commercial, comme Salesforce.


## <a name="prerequisites"></a>Prérequis

Pour configurer les conditions d’utilisation d’Azure AD, effectuez les étapes suivantes :

1. Connectez-vous à Azure AD en tant qu’administrateur général, administrateur de la sécurité ou administrateur à accès conditionnel de l’annuaire pour lequel vous souhaitez configurer les conditions d’utilisation d’Azure AD.
2. Vérifiez que l’annuaire est associé à un abonnement Azure AD Premium P1, P2, EMS E3 ou EMS E5.  Si ce n’est pas le cas, [abonnez-vous à Azure AD Premium](active-directory-get-started-premium.md) ou [optez pour un essai](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Affichez le tableau de bord des conditions d’utilisation d’Azure AD à l’adresse [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Les contrôles de stratégie d’accès conditionnel (y compris les conditions d’utilisation) ne peuvent pas être appliqués sur les comptes de service.  Nous vous recommandons d’exclure tous les comptes de service de la stratégie d’accès conditionnel.

## <a name="add-company-terms-of-use"></a>Ajouter les conditions d’utilisation de l’entreprise
Après avoir finalisé vos conditions d’utilisation, effectuez les étapes suivantes pour les ajouter.

### <a name="to-add-terms-of-use"></a>Pour ajouter des conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou).
2. Cliquez sur Ajouter.</br>
![Ajouter des conditions d’utilisation](media/active-directory-tou/tou12.png)
3. Entrer le **nom** des conditions d’utilisation
4. Entrez **Nom d’affichage**.  Cet en-tête correspond à ce que les utilisateurs voient quand ils se connectent.
5. **Accédez** au fichier PDF finalisé de vos conditions et sélectionnez-le.  Il est recommandé d’utiliser une taille de police de 24.
6. **Sélectionnez** la langue des conditions d’utilisation.  L’option de langue vous permet de charger plusieurs conditions d’utilisation, chacune dans une langue différente.  La version des conditions d’utilisation affichée pour l’utilisateur final dépend des préférences de son navigateur.
7. Activez ou désactivez l’option **Demander aux utilisateurs d’étendre les conditions d’utilisation**.  Si cette option est activée, les utilisateurs finaux sont priés d’afficher les conditions d’utilisation avant de les accepter.
8. Dans la section **Accès conditionnel**, vous pouvez **appliquer** les conditions d’utilisation chargées à l’aide d’un modèle ou d’une stratégie d’accès conditionnel personnalisée.  Les stratégies d’accès conditionnel personnalisées permettent de définir des conditions d’utilisation plus précises, jusqu’au niveau d’une application cloud ou d’un groupe d’utilisateurs.  Pour plus d’informations, consultez [Configuration de stratégies d’accès conditionnel personnalisées](active-directory-conditional-access-best-practices.md).
9. Cliquez sur **Créer**.
10. Si vous avez sélectionné un modèle d’accès conditionnel personnalisé, un nouvel écran s’affiche pour vous permettre de personnaliser la stratégie d’accès conditionnel.
11. Vos nouvelles conditions d’utilisation doivent désormais s’afficher.</br>

![Ajouter des conditions d’utilisation](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Supprimer des conditions d’utilisation
Pour supprimer d’anciennes conditions d’utilisation, effectuez les étapes suivantes :

### <a name="to-delete-terms-of-use"></a>Pour supprimer des conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou).
2. Sélectionnez les conditions d’utilisation à supprimer.
3. Cliquez sur **Supprimer**.
4. Vos nouvelles conditions d’utilisation doivent désormais ne plus s’afficher.


## <a name="audit-terms-of-use"></a>Auditer les conditions d’utilisation
La fonctionnalité Conditions d’utilisation d’Azure AD offre un outil d’audit convivial à l’aide duquel vous pouvez voir qui a accepté vos conditions d’utilisation et quand.  Pour démarrer l’audit, effectuez les étapes suivantes :

### <a name="to-audit-terms-of-use"></a>Pour auditer les conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou).
2. Cliquez sur Événement d’audit.</br>
![Événement d’audit](media/active-directory-tou/tou8.png)
3.  Sur l’écran des journaux d’audit Azure AD, vous pouvez filtrer les informations à l’aide des menus déroulants pour cibler des éléments spécifiques du journal d’audit.
![Événement d’audit](media/active-directory-tou/tou9.png)
4.  Vous pouvez également télécharger les informations dans un fichier .csv en vue d’une utilisation locale.

## <a name="what-users-see"></a>Ce que voient les utilisateurs
Les utilisateurs dans l’étendue voient ce qui suit une fois les conditions d’utilisation créées et appliquées.  Ces écrans s’affichent durant la procédure de connexion.
-   Il est recommandé d’utiliser une police de taille 24 dans le document PDF.
![Événement d’audit](media/active-directory-tou/tou10.png)
-   L’écran apparaît comme suit sur les mobiles.</br></br>
![Événement d’audit](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Vérifier les conditions d’utilisation
Vous pouvez consulter et vérifier les conditions d’utilisation qu’ils ont acceptées.  Pour ce faire, procédez comme suit :

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil** dans la liste déroulante.
![Profil](media/active-directory-tou/tou14.png)

3. Dans votre profil, cliquez sur **Vérifier les conditions d’utilisation**.
![Événement d’audit](media/active-directory-tou/tou13a.png)

4.  C’est là que vous pouvez vérifier les conditions d’utilisation que vous avez acceptées. 


## <a name="additional-information"></a>Informations supplémentaires
Tenez compte des points suivants pour faciliter la mise en place de conditions d’utilisation.

Les utilisateurs dans l’étendue doivent se déconnecter et se reconnecter pour satisfaire aux exigences d’une nouvelle stratégie si :
 - une stratégie d’accès conditionnel est activée sur les conditions d’utilisation ;
 - une deuxième instance des conditions d’utilisation est créée.

Cela vient du fait que les stratégies d’accès conditionnel prennent effet immédiatement. Dans ce cas, l’administrateur commence à voir des « clouds tristes » ou des « problèmes liés aux jetons Azure AD ». L’administrateur doit se déconnecter et se reconnecter pour satisfaire aux exigences de la nouvelle stratégie.





## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Comment déterminer si un utilisateur a accepté les conditions d’utilisation et quand ?**</br>
R : Un utilisateur qui accepte les conditions d’utilisation est consigné dans le journal d’audit. Vous pouvez rechercher le journal d’audit Azure AD pour voir les résultats.  

**Q : Si vous changez les conditions d’utilisation, les utilisateurs doivent-ils les accepter à nouveau ?**</br>
R : Oui. Si un administrateur change les conditions d’utilisation, les utilisateurs doivent les accepter à nouveau.

**Q : Les conditions d’utilisation prennent-elles en charge plusieurs langues ?**</br>
R. : Oui.  Actuellement, un administrateur peut configurer 18 langues différentes pour une seule condition d’utilisation. 

**Q : À quel moment les conditions d’utilisation sont-elles déclenchées ?**</br>
R : Les conditions d’utilisation sont déclenchées durant la procédure de connexion.

**Q : Quelles sont les applications pouvant être ciblées par des conditions d’utilisation ?**</br>
R : Vous pouvez créer une stratégie d’accès conditionnel sur les applications d’entreprise à l’aide de l’authentification moderne.  Pour plus d’informations, consultez [Applications d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Q : Puis-je ajouter plusieurs conditions d’utilisation à une application ou un utilisateur donné ?**</br>
R : Oui. Pour cela, créez plusieurs stratégies d’accès conditionnel ciblant ces groupes ou applications. Si un utilisateur est dans l’étendue de plusieurs conditions d’utilisation, il accepte une instance des conditions d’utilisation à la fois.
 
**Q : Que se passe-t-il si un utilisateur refuse les conditions d’utilisation ?**</br>
R : L’utilisateur n’a pas accès à l’application. Il doit se reconnecter et accepter les conditions pour se voir octroyer des droits d’accès.