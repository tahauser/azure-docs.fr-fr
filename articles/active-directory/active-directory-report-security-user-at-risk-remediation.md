---
title: "Rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory | Microsoft Docs"
description: "En savoir plus sur le rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ed6201e9edcef39b14b948b6b2f6e0b5da01ec60
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Résoudre des utilisateurs avec indicateur de risque dans le portail Azure Active Directory

Grâce aux rapports sur la sécurité dans Azure Active Directory (Azure AD), vous pouvez obtenir des informations sur les risques de compromission des comptes d’utilisateur au sein de votre environnement. Un [utilisateur signalé comme présentant un risque](active-directory-identityprotection.md#users-flagged-for-risk) indique qu’un compte d’utilisateur est susceptible d’avoir été compromis.

Microsoft s’engage à vous aider à conserver un environnement protégé. Dans le cadre de cet engagement, Microsoft surveille continuellement les activités inhabituelles ou qui correspondent à des modèles d’attaque connus. 


Si des activités inhabituelles indiquant un accès non autorisé à des comptes de vos utilisateurs sont détectées, vous recevez des notifications qui vous permettent d’agir. Le fait que Microsoft vous envoie des notifications ne signifie pas que ses systèmes ont été compromis d’une quelconque façon.
 

## <a name="azure-active-directory-report-access"></a>Rapport d’accès Azure Active Directory

Vous pouvez analyser les utilisateurs signalés comme présentant un risque par le biais d’un rapport en ligne Azure Active Directory. Si vous n’êtes pas abonné à Azure, vous pouvez vous inscrire gratuitement à l’adresse [http://aka.ms/AccessAAD](http://aka.ms/AccessAAD).  
Une fois inscrit, vous pouvez utiliser vos informations d’identification Office 365 pour accéder au centre d’administration Azure. Remarque : au niveau d’inscription de base, la quantité d’informations fournies est limitée. Des analyses et données supplémentaires sont disponibles pour les abonnés Azure Premium. Pour en savoir plus, consultez le [rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory](active-directory-reporting-security-user-at-risk.md).

Une fois votre accès à Azure AD activé, vous êtes redirigé vers le [portail Azure AD](https://portal.azure.com). Pour accéder directement au rapport, suivez l’URL suivante : [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk).

**Pour accéder aux rapports sur les utilisateurs avec indicateur de risque dans le centre d’administration Office 365 :**

1.  À gauche du menu de navigation, cliquez sur **Centres d’administration**. 
2.  Cliquez sur **Azure AD**.
3.  Connectez-vous au **centre d’administration Azure Active Directory**.
4.  Si une bannière s’affiche en haut de la page, indiquant **Vérifier le nouveau portail**, cliquez sur le lien.
4.  Dans le menu de navigation gauche, cliquez sur **Azure Active Directory**. 
5.  Dans le volet de navigation, sous **Sécurité**, cliquez sur **Utilisateurs avec indicateur de risque**.

Analysez les informations affichées. Vous devez réinitialiser le mot de passe de chaque compte listé. 

## <a name="remediation-actions"></a>Actions de correction

Effectuez les actions suivantes pour aider à rectifier les comptes affectés et à sécuriser votre environnement :

1.  [Validez](http://aka.ms/MFAValid) les informations correctes pour l’authentification multifacteur et la réinitialisation de mot de passe libre-service. 
2.  [Activez](http://aka.ms/MFAuth) l’authentification multifacteur (MFA) pour tous les utilisateurs. 
3.  Avec ce [script de correction](http://aka.ms/remediate), pour chaque compte affecté, vous pouvez automatiquement effectuer les étapes suivantes : 

    a. Réinitialisez le mot de passe pour sécuriser le compte et éliminez les sessions actives.

    b. Supprimez les délégués de boîte aux lettres.

    c. Désactivez les règles d’envoi de courrier à des domaines externes.

    d. Supprimez la propriété d’envoi de courrier global de la boîte aux lettres.

    e. Activez l’authentification multifacteur (MFA) du compte de l’utilisateur.

    f. Définissez la complexité du mot de passe du compte sur « élevée ».

    g. Activez l’audit de la boîte aux lettres.

    h. Générez le journal d’audit pour que l’administrateur puisse l’analyser.

4. Dans votre abonné Office 365 et autres infrastructure informatique, analysez tous les paramètres d’abonné, comptes d’utilisateurs et les paramètres de configuration par utilisateur et cherchez toute modification éventuelle. Vérifiez les indicateurs de méthodes ou de persistance, ainsi que tout signe qu’un intrus aurait pu mettre la main sur des informations d’identification VPN, ou accéder à d’autres ressources organisationnelles. 

5.  Lors de votre enquête, vous devrez décider si vous devez ou devriez avertir les autorités gouvernementales, concernant les lois en vigueur.

De plus, vous devez :

- Lire et implémenter ce [conseil](http://aka.ms/fixaccount) sur la gestion d’activités inhabituelles. 
- [Activer le pipeline d’audit](http://aka.ms/improvesecurity) pour vous aider à analyser l’activité sur votre abonnement. Une fois fini, votre banque d’informations d’audit commence à remplir tous les journaux d’activités. À ce stade, vous êtes également capable de tirer profit de la [recherche et enquête du centre de sécurité et de conformité](http://aka.ms/sccsearch). 
- Utilisez ce [script](http://aka.ms/mailboxaudit1) pour activer l’audit de la boîte aux lettres pour tous vos comptes. 
- Vérifiez les autorisations des délégués et les règles d’envoi de courrier pour toutes vos boîtes aux lettres. Vous pouvez utiliser ce [script PowerShell](http://aka.ms/delegateforwardrules) pour réaliser cette tâche. 



## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Azure Active Directory Identity Protection, voir [Protection de l’identité Azure Active Directory](active-directory-identityprotection.md).

