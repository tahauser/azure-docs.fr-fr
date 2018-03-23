---
title: 'Authentification directe Azure AD : Démarrage rapide | Microsoft Docs'
description: Cet article explique comment commencer à utiliser l’authentification directe d’Azure Active Directory (Azure AD).
services: active-directory
keywords: Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: b592eb8ca43e5bf3eebe2b0c47d8f17dbec7b238
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Authentification directe Azure Active Directory : Démarrage rapide

## <a name="deploy-azure-ad-pass-through-authentication"></a>Déployer l’authentification directe Azure AD

L’authentification directe Azure Active Directory (Azure AD) permet à vos utilisateurs de se connecter aux applications sur site et aux applications cloud à l’aide des mêmes mots de passe. L'authentification directe connecte les utilisateurs en validant leurs mots de passe directement par rapport à l'annuaire Active Directory sur site.

>[!IMPORTANT]
>Si vous utilisez cette fonctionnalité à l'aide d'une version préliminaire, veillez à mettre à niveau les versions préliminaires des agents d'authentification en suivant les instructions fournies dans [Azure Active Directory Connect : authentification directe - mise à niveau de la version préliminaire des agents d’authentification](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Procédez comme suit pour déployer l’authentification directe :

## <a name="step-1-check-the-prerequisites"></a>Étape 1 : Vérifier les prérequis

Vérifiez que les prérequis suivants sont remplis.

### <a name="in-the-azure-active-directory-admin-center"></a>Dans le Centre d’administration Azure Active Directory

1. Créez un compte d’administrateur général « cloud uniquement » dans votre locataire Azure AD. De cette façon, vous pouvez gérer la configuration de votre locataire si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle si vous voulez éviter de vous retrouver en dehors de votre locataire.
2. Ajoutez un ou plusieurs [noms de domaine personnalisés](../active-directory-domains-add-azure-portal.md) à votre locataire Azure AD. Vos utilisateurs peuvent se connecter à l’aide de l’un de ces noms de domaine.

### <a name="in-your-on-premises-environment"></a>Dans votre environnement local

