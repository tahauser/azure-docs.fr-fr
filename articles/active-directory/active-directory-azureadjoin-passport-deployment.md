---
title: Activer Microsoft Windows Hello Entreprise dans votre organisation | Microsoft Docs
description: "Instructions de déploiement pour activer Microsoft Passport dans votre organisation."
services: active-directory
documentationcenter: 
keywords: "configurer le déploiement Microsoft Passport, Microsoft Windows Hello Entreprise"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Activer Microsoft Windows Hello Entreprise dans votre organisation
Après avoir [connecté les appareils du domaine Windows 10 à Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), procédez comme suit pour activer Microsoft Windows Hello Entreprise dans votre organisation :

1. Déployer System Center Configuration Manager  
2. Configuration des paramètres de stratégie
3. Configurer le profil de certificat  

## <a name="deploy-system-center-configuration-manager"></a>Déployer System Center Configuration Manager
Pour déployer des certificats utilisateur en fonction des clés de Windows Hello Entreprise, vous avez besoin des ressources suivantes :

* **System Center Configuration Manager Current Branch** - Vous devez installer la version 1606 ou une version ultérieure. Pour plus d’informations, consultez [Documentation de System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) et [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infrastructure à clé publique (PKI)** : pour activer Microsoft Windows Hello Entreprise à l’aide de certificats utilisateur, vous devez disposer d’une infrastructure PKI. Si vous n’en avez pas ou si vous ne souhaitez pas l’utiliser pour les certificats utilisateur, vous pouvez déployer un nouveau contrôleur de domaine disposant de Windows Server 2016 version 10551 (ou version ultérieure). Suivez les étapes pour [installer un contrôleur de domaine réplica dans un domaine existant](https://technet.microsoft.com/library/jj574134.aspx) ou [installer une nouvelle forêt Active Directory, si vous créez un nouvel environnement](https://technet.microsoft.com/library/jj574166). (Les fichiers ISO sont disponibles en téléchargement sur [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Configuration des paramètres de stratégie
Pour configurer les paramètres de stratégie de Microsoft Windows Hello Entreprise, vous avez deux options :

* Stratégie de groupe dans Active Directory 
* System Center Configuration Manager 

Il est conseillé d’utiliser la stratégie de groupe dans Active Directory pour configurer les paramètres de stratégie de Microsoft Windows Hello Entreprise. 

Il est conseillé d’utiliser System Center Configuration Manager lorsque vous l’utilisez également pour déployer des certificats. Ce scénario :

* Garantit la compatibilité avec les scénarios de déploiement plus récents
* Requiert Windows 10 Version 1607 ou version ultérieure du côté client.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurer Microsoft Windows Hello Entreprise via la stratégie de groupe dans Active Directory
**Étapes** :

1. Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2. Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine dans lequel vous voulez activer Rejoindre Azure AD.
3. Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Donnez un nom à votre objet de stratégie de groupe, par exemple Activer Windows Hello Entreprise. Cliquez sur **OK**.
4. Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5. Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Windows Hello Entreprise**.
6. Avec le bouton droit, cliquez sur **Activer Windows Hello Entreprise**, puis sélectionnez **Modifier**.
7. Sélectionnez l’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8. Vous pouvez maintenant lier l’objet de stratégie de groupe à l’emplacement de votre choix. Pour activer cette stratégie pour tous les appareils joints à un domaine Windows 10 de votre organisation, liez la stratégie de groupe au domaine. Par exemple : 
   * Une unité d’organisation spécifique dans Active Directory où les ordinateurs Windows 10 joints au domaine seront situés
   * Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine qui sera inscrit automatiquement auprès d’Azure AD

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurer Windows Hello Entreprise à l’aide de System Center Configuration Manager
**Étapes** :

1. Ouvrez **System Center Configuration Manager**, puis accédez à **Actifs et compatibilité > Paramètres de conformité > Accès aux ressources de l’entreprise > Profils Windows Hello Entreprise**.
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Dans la barre d’outils en haut, cliquez sur **Créer un profil Windows Hello Entreprise**.
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Dans la boîte de dialogue **Général** , procédez comme suit :
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. Dans la zone de texte **Nom**, entrez un nom pour votre profil, par exemple, **Mon profil WHfB**.
   
    b. Cliquez sur **Suivant**.
4. Dans la boîte de dialogue **Plateformes prises en charge**, sélectionnez les plateformes qui seront configurées avec ce profil Windows Hello Entreprise, puis cliquez sur **Suivant**.
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Dans la boîte de dialogue **Paramètres** , procédez comme suit :
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Pour **Configurer Windows Hello Entreprise**, sélectionnez **Activé**.
   
    b. Pour **Utiliser un module de plateforme sécurisée (TPM)**, sélectionnez **Obligatoire**. 
   
    c. En tant que **Méthode d’authentification**, sélectionnez **Basée sur un certificat**.
   
    d. Cliquez sur **Suivant**.
6. Sur la page **Résumé**, cliquez sur **Suivant**.
7. Dans la boîte de dialogue **Exécution terminée**, cliquez sur **Fermer**.
8. Dans la barre d’outils en haut, cliquez sur **Déployer**.
   
    ![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Configurer le profil de certificat
Si vous utilisez l’authentification basée sur un certificat pour l’authentification locale, vous devez configurer et déployer un profil de certificat. Pour ce faire, vous devez configurer un serveur NDES et le rôle de site Point d’enregistrement de certificat dans System Center Configuration Manager. Pour plus d’informations, consultez [Configuration requise pour les profils de certificat dans Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Ouvrez **System Center Configuration Manager**, puis accédez à **Actifs et compatibilité > Paramètres de conformité > Accès aux ressources de l’entreprise > Profils de certificat**.
2. Sélectionnez un modèle qui dispose d’une ouverture de session avec carte à puce EKU.

Sur la page **Enregistrement SCEP** du profil de certificat, vous devez choisir **Installer dans Passport for Work, sinon mettre en échec** en tant que **Fournisseur de stockage de clés**.

## <a name="next-steps"></a>Étapes suivantes
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [S’authentifier sans mots de passe](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

