---
title: "Questions fréquentes (FAQ) sur Azure Multi-Factor Authentication | Microsoft Docs"
description: "Questions fréquentes et réponses sur Azure Multi-Factor Authentication. Multi-Factor Authentication est une méthode permettant de vérifier l’identité d’un utilisateur en exigeant plus que le nom d’utilisateur et le mot de passe. Ce service fournit une couche de sécurité supplémentaire pour la connexion et les transactions de l’utilisateur."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8881fc9193aa905b5d7083644a3ce8ac4411d693
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Questions fréquentes (FAQ) sur Azure Multi-Factor Authentication
Ce FAQ répond aux questions courantes sur Azure Multi-Factor Authentication et sur l’utilisation du service Multi-Factor Authentication. Il s’articule autour de questions sur le service en général, les modèles de facturation, les expériences utilisateur et le dépannage.

## <a name="general"></a>Généralités
**Q : Comment les données utilisateur sont-elles gérées par le serveur Azure Multi-Factor Authentication ?**

Avec le serveur Multi-Factor Authentication, les données utilisateur sont stockées uniquement sur les serveurs locaux. Aucune donnée utilisateur persistante n'est stockée dans le cloud. Lorsque l’utilisateur effectue une vérification en deux étapes, le serveur Multi-Factor Authentication envoie les données au service cloud Azure Multi-Factor Authentication à des fins d’authentification. La communication entre le serveur Multi-Factor Authentication et le service cloud Multi-Factor Authentication utilise le protocole SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) sur le port 443 sortant.

Lorsque les demandes d’authentification sont envoyées au service cloud, les données collectées pour les rapports d’utilisation et d’authentification. Les champs de données inclus dans les journaux de vérification en deux étapes sont les suivants :

* **ID unique** (soit le nom d’utilisateur soit l’ID de serveur Multi-Factor Authentication local)
* **Prénom et nom** (facultatif)
* **Adresse e-mail** (facultatif)
* **Numéro de téléphone** (en cas d’utilisation de l’authentification par appel vocal ou SMS)
* **Jeton de l’appareil** (en cas d’utilisation de l’authentification par application mobile)
* **Mode d'authentification**
* **Résultat de l'authentification**
* **Nom du serveur Multi-Factor Authentication**
* **Adresse IP du serveur Multi-Factor Authentication**
* **Adresse IP du client** (si disponible)

Les champs facultatifs peuvent être configurés dans le serveur Multi-Factor Authentication.

Le résultat de la vérification (réussite ou échec) et le motif de refus sont stockés avec les données d’authentification. Celles-ci sont disponibles dans les rapports d’utilisation et d’authentification.

**Q : Quels sont les codes courts SMS utilisés pour envoyer des SMS à mes utilisateurs ?**

Aux États-Unis, Microsoft utilise les codes courts SMS suivants :

   * 97671
   * 69829
   * 51789
   * 99399

Au Canada, Microsoft utilise les codes courts SMS suivants :

   * 759731 
   * 673801

Microsoft ne garantit pas que les notifications Multi-Factor Authentication envoyées par SMS ou message vocale proviennent systématiquement du même numéro. Dans l’intérêt de ses utilisateurs, Microsoft peut à tout moment ajouter ou supprimer des codes courts pour refléter les ajustements apportés aux itinéraires dans le but d’améliorer la remise des SMS. Microsoft ne prend pas en charge les codes courts en dehors des États-Unis et du Canada.

