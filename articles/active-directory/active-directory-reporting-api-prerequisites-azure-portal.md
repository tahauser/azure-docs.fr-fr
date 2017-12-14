---
title: "Configuration requise pour accéder à l’API de création de rapports Azure AD | Microsoft Docs"
description: "En savoir plus sur la configuration requise pour accéder à l’API de création de rapports Azure AD"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1f4d79b59d44bc1c151391290e79026051fe1df
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Configuration requise pour accéder à l’API de création de rapports Azure AD

Les [API de création de rapports Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès aux API web. 

Pour accéder aux données de rapports via l’API, vous devez avoir un des rôles suivants :

- Lecteur de sécurité
- Administrateur de la sécurité
- Administrateur général


Pour préparer votre accès à l’API de création de rapports, vous devez :

1. Inscrire une application 
2. Accorder des autorisations 
3. Rassembler les paramètres de configuration 

Si vous avez des questions, des problèmes ou des commentaires, [créez un ticket de support](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Inscrire une application Azure Active Directory

Vous devez inscrire une application, même si vous accédez à l’API de création de rapports à l’aide d’un script. Vous obtenez ainsi un **ID d’application**, qui est requis pour un appel d’autorisation et permet à votre code de recevoir des jetons.

Pour configurer votre annuaire et lui permettre d’accéder à l’API de création de rapports Azure AD, vous devez vous connecter au portail Azure avec un compte d’administrateur Azure, également membre du rôle d’annuaire **Administrateur général** dans votre locataire Azure AD.

> [!IMPORTANT]
> Les applications qui s’exécutent sous les informations d’identification pourvues de privilèges d’administrateur de ce type peuvent être très puissantes. Veillez donc à sécuriser les informations d’identification d’ID/du secret de l’application.
> 


**Pour inscrire une application Azure Active Directory :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions des applications**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Dans le panneau **Inscriptions des applications**, dans la barre d’outils en haut, cliquez sur **Nouvelle inscription d’application**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Dans le panneau **Créer**, procédez comme suit :

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Dans la zone de texte **Nom**, tapez `Reporting API application`.

    b. Sous **Type d’application**, sélectionnez **Application web/API**.

    c. Dans la zone de texte **URL d’authentification**, tapez `https://localhost`.

    d. Cliquez sur **Créer**. 


## <a name="grant-permissions"></a>Accorder des autorisations 

L’objectif de cette étape consiste à accorder à votre application l’autorisation **Lire les données de l’annuaire** pour l’API **Windows Azure Active Directory**.

![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Pour autoriser l’utilisation de l’API par votre application :**

1. Dans le panneau **Inscriptions des applications**, dans la liste des applications, cliquez sur **Reporting API application (Application API de création de rapports)**.

2. Dans le panneau **Reporting API application (Application API de création de rapports)**, dans la barre d’outils en haut, cliquez sur **Paramètres**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Dans le panneau **Paramètres**, cliquez sur **Autorisations requises**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Dans le panneau **Autorisations requises**, cliquez la liste **API**, puis sur **Microsoft Azure Active Directory**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Dans le panneau **Activer l’accès**, sélectionnez **Lire les données de l’annuaire**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Dans la barre d’outils située en haut, cliquez sur **Enregistrer**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Cliquez sur **Accorder des autorisations** puis cliquez sur **Oui**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Rassembler les paramètres de configuration 
Cette section vous montre comment obtenir les paramètres suivants à partir de votre annuaire :

* Nom de domaine
* ID client
* Clé secrète client

Ces valeurs sont nécessaires lors de la configuration des appels à l’API de création de rapports. 

### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine

**Pour obtenir votre nom de domaine :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans le panneau **Azure Active Directory**, cliquez sur **Noms de domaine personnalisés**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copiez votre nom de domaine à partir de la liste des domaines.


### <a name="get-your-applications-client-id"></a>Obtenir l’ID client de l’application

**Pour obtenir l’ID client de votre application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans le panneau **Inscriptions des applications**, dans la liste des applications, cliquez sur **Reporting API application (Application API de création de rapports)**.

3. Dans le panneau **Reporting API application (Application API de création de rapports)**, dans **ID d’application**, cliquez sur **Cliquer pour copier**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obtenir la clé secrète client de l’application
Pour obtenir la clé secrète client de l’application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé car il est impossible de récupérer cette valeur ultérieurement.

**Pour obtenir la clé secrète client de l’application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans le panneau **Inscriptions des applications**, dans la liste des applications, cliquez sur **Reporting API application (Application API de création de rapports)**.


3. Dans le panneau **Reporting API application (Application API de création de rapports)**, dans la barre d’outils en haut, cliquez sur **Paramètres**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Dans le panneau **Paramètres**, dans la section **APIR Access (Accès APIR)**, cliquez sur **Clés**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Dans le panneau **Clés**, procédez comme suit :

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Dans la zone de texte **Description**, tapez `Reporting API`.

    b. Dans **Expire le**, sélectionnez **Dans 2 ans**.

    c. Cliquez sur **Enregistrer**.

    d. Copiez la valeur de la clé.


## <a name="next-steps"></a>Étapes suivantes
* Vous souhaitez accéder aux données de l’API de création de rapports Azure AD par programme ? Consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si vous souhaitez en savoir plus sur la création de rapports Azure Active Directory, consultez le [Guide Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  

