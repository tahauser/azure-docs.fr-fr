---
title: "Azure AD Connect : résolution des problèmes d’authentification directe | Microsoft Docs"
description: "Cet article décrit la résolution des problèmes d’authentification directe d’Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Résolution des problèmes d’authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: billmath
ms.openlocfilehash: b842791be74094c87643528c0b4d3a65be6b3cb1
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Résolution des problèmes d’authentification directe Azure Active Directory

Cet article fournit des informations sur les problèmes courants liés à l’authentification directe Azure AD.

>[!IMPORTANT]
>Si vous êtes confronté à des problèmes de connexion utilisateur avec l’authentification directe, ne désactivez pas la fonctionnalité. En outre, ne désinstallez pas les agents d’authentification directe si vous ne disposez pas d’un compte d’administrateur général cloud comme solution de secours. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle pour éviter que votre locataire ne soit verrouillé.

## <a name="general-issues"></a>Problèmes d’ordre général

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Vérifiez l’état de la fonctionnalité et des agents d’authentification

Assurez-vous que la fonctionnalité d’authentification directe est toujours **activée** sur votre locataire et l’état de l’agent d’authentification indique **actif**et non **inactif**. Vous pouvez vérifier l’état en accédant au panneau **Azure AD Connect** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory - panneau Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centre d’administration Azure Active Directory - panneau Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Message d’erreur lors de la connexion utilisateur

Si l’utilisateur ne peut pas se connecter avec l’authentification directe, l’un des messages d’erreur suivants peut s’afficher sur l’écran de connexion Azure AD : 

|Error|Description|Résolution :
| --- | --- | ---
|AADSTS80001|Impossible de se connecter à Active Directory|Assurez-vous que les serveurs des agents sont membres de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés, et qu’ils peuvent se connecter à Active Directory.  
|AADSTS8002|Délai d’attente dépassé lors de la connexion à Active Directory|Vérifiez qu’Active Directory est disponible et répond aux demandes des agents.
|AADSTS80004|Le nom d’utilisateur envoyé à l’agent n’était pas valide|Vérifiez que l’utilisateur tente de se connecter avec le nom d’utilisateur correct.
|AADSTS80005|La validation a rencontré une WebException imprévisible|Erreur temporaire. relancez la requête. Si l’erreur se reproduit, contactez le Support Microsoft.
|AADSTS80007|Une erreur s’est produite lors de la communication avec Active Directory|Consultez les journaux de l’agent pour plus d’informations, et vérifiez qu’Active Directory fonctionne comme prévu.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Raisons des échecs de connexion dans le Centre d’administration Azure Active Directory (licence Premium requise)

Si votre locataire dispose d’une licence Azure AD Premium, vous pouvez également consulter le [rapport d’activité de connexion](../active-directory-reporting-activity-sign-ins.md) dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory - rapport de connexions](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Accédez à **Azure Active Directory** -> **Connexions** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/), puis cliquez sur une activité de connexion. Recherchez le champ **Code d’erreur de connexion**. Notez la valeur de ce champ et cherchez dans le tableau suivant la raison de l’échec et la solution à appliquer :

|Code d’erreur de connexion|Raison de l’échec de connexion|Résolution :
| --- | --- | ---
| 50144 | Le mot de passe Active Directory de l’utilisateur est arrivé à expiration. | Réinitialisez le mot de passe dans votre instance locale d’Active Directory.
| 80001 | Agents d’authentification non disponibles. | Installez l’agent d’authentification, puis procédez à son inscription.
| 80002 | La demande de validation du mot de passe de l’Agent d’authentification est arrivée à expiration. | Vérifiez que l’agent d’authentification peut accéder à Active Directory.
| 80003 | Réponse non valide reçue par l’Agent d’authentification. | Si le problème se produit pour tous les utilisateurs, vérifiez votre configuration Active Directory.
| 80004 | Nom d’utilisateur principal (UPN) incorrect utilisé dans la demande de connexion. | Demandez à l’utilisateur de se connecter avec le bon nom d’utilisateur.
| 80005 | Agent d’authentification : une erreur s’est produite. | Erreur temporaire. Réessayez ultérieurement.
| 80007 | L’Agent d’authentification ne peut pas se connecter à Active Directory. | Vérifiez que l’agent d’authentification peut accéder à Active Directory.
| 80010 | L’Agent d’authentification ne peut pas déchiffrer le mot de passe. | Si le problème se produit régulièrement, installez un nouvel agent d’authentification, puis inscrivez-le. Veillez à désinstaller l’agent actuel. 
| 80011 | L’Agent d’authentification n’a pas pu récupérer la clé de déchiffrement. | Si le problème se produit régulièrement, installez un nouvel agent d’authentification, puis inscrivez-le. Veillez à désinstaller l’agent actuel.

## <a name="authentication-agent-installation-issues"></a>Problèmes d’installation de l’agent d’authentification

### <a name="an-unexpected-error-occurred"></a>Une erreur inattendue s’est produite

