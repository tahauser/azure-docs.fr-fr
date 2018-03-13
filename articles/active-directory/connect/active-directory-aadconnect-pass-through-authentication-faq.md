---
title: 'Azure AD Connect : authentification directe - Forum aux questions | Documents Microsoft'
description: "Réponses au forum aux questions sur l’authentification directe d’Azure Active Directory"
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: 077a60949b5eed24cb9a1c56008a0073693f121e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Authentification directe Azure Active Directory : forum aux questions

Cet article présente les réponses aux questions fréquemment posées sur l’authentification directe d’Azure Active Directory (Azure AD). N'hésitez pas à le consulter pour vous tenir au courant des mises à jour.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Parmi les nouvelles méthodes de connexion Azure AD - authentification directe, synchronisation de hachage de mot de passe et services de fédération Active Directory (AD FS) - laquelle dois-je choisir ?

Cela dépend de votre environnement local et des exigences de votre organisation. Lisez l'article [Options de connexion de l’utilisateur via Azure AD Connect](active-directory-aadconnect-user-signin.md) pour obtenir une comparaison des différentes méthodes de connexion Azure AD.

## <a name="is-pass-through-authentication-a-free-feature"></a>L’authentification directe est-elle une fonctionnalité gratuite ?

L’authentification directe est une fonctionnalité gratuite. Il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L’authentification directe est-elle disponible dans le [cloud Microsoft Azure Allemagne](http://www.microsoft.de/cloud-deutschland) et le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) ?

Non. L’authentification directe est uniquement disponible dans l’instance à l’échelle mondiale d’Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[L’accès conditionnel](../active-directory-conditional-access-azure-portal.md) fonctionne-t-il avec l’authentification directe ?

Oui. Toutes les fonctionnalités, y compris l’authentification multifacteur Azure, fonctionnent avec l’authentification directe.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L’authentification directe prend-elle en charge « l’ID alternatif » comme le nom d’utilisateur, plutôt que « userPrincipalName » ?

Oui. L’authentification directe prend en charge `Alternate ID` comme nom d’utilisateur lorsqu’elle est configurée dans Azure AD Connect. Pour plus d’informations, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Toutes les applications Office 365 ne prennent pas en charge `Alternate ID`. Reportez-vous à la documentation de l’application qui vous intéresse pour avoir des précisions sur sa prise en charge.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>La synchronisation du hachage de mot de passe agit-elle comme solution de secours pour l’authentification directe ?

Non. L’authentification directe _ne bascule pas_ automatiquement vers la synchronisation de hachage de mot de passe. Elle agit uniquement comme solution de secours pour les [scénarios que l’authentification directe ne prend pas en charge aujourd'hui](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Pour éviter les échecs de connexion de l’utilisateur, vous devez configurer l’authentification directe pour une [haute disponibilité](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Puis-je installer un connecteur de [proxy d’application Azure AD](../active-directory-application-proxy-get-started.md) sur le même serveur qu’un agent d’authentification directe ?

Oui. Les versions renommées de l’agent d’authentification directe (versions 1.5.193.0 ou versions ultérieures) prennent en charge cette configuration.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>De quelles versions d’Azure AD Connect et de quel agent d’authentification directe avez-vous besoin ?

Pour que cette fonctionnalité puisse fonctionner, vous devez utiliser la version 1.1.486.0 ou une version ultérieure pour Azure AD Connect et 1.5.58.0 ou version ultérieure pour l’agent d’authentification directe. Installez tous les logiciels sur des serveurs avec Windows Server 2012 R2 ou version ultérieure.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Que se passe-t-il si mon mot de passe utilisateur a expiré et qu’ils essaient de se connecter à l’aide de l’authentification directe ?

Dans le cas où vous avez configuré [la réécriture du mot de passe](../active-directory-passwords-update-your-own-password.md) pour un utilisateur spécifique et que cet utilisateur se connecte à l’aide de l’authentification directe, leurs mots de passe peuvent être modifiés ou réinitialisés. Les mots de passe seront réécrits dans l’annuaire Active Directory local comme prévu.

Si la réécriture du mot de passe n’est pas configurée pour un utilisateur spécifique ou si l’utilisateur n’a aucune licence Azure AD valide attribuée, il ne peut pas mettre à jour son mot de passe dans le cloud. Il ne peut pas mettre à jour son mot de passe même si le mot de passe a expiré. À la place, l’utilisateur voit le message : « Votre organisation ne vous autorise pas à mettre à jour votre mot de passe sur ce site. Mettez-le à jour en fonction de la méthode recommandée par votre organisation, ou contactez votre administrateur si vous avez besoin d’aide ». L’utilisateur ou l’administrateur doit réinitialiser son mot de passe dans Active Directory sur site.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Comment l’authentification directe vous protège-t-elle contre les attaques par recherche exhaustive de mot de passe ?

Voir [Authentification directe Azure Active Directory : Verrouillage intelligent](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) pour plus d'informations.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Qu’est-ce que les agents de l’authentification directe communiquent via les ports 80 et 443 ?

- Les agents d’authentification établissent les requêtes HTTPS sur le port 443 pour toutes les opérations de fonctionnalité.
- Les agents d’authentification établissent des requêtes HTTP sur le port 80 pour télécharger des listes de révocations de certificats SSL (CRL).

     >[!NOTE]
     >Dans les mises à jour récentes, le nombre de ports requis par la fonctionnalité a été diminué. Si vous disposez de versions antérieures d’Azure AD Connect ou de l’agent d’authentification, laissez ces ports également ouverts : 5671, 8080, 9090, 9091, 9350, 9352 et 10100 à 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Les agents d’authentification directe peuvent-ils communiquer sur un serveur proxy web sortant ?

Oui. Si Web Proxy Auto-Discovery (WPAD) est activé dans votre environnement sur site, les agents d’authentification essayent automatiquement de localiser et d’utiliser un serveur proxy web sur le réseau.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Puis-je installer deux ou plusieurs agents d’authentification directe sur le même serveur ?

Non, vous ne pouvez installer qu’un seul agent d’authentification directe sur un serveur unique. Si vous souhaitez configurer l’authentification directe pour la haute disponibilité, suivez les instructions de la section [Authentification directe Azure Active Directory : Démarrage rapide](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Comment supprimer un agent d’authentification directe ?

Tant qu’un agent d’authentification directe est en cours d’exécution, il reste actif et gère en permanence les demandes de connexion utilisateur. Si vous souhaitez désinstaller un agent d’authentification, accédez à **Panneau de configuration -> Programmes -> Programmes et fonctionnalités** et désinstallez les programmes **Agent d’authentification Microsoft Azure AD Connect** et **Outil de mise à jour de l’agent Microsoft Azure AD Connect**.

Si vous consultez le panneau Authentification directe dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) une fois l’étape précédente effectuée, vous pouvez voir que l’agent d’authentification est indiqué comme étant **inactif**. Ceci est _normal_. L’agent d’authentification est automatiquement supprimé de la liste au bout de quelques jours.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>J'utilise déjà AD FS pour me connecter Azure AD. Comment basculer vers l’authentification directe ?

Si vous avez configuré AD FS en tant que méthode de connexion à l’aide de l’Assistant Azure AD Connect, choisissez Authentification directe comme méthode permettant à l'utilisateur de se connecter. Cette modification permet l’authentification directe sur le client et convertit _tous_ vos domaines fédérés en domaines gérés. L’authentification directe gère toutes les prochaines requêtes de connexion à votre client. Actuellement, il n’existe aucun moyen pris en charge dans Azure AD Connect pour utiliser une combinaison d’AD FS et d’authentification directe sur différents domaines.

Si AD FS a été configuré en tant que méthode de connexion _à l'extérieur_ de l’Assistant Azure AD Connect, définissez la méthode de connexion utilisateur sur Authentification directe. Vous pouvez effectuer cette modification à l'aide de l'option **Ne pas configurer**. Cette modification permet l’authentification directe sur le client, mais tous vos domaines fédérés continueront d'utiliser AD FS pour la connexion. Utilisez PowerShell pour convertir manuellement certains ou l’ensemble de ces domaines fédérés en domaines gérés. Une fois cette modification effectuée, *seule* l'authentification directe gère toutes les requêtes de connexion aux domaines gérés.

>[!IMPORTANT]
>L’authentification directe ne gère pas la connexion pour les utilisateurs Azure AD dans le cloud uniquement.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Puis-je utiliser l’authentification directe dans un environnement à plusieurs forêts Active Directory ?

Oui. Les environnements à plusieurs forêts sont pris en charge s’il existe des approbations de forêts entre les forêts Active Directory et si le routage du suffixe de leurs noms est configuré correctement.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Combien d’agents d’authentification directe dois-je installer ?

L’installation de plusieurs agents d’authentification directe assure une [haute disponibilité](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Mais cela ne fournit pas un équilibrage de charge déterministe entre les agents d’authentification.

Envisagez la charge moyenne et les pics de charge lors des demandes de connexion que vous attendez de la part de votre locataire. À titre de référence, un seul agent d’authentification peut gérer entre 300 et 400 authentifications par seconde sur un serveur doté d’un CPU à 4 cœurs et de 16 Go de RAM.

Pour estimer le trafic réseau, suivez les instructions de dimensionnement suivantes :
- Chaque requête a une taille de charge utile de (0,5 K + 1 K * num_of_agents) octets. Ceci concerne les données d’Azure AD vers l’agent d’authentification. Ici, « num_of_agents » indique le nombre d’agents d’authentification inscrit sur votre abonné.
- Chaque réponse a une taille de charge utile de 1 kilooctet. Ceci concerne les données de l’agent d’authentification vers Azure AD.

Pour la plupart des clients, deux ou trois agents d’authentification au total suffisent à offrir la haute disponibilité et suffisamment de capacité. Vous devriez installer des agents d’authentification près de vos contrôleurs de domaine pour améliorer la latence de connexion.

>[!NOTE]
>Il existe une limite système de 12 agents d’authentification par client.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Puis-je installer le premier agent d’authentification directe sur un serveur autre que celui qui exécute Azure AD Connect ?

Non, ce scénario n’est _pas_ pris en charge.

## <a name="how-can-i-disable-pass-through-authentication"></a>Comment désactiver l’authentification directe ?

Réexécutez l’assistant Azure AD Connect et modifiez la méthode de connexion utilisateur de l’authentification directe à une autre méthode. Cette modification désactive l’authentification directe sur le client et désinstalle l’agent d’authentification du serveur. Vous devez désinstaller manuellement les agents d’authentification à partir des autres serveurs.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Que se passe-t-il lorsque je désinstalle un agent d’authentification directe ?

La désinstallation d’un agent d’authentification directe à partir d’un serveur provoque l’interruption de l’acceptation des requêtes de connexion. Pour éviter d'interrompre la fonctionnalité de connexion de l'utilisateur sur votre client, assurez-vous qu'un autre agent d'authentification est en cours d'exécution avant de désinstaller un agent d'authentification directe.

## <a name="next-steps"></a>Étapes suivantes
- [Limitations actuelles](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [Démarrage rapide](active-directory-aadconnect-pass-through-authentication-quick-start.md) : soyez opérationnel sur l’authentification directe Azure AD.
- [Verrouillage intelligent](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : guide pratique pour configurer la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [Présentation technique approfondie](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne l'authentification directe.
- [Résoudre les problèmes](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants liés à la fonctionnalité d’authentification directe.
- [Présentation approfondie de sécurité](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : obtenez des informations techniques approfondies sur l’authentification directe.
- [Authentification unique transparente Azure AD](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : utilisez le Forum Azure Active Directory pour consigner de nouvelles demandes de fonctionnalités.

