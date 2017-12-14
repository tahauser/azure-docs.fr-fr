---
title: "Azure Active Directory Domain Services : déployer le Proxy d’application Azure Active Directory | Microsoft Docs"
description: "Utiliser le proxy d'application sur les domaines managés par les Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: bd79644c6a13ccaab8eb7e14131465cc7c3165d3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Déployer le proxy d'application sur un domaine managé par les Azure Active Directory Domain Services
Le service Proxy d’application Azure Active Directory (AD) vous aide à prendre en charge les personnes qui travaillent à distance en publiant des applications locales afin de les rendre accessibles sur Internet. Avec les Azure AD Domain Services, vous pouvez désormais transférer des applications héritées vers des services d’infrastructure Azure. Vous pouvez ensuite publier ces applications à l’aide du proxy d’application Azure AD pour fournir un accès à distance sécurisé aux utilisateurs de votre organisation.

Si vous n’êtes pas familiarisé avec le proxy d’application Azure AD, consultez l’article suivant : [Offrir un accès à distance sécurisé aux applications locales](../active-directory/active-directory-application-proxy-get-started.md).


## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :

1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. Une **licence Azure AD de base ou Premium** est nécessaire pour utiliser le proxy d’application Azure AD.
4. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tâche 1 : activer le proxy d’application Azure AD pour votre annuaire Azure AD
Procédez comme suit pour activer le proxy d’application Azure AD pour votre annuaire Azure AD.

