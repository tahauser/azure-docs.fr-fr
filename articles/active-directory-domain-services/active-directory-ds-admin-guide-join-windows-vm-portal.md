---
title: "Version préliminaire des services de domaine Azure Active Directory : associer une machine virtuelle Windows Server à un domaine géré | Microsoft Docs"
description: "Joindre une machine virtuelle Windows Server à Azure AD DS"
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
ms.openlocfilehash: 7b5c23f1f4b6180d8b664f1371ccfd8a075572e6
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle Windows Server à un domaine géré
Cet article explique comment déployer une machine virtuelle Windows Server à l’aide du portail Azure. Il indique également comment joindre une machine à un domaine managé Azure Active Directory Domain Services.

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Étape 1 : créer une machine virtuelle Windows Server
Pour créer une machine virtuelle Windows jointe au réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD DS, procédez comme suit :

1. Connectez-vous au [Portail Azure](http://portal.azure.com).
2. En haut du volet gauche, sélectionnez **Nouveau**.
3. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**.

    ![Lien vers Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Dans le volet **Concepts de base** de l’assistant, configurez les paramètres de base de la machine virtuelle.

    ![Volet Concepts de base](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Le nom d’utilisateur et le mot de passe saisis ici concernent le compte d’administrateur local utilisé pour se connecter à la machine virtuelle. Choisissez un mot de passe robuste pour protéger la machine virtuelle contre les attaques de mot de passe par force brute. Ne saisissez pas les informations d’identification associées à un compte d’utilisateur de domaine ici.
    >

5. Sélectionnez une valeur de **taille** pour la machine virtuelle. Pour afficher plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**.

    ![Volet Choisir une taille](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Dans le volet **Paramètres** de l’assistant, sélectionnez le réseau virtuel dans lequel le domaine Azure AD DS managé doit être déployé. Choisissez un sous-réseau différent de celui dans lequel le domaine managé est déployé. Conservez les valeurs par défaut des autres paramètres et sélectionnez **OK**.

    ![Paramètres réseau virtuel de la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Choisissez les sous-réseau et réseau virtuel adaptés.**
    >
    > Sélectionnez le réseau virtuel que dans lequel est déployé votre domaine managé, ou un réseau virtuel qui lui est connecté à l’aide de l’homologation de réseau virtuel. Si vous sélectionnez un réseau virtuel non connecté, vous ne pouvez pas joindre la machine virtuelle au domaine managé.
    >
    > Nous vous recommandons de déployer votre domaine managé dans un sous-réseau dédié. Par conséquent, ne choisissez pas le sous-réseau dans lequel vous avez activé votre domaine managé.

7. Conservez les valeurs par défaut des autres paramètres et sélectionnez **OK**.
8. Sur la page **Achat**, vérifiez les paramètres et sélectionnez **OK** pour déployer la machine virtuelle.
9. Le déploiement de cette machine est épinglé au tableau de bord du portail Azure.

    ![Terminé](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Une fois le déploiement terminé, vous pouvez afficher des informations sur la machine virtuelle sur la page **Vue d’ensemble**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Étape 2 : connexion à la machine virtuelle Windows Server à l’aide du compte d’administrateur local
Ensuite, connectez-vous à la nouvelle machine virtuelle Windows Server pour la joindre au domaine. Utilisez les informations d’identification de l’administrateur local que vous avez spécifiées lorsque vous avez créé la machine virtuelle.

Pour vous connecter à la machine virtuelle, procédez comme suit :

1. Dans le volet **Vue d’ensemble** du laboratoire, sélectionnez **Connexion**.  
    Un fichier de protocole Remote Desktop Protocol (.rdp) est créé et téléchargé.

    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
3. À l’invite d’ouverture de session, entrez vos **informations d’identification d’administrateur local**que vous avez spécifiées lors de la création de la machine virtuelle (par exemple, *localhost\mahesh*).
4. Si vous recevez un avertissement de certificat lors du processus de connexion, poursuivez la connexion en sélectionnant **Oui** ou **Continuer**.

À ce stade, vous devez être connecté à la nouvelle machine virtuelle Windows avec les informations d’identification de l’administrateur local. L’étape suivante consiste à joindre la machine virtuelle au domaine.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Étape 3 : joindre la machine virtuelle Windows Server au domaine Azure AD DS managé
Pour joindre la machine virtuelle Windows Server au domaine Azure AD DS managé, procédez comme suit :

1. Connectez-vous à la machine virtuelle Windows Server, comme indiqué à l’étape 2. Dans l’écran **Démarrer**, ouvrez **Gestionnaire de serveur**.
2. Dans le volet gauche de la fenêtre **Gestionnaire de serveur**, cliquez sur l’option **Serveur local**.

    ![Fenêtre Gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Sous **Propriétés**, sélectionnez **Groupe de travail**. 
4. Dans la fenêtre **Propriétés système**, sélectionnez **Modifier** pour joindre le domaine.

    ![Fenêtre Propriétés système](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Dans la zone**Domaine**, spécifiez le nom de votre domaine Azure AD DS managé, puis sélectionnez **OK**.

    ![Spécifier le domaine à joindre](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Vous devez entrer vos informations d’identification pour joindre le domaine. Veillez à spécifier les informations d’identification d’un *utilisateur appartenant au groupe d’administrateurs Azure AD DC*. Seuls les membres de ce groupe disposent de privilèges suffisants pour pouvoir joindre des ordinateurs au domaine géré.

    ![Fenêtre Sécurité Windows pour spécifier les informations d’identification](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Vous pouvez spécifier les informations d’identification de l’une des manières suivantes :

   * **Format UPN** : (recommandé) spécifiez le suffixe du nom de l’utilisateur principal (UPN) pour le compte d’utilisateur, tel qu’il est configuré dans Azure AD. Dans cet exemple, le suffixe UPN de l’utilisateur *bob* est *bob@domainservicespreview.onmicrosoft.com*.

   * **Format SAMAccountName** : vous pouvez spécifier le nom du compte au format SAMAccountName. Dans cet exemple, l’utilisateur *bob* doit saisir *CONTOSO100\bob*.

     > [!TIP]
     > **Nous conseillons d’utiliser le format UPN pour spécifier les informations d’identification.**
     >
     > La valeur SAMAccountName peut être générée automatiquement si le préfixe UPN d’un utilisateur est anormalement long (par exemple, *joehasareallylongname*). Si, au sein de votre locataire Azure AD, plusieurs utilisateurs présentent le même préfixe UPN (par exemple, *bob*), le format SAMAccountName peut être généré automatiquement par le service. Dans ce cas, le format UPN peut être utilisé pour assurer une connexion fiable au domaine.
     >

8. Une fois que vous avez réussi à joindre le domaine, le message suivant vous accueille dans le domaine.

    ![Bienvenue dans le domaine](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Pour terminer la jonction du domaine, redémarrez la machine virtuelle.

## <a name="troubleshoot-joining-a-domain"></a>Résoudre les problèmes lors de la jonction d’un domaine
### <a name="connectivity-issues"></a>Problèmes de connectivité
Si la machine virtuelle ne peut pas trouver le domaine, essayez une ou plusieurs actions suivantes :

* Vérifiez que la machine virtuelle est connectée au réseau virtuel au sein duquel vous avez activé Azure AD DS. Si tel n’est pas le cas, la machine virtuelle ne peut pas se connecter au domaine, et la jonction est donc impossible.

* Vérifiez que la machine virtuelle est reliée à un réseau virtuel lui-même connecté à celui dans lequel vous avez activé Azure AD DS.

* Effectuez un test Ping du domaine en utilisant le nom du domaine managé (par exemple, *ping contoso100.com*). Si vous n’y parvenez pas, envoyez une commande Ping aux adresses IP du domaine affichées sur la page sur laquelle vous avez activé Azure AD DS (par exemple, *ping 10.0.0.4*). Si le test de l’adresse IP aboutit et non celui du domaine, il se peut que la fonction DNS ne soit pas correctement configurée. Vérifiez que les adresses IP du domaine sont configurées en tant que serveurs DNS du réseau virtuel.

* Essayez de vider le cache de résolution DNS sur la machine virtuelle (*ipconfig /flushdns*).

Si une fenêtre s’affiche, vous demandant de saisir vos informations d’identification pour joindre le domaine, c’est que vous n’avez pas de problèmes de connectivité.

### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification
Si vous rencontrez des problèmes concernant les informations d’identification et que vous ne parvenez pas à joindre le domaine, essayez une ou plusieurs actions suivantes :

* Essayez d’utiliser le format UPN pour spécifier les informations d’identification. S’il existe plusieurs utilisateurs présentant le même préfixe UPN sur votre locataire, ou si votre préfixe UPN est trop long, la valeur SAMAccountName de votre compte peut être générée automatiquement. Par conséquent, le format SAMAccountName de votre compte peut différer de ce à quoi vous vous attendiez ou de ce que vous utilisez dans votre domaine local.

* Essayez d’utiliser les informations d’identification d’un compte d’utilisateur qui appartient au groupe *AAD DC Administrators*.

* Veillez à [activer la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md), conformément aux étapes décrites dans le guide de démarrage.

* Veillez à utiliser l’UPN de l’utilisateur tel que configuré dans Azure AD (par exemple, *bob@domainservicespreview.onmicrosoft.com*) pour vous connecter.

* Vérifiez que vous avez bien attendu la fin de la synchronisation des mots de passe, comme indiqué dans le guide de démarrage.

## <a name="related-content"></a>Contenu connexe
* [Guide de démarrage Azure AD DS](active-directory-ds-getting-started.md)
* [Administrer un domaine Azure AD DS managé](active-directory-ds-admin-guide-administer-domain.md)
