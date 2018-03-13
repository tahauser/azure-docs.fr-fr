---
title: "Accès conditionnel Azure Active Directory pour la connectivité VPN (préversion) | Microsoft Docs"
description: "Découvrez le fonctionnement de l’accès conditionnel Azure Active Directory pour la connectivité VPN. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Accès conditionnel Azure Active Directory pour la connectivité VPN (préversion)

Avec [l’accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez préciser la manière dont les utilisateurs autorisés accèdent aux ressources. L’accès conditionnel Azure AD pour la connectivité de réseau privé virtuel (VPN) vous aide à protéger vos connexions VPN.


Afin de configurer l’accès conditionnel pour la connectivité VPN, vous devez effectuer les étapes suivantes : 

1.  Configurez votre serveur VPN.
2.  Configurez votre client VPN.
3.  Configurez votre stratégie d’accès conditionnel.


## <a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez les sujets suivants :

- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN et accès conditionnel](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Pour savoir comment Microsoft a implémenté cette fonctionnalité, consultez [Amélioration de l’accès à distance dans Windows 10 avec un profil VPN automatique](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).   


## <a name="prerequisites"></a>configuration requise

Pour configurer l’accès conditionnel Azure Active Directory pour la connectivité VPN, vous devez disposer d’un serveur VPN configuré. 



## <a name="step-1-configure-your-vpn-server"></a>Étape 1 : Configurer votre serveur VPN 

Dans cette étape, vous allez configurer des certificats racines pour l’authentification VPN auprès d’Azure AD. Pour configurer l’accès conditionnel pour la connectivité VPN, vous devez :

1. Créer un certificat VPN dans le portail Azure
2. Télécharger le certificat VPN
2. Déployer le certificat sur votre serveur VPN

Azure AD utilise le certificat VPN pour signer les certificats émis pour les clients Windows 10 lors de l’authentification auprès d’Azure AD pour la connectivité VPN. Le jeton demandé par le client Windows 10 est un certificat qui est ensuite présenté à l’application, qui dans le cas présent, est le serveur VPN.

![Télécharger le certificat pour l’accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Dans le portail Azure, vous pouvez créer deux certificats pour gérer la transition quand un certificat est sur le point d’expirer. Lorsque vous créez un certificat, vous pouvez le désigner comme certificat principal, et l’utiliser lors de l’authentification pour signer le certificat dans le cadre de la connexion.

Pour créer un certificat VPN :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Dans le menu de gauche, cliquez sur **Azure Active Directory**. 

    ![Sélectionnez Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Sélection de l’option Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Dans la page **Accès conditionnel**, dans la section **Gérer**, cliquez sur **Connectivité VPN (préversion)**.

    ![Sélection de l’option Connectivité VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Dans la page **Connectivité VPN**, cliquez sur **Nouveau certificat**.

    ![Sélection de l’option Nouveau certificat](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Dans la page **Nouveau**, effectuez les étapes suivantes :

    ![Sélection des options Sélectionner une durée et Principal](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Pour **Sélectionner une durée**, sélectionnez **1 an**.

    b. Pour **Principal**, sélectionnez **Oui**.

    c. Cliquez sur **Créer**.

7. Dans la page de connectivité VPN, cliquez sur **Télécharger le certificat**.


Vous êtes désormais prêt à déployer votre nouveau certificat sur votre serveur VPN. Sur votre serveur VPN, ajoutez le certificat téléchargé comme une *autorité de certification racine de confiance pour l’authentification VPN*.

Pour les déploiements Windows basés sur RRAS, vous devez ajouter le certificat racine dans le magasin *Enterprise NTauth* sur votre serveur NPS, en exécutant les commandes suivantes :

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Étape 2 : Configurer votre client VPN 

Dans cette étape, vous configurez votre profil de connectivité client VPN comme indiqué dans [VPN et accès conditionnel](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Étape 3 : Configurer votre stratégie d’accès conditionnel

Cette section explique comment configurer votre stratégie d’accès conditionnel pour la connectivité VPN.


1. Dans la page **Accès conditionnel**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![Sélection de l’option Ajouter dans la page Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Dans la page **Nouveau**, tapez le nom de votre stratégie dans la zone **Nom**. Par exemple, tapez **stratégie VPN**.

    ![Ajout du nom de la stratégie dans la page Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Sélection de l’option Utilisateurs et groupes](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Dans la page **Utilisateurs et groupes**, effectuez les étapes suivantes :

    ![Sélection de l’utilisateur test](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez votre utilisateur de test, puis cliquez sur **Sélectionner**.

    d. Dans la page **Utilisateurs et groupes**, cliquez sur **Terminé**.

7. Dans la page **Nouveau**, effectuez les étapes suivantes :

    ![Sélection des applications cloud](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Dans la section **Affectations**, cliquez sur **Applications cloud**.

    b. Dans la page **Applications cloud**, cliquez sur **Sélectionner les applications**, puis sur **Sélectionner**.

    c. Dans la page **Sélectionner**, tapez **vpn** dans la zone **Applications**.

    d. Sélectionnez **Serveur VPN**.

    e. Cliquez sur **Sélectionner**.


13. Dans la page **Nouveau**, pour ouvrir la page **Octroyer**, cliquez sur **Octroyer** dans la section **Contrôles**.

    ![Sélection de l’option Accorder](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Dans la page **Octroyer**, effectuez les étapes suivantes :

    ![Sélection de l’option Exiger une authentification multifacteur](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Sélectionnez **Exiger une authentification multifacteur**.

    b. Cliquez sur **Sélectionner**.

15. Dans la page **Nouveau**, sous **Activer la stratégie**, cliquez sur **Activé**.

    ![Activer la stratégie](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Dans la page **Nouveau**, cliquez sur **Créer**.



## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment Microsoft a implémenté cette fonctionnalité, consultez [Amélioration de l’accès à distance dans Windows 10 avec un profil VPN automatique](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).    