1. Identifiez un serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter Azure AD Connect. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont vous devez valider les mots de passe.
2. Installez la [version la plus récente d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) sur le serveur identifié à l’étape précédente. Si vous exécutez déjà Azure AD Connect, vérifiez que la version est 1.1.644.0 ou ultérieure.

    >[!NOTE]
    >Les versions 1.1.557.0, 1.1.558.0, 1.1.561.0 et 1.1.614.0 d’Azure AD Connect comportent un problème lié à la synchronisation de hachage de mot de passe. Si vous _ne prévoyez pas_ d’utiliser la synchronisation de hachage de mot de passe en même temps que l’authentification directe, lisez les [Notes de publication Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identifiez un autre serveur (Windows Server 2012 R2 ou ultérieur) sur lequel vous pouvez exécuter l’agent d’authentification autonome. La version de l’agent d’authentification doit être 1.5.193.0 ou ultérieure. Ce serveur supplémentaire est nécessaire pour garantir une haute disponibilité des demandes de connexion. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont vous devez valider les mots de passe.
4. S’il existe un pare-feu entre vos serveurs et Azure AD, configurez les éléments suivants :
   - Assurez-vous que les agents d’authentification peuvent effectuer des requêtes *sortantes* vers Azure AD sur les ports suivants :
   
    | Numéro de port | Utilisation |
    | --- | --- |
    | **80** | Télécharge les listes de révocation de certificats lors de la validation du certificat SSL |
    | **443** | Gère toutes les communications sortantes avec le service |
   
    Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
   - Si votre pare-feu ou votre proxy autorise la mise en liste verte de DNS, vous pouvez y placer les connexions vers **\*.msappproxy.net** et **\*.servicebus.windows.net**. Dans le cas contraire, autorisez l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.
   - Vos agents d’authentification doivent accéder à **login.windows.net** et à **login.microsoftonline.net** pour l’inscription initiale. Par conséquent, ouvrez également votre pare-feu pour ces URL.
   - Pour valider le certificat, débloquez les URL suivantes : **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** et **www.microsoft.com:80**. Ces URL sont utilisées pour la validation de certificat avec d’autres produits Microsoft. Ces URL seront peut-être déjà débloquées.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Étape 2 : Activer la prise en charge d’Exchange ActiveSync (facultatif)

Suivez ces instructions pour activer la prise en charge d’Exchange ActiveSync :

1. Utilisez [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) pour exécuter la commande suivante :
```
Get-OrganizationConfig | fl per*
```

2. Vérifiez la valeur du paramètre `PerTenantSwitchToESTSEnabled`. Si la valeur est **true**, votre client est correctement configuré. C'est généralement le cas pour la plupart des clients. Si la valeur est **false**, exécutez la commande suivante :
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Vérifiez que la valeur du paramètre `PerTenantSwitchToESTSEnabled` est désormais définie sur **true**. Patientez une heure avant de passer à l’étape suivante.

Si vous rencontrez des problèmes au cours de cette étape, consultez le [guide de dépannage](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Étape 3 : Activer la fonctionnalité

Activez l’authentification directe via [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Vous pouvez activer l’authentification directe sur le serveur principal ou sur un serveur intermédiaire Azure AD Connect. Vous devez l'activer depuis le serveur principal.

Si vous installez Azure AD Connect pour la première fois, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Dans la page **Connexion utilisateur**, choisissez **Authentification directe** comme **méthode d’authentification**. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect. La fonctionnalité d’authentification directe est également activée sur votre locataire.

![Azure AD Connect : connexion utilisateur](./media/active-directory-aadconnect-sso/sso3.png)

Si vous avez déjà installé Azure AD Connect à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md), sélectionnez la tâche **Modifier la connexion utilisateur** dans Azure AD Connect, puis cliquez sur **Suivant**. Ensuite, sélectionnez **Authentification directe** comme mode d’authentification. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect et la fonctionnalité est activée sur votre locataire.

![Azure AD Connect : Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L’authentification directe est une fonctionnalité au niveau du locataire. Son activation affecte la connexion des utilisateurs dans _tous_ les domaines gérés dans votre locataire. Si vous passez d'Active Directory Federation Services (ADFS) à l'authentification directe, vous devez attendre au moins 12 heures avant de fermer votre infrastructure AD FS. Ce délai d'attente permet aux utilisateurs de continuer à se connecter à Exchange ActiveSync pendant la transition.

## <a name="step-4-test-the-feature"></a>Étape 4 : Tester la fonctionnalité

Suivez ces instructions pour vérifier que vous avez activé correctement l’authentification directe :

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) à l’aide des informations d’identification d’administrateur général de votre locataire.
2. Sélectionnez **Active Directory** dans le volet de gauche.
3. Sélectionnez ensuite **Azure AD Connect**.
4. Vérifiez que la fonctionnalité **Authentification directe** est **activée**.
5. Sélectionnez **Authentification directe**. Le volet **Authentification directe** répertorie les serveurs sur lesquels vos agents d’authentification sont installés.

![Centre d’administration Azure Active Directory : volet Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centre d’administration Azure Active Directory : volet Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

À ce stade, les utilisateurs de tous les domaines gérés de votre locataire peuvent se connecter à l’aide de l’authentification directe. Toutefois, les utilisateurs des domaines fédérés continuent à se connecter à l’aide d'AD FS ou d’un autre fournisseur de fédération précédemment configuré. Si vous convertissez un domaine fédéré en domaine géré, tous les utilisateurs de ce domaine se connectent alors automatiquement à l’aide de l’authentification directe. La fonctionnalité Authentification directe n’a pas d’incidence sur les utilisateurs cloud uniquement.

## <a name="step-5-ensure-high-availability"></a>Étape 5 : Garantir une haute disponibilité

Si vous envisagez de déployer l’authentification directe dans un environnement de production, vous devez installer au moins un agent d’authentification autonome. Installez ces agents d’authentification sur des serveurs _autres_ que celui qui exécute Azure AD Connect. Cette configuration fournit une haute disponibilité pour les requêtes de connexion des utilisateurs.

Pour télécharger l’agent d’authentification, effectuez les étapes suivantes :

1. Pour télécharger la dernière version de l’agent d’authentification (version 1.5.193.0 ou ultérieure), connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) avec les droits d’administrateur général de votre locataire.
2. Sélectionnez **Active Directory** dans le volet de gauche.
3. Sélectionnez **Azure AD Connect**, **Authentification directe**, puis **Télécharger l'agent**.
4. Cliquez sur le bouton **Accepter les conditions et télécharger**.

![Centre d’administration Azure Active Directory : bouton Télécharger Agent d’authentification](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centre d’administration Azure Active Directory : volet Télécharger l’agent](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Vous pouvez également télécharger directement l’agent d’authentification [ici](https://aka.ms/getauthagent). Lisez et acceptez les [conditions de service](https://aka.ms/authagenteula) de l’agent d’authentification _avant_ de l’installer.

Il existe deux méthodes pour déployer un agent d’authentification autonome :

Tout d’abord, vous pouvez le faire interactivement en exécutant le fichier exécutable de l’agent d’authentification téléchargé et en fournissant les informations d’identification d’administrateur général de votre locataire lorsque vous y êtes invité.

La deuxième solution consiste à créer et à exécuter un script de déploiement sans assistance. C’est utile lorsque vous souhaitez déployer plusieurs agents d’authentification à la fois, ou installer des agents d’authentification sur des serveurs Windows qui ne disposent pas d’une interface utilisateur, ou auxquels vous ne pouvez pas accéder avec le Bureau à distance. Voici des instructions pour utiliser cette méthode :

1. Exécutez la commande suivante pour installer un agent d’authentification : `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Vous pouvez inscrire l’agent d’authentification auprès de notre service à l’aide de Windows PowerShell. Créez un objet d’informations d’identification PowerShell `$cred` qui contient un nom d’utilisateur et un mot de passe d’administrateur général pour votre locataire. Exécutez la commande suivante, en remplaçant *\<username\>* et *\<password\>* :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Accédez à **C:\Program Files\Microsoft Azure AD Connect Authentication Agent** et exécutez le script suivant en utilisant l’objet `$cred` que vous venez de créer :
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Étapes suivantes
- [Verrouillage intelligent](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : guide pratique pour configurer la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [Limitations actuelles](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge avec l'authentification directe et ceux qui ne le sont pas.
- [Présentation technique approfondie](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne l'authentification directe.
- [Forum aux questions](active-directory-aadconnect-pass-through-authentication-faq.md) : trouvez des réponses aux questions fréquemment posées.
- [Résoudre les problèmes](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants liés à la fonctionnalité d’authentification directe.
- [Présentation approfondie de sécurité](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : obtenez des informations techniques sur l’authentification directe.
- [Authentification unique transparente Azure AD](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : utilisez le Forum Azure Active Directory pour consigner de nouvelles demandes de fonctionnalités.