1. Connectez-vous au [portail Azure](http://portal.azure.com) en tant qu’administrateur.

2. Cliquez sur **Azure Active Directory** pour afficher la vue d’ensemble de l’annuaire. Cliquez sur **Applications d’entreprise**.

    ![Sélectionnez un annuaire Azure AD.](./media/app-proxy/app-proxy-enable-start.png)
3. Cliquez sur **Proxy d’application**. Si vous n’avez pas d’abonnement Azure AD de base ou Premium, vous voyez une option permettant d'activer une version d’évaluation. Définissez **Activer le proxy d'application ?** sur **Activer**, puis cliquez sur **Enregistrer**.

    ![Activer le Proxy d’application](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Pour télécharger le connecteur, cliquez sur le bouton **Connecteur**.

    ![Télécharger le connecteur](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Sur la page de téléchargement, acceptez les termes du contrat de licence et l'accord de confidentialité, puis cliquez sur le bouton **Télécharger**.

    ![Confirmer le téléchargement](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tâche 2 : configurer des serveurs Windows joints à un domaine pour déployer le connecteur de proxy d’application Azure AD
Vous avez besoin de machines virtuelles Windows Server jointes à un domaine sur lesquelles vous pouvez installer le connecteur de proxy d’application Azure AD. Pour certaines applications, vous pouvez choisir d'approvisionner plusieurs serveurs sur lesquels le connecteur est installé. Cette option de déploiement vous donne une plus grande disponibilité et vous permet de traiter des charges d’authentification plus importantes.

Configurez les serveurs de connecteur sur le même réseau virtuel (ou un réseau virtuel connecté/homologué) dans lequel vous avez activé votre domaine managé Azure AD Domain Services. De même, les serveurs hébergeant les applications que vous publiez par le biais du proxy d’application doivent être installés sur le même réseau virtuel Azure.

Pour approvisionner les serveurs de connecteur, suivez les tâches décrites dans l’article [Joindre une machine virtuelle Windows Server à un domaine géré](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tâche 3 : installer et inscrire le connecteur du proxy d'application Azure AD
Auparavant, vous approvisionniez une machine virtuelle Windows Server et la joigniez au domaine managé. Dans cette tâche, vous allez installer le connecteur de proxy d’application Azure AD sur cette machine virtuelle.

1. Copiez le package d’installation du connecteur sur la machine virtuelle sur laquelle vous installez le connecteur de proxy d’application web Azure AD.

2. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur la machine virtuelle. Acceptez les termes du contrat de licence logicielle.

    ![Accepter les termes du contrat pour l’installation](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Au cours de l’installation, vous êtes invité à inscrire le connecteur auprès du service de proxy d’application de votre annuaire Azure AD.
    * Fournissez vos **informations d’identification d’administrateur général d’Azure AD**. Votre client d’administrateur global peut être différent de vos informations d’identification Microsoft Azure.
    * Le compte d'administrateur utilisé pour inscrire le connecteur doit figurer dans l’annuaire dans lequel vous avez activé le service Proxy d’application. Par exemple, si le domaine du locataire est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias valide sur ce domaine.
    * Si l’option Configuration de sécurité renforcée d’Internet Explorer est activée sur le serveur sur lequel vous installez le connecteur, l’écran d’inscription risque d’être bloqué. Suivez les instructions du message d’erreur pour autoriser l’accès. Vérifiez que la configuration de sécurité renforcée d’Internet Explore est désactivée.
    * Si l’inscription du connecteur n’aboutit pas, voir [Résoudre les problèmes du proxy d’application](../active-directory/active-directory-application-proxy-troubleshoot.md).

    ![Connecteur installé](./media/app-proxy/app-proxy-connector-installed.png)
4. Pour garantir un fonctionnement correct du connecteur, exécutez l'utilitaire de résolution des problèmes du connecteur de proxy Azure AD. Après l'exécution de l'utilitaire de résolution des problèmes, vous devez voir un rapport de réussite.

    ![Réussite de l'utilitaire de résolution des problèmes](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Vous devez voir le connecteur nouvellement installé sur la page Proxy d’application dans votre annuaire Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Vous pouvez choisir d’installer les connecteurs sur plusieurs serveurs pour garantir une haute disponibilité pour l’authentification des applications publiées par le biais du proxy d’application Azure AD. Effectuez les étapes répertoriées ci-dessus pour installer le connecteur sur d’autres serveurs joints à votre domaine managé.
>
>

## <a name="next-steps"></a>Étapes suivantes
Vous avez configuré le proxy d’application Azure AD et l'avez intégré à votre domaine managé Azure AD Domain Services.

* **Migrer vos applications vers des machines virtuelles Azure :** vous pouvez transférer vos applications depuis des serveurs locaux vers des machines virtuelles jointes à votre domaine managé. Cela vous permet de supprimer les coûts de fonctionnement de l’infrastructure de serveurs en local.

* **Publier des applications à l’aide du proxy d’application Azure AD :** publiez des applications exécutées sur vos machines virtuelles à l’aide du proxy d’application Azure AD. Pour plus d'informations, consultez [Publier des applications avec le proxy d'application Azure AD](../active-directory/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Remarque relative au déploiement - Publier des applications IWA (authentification Windows intégrée) à l’aide du proxy d’application Azure AD
Activez l’authentification unique pour vos applications avec l’authentification Windows intégrée en attribuant aux connecteurs du proxy d’application l’autorisation d’emprunter l’identité des utilisateurs et d’envoyer et de recevoir des jetons en leur nom. Configurez la délégation Kerberos contrainte (KCD) pour le connecteur afin d’accorder les autorisations nécessaires pour accéder aux ressources sur le domaine managé. Utilisez le mécanisme KCD basé sur la ressource sur des domaines managés pour renforcer la sécurité.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Activer la délégation Kerberos contrainte basée sur la ressource pour le connecteur de proxy d’application Azure AD
Le connecteur de proxy d’application Azure doit être configuré pour la délégation Kerberos contrainte (KCD) pour pouvoir emprunter l’identité des utilisateurs sur le domaine managé. Sur un domaine managé Azure AD Domain Services, vous n’avez pas les privilèges d’administrateur de domaine. Par conséquent, **la KCD traditionnelle au niveau des comptes ne peut pas être configurée sur un domaine managé**.

Utilisez plutôt la KCD basée sur la ressource, comme décrit dans cet [article](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Vous devez être membre du groupe « AAD DC Administrators » pour administrer le domaine managé à l'aide d'applets de commande AD PowerShell.
>
>

Utilisez l’applet de commande PowerShell Get-ADComputer pour récupérer les paramètres de l’ordinateur sur lequel est installé le connecteur de proxy d’application Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Par la suite, utilisez l’applet de commande Set-ADComputer pour configurer la KCD basée sur la ressource pour le serveur de ressources.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Si vous avez déployé plusieurs connecteurs de proxy d’application sur votre domaine managé, vous devez configurer la KCD basée sur la ressource pour chacune de ces instances du connecteur.


## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Configurer la délégation Kerberos contrainte sur un domaine managé](active-directory-ds-enable-kcd.md)
* [Présentation de la délégation Kerberos contrainte](https://technet.microsoft.com/library/jj553400.aspx)