## <a name="billing"></a>Facturation
Vous trouverez les réponses à la plupart des questions de facturation en consultant la [page de tarification relative à Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou la documentation portant sur la [manière d’obtenir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**Q : L’envoi des appels téléphoniques et des SMS utilisés pour l’authentification est-il facturé à mon organisation ?**

Non, les appels émis et les SMS envoyés aux utilisateurs avec Azure Multi-Factor Authentication ne sont pas facturés aux organisations. Si vous utilisez un fournisseur MFA par authentification, vous êtes facturé pour chaque authentification, mais pas pour la méthode utilisée.

Les utilisateurs peuvent se voir facturer les appels téléphoniques ou les SMS qu’ils reçoivent, en fonction de leur service téléphonique personnel.

**Q : Le modèle de facturation par utilisateur me facture-t-il tous les utilisateurs activés ou seulement ceux ayant effectué la vérification en deux étapes ?**

La facturation est basée sur le nombre d’utilisateurs configurés pour l’utilisation de Multi-Factor Authentication, qu’ils aient ou non lancé une vérification en deux étapes ce mois-ci.

**Q : Comment fonctionne la facturation Multi-Factor Authentication ?**

Lorsque vous créez un fournisseur MFA par utilisateur ou par authentification, l’abonnement Azure de votre organisation est facturé chaque mois en fonction de l’utilisation. Ce modèle de facturation est similaire à celui d’Azure pour l’utilisation d’ordinateurs virtuels et de sites web.

Lorsque vous achetez un abonnement pour Microsoft Azure Multi-Factor Authentication, votre organisation paie uniquement les frais de licence annuels pour chaque utilisateur. Les licences MFA et Office 365, Azure AD Premium ou les offres groupées Enterprise Mobility + Security sont facturés de cette manière. 

Pour plus d’informations sur les options disponibles, consultez [Comment obtenir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**Q : Existe-t-il une version gratuite d’Azure Multi-Factor Authentication ?**

Dans certains cas, oui.

Multi-Factor Authentication pour les administrateurs Azure propose gratuitement un sous-ensemble de fonctionnalités Azure MFA pour accéder aux services en ligne de Microsoft, y compris les portails d’administration Azure et Office 365. Cette offre s’applique uniquement aux administrateurs généraux des instances Azure Active Directory qui ne disposent pas de la version complète d’Azure MFA via une licence MFA, une offre groupée ou un fournisseur autonome basé sur la consommation. Si vos administrateurs utilisent la version gratuite et que vous achetez une version complète d’Azure MFA, tous les administrateurs généraux accéderont automatiquement à la version payante.

Multi-Factor Authentication pour les utilisateurs d’Office 365 propose gratuitement un sous-ensemble de fonctionnalités Azure MFA pour accéder aux services Office 365, y compris à Exchange Online et SharePoint Online. Cette offre s’applique aux utilisateurs auxquels une licence Office 365 est affectée lorsque l’instance d’Active Directory correspondante ne dispose pas de la version complète d’Azure MFA via une licence MFA, une offre groupée ou un fournisseur autonome basé sur la consommation.

**Q : Mon organisation peut-elle passer de la facturation de la consommation par utilisateur à la facturation par authentification, et inversement, à tout instant ?**

Si votre organisation acquiert MFA en tant que service autonome avec une facturation à la consommation, vous choisissez un modèle de facturation lorsque vous créez un fournisseur MFA. Vous ne pouvez pas modifier le modèle de facturation après la création d’un fournisseur MFA. Toutefois, vous pouvez supprimer le fournisseur MFA, puis en créer un avec un autre modèle de facturation.

Une fois le fournisseur MFA créé, il peut être associé à un Azure Active Directory (également appelé « client Azure AD »). Si le fournisseur MFA actuel est associé à un locataire Azure AD, vous pouvez en toute sécurité le supprimer et en créer un associé au même locataire Azure AD. Si vous avez acheté suffisamment de licences MFA, Azure AD Premium, ou Enterprise Mobility + Security (EMS) pour couvrir tous les utilisateurs activés pour MFA, vous pouvez également supprimer complètement le fournisseur MFA.

Si votre fournisseur MFA n’est *pas* lié à un locataire Azure AD, ou si vous associez le nouveau fournisseur MFA à un autre locataire Azure AD, les paramètres utilisateur et les options de configuration ne sont pas transférés. Par ailleurs, les serveurs Azure MFA existants doivent être réactivés à l’aide des informations d’identification d’activation générées via le nouveau fournisseur MFA. Le fait de réactiver les serveurs MFA afin de les lier au nouveau fournisseur MFA n’a pas de conséquence sur l’authentification des appels téléphoniques et des SMS. Toutefois, les notifications d’applications mobiles cessent de fonctionner pour tous les utilisateurs jusqu’à ce qu’ils réactivent l’application mobile.

Pour plus d’informations sur les fournisseurs MFA, consultez [Prise en main du fournisseur Azure Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md).

**Q : Mon organisation peut-elle passer du modèle de facturation à la consommation aux abonnements (modèle basé sur licence) à tout instant ?**

Dans certains cas, oui.

Vous pouvez ajouter des licences MFA, si votre répertoire comporte un fournisseur Azure Multi-Factor Authentication *par utilisateur*. Les utilisateurs disposant de licences ne sont pas comptés dans la facturation à la consommation par utilisateur. Les utilisateurs sans licence peuvent toujours être activés pour MFA via le fournisseur MFA. Si vous achetez et affectez des licences à tous les utilisateurs pour utiliser Multi-Factor Authentication, vous pouvez supprimer le fournisseur Azure Multi-Factor Authentication. Vous pouvez toujours créer un autre fournisseur MFA par utilisateur si vous prévoyez d’avoir plus d’utilisateurs que de licences à l’avenir.

Si votre répertoire comporte un fournisseur Azure Multi-Factor Authentication *par authentification*, vous continuez d’être facturé pour chaque authentification, tant que le fournisseur MFA est lié à votre abonnement. Vous pouvez attribuer des licences MFA aux utilisateurs, mais vous continuez d’être facturé pour chaque demande de vérification en deux étapes, qu’elle provienne d’une personne à laquelle une licence MFA a été affectée ou non.

**Q : Mon organisation doit-elle utiliser et synchroniser les identités pour utiliser Azure Multi-Factor Authentication ?**

Azure Active Directory est facultatif et n’est pas requis en cas d’utilisation des modèles de facturation basés sur la consommation par l’organisation. Si votre fournisseur MFA n’est pas lié à un locataire Azure AD, vous pouvez déployer le serveur Azure multi-Factor Authentication localement uniquement.

Azure Active Directory est requis pour le modèle basé sur licence car les licences sont ajoutées au client Azure AD lorsque vous les achetez et que vous les affectez à des utilisateurs dans le répertoire.

## <a name="manage-and-support-user-accounts"></a>Gérer et prendre en charge les comptes d’utilisateurs

**Q : Que dois-je dire à mes utilisateurs s’ils ne reçoivent aucune réponse sur leur téléphone ou n’ont pas leur téléphone avec eux ?**

Espérons que tous vos utilisateurs ont configuré plus d’une méthode de vérification. Encouragez-les à tenter de se reconnecter, mais sélectionnez une autre méthode de vérification sur la page de connexion.

Vous pouvez orienter vos utilisateurs vers le [Guide de dépannage de l’utilisateur final](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**Q : Que dois-je faire si un de mes utilisateurs ne peut pas accéder son compte ?**

Vous pouvez réinitialiser le compte de l’utilisateur en lui demandant d’effectuer à nouveau le processus d’inscription. Pour en savoir plus, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : Que dois-je faire si un de mes utilisateurs perd un téléphone utilisant des mots de passe d’applications ?**

Supprimez tous les mots de passe d’applications de l’utilisateur afin d’empêcher tout accès non autorisé. Une fois que l’utilisateur dispose d’un appareil de remplacement, il peut recréer les mots de passe. Pour en savoir plus, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : Que se passe-t-il si un utilisateur ne peut pas se connecter à des applications sans navigateur ?**

Si votre organisation utilise encore des clients hérités et si vous [avez autorisé l’utilisation de mots de passe d’application](multi-factor-authentication-whats-next.md#app-passwords), vos utilisateurs ne peuvent pas se connecter à ces clients hérités avec leur nom d’utilisateur et leur mot de passe. Au lieu de cela, ils doivent [configurer des mots de passe d’application](./end-user/multi-factor-authentication-end-user-app-passwords.md). Vos utilisateurs doivent effacer (supprimer) leurs informations de connexion, redémarrer l’application, puis se connecter avec leur nom d’utilisateur et leur *mot de passe d’application* au lieu de leur mot de passe standard.

Si votre organisation ne comporte pas de clients hérités, vous ne devez pas autoriser vos utilisateurs à créer de mots de passe d’application.

> [!NOTE]
> Authentification moderne pour les clients Office 2013
>
> Les mots de passe d’application sont nécessaires uniquement pour les applications ne prenant pas en charge l’authentification moderne. Les clients Office 2013 prennent en charge les protocoles d’authentification modernes, mais doivent être configurés. Les nouveaux clients Office prennent automatiquement en charge les protocoles d’authentification moderne. Pour plus d’informations, consultez [l’annonce de la préversion publique de l’authentification moderne Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Q : Mes utilisateurs disent qu’ils ne reçoivent parfois pas le SMS ou qu’ils répondent à des SMS bidirectionnels, alors que la vérification a expiré.**

La remise de SMS et la réception de réponses à des SMS bidirectionnels ne sont pas garanties, en raison de facteurs incontrôlables susceptibles d’affecter la fiabilité du service. Sont notamment en cause le pays de destination, l’opérateur de téléphonie mobile et la puissance du signal.

Si vos utilisateurs ont souvent des problèmes liés à la fiabilité de la réception des SMS, indiquez leur d’utiliser plutôt l’application mobile ou l’appel téléphonique. L’application mobile peut recevoir des notifications à la fois sur des connexions cellulaires et Wi-Fi. En outre, l’application mobile peut générer des codes de vérification même si l’appareil n’a aucun signal. L’application Microsoft Authenticator est disponible pour [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), et [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Si vous devez utiliser des messages texte, nous vous recommandons d’utiliser des SMS unidirectionnels plutôt que bidirectionnels dans la mesure du possible. Le SMS unidirectionnel est plus fiable et évite aux utilisateurs des frais de SMS internationaux en cas de réponse à un message envoyé à partir d’un autre pays.

**Q : Puis-je modifier la durée liée à la saisie par mes utilisateurs du code de vérification à partir d’un SMS avant expiration du système ?**

Oui, dans certains cas. 

Pour les SMS unidirectionnels avec un serveur Azure MFA en version 7.0 ou supérieure, vous pouvez configurer le paramètre de délai d’expiration en définissant une clé de Registre. Une fois le SMS envoyé par le service cloud MFA, le code de vérification (ou code secret à usage unique) est retourné au serveur MFA. Le serveur MFA stocke le code en mémoire pendant 300 secondes par défaut. Si l’utilisateur n’entre pas son code avant l’expiration du délai de 300 secondes, son authentification est refusée. Suivez ces étapes pour modifier le paramètre de délai d’expiration par défaut :

1. Accédez à HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Créez une clé de Registre DWORD appelée **pfsvc_pendingSmsTimeoutSeconds** et définissez la durée de stockage en secondes des codes secrets à usage unique du Azure MFA Server.

>[!TIP] 
>Si vous utilisez plusieurs serveurs MFA, seul celui qui a traité la demande d’authentification d’origine connaît le code de vérification qui a été envoyé à l’utilisateur. Lorsque l’utilisateur entre le code, la demande d’authentification pour le valider doit être envoyée au même serveur. Si la validation du code est envoyée à un autre serveur, l’authentification est refusée. 

Pour les SMS bidirectionnels avec un serveur Azure MFA, vous pouvez configurer le paramètre de délai d’expiration dans le Portail de gestion MFA. Si les utilisateurs ne répondent pas au SMS avant la fin du délai d’expiration défini, leur authentification est refusée. 

Pour les SMS unidirectionnels avec Azure MFA dans le cloud, notamment l’adaptateur AD FS ou l’extension de serveur NPS (Network Policy Server), vous ne pouvez pas configurer le paramètre de délai d’expiration. Azure AD stocke le code de vérification pendant 180 secondes. 

**Q : Puis-je utiliser des jetons matériels avec le serveur Azure Multi-Factor Authentication ?**

Si vous utilisez le serveur Azure Multi-Factor Authentication, vous pouvez importer des jetons de mots de passe à usage unique et durée définie (TOTP) d’authentification ouverte tierce (OATH), puis les utiliser pour la vérification en deux étapes.

Vous pouvez utiliser des jetons ActiveIdentity, qui sont des jetons OATH TOTP, si vous placez la clé secrète dans un fichier CSV que vous importez sur le serveur Azure Multi-Factor Authentication. Vous pouvez utiliser des jetons OATH avec les services de fédération Active Directory (AD FS), l’authentification par formulaires Internet Information Server (IIS) et le protocole RADIUS (Remote Authentication Dial-In User Service) à partir du moment où le système client peut accepter une entrée utilisateur.

Vous pouvez importer des jetons OATH TOTP tiers avec les formats suivants :  

- Conteneur portable de clé symétrique (PSKC)  
- CSV si le fichier contient un numéro de série, une clé secrète au format Base 32 et un intervalle de temps  

**Q : Puis-je utiliser le serveur Azure Multi-Factor Authentication pour sécuriser les Services Terminal Server ?**

Oui, mais si vous utilisez Windows Server 2012 R2 ou une version ultérieure, vous pouvez le faire uniquement avec la Passerelle des services Bureau à distance (passerelle RD).

Les modifications de sécurité dans Windows Server 2012 R2 ont changé la façon dont le serveur Azure Multi-Factor Authentication se connecte au package de sécurité de l’autorité de sécurité locale (LSA) dans Windows Server 2012 et les versions antérieures. Pour les versions de Terminal Services sous Windows 2012 ou une version antérieure, vous pouvez [sécuriser une application avec l’authentification Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Si vous utilisez Windows Server 2012 R2, vous devez utiliser une passerelle RD.

**Q : J’ai configuré ID de l’appelant dans le serveur MFA, mais mes utilisateurs continuent de recevoir des appels Multi-Factor Authentication d’un appelant anonyme.**

Lorsque des appels Multi-Factor Authentication sont transmis sur le réseau téléphonique public, ils sont parfois acheminés par le biais d’un opérateur qui ne prend pas en charge les ID d’appelant. Pour cette raison, l’ID de l’appelant n’est pas garanti, même si le système Multi-Factor Authentication l’envoie toujours.

**Q : Pourquoi mes utilisateurs sont invités à enregistrer leurs informations de sécurité ?**
Il existe plusieurs raisons à cela :

- L’utilisateur a été activé pour MFA par son administrateur dans Azure AD, mais n’a encore enregistré aucune information de sécurité pour son compte.
- L’utilisateur a été autorisé à utiliser la réinitialisation du mot de passe en libre-service dans Azure AD. Les informations de sécurité l’aident à réinitialiser son mot de passe ultérieurement en cas d’oubli.
- L’utilisateur a accédé à une application dotée d’une stratégie d’accès conditionnel exigeant la MFA et n’est pas encore enregistré pour celle-ci.
- Les utilisateurs enregistrent un appareil avec Azure AD (y compris Azure AD Join) et votre organisation exige la MFA pour l’enregistrement de l’appareil. Toutefois, l’utilisateur n’a pas encore été enregistré pour la MFA.
- L’utilisateur génère Windows Hello Entreprise dans Windows 10 (qui nécessite MFA) et n’a pas encore été enregistré pour la MFA.
- L’organisation a créé et activé une stratégie d’inscription MFA qui a été appliquée à l’utilisateur.
- L’utilisateur a été précédemment enregistré pour MFA, mais a choisi une méthode de vérification, qui a depuis été désactivée par un administrateur. L’utilisateur doit donc de nouveau passer par l’inscription MFA pour sélectionner une nouvelle méthode de vérification par défaut.


## <a name="errors"></a>Erreurs
**Q : Que doit faire un utilisateur s’il voit un message d’erreur « La demande d’authentification ne concerne pas un compte activé » lorsqu’il utilise les notifications d’applications mobiles ?**

Encouragez-le à suivre cette procédure pour supprimer son compte de l’application mobile, puis à l’ajouter de nouveau :

1. Accédez à [votre profil du portail Azure](https://account.activedirectory.windowsazure.com/profile/) et connectez-vous avec votre compte professionnel.
2. Sélectionnez **Vérification de sécurité supplémentaire**.
3. Supprimez le compte existant de l’application mobile.
4. Cliquez sur **Configurer**et suivez les instructions pour configurer à nouveau l’application mobile.

**Q : que doivent faire les utilisateurs s’ils reçoivent un message d’erreur 0x800434D4L lorsqu’ils se connectent à une application sans navigateur ?**

L’erreur 0x800434D4L se produit lorsque vous essayez de vous connecter à une application sans navigateur, installée sur un ordinateur local, qui ne fonctionne pas avec des comptes nécessitant une vérification en deux étapes.

La solution consiste à disposer de comptes d’utilisateur distincts pour les opérations d’administration et les autres. Vous pouvez ultérieurement lier les boîtes de réception de votre compte d’administrateur et d’un compte non administrateur pour vous connecter à Outlook à l’aide de votre compte non administrateur. Pour plus d’informations sur cette solution, consultez [Permettre à un administrateur d’ouvrir et d’afficher le contenu de la boîte aux lettres d’un utilisateur](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Étapes suivantes
Si vous ne trouvez pas ici la réponse à votre question, veuillez laisser un commentaire en bas de la page. Sinon, voici quelques options supplémentaires pour obtenir de l’aide :

* Recherchez des solutions aux problèmes techniques courants dans la [base de connaissances du support Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport).
* Parcourez les forums et recherchez des questions et des réponses techniques de la communauté ou posez votre question sur les [forums Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Si vous êtes un client hérité de PhoneFactor et que vous avez des questions ou besoin d’aide pour réinitialiser un mot de passe, utilisez le lien [Réinitialisation de mot de passe](mailto:phonefactorsupport@microsoft.com) pour ouvrir une demande de support.
* Contactez un professionnel du support via le [Support du serveur Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Lorsque vous nous contactez, veillez à inclure autant d’informations que possible concernant votre problème. Vous pouvez notamment préciser la page sur laquelle vous avez rencontré l’erreur, le code d’erreur spécifique, l’ID de session spécifique et l’ID de l’utilisateur qui a vu l’erreur.
