---
title: Configurer Azure Multi-Factor Authentication | Microsoft Docs
description: "Cet article décrit comment configurer les paramètres d’Azure Multi-Factor Authentication pour les rapports, les alertes de fraude, les contournements à usage unique, les messages vocaux personnalisés, la mise en cache, les adresses IP approuvées et les mots de passe d’application."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4421b995e69e115fbb6c7379af79aaef537aed0d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurer les paramètres d’Azure Multi-Factor Authentication

Cet article vous aide à gérer Azure Multi-Factor Authentication, maintenant que vous êtes opérationnel. Il aborde divers sujets qui vous permettent de tirer le meilleur parti d’Azure Multi-Factor Authentication. Toutes les fonctionnalités ne sont pas disponibles dans chaque [version d’Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Fonctionnalité | Description | 
|:--- |:--- |
| [Bloquer et débloquer des utilisateurs](#block-and-unblock-users) |Utilisez la fonctionnalité de blocage/déblocage pour empêcher les utilisateurs de recevoir des requêtes d’authentification. |
| [Alerte de fraude](#fraud-alert) |Configurez la fonctionnalité d’alerte de fraude pour que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. |
| [Contournement à usage unique](#one-time-bypass) |Utilisez la fonctionnalité de contournement à usage unique pour permettre à un utilisateur de s’authentifier une seule fois en _contournant_ Multi-Factor Authentication. |
| [Messages vocaux personnalisés](#custom-voice-messages) |Utilisez la fonctionnalité des messages vocaux personnalisés pour utiliser vos propres enregistrements ou messages d’accueil avec Multi-Factor Authentication. |
| [Mise en cache](#caching-in-azure-multi-factor-authentication) |Utilisez la mise en cache pour définir une période spécifique afin que les tentatives d’authentification suivantes aboutissent automatiquement. |
| [Adresses IP approuvées](#trusted-ips) |Les administrateurs d’un locataire géré ou fédéré peuvent utiliser la fonctionnalité des adresses IP approuvées pour contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet de l’entreprise. |
| [Mots de passe d’application](#app-passwords) |Utilisez la fonctionnalité de mot de passe d’application pour permettre à une application de contourner Multi-Factor Authentication et continuer à travailler. |
| [Mémoriser Multi-Factor Authentication pour les appareils et les navigateurs approuvés](#remember-multi-factor-authentication-for-trusted-devices) |Utilisez cette fonctionnalité pour mémoriser les appareils et navigateurs approuvés pendant un nombre défini de jours lorsqu’un utilisateur a réussi à se connecter à l’aide de Multi-Factor Authentication. |
| [Méthodes de vérification sélectionnables](#selectable-verification-methods) |Utilisez cette fonctionnalité pour sélectionner la liste des méthodes d’authentification que les utilisateurs sont en mesure d’utiliser. |

## <a name="block-and-unblock-users"></a>Bloquer et débloquer des utilisateurs

Utilisez la fonctionnalité de _blocage/déblocage_ pour empêcher les utilisateurs de recevoir des requêtes d’authentification. Toutes les tentatives d’authentification des utilisateurs bloqués sont automatiquement refusées. La durée de blocage de ces utilisateurs est de 90 jours à partir du moment où ils sont bloqués.

### <a name="block-a-user"></a>Bloquer un utilisateur

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Bloquer/débloquer des utilisateurs**.
3. Sélectionnez **Ajouter** pour bloquer un utilisateur.
4. Sélectionnez le **Groupe de réplication**. Entrez **username<span></span>@domain.com** comme nom de l’utilisateur bloqué. Entrez un commentaire dans le champ **Motif**.
5. Sélectionnez **Ajouter** pour achever de bloquer l’utilisateur.

### <a name="unblock-a-user"></a>Débloquer un utilisateur

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Bloquer/débloquer des utilisateurs**.
3. Sélectionnez **Débloquer** dans la colonne **Action** en regard de l’utilisateur à débloquer.
4. Entrez un commentaire dans le champ **Motif du déblocage**.
5. Sélectionnez **Débloquer** pour achever de débloquer l’utilisateur.

## <a name="fraud-alert"></a>Alerte de fraude

Configurez la fonctionnalité d’_alerte de fraude_ pour que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. Les utilisateurs peuvent signaler une tentative de fraude avec l’application mobile ou leur téléphone.

### <a name="turn-on-fraud-alerts"></a>Activer les alertes de fraude

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Alerte fraude**.

   ![Activer les alertes de fraude](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Réglez **Autoriser les utilisateurs à envoyer des alertes de fraude** sur **Activé**.
4. Sélectionnez **Enregistrer**.

### <a name="configuration-options"></a>Options de configuration

- **Bloquer l’utilisateur en cas de signalement de fraude** : si un utilisateur fait l’objet d’un signalement de fraude, son compte est bloqué pendant 90 jours ou jusqu’à ce qu’un administrateur le débloque. Un administrateur peut consulter les connexions à l’aide du rapport de connexion et prendre les mesures appropriées pour empêcher les fraudes futures. Un administrateur peut ensuite [débloquer](#unblock-a-user) le compte de l’utilisateur.
- **Code pour signaler une fraude lors du message d’accueil initial** : quand les utilisateurs reçoivent un appel téléphonique, pour effectuer la vérification en deux étapes, ils appuient normalement sur **#** pour confirmer leur connexion. Pour signaler une fraude, l’utilisateur doit saisir un code avant d’appuyer sur **#**. Ce code est **0** par défaut, mais vous pouvez le personnaliser.

  >[!NOTE]
  >Le message d’accueil par défaut de Microsoft demande aux utilisateurs d’appuyer sur **0#** pour envoyer une alerte de fraude. Si vous souhaitez utiliser un code autre que **0**, enregistrez et chargez vos propres messages d’accueil vocaux personnalisés avec les instructions appropriées pour vos utilisateurs.
  >

### <a name="view-fraud-reports"></a>Afficher les rapports de fraude

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** > **Connexions**. Le rapport de fraude fait désormais partie du rapport de connexions Azure Active Directory standard.

## <a name="one-time-bypass"></a>Contournement à usage unique

La fonctionnalité de _contournement à usage unique_ permet à un utilisateur de s’authentifier une seule fois sans passer par la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié. Lorsque l’application mobile ou le téléphone ne reçoit pas de notification ou d’appel téléphonique, vous pouvez autoriser un contournement à usage unique pour que l’utilisateur puisse accéder à la ressource souhaitée.

### <a name="create-a-one-time-bypass"></a>Créer un contournement à usage unique

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Contournement à usage unique**.

   ![Créer un contournement à usage unique](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Sélectionnez **Ajouter**.
4. Si nécessaire, sélectionnez le groupe de réplication de ce contournement.
5. Entrez le nom d’utilisateur **username<span></span>@domain.com**. Entrez la durée du contournement en secondes. Entrez le motif du contournement. 
6. Sélectionnez **Ajouter**. La limite de temps entre immédiatement en vigueur. L’utilisateur doit se connecter avant que le délai de contournement à usage unique ne soit écoulé. 

### <a name="view-the-one-time-bypass-report"></a>Afficher le rapport de contournement à usage unique

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Contournement à usage unique**.

## <a name="custom-voice-messages"></a>Messages vocaux personnalisés
Vous pouvez utiliser vos propres enregistrements ou messages d’accueil pour la vérification en deux étapes, avec la fonction _Messages vocaux personnalisés_. Ces messages peuvent servir à compléter ou à remplacer les enregistrements Microsoft.

Avant de commencer, tenez compte des restrictions suivantes :

* Les formats de fichiers pris en charge sont .wav et .mp3.
* La taille limite des fichiers est de 5 Mo.
* Les messages d’authentification doivent durer moins de 20 secondes. Les messages de plus de 20 secondes peuvent faire échouer la vérification. L’utilisateur peut ne pas répondre avant la fin du message et de la durée de vérification.

### <a name="set-up-a-custom-message"></a>Configurer un message personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Paramètres de l’appel téléphonique**.

   ![Enregistrer les messages téléphoniques personnalisés](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Sélectionnez **Ajouter un message d’accueil**.
4. Choisissez le type de message d’accueil. Choisissez la langue.
5. Sélectionnez un fichier audio .mp3 ou .wav à charger.
6. Sélectionnez **Ajouter**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Mise en cache dans Azure Multi-Factor Authentication

Vous pouvez définir une période pendant laquelle autoriser les tentatives d’authentification lorsqu’un utilisateur est authentifié à l’aide de la fonctionnalité de _mise en cache_. Les tentatives d’authentification suivantes de l’utilisateur durant la période spécifiée aboutissent automatiquement. La mise en cache s’utilise principalement lorsque des systèmes locaux, comme un VPN, envoient plusieurs demandes de vérification alors que la première demande est toujours en cours. Cette fonctionnalité permet aux demandes suivantes d’aboutir automatiquement, une fois que l’utilisateur passe la première vérification en cours. 

>[!NOTE]
>La fonctionnalité de mise en cache n’est pas destinée à être utilisée pour les connexions à Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Configurer la mise en cache 

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Règles de mise en cache**.

   ![Configurer les règles de mise en cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Sélectionnez **Ajouter**.
4. Dans la liste déroulante, sélectionnez le **type de cache**. Entrez le nombre maximum de **secondes de mise en cache**. 
5. Au besoin, sélectionnez un type d’authentification et spécifiez une application. 
6. Sélectionnez **Ajouter**.


## <a name="trusted-ips"></a>Adresses IP approuvées

La fonctionnalité d’_adresses IP approuvées_ d’Azure Multi-Factor Authentication est utilisée par les administrateurs d’un locataire géré ou fédéré. Elle contourne la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet d’entreprise. Cette fonctionnalité est disponible dans la version complète d’Azure Multi-Factor Authentication, mais pas dans la version gratuite pour les administrateurs. Pour plus d’informations sur l’obtention de la version complète d’Azure Multi-Factor Authentication, consultez [Présentation d’Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Type de locataire Azure AD | Options de la fonctionnalité Adresses IP approuvées |
|:--- |:--- |
| Adresses IP gérées |**Plages d’adresses IP spécifiques** : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise.|
| Adresses IP fédérées |**All Federated Users (Tous les utilisateurs fédérés)** : tous les utilisateurs fédérés qui se connectent au sein de l’organisation peuvent contourner la vérification en deux étapes. Les utilisateurs contournent la vérification à l’aide d’une revendication émise par les Services de fédération Active Directory (AD FS).<br/>**Plages d’adresses IP spécifiques** : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise. |

Le contournement des adresses IP approuvées ne fonctionne que depuis l’intranet de l’entreprise. Si vous sélectionnez l’option **All Federated Users (Tous les utilisateurs fédérés)** et qu’un utilisateur se connecte en dehors de l’intranet de l’entreprise, il doit s’authentifier avec la vérification en deux étapes. Le processus est le même, même si l’utilisateur présente une revendication AD FS. 

**Expérience de l’utilisateur final au sein du réseau d’entreprise**

Quand la fonction Adresses IP approuvées est activée, la vérification en deux étapes est nécessaire pour les flux de navigateur. Les mots de passe d’application sont requis pour les anciennes applications clientes enrichies. 

Lorsque la fonctionnalité Adresses IP approuvées est activée, la vérification en deux étapes *n’est pas* requise pour les flux de navigateur. Les mots de passe d’application ne *sont pas* requis pour les anciennes applications clients enrichies, si l’utilisateur n’a pas créé un mot de passe d’application. Dès qu’un mot de passe est utilisé, il reste requis. 

**Expérience de l’utilisateur final en dehors du réseau de l’entreprise**

Que la fonctionnalité Adresses IP approuvées soit activée ou non, la vérification en deux étapes est requise pour les flux de navigateur. Les mots de passe d’application sont requis pour les anciennes applications clientes enrichies. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Activer les emplacements nommés à l’aide de l’accès conditionnel

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Accès conditionnel** > **Emplacements nommés**.
3. Sélectionnez **Nouvel emplacement**.
4. Entrez le nom de l’emplacement.
5. Sélectionnez **Marquer comme emplacement approuvé**.
6. Spécifiez la plage d’adresses IP en notation CIDR, comme **192.168.1.1/24**.
7. Sélectionnez **Créer**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Activer la fonctionnalité Adresses IP approuvées à l’aide de l’accès conditionnel

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Accès conditionnel** > **Emplacements nommés**.
3. Sélectionnez **Configurer des adresses IP approuvées MFA**.
4. Dans la page **Paramètres du service**, sous **Adresses IP approuvées**, vous disposez de deux options :
   
   * **Pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** : pour choisir cette option, activez sa case à cocher. Tous les utilisateurs fédérés qui se connectent depuis le réseau d’entreprise contournent la vérification en deux étapes en utilisant une revendication émise par AD FS. Vérifiez qu’AD FS possède une règle pour ajouter la revendication de l’intranet au trafic approprié. Si la règle n’existe pas, créez la règle suivante dans AD FS :<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Pour les demandes provenant d’une plage spécifique d’adresses IP** : pour choisir cette option, saisissez les adresses IP dans la zone de texte en notation CIDR.
   
     * Pour les adresses IP qui se trouvent entre xxx.xxx.xxx.1 et xxx.xxx.xxx.254, utilisez la notation **xxx.xxx.xxx.0/24**.
     * Pour une adresse IP, utilisez la notation **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Vous pouvez saisir jusqu’à 50 plages d’adresses IP. Les utilisateurs qui se connectent à partir de ces adresses IP contournent la vérification en deux étapes.

5. Sélectionnez **Enregistrer**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Activer la fonctionnalité Adresses IP approuvées à l’aide des paramètres du service

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans la page **Paramètres du service**, sous **Adresses IP approuvées**, vous disposez de deux options :
   
   * **For requests from federated users on my intranet (Pour les demandes issues d’utilisateurs fédérés provenant de mon intranet)** : pour choisir cette option, activez sa case à cocher. Tous les utilisateurs fédérés qui se connectent depuis le réseau d’entreprise contournent la vérification en deux étapes en utilisant une revendication émise par AD FS. Vérifiez qu’AD FS possède une règle pour ajouter la revendication de l’intranet au trafic approprié. Si la règle n’existe pas, créez la règle suivante dans AD FS :<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **For requests from a specified range of IP address subnets (Pour les demandes provenant d’une plage d’adresses IP spécifique)** : pour choisir cette option, saisissez les adresses IP dans la zone de texte en notation CIDR. 
     
     * Pour les adresses IP qui se trouvent entre xxx.xxx.xxx.1 et xxx.xxx.xxx.254, utilisez la notation **xxx.xxx.xxx.0/24**.
     * Pour une adresse IP, utilisez la notation **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Vous pouvez saisir jusqu’à 50 plages d’adresses IP. Les utilisateurs qui se connectent à partir de ces adresses IP contournent la vérification en deux étapes.

6. Sélectionnez **Enregistrer**.

![Activer les adresses IP approuvées avec les paramètres du service](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Mots de passe d'application

Certaines applications, comme Office 2010 ou version antérieure et Apple Mail, ne prennent pas en charge la vérification en deux étapes. Ces applications ne sont pas configurées pour accepter une deuxième vérification. Pour les utiliser, activez la fonctionnalité _Mots de passe d’application_. Vous pouvez utiliser un mot de passe d’application à la place de votre mot de passe traditionnel pour permettre à une application de contourner la vérification en deux étapes et continuer à travailler.

>[!NOTE]
>Authentification moderne pour les clients Microsoft Office 2013 et versions ultérieures
> 
>Les clients Office 2013 et versions ultérieures (y compris Outlook) prennent en charge les protocoles d’authentification modernes et peuvent fonctionner avec la vérification en deux étapes. Une fois le client activé, les mots de passe d’application ne sont pas requis par le client. Pour plus d’informations, consultez [l’annonce de la version préliminaire publique de l’authentification moderne Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>

### <a name="considerations-about-app-passwords"></a>Considérations sur les mots de passe d’application

Lorsque vous utilisez des mots de passe d’application, tenez compte des points importants suivants :

* Les mots de passe d’application ne sont saisis qu’une seule fois par application. Les utilisateurs n’ont pas à s’en souvenir ni à les entrer à chaque fois.
* Le mot de passe est généré automatiquement et il n'est pas fourni par l'utilisateur. Le mot de passe généré automatiquement est beaucoup plus difficile à pirater et bien mieux sécurisé.
* Un utilisateur peut posséder jusqu’à 40 mots de passe. 
* Les applications qui mettent en cache les mots de passe et les utilisent dans les scénarios locaux peuvent se bloquer, car le mot de passe d’application n’est pas connu à l’extérieur du compte professionnel ou scolaire. Par exemple, les courriers électroniques Exchange sont, par exemple, stockés localement, mais la messagerie archivée se trouve dans le cloud. Ainsi, le même mot de passe ne peut pas fonctionner.
* Dès que Multi-Factor Authentication est activé sur le compte d’un utilisateur, les mots de passe d’application peuvent être utilisés avec la plupart des clients sans navigateur comme Outlook et Microsoft Skype Entreprise. Les actions d’administration ne peuvent pas être effectuées à l’aide de mots de passe d’application, dans des applications sans navigateur comme Windows PowerShell. Les actions sont impossibles, même quand l’utilisateur a un compte d’administrateur. Pour exécuter des scripts PowerShell, créez un compte de service avec un mot de passe fort et n’activez pas la vérification en deux étapes sur ce compte.

>[!WARNING]
>Les mots de passe d’application ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec les points de terminaison locaux et les points de terminaison à découverte automatique cloud. Les mots de passe de domaine sont requis pour l’authentification locale. Les mots de passe d’application sont requis pour s’authentifier auprès du cloud.
>

### <a name="guidance-for-app-password-names"></a>Recommandations pour les noms des mots de passe d’application

Les noms des mots de passe d’application doivent correspondre à l’appareil sur lequel ils sont utilisés. Si vous disposez d’un ordinateur portable qui contient des applications sans navigateur, comme Outlook, Word et Excel, créez un mot de passe d’application nommé **Ordinateur portable** pour ces applications. Créez un autre mot de passe d’application nommé **Bureau** pour les mêmes applications qui s’exécutent sur votre ordinateur de bureau. 

>[!NOTE]
>Nous vous recommandons de créer un mot de passe d’application par appareil, au lieu d’un mot de passe d’application par application.

### <a name="federated-or-single-sign-on-app-passwords"></a>Mots de passe d’application pour authentification unique ou fédérée

Azure AD prend en charge la fédération (ou authentification unique) avec les services de domaine Windows Server Active Directory (AD DS) locaux. Si votre organisation est fédérée à Azure AD et que vous utilisez Azure Multi-Factor Authentication, gardez en tête les points suivants sur les mots de passe d’application.

>[!NOTE]
>Les points suivants ne concernent que les clients fédérés (SSO).

* Les mots de passe d’application sont vérifiés par Azure AD et contournent ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez des mots de passe d’application.
* Le fournisseur d’identité (IdP) n’est jamais contacté pour les utilisateurs fédérés (SSO), contrairement au flux passif. Les mots de passe d’application sont stockés dans le compte professionnel ou scolaire. Si un utilisateur quitte l’entreprise, ses informations sont transférées au compte professionnel ou scolaire à l’aide de **DirSync** en temps réel. La désactivation/suppression du compte peut prendre jusqu’à trois heures, retardant la désactivation/suppression du mot de passe d’application dans Azure AD.
* Les paramètres Microsoft Azure Access Control Service des clients locaux ne sont pas honorés par la fonctionnalité des mots de passe d’application.
* Aucune fonctionnalité locale de journalisation/d’audit de l’authentification n’est disponible pour les mots de passe d’application.
* Certaines architectures avancées nécessitent une combinaison d’informations d’identification pour la vérification en deux étapes des clients. Ces informations d’identification peuvent inclure le nom d’utilisateur et les mots de passe d’un compte professionnel ou scolaire, ainsi que des mots de passe d’application. La configuration requise dépend de la façon dont l’authentification est effectuée. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’un compte professionnel ou scolaire. Pour les clients qui s’authentifient auprès d’Azure AD, un mot de passe d’application est indispensable.

  Par exemple, supposons que vous ayez l’architecture suivante :

  * Votre instance locale d’Active Directory est fédérée à Azure AD.
  * Vous utilisez Exchange Online.
  * Vous utilisez Skype Entreprise localement.
  * Vous utilisez Azure Multi-Factor Authentication.

  ![Utilisation de mots de passe d’application dans une organisation fédérée](./media/multi-factor-authentication-whats-next/federated.png)

  Dans ce scénario, vous utilisez les informations d’identification suivantes :

  * Pour vous connecter à Skype Entreprise, utilisez le nom d’utilisateur et le mot de passe de votre compte professionnel ou scolaire.
  * Pour accéder au carnet d’adresses depuis un client Outlook qui se connecte à Exchange Online, utilisez un mot de passe d’application.

### <a name="allow-users-to-create-app-passwords"></a>Permettre aux utilisateurs de créer des mots de passe d’application

Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d’application. La fonctionnalité des mots de passe d’application doit être activée. Pour permettre aux utilisateurs de créer des mots de passe d’application, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Sur la page **Paramètres du service**, sélectionnez l’option **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

   ![Permettre aux utilisateurs de créer des mots de passe d’application](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Créer des mots de passe d’application

Les utilisateurs peuvent créer des mots de passe d'application lors de leur inscription initiale. L’utilisateur a la possibilité de créer des mots de passe d’application à la fin du processus d’inscription.

Les utilisateurs peuvent aussi créer des mots de passe d’application après l’inscription. Les mots de passe d’applications sont modifiables dans les paramètres du portail Azure ou du portail Office 365. Pour plus d’informations et pour connaître les étapes détaillées pour vos utilisateurs, consultez [Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?](./end-user/multi-factor-authentication-end-user-app-passwords.md).

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Mémoriser Multi-Factor Authentication pour les appareils approuvés
La fonctionnalité _Mémoriser Multi-Factor Authentication_ pour les appareils et navigateurs approuvés par l’utilisateur est gratuite pour tous les utilisateurs de Multi-Factor Authentication. Les utilisateurs peuvent contourner les vérifications suivantes pendant un nombre spécifié de jours, une fois qu’ils se sont connectés à un appareil à l’aide de Multi-Factor Authentication. Cette fonctionnalité améliore le confort d’utilisation en réduisant le nombre d’exécutions de la vérification en deux étapes sur un même appareil.

>[!IMPORTANT]
>Si un appareil ou un compte est compromis, la mémorisation de Multi-Factor Authentication sur des appareils approuvés peut affecter la sécurité. En cas de violation d’un compte d’entreprise ou de perte/vol d’un appareil fiable, vous devez [restaurer Multi-Factor Authentication sur tous les appareils](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>L’action de restauration révoque le statut approuvé de tous les appareils et oblige l’utilisateur à procéder de nouveau à la vérification en deux étapes. Vous pouvez également demander à vos utilisateurs de restaurer Multi-Factor Authentication sur leurs propres appareils, en suivant les instructions détaillées dans l’article [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Principe de la fonctionnalité

La fonctionnalité de mémorisation de Multi-Factor Authentication crée un cookie persistant sur le navigateur lorsqu’un utilisateur sélectionne **Don’t ask again for X days (Ne plus me demander pendant X jours)** lors de sa connexion. L’utilisateur n’est pas réinvité à se connecter à Multi-Factor Authentication sur ce navigateur tant que le cookie reste valable. Si l’utilisateur ouvre un autre navigateur sur le même appareil ou s’il efface les cookies, il recevra de nouveau l’invite de vérification. 

L’option **Don’t ask again for X days (Ne plus me demander pendant X jours)** ne s’affiche pas dans les applications sans navigateur, qu’elles prennent en charge ou non l’authentification moderne. Ces applications utilisent des _jetons d’actualisation_ qui fournissent de nouveaux jetons d’accès toutes les heures. Quand un jeton d’actualisation est validé, Azure AD vérifie que la dernière vérification en deux étapes est intervenue dans le délai spécifié (en jours). 

La fonctionnalité réduit le nombre d’authentifications sur les applications web, qui interviennent normalement à chaque fois. Elle augmente le nombre d’authentifications pour les clients qui utilisent une authentification moderne (normalement exigée tous les 90 jours).

>[!IMPORTANT]
>La fonctionnalité **Mémoriser Multi-Factor Authentication** n’est pas compatible avec la fonctionnalité **Maintenir la connexion** d’AD FS, lorsque les utilisateurs effectuent la vérification en deux étapes pour AD FS via le serveur Microsoft Azure Multi-Factor Authentication ou une solution d’authentification multifacteur tierce.
>
>Si votre utilisateur sélectionne **Maintenir la connexion** dans AD FS et désigne son appareil comme approuvé pour Multi-Factor Authentication, il n’est pas vérifié automatiquement à l’issue du délai en jours spécifiés dans **Mémoriser Multi-Factor Authentication**. Azure AD demande une nouvelle vérification en deux étapes, mais AD FS renvoie un jeton avec la revendication Multi-Factor Authentication et la date associée, au lieu d’effectuer de nouveau la vérification en deux étapes. Cette réaction déclenche une boucle de vérification entre Azure AD et AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Activer Mémoriser Multi-Factor Authentication

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans la page **Paramètres du service**, sous **Gérer Mémoriser Multi-Factor Authentication**, sélectionnez l’option **Permettre aux utilisateurs de mémoriser l’authentification multifacteur sur des appareils de confiance**.

   ![Mémoriser Multi-Factor Authentication pour les appareils approuvés](./media/multi-factor-authentication-whats-next/remember.png)

6. Définissez le nombre de jours pendant lesquels les appareils approuvés peuvent contourner la vérification en deux étapes. La valeur par défaut est de 14 jours.
7. Sélectionnez **Enregistrer**.

### <a name="mark-a-device-as-trusted"></a>Marquer un appareil en tant qu’appareil de confiance

Après avoir activé la fonctionnalité Mémoriser Multi-Factor Authentication, les utilisateurs peuvent affecter un dispositif comme approuvé quand ils se connectent, en sélectionnant **Ne plus me demander**.

![Sélectionner « Ne plus me demander » pour les appareils de confiance](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Méthodes de vérification sélectionnables

Vous pouvez choisir les méthodes d’authentification disponibles pour vos utilisateurs, grâce à la fonctionnalité _Méthodes de vérification sélectionnables_. Le tableau ci-dessous présente brièvement chaque méthode.

Lorsque vos utilisateurs inscrivent leurs comptes à Multi-Factor Authentication, ils choisissent leur méthode de vérification préférée parmi celles que vous avez activées. Vous trouverez de l’aide sur le processus d’inscription des utilisateurs dans [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md).

| Méthode | Description |
|:--- |:--- |
| Appel vers le téléphone |Passe un appel vocal automatisé. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory en local. |
| Message texte vers le téléphone |Envoie un message texte contenant un code de vérification. L’utilisateur est invité à entrer le code de vérification dans l’interface de connexion. On parle alors « SMS unidirectionnel ». Ce terme signifie que l’utilisateur doit renvoyer par SMS dans un code spécifique. Le SMS bidirectionnel est déconseillé et ne sera plus pris en charge après le 14 novembre 2018. Les utilisateurs qui ont configuré les SMS bidirectionnels passeront automatiquement à la vérification _appel téléphonique_ à ce moment-là.|
| Notification via une application mobile |Envoie une notification Push sur votre téléphone ou votre appareil inscrit. L’utilisateur consulte la notification et sélectionne **Vérifier** pour terminer la vérification. L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Code de vérification de l’application mobile |L’application Microsoft Authenticator génère un nouveau code de vérification OATH toutes les 30 secondes. L’utilisateur entre ce code de vérification dans l’interface de connexion. L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Activer et désactiver des méthodes de vérification

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans la page **Paramètres du service**, sous **Options de vérification**, sélectionnez (ou désélectionnez) les options que vous souhaitez proposer à vos utilisateurs.

   ![Sélectionner les méthodes de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Cliquez sur **Enregistrer**.
