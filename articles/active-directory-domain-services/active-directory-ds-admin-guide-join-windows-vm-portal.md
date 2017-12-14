---
title: "Version préliminaire des services de domaine Azure Active Directory : associer une machine virtuelle Windows Server à un domaine géré | Microsoft Docs"
description: Joindre une machine virtuelle Windows Server aux services de domaine Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 1ea3f7271bd165bf42d520e4a0267a80dcca58d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle Windows Server à un domaine géré
Cet article explique comment déployer une machine virtuelle Windows Server à l’aide du portail Azure. Il indique également comment joindre une machine à un domaine managé Azure AD Domain Services.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Étape 1 : créer la machine virtuelle Windows Server
Pour créer une machine virtuelle Windows jointe au réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD, procédez comme suit :

1. Connectez-vous au Portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com).
2. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
3. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**.

    ![Sélectionner une image](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Configurez les paramètres de base de la machine virtuelle sur la page **Concepts de base** de l’assistant.

    ![Configurer les paramètres de base des machines virtuelles](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Le nom d’utilisateur et le mot de passe saisis ici concernent le compte d’administrateur local utilisé pour la connexion à la machin virtuelle. Choisissez un mot de passe robuste pour protéger la machine virtuelle contre les attaques de mot de passe par force brute. Ne saisissez pas les informations d’identification associées à un compte d’utilisateur de domaine ici.
    >

5. Sélectionnez une valeur de **taille** pour la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**.

    ![Sélectionner la taille de la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Sur la page**Paramètres** de l’assistant, sélectionnez le réseau virtuel dans lequel le domaine Azure AD Domain Services managé doit être déployé. Choisissez un sous-réseau différent de celui dans lequel le domaine managé est déployé. Conservez les valeurs par défaut des autres paramètres et cliquez sur **OK**.

    ![Sélectionner un réseau virtuel pour la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Choisissez les sous-réseau et réseau virtuel adaptés.**
    > Sélectionnez le réseau virtuel que dans lequel est déployé votre domaine managé, ou un réseau virtuel qui lui est connecté à l’aide de l’homologation de réseau virtuel. Si vous sélectionnez un réseau virtuel non connecté, vous ne pouvez pas joindre la machine virtuelle au domaine managé.
    > Nous vous recommandons de déployer votre domaine managé dans un sous-réseau dédié. Par conséquent, ne choisissez pas le sous-réseau dans lequel vous avez activé votre domaine managé.

7. Sur la page **Achat**, vérifiez les paramètres et cliquez sur **OK** pour déployer la machine virtuelle.
8. Le déploiement de cette machine est épinglé au tableau de bord du portail Azure.

    ![Terminé](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. Une fois le déploiement terminé, vous pouvez afficher des informations sur la machine virtuelle sur la page **Vue d’ensemble**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Étape 2 : connexion à la machine virtuelle Windows Server à l’aide du compte d’administrateur local
À présent, connectez-vous à la nouvelle machine virtuelle Windows Server pour la joindre au domaine. Utilisez les informations d’identification de l’administrateur local que vous avez spécifiées lors de la création de la machine virtuelle.

Pour vous connecter à la machine virtuelle, procédez comme suit :

1. Cliquez sur le bouton **Connecter** de la page **Vue d’ensemble**. Un fichier de protocole Remote Desktop Protocol (.rdp) est créé et téléchargé.

    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**.
3. À l’invite de connexion, entrez vos **informations d’identification d’administrateur local**que vous avez spécifiées lors de la création de la machine virtuelle. Nous avons utilisé « localhost\mahesh » dans cet exemple.
4. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur Oui ou Continuer pour poursuivre le processus de connexion.

À ce stade, vous devez être connecté à la nouvelle machine virtuelle Windows à l’aide des informations d’identification de l’administrateur local. L’étape suivante consiste à joindre la machine virtuelle au domaine.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Étape 3 : joindre la machine virtuelle Windows Server au domaine géré par les services de domaine Azure AD
Pour joindre la machine virtuelle Windows Server au domaine géré par les services de domaine Azure AD, procédez comme suit :

1. Connectez-vous à la machine virtuelle Windows Server, comme indiqué à l’étape 2. Dans l’écran d’accueil, ouvrez **Gestionnaire de serveur**.
2. Cliquez sur l’option **Serveur local** dans le volet gauche de la fenêtre Gestionnaire de serveur.

    ![Lancer le gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Cliquez sur **GROUPE DE TRAVAIL** dans la section **PROPRIÉTÉS**. Dans la page de propriétés **Propriétés système**, cliquez sur **Modifier** pour joindre le domaine.

    ![Page Propriétés système](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Spécifiez le nom du domaine géré par les services de domaine Azure Active Directory dans la zone de texte **Domaine**, puis cliquez sur **OK**.

    ![Spécifier le domaine à joindre](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Vous devez entrer vos informations d’identification pour joindre le domaine. Veillez à **spécifier les informations d’identification d’un utilisateur appartenant au groupe « AAD DC Administrators »** . Seuls les membres de ce groupe disposent de privilèges suffisants pour pouvoir joindre des ordinateurs au domaine géré.

    ![Spécifier les informations d’identification pour la jonction au domaine](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Vous pouvez spécifier les informations d’identification de l’une des manières suivantes :

   * **Format UPN** (recommandé) : spécifiez le suffixe UPN du compte d’utilisateur, tel qu’il est configuré dans Azure AD. Dans cet exemple, le suffixe UPN de l’utilisateur « bob » est bob@domainservicespreview.onmicrosoft.com.
   * **Format SAMAccountName** : vous pouvez spécifier le nom du compte au format SAMAccountName. Dans cet exemple, l’utilisateur « bob » doit saisir « CONTOSO100\bob ».

     > [!TIP]
     > **Nous conseillons d’utiliser le format UPN pour spécifier les informations d’identification.**
     > La valeur SAMAccountName peut être générée automatiquement si le préfixe UPN d’un utilisateur est anormalement long (par exemple, « joereallylongnameuser »). Si, au sein de votre locataire Azure AD, plusieurs utilisateurs présentent le même préfixe UPN (par exemple, « bob »), le format SAMAccountName peut être généré automatiquement par le service. Dans ce cas, le format UPN peut être utilisé pour assurer une connexion fiable au domaine.
     >

7. Une fois que la jonction au domaine a abouti, le message suivant s’affiche afin de vous accueillir dans le domaine. Redémarrez la machine virtuelle pour terminer la jonction au domaine.

    ![Bienvenue dans le domaine](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
### <a name="connectivity-issues"></a>Problèmes de connectivité
Si la machine virtuelle ne peut pas trouver le domaine, consultez les étapes suivantes pour résoudre le problème :

* Vérifiez que la machine virtuelle est connectée au réseau virtuel au sein duquel vous avez activé les services de domaine. Si tel n’est pas le cas, la machine virtuelle ne peut pas se connecter au domaine, et la jonction est donc impossible.
* Vérifiez que la machine virtuelle est reliée à un réseau virtuel lui-même connecté à celui dans lequel vous avez activé Domain Services.
* Effectuez un test Ping du domaine en utilisant le nom du domaine géré (par exemple, « ping contoso100.com »). Si vous n’y parvenez pas, envoyez une commande Ping aux adresses IP du domaine affichées sur la page sur laquelle vous avez activé les services de domaine Azure Active Directory (par exemple, « ping 10.0.0.4 »). Si le test de l’adresse IP aboutit et non celui du domaine, il se peut que la fonction DNS ne soit pas correctement configurée. Vérifiez que les adresses IP du domaine sont configurées en tant que serveurs DNS du réseau virtuel.
* Essayez de vider le cache de résolution DNS sur la machine virtuelle (« ipconfig /flushdns »).

Si une boîte de dialogue s’affiche, vous demandant de saisir vos informations d’identification pour joindre le domaine, cela peut être le signe de problèmes de connectivité.

### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification
Reportez-vous à la procédure suivante si vous rencontrez des problèmes concernant les informations d’identification et que vous ne parvenez pas à joindre le domaine.

* Essayez d’utiliser le format UPN pour spécifier les informations d’identification. S’il existe plusieurs utilisateurs présentant le même préfixe UPN sur votre locataire, ou si votre préfixe UPN est trop long, la valeur SAMAccountName de votre compte peut être générée automatiquement. Par conséquent, le format SAMAccountName de votre compte peut différer de ce à quoi vous vous attendiez ou de ce que vous utilisez dans votre domaine local.
* Essayez d’utiliser les informations d’identification d’un compte d’utilisateur qui appartient au groupe « AAD DC Administrators ».
* Assurez-vous d'avoir [activé la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.
* Veillez à utiliser l’UPN de l’utilisateur tel que configuré dans Azure AD (par exemple, bob@domainservicespreview.onmicrosoft.com) pour vous connecter.
* Vérifiez que vous avez bien attendu la fin de la synchronisation des mots de passe, comme indiqué dans le guide de prise en main.

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