[Collectez les journaux de l’agent](#collecting-pass-through-authentication-agent-logs) à partir du serveur et contactez le Support Microsoft pour lui faire part de votre problème.

## <a name="authentication-agent-registration-issues"></a>Problèmes d’inscription de l’agent d’authentification

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>L’inscription de l’agent a échoué en raison de ports bloqués

Vérifiez que le serveur sur lequel l’agent a été installé peut communiquer avec nos URL de service et les ports répertoriés [ici](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Échec de l’inscription de l’agent d’authentification en raison d’erreurs d’autorisations liées au jeton ou au compte

Veillez à utiliser un compte d’administrateur général cloud pour toutes les opérations d’installation et d’inscription Azure AD Connect ou d’agent d’authentification autonome. Il existe un problème connu avec les comptes d’administrateur général compatibles MFA ; désactivez MFA temporairement (uniquement pour effectuer les opérations) comme solution de contournement.

### <a name="an-unexpected-error-occurred"></a>Une erreur inattendue s’est produite

[Collectez les journaux de l’agent](#collecting-pass-through-authentication-agent-logs) à partir du serveur et contactez le Support Microsoft pour lui faire part de votre problème.

## <a name="authentication-agent-uninstallation-issues"></a>Problèmes de désinstallation de l’agent d’authentification

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Message d’avertissement lors de la désinstallation d’Azure AD Connect

Si l’authentification directe est activée sur votre locataire et que vous tentez de désinstaller Azure AD Connect, le message d’avertissement suivant apparaît : « Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers » (Les utilisateurs ne seront pas en métrique de se connecter, sauf si d’autres agents d’authentification directe sont installés sur d’autres serveurs).

Vous devez avoir configuré la [haute disponibilité](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) avant de désinstaller Azure AD Connect pour éviter d’interrompre la connexion utilisateur.

## <a name="issues-with-enabling-the-feature"></a>Problèmes liés à l’activation de la fonctionnalité

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>L’activation de la fonctionnalité a échoué, car il n’y a aucun agent d’authentification disponible

Vous devez disposer d’au moins un agent d’authentification actif pour activer l’authentification directe sur votre locataire. Vous pouvez installer un agent d’authentification en installant Azure AD Connect ou un agent d’authentification autonome.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>L’activation de la fonctionnalité a échoué en raison de ports bloqués

Assurez-vous que le serveur sur lequel Azure AD Connect est installé peut communiquer avec nos URL de service et les ports répertoriés [ici](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>L’activation de la fonctionnalité a échoué en raison d’erreurs d’autorisations liées au jeton ou au compte

Veillez à utiliser un compte d’administrateur général pour le cloud uniquement lors de l’activation de la fonctionnalité. Il existe un problème connu avec les comptes d’administrateur général compatibles MFA (Multi-Factor Authentication) ; désactivez MFA temporairement (uniquement pour effectuer l’opération) comme solution de contournement.

## <a name="exchange-activesync-configuration-issues"></a>Problèmes de configuration Exchange ActiveSync

Voici les problèmes courants liés à la configuration de la prise en charge Exchange ActiveSync de l’authentification directe.

### <a name="exchange-powershell-issue"></a>Problème lié à Exchange PowerShell

Si le message d’erreur « **Impossible de trouver un paramètre correspondant au nom « PerTenantSwitchToESTSEnabled »\.** » quand vous exécutez la commande Exchange PowerShell `Set-OrganizationConfig`, contactez le support Microsoft.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync ne fonctionne ne pas

La configuration prend un certain temps pour entrer en vigueur : ce délai dépend de votre environnement. Si la situation dure longtemps, contactez le support Microsoft.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Collecte des journaux des agents d’authentification directe

Selon le type de problème rencontré, vous devez regarder à différents emplacements pour trouver les journaux des agents d’authentification directe.

### <a name="azure-ad-connect-logs"></a>Journaux Azure AD Connect

Pour vérifier les erreurs liées à l’installation, consultez les journaux Azure AD Connect à l’emplacement suivant : **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Journaux des événements des agents d’authentification

Pour les erreurs relatives à l’agent d’authentification, ouvrez l’application Observateur d’événements sur le serveur et cherchez dans **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Pour une analytique détaillée, activez le journal de la session. N’exécutez pas l’agent d’authentification lorsque ce journal est activé pendant le fonctionnement normal. Utilisez-le uniquement pour la résolution des problèmes. Le contenu du journal n’est visible qu’une fois celui-ci désactivé.

### <a name="detailed-trace-logs"></a>Journaux de suivi détaillés

Pour résoudre les problèmes d’échec de connexion de l’utilisateur, consultez les journaux de suivi situés à l’emplacement **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Ces journaux incluent les raisons de l’échec de connexion utilisateur à l’aide de la fonctionnalité d’authentification directe. Le [tableau](#sign-in-failure-reasons-on-the-Azure-portal) précédent établit une correspondance entre ces erreurs et les raisons des échecs de connexion. Vous trouverez ci-dessous un exemple d’entrée de journal :

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Vous pouvez obtenir une description détaillée de l’erreur (« 1328 » dans l’exemple ci-dessus) en ouvrant l’invite de commandes et en exécutant la commande suivante (Remarque : remplacez « 1328 » par le numéro d’erreur qui s’affiche dans vos journaux) :

`Net helpmsg 1328`

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Journaux des contrôleurs de domaine

Si la journalisation d’audit est activée, des informations complémentaires se trouvent dans les journaux de sécurité des contrôleurs de domaine. Vous pouvez interroger les demandes de connexion envoyées par les agents d’authentification directe en utilisant la méthode suivante :

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Compteurs Analyseur de performances

Un autre moyen de surveiller les agents d’authentification consiste à effectuer un suivi de compteurs Analyseur de performances spécifiques sur chaque serveur où est installé l’agent d’authentification. Utilisez les compteurs globaux suivants (**#PTA Authentifications**, **#PTA Authentifications ayant échoué** et **#PTA Authentifications réussies**), ainsi que les compteurs d’erreurs (**#PTA Erreurs d’authentification**) :

![Compteurs Analyseur de performances de l’authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>L’authentification directe assure la haute disponibilité à l’aide de plusieurs agents d’authentification, et _non_ de l’équilibrage de charge. Selon votre configuration, les agents d’authentification ne reçoivent _pas_ tous le _même_ nombre exact de demandes. Il est possible qu’un agent d’authentification spécifique ne reçoive aucun trafic du tout.
