---
title: "Configuration d’Azure Multi-Factor Authentication | Microsoft Docs"
description: "Voici la page Multi-Factor Authentication qui vous indique ce qu’il faut faire avec MFA.  Cela inclut les rapports, l’alerte de fraude, le contournement à usage unique, les messages vocaux personnalisés, la mise en cache, les adresses IP approuvées et les mots de passe d’application."
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
ms.date: 11/29/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4dc029e01762d9ab6eb1c930f2941d0c1ac8ed7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurer les paramètres d’Azure Multi-Factor Authentication

Cet article vous aide à gérer Azure Multi-Factor Authentication, maintenant que vous êtes opérationnel.  Il aborde divers sujets qui vous permettent de tirer le meilleur parti d’Azure Multi-Factor Authentication.  Ces fonctionnalités ne sont pas disponibles dans toutes les [versions d’Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Fonctionnalité | Description | 
|:--- |:--- |
| [Bloquer et débloquer des utilisateurs](#block-and-unblock) |Bloquer/débloquer des utilisateurs peut empêcher les utilisateurs de recevoir des demandes d’authentification. |
| [Alerte de fraude](#fraud-alert) |Une alerte de fraude peut être configurée et installée de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. |
| [Contournement à usage unique](#one-time-bypass) |Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur. |
| [Messages vocaux personnalisés](#custom-voice-messages) |Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. |
| [Mise en cache](#caching-in-azure-multi-factor-authentication) |La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. |
| [Adresses IP approuvées](#trusted-ips) |Les administrateurs d’un locataire géré ou fédéré peuvent utiliser les adresses IP approuvées pour contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. |
| [Mots de passe d'application](#app-passwords) |Un mot de passe d’application permet à une application qui ne prend pas en charge MFA de contourner l’authentification multifacteur et de continuer à fonctionner. |
| [Mémoriser Multi-Factor Authentication pour les appareils et les navigateurs mémorisés](#remember-multi-factor-authentication-for-devices-that-users-trust) |Vous permet de mémoriser des appareils pour un nombre défini de jours après qu’un utilisateur soit parvenu à se connecter à l’aide de MFA. |
| [Méthodes de vérification sélectionnables](#selectable-verification-methods) |Vous permet de choisir les méthodes d'authentification disponibles pour les utilisateurs. |

## <a name="block-and-unblock"></a>Bloquer et débloquer
En bloquant/débloquant des utilisateurs, vous pouvez les empêcher de recevoir des demandes d’authentification. Toutes les tentatives d’authentification des utilisateurs bloqués sont automatiquement refusées. La durée de blocage de ces utilisateurs est de 90 jours à partir du moment où ils sont bloqués.

### <a name="block-a-user"></a>Bloquer un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Bloquer/débloquer des utilisateurs**.
3. Cliquez sur **Ajouter** pour bloquer un utilisateur.
4. Sélectionnez le **Groupe de réplication**, indiquez le nom d’utilisateur bloqué en tant que **username@domain.com** et entrez un commentaire dans le champ **Motif**.
5. Cliquez sur **Ajouter** pour achever de bloquer l’utilisateur.

### <a name="unblock-a-user"></a>Débloquer un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Bloquer/débloquer des utilisateurs**.
3. Cliquez sur **Débloquer** dans la colonne **Action** en regard de l’utilisateur que vous voulez débloquer.
4. Entrez un commentaire dans le champ **Motif du déblocage**.
5. Cliquez sur **Débloquer** pour achever de débloquer l’utilisateur.

## <a name="fraud-alert"></a>Alerte de fraude
Une alerte de fraude peut être configurée et installée de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources.  Les utilisateurs peuvent signaler une fraude à l’aide de l'application mobile ou de leur téléphone.

### <a name="turn-on-fraud-alert"></a>Activer l’alerte de fraude
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Alerte fraude**.

   ![Alerte de fraude](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Définissez **Autoriser les utilisateurs à envoyer des alertes de fraude** avec la valeur **Activé**.
4. Sélectionnez **Enregistrer**.

### <a name="configuration-options"></a>Options de configuration

- **Bloquer l'utilisateur en cas de signalement de fraude** : Si un utilisateur fait l’objet d’un signalement de fraude, son compte est bloqué pendant 90 jours ou jusqu'à ce qu’un administrateur débloque son compte. Un administrateur peut consulter les connexions à l’aide du rapport de connexion et prendre les mesures appropriées pour empêcher les fraudes futures. Un administrateur peut ensuite [débloquer](#unblock-a-user) le compte de l’utilisateur.
- **Code pour signaler une fraude lors du message d’accueil initial** : quand les utilisateurs reçoivent un appel téléphonique pour effectuer la vérification en deux étapes, ils appuient normalement sur # pour confirmer leur connexion. S’ils veulent signaler une fraude, ils doivent saisir un code avant d’appuyer sur #. Ce code est **0** par défaut, mais vous pouvez le personnaliser.

> [!NOTE]
> Le message d'accueil de Microsoft par défaut demande aux utilisateurs d'appuyer sur 0# pour envoyer une alerte de fraude. Si vous souhaitez utiliser un code autre que 0, vous devrez enregistrer et charger vos propres messages d’accueil vocaux personnalisés avec les instructions appropriées.

### <a name="view-fraud-reports"></a>Afficher les rapports de fraude
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. Sélectionnez le répertoire à gérer. 
4. Sélectionnez **Configurer**.
5. Sous Multi-Factor Authentication, sélectionnez **Gérer les paramètres du service**.
6. En bas de la page Paramètres du service, sélectionnez **Accéder au portail** .
7. Dans le portail de gestion Azure Multi-Factor Authentication, sous Afficher un rapport, cliquez sur **Alerte fraude**.
8. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur, les numéros de téléphone et l’état des utilisateurs.
9. Cliquez sur **Exécuter** pour afficher un rapport des alertes de fraude. Cliquez sur **Exporter au format CSV** si vous souhaitez exporter le rapport.

## <a name="one-time-bypass"></a>Contournement à usage unique
Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois sans procéder à la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié. Par conséquent, lorsque l’application mobile ou le téléphone ne reçoit pas de notification ou d’appel téléphonique, vous pouvez activer un contournement à usage unique afin que l’utilisateur puisse accéder à la ressource souhaitée.

### <a name="create-a-one-time-bypass"></a>Créer un contournement à usage unique

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Contournement à usage unique**.

   ![Contournement à usage unique](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. Sélectionnez **Ajouter**.
4. Si nécessaire, sélectionnez le groupe de réplication pour ce contournement.
5. Entrez le nom d’utilisateur (au format username@domain.com), le nombre de secondes pendant lesquelles le contournement se produira, ainsi que la raison du contournement. 
6. Sélectionnez **Ajouter**. La limite de temps entre en vigueur immédiatement, par conséquent, l’utilisateur doit se connecter avant l’expiration du contournement à usage unique. 

### <a name="view-the-one-time-bypass-report"></a>Afficher le rapport de contournement à usage unique
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. Sélectionnez le répertoire à gérer. 
4. Sélectionnez **Configurer**.
5. Sous Multi-Factor Authentication, sélectionnez **Gérer les paramètres du service**.
6. En bas de la page Paramètres du service, sélectionnez **Accéder au portail** .
7. Dans le portail de gestion Azure Multi-Factor Authentication, sous Afficher un rapport, cliquez sur **Contournement à usage unique**.
8. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur, les numéros de téléphone et l’état des utilisateurs.
9. Cliquez sur **Exécuter** pour afficher un rapport des contournements. Cliquez sur **Exporter au format CSV** si vous souhaitez exporter le rapport.

## <a name="custom-voice-messages"></a>Messages vocaux personnalisés
Les messages vocaux personnalisés vous permettent d’utiliser vos propres enregistrements ou messages d’accueil pour la vérification en deux étapes. Ils peuvent être utilisés pour compléter ou remplacer les enregistrements Microsoft.

Avant de commencer, tenez compte des restrictions suivantes :

* Les formats de fichiers pris en charge sont .wav et .mp3.
* La taille limite des fichiers est de 5 Mo.
* Les messages d’authentification doivent durer moins de 20 secondes. Toute durée supérieure à 20 secondes peut entraîner l’échec de la vérification, car l’utilisateur ne répondra peut-être pas avant que le message se termine et que la vérification expire.

### <a name="set-up-a-custom-message"></a>Configurer un message personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Paramètres de l’appel téléphonique**.

   ![Paramètres de l’appel téléphonique](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Sélectionnez **Ajouter un message d’accueil**.
4. Choisissez le type de message d’accueil et la langue.
5. Sélectionnez un fichier audio .mp3 ou .wav à charger.
6. Sélectionnez **Ajouter**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Mise en cache dans Azure Multi-Factor Authentication
La mise en cache vous permet de définir une période spécifique pour que les tentatives d’authentification suivantes au cours de cette période aboutissent automatiquement. Cette fonctionnalité est principalement utilisée lorsque les systèmes locaux, comme un VPN, envoient plusieurs demandes de vérification alors que la première demande est toujours en cours. La mise en cache permet aux demandes suivantes de réussir automatiquement après que l’utilisateur a réussi la première vérification en cours. 

La mise en cache n’est pas destinée à être utilisée pour les connexions à Azure AD.

### <a name="set-up-caching"></a>Configurer la mise en cache 
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Serveur MFA** > **Règles de mise en cache**.

   ![Règles de mise en cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. Sélectionnez **Ajouter**.
5. Sélectionnez le type de cache dans la liste déroulante et spécifiez le nombre maximal de secondes pour la mise en cache. 
6. Si nécessaire, sélectionnez un type d’authentification et spécifiez une application. 
7. Sélectionnez **Ajouter**.


## <a name="trusted-ips"></a>Adresses IP approuvées
La fonction Adresses IP approuvées d’Azure MFA permet aux administrateurs d’un client géré ou fédéré de contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. Cette fonctionnalité est disponible avec la version complète d’Azure Multi-Factor Authentication, mais pas dans la version gratuite pour les administrateurs. Pour plus d’informations sur l’obtention de la version complète d’Azure Multi-Factor Authentication, consultez [Présentation d’Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Type de client Azure AD | Options d’Adresses IP approuvées disponibles |
|:--- |:--- |
| Adresses IP gérées |<li>Plages d’adresses IP spécifiques : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise.</li> |
| Adresses IP fédérées |<li>Tous les utilisateurs fédérés : tous les utilisateurs fédérés qui se connectent au sein de l’organisation contourneront la vérification en deux étapes à l’aide d’une revendication émise par AD FS.</li><br><li>Plages d’adresses IP spécifiques : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise. |

Ce contournement ne fonctionne qu’à partir de l'intranet d'une entreprise. Par exemple, si vous n’avez sélectionné que les utilisateurs fédérés, et qu’un utilisateur se connecte en dehors de l’intranet de l’entreprise, cet utilisateur doit s’authentifier à l’aide de la vérification en deux étapes, même si celui-ci présente une revendication AD FS. 

**Expérience de l’utilisateur final au sein du réseau d’entreprise :**

Lorsque la fonction Adresses IP approuvées est désactivée, la vérification en deux étapes est requise pour les flux de navigateur et les mots de passe d’application sont requis pour les applications client riches plus anciennes. 

Lorsque la fonction Adresses IP approuvées est activée, la vérification en deux étapes n’est *pas* requise pour les flux de navigateur et les mots de passe d’application ne sont *pas* requis pour les applications client riches plus anciennes, sous réserve que l’utilisateur n’ait pas déjà créé un mot de passe d’application. Une fois qu’un mot de passe est en cours d’utilisation, il reste requis. 

**Expérience de l’utilisateur final en dehors du réseau d’entreprise :**

Que la fonction Adresses IP approuvées soit activée ou non, la vérification en deux étapes est requise pour les flux de navigateur et les mots de passe d’application sont requis pour les applications client riches plus anciennes. 

### <a name="enable-named-locations-using-conditional-access"></a>Activer les emplacements nommés à l’aide de l’accès conditionnel

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Accès conditionnel** > **Emplacements nommés**.
3. Sélectionnez **Nouvel emplacement**.
4. Donnez un nom à l’emplacement.
5. Sélectionnez **Marquer comme emplacement approuvé**
6. Spécifiez la plage d’adresses IP en notation CIDR (exemple : 192.168.1.1/24).
7. Sélectionnez **Créer**

### <a name="enable-trusted-ips-using-conditional-access"></a>Activer les adresses IP approuvées à l’aide de l’accès conditionnel

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Accès conditionnel** > **Emplacements nommés**.
3. Sélectionnez **Configurer des adresses IP approuvées MFA**
4. Dans la page Paramètres du service, sous Adresses IP approuvées, vous disposez de deux options :
   
   * **Pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** – Activez la case à cocher. Tous les utilisateurs fédérés qui se connectent à partir du réseau d’entreprise contourneront la vérification en deux étapes à l’aide d’une revendication émise par AD FS. Vérifiez qu’AD FS possède une règle pour ajouter la revendication de l’intranet au trafic approprié. Si aucune règle n’existe, créez la règle suivante dans AD FS : "c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);"

   * **Pour les demandes provenant d’une plage spécifique d’adresses IP** – Saisissez les adresses IP dans les zones de texte à l’aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez saisir jusqu'à 50 plages d'adresses IP. Les utilisateurs qui se connectent à partir de ces adresses IP contournent la vérification en deux étapes.
5. Sélectionnez **Enregistrer**.

### <a name="enable-trusted-ips-using-service-settings"></a>Activer les adresses IP approuvées à l’aide des paramètres de service

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Authentification multifacteur**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans la page Paramètres du service, sous Adresses IP approuvées, vous disposez de deux options :
   
   * **Pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** – Activez la case à cocher. Tous les utilisateurs fédérés qui se connectent à partir du réseau d’entreprise contourneront la vérification en deux étapes à l’aide d’une revendication émise par AD FS. Vérifiez qu’AD FS possède une règle pour ajouter la revendication de l’intranet au trafic approprié. Si aucune règle n’existe, créez la règle suivante dans AD FS : "c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);"

   * **Pour les demandes provenant d’une plage spécifique d’adresses IP** – Saisissez les adresses IP dans les zones de texte à l’aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez saisir jusqu'à 50 plages d'adresses IP. Les utilisateurs qui se connectent à partir de ces adresses IP contournent la vérification en deux étapes.
6. Sélectionnez **Enregistrer**.

![Adresses IP approuvées](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Mots de passe d'application
Certaines applications, telles qu’Office 2010 ou version antérieure et qu’Apple Mail, ne prennent pas en charge la vérification en deux étapes. Elles ne sont pas configurées pour accepter une deuxième vérification. Pour utiliser ces applications, vous devez utiliser des « mots de passe d’application » à la place de votre mot de passe traditionnel. Le mot de passe d’application permet à l’application de contourner la vérification en deux étapes et de continuer à fonctionner.

> [!NOTE]
> Authentification moderne pour les clients Office 2013
> 
> Les clients Office 2013 (y compris Outlook) et versions ultérieures prennent en charge des protocoles modernes d’authentification et peuvent être activés dans le cadre de la vérification en deux étapes. Une fois activés, les mots de passe d’application ne sont pas requis pour ces clients.  Pour plus d’informations, consultez [Version préliminaire publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Informations à connaître sur les mots de passe d’application
Voici ce qu’il faut absolument savoir sur les mots de passe d'application.

* Les mots de passe d’application ne doivent être entrés qu’une seule fois par application. Les utilisateurs n’ont pas à s’en souvenir et à les entrer à chaque fois.
* Le mot de passe est généré automatiquement et il n'est pas fourni par l'utilisateur. Le mot de passe automatiquement généré est en effet beaucoup plus difficile à pirater et bien mieux sécurisé.
* Un utilisateur peut posséder jusqu’à 40 mots de passe. 
* Les applications qui mettent en cache des mots de passe et les utilisent dans des scénarios locaux peuvent échouer, car le mot de passe d’application n’est pas connu en dehors de l’ID d’organisation. Des courriers électroniques Exchange sont, par exemple, stockés localement, mais la messagerie archivée se trouve dans le cloud. Le même mot de passe ne fonctionne pas.
* Une fois que l’authentification multifacteur est activée sur un compte utilisateur, les mots de passe d’application peuvent être utilisés avec la plupart des clients sans navigateur tels qu’Outlook et Lync. Les actions d’administration ne peuvent pas être effectuées à l’aide de mots de passe d’application via des applications sans navigateur telles que Windows PowerShell, même si l’utilisateur dispose d’un compte d’administrateur.  Créez un compte de service avec un mot de passe fort pour exécuter des scripts PowerShell et n’activez pas la vérification en deux étapes pour ce compte.

> [!WARNING]
> Les mots de passe d’application ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec les points de terminaison locaux et les points de terminaison à découverte automatique cloud. En effet, les mots de passe de domaine sont tenus de s’authentifier en local et les mots de passe d’application doivent s’authentifier avec le cloud.

### <a name="naming-guidance-for-app-passwords"></a>Recommandations en matière d'affectation de noms pour les mots de passe d'application
Les noms des mots de passe d’application doivent refléter l’appareil sur lequel ils sont utilisés. Si vous disposez, par exemple, d’un ordinateur portable qui contient des applications sans navigateur, telles qu’Outlook, Word et Excel, créez un seul mot de passe d’application intitulé Ordinateur portable et utilisez-le dans toutes ces applications. Ensuite, créez un autre mot de passe d’application nommé Bureau pour les mêmes applications sur votre ordinateur de bureau. 

Microsoft recommande de créer un mot de passe par appareil, et non un mot de passe d’application par application.

### <a name="federated-sso-app-passwords"></a>Mots de passe d'application fédérés (SSO)
Azure AD prend en charge la fédération (authentification unique) avec les services de domaine Windows Server Active Directory (AD DS) locaux. Si votre organisation est fédérée avec Azure AD et que vous vous apprêtez à utiliser Azure Multi-Factor Authentication, vous trouverez ci-dessous des informations importantes à propos de l’utilisation des mots de passe. Cette section s’applique uniquement aux clients fédérés (SSO).

* Les mots de passe d’application sont vérifiés par Azure AD et contournent ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez les mots de passe d’application.
* Pour les utilisateurs fédérés (SSO), nous ne contactons jamais le fournisseur d’identité (IdP), contrairement au flux passif. Les mots de passe sont stockés dans l’ID d’organisation. Si l'utilisateur quitte l'entreprise, ces informations doivent être stockées en temps réel dans l'id d'organisation à l'aide de DirSync. La désactivation/suppression de compte peut mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
* Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
* Aucune authentification locale de journalisation/fonctionnalité d’audit n’est disponible pour les mots de passe d’application.
* Certaines conceptions architecturales avancées peuvent nécessiter une combinaison de noms d’utilisateur, de mots de passe et de mots de passe d’application durant l’utilisation de la vérification en deux étapes avec les clients, selon l’emplacement où ils s’authentifient. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.

  Supposons, par exemple, que vous disposez d'une architecture qui se compose des éléments suivants :

  * Vous fédérez votre instance locale d’Active Directory avec Azure AD
  * Vous utilisez Exchange Online
  * Vous utilisez Lync qui est spécifiquement local
  * Vous utilisez Azure Multi-Factor Authentication

  ![Vérification](./media/multi-factor-authentication-whats-next/federated.png)

  Dans ce cas, vous devez procéder comme suit :

  * Lorsque vous vous connectez à Lync, utilisez le nom d'utilisateur et le mot de passe de votre organisation.
  * Lorsque vous tentez d'accéder au carnet d'adresses via un client Outlook qui se connecte à Exchange Online, utilisez un mot de passe d’application.

### <a name="allow-app-password-creation"></a>Autoriser la création de mots de passe d’application
Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Cette fonctionnalité doit être activée. Pour permettre aux utilisateurs de créer des mots de passe d’application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Authentification multifacteur**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
6. Sélectionnez la case d’option située à côté de **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

![Création de mots de passe d'application](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Créer des mots de passe d’application
Les utilisateurs peuvent créer des mots de passe d'application lors de leur inscription initiale. Ils ont la possibilité de créer des mots de passe d’application à la fin du processus d’inscription.

Les utilisateurs peuvent également créer des mots de passe d’application après l’inscription, en modifiant leurs paramètres dans le portail Azure ou le portail Office 365. Pour plus d’informations et pour connaître les étapes détaillées pour vos utilisateurs, consultez [Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Mémoriser Multi-Factor Authentication pour les appareils utilisateur de confiance
La mémorisation Multi-Factor Authentication pour les appareils et les navigateurs de confiance est une fonctionnalité disponible gratuitement pour tous les utilisateurs MFA. Ce paramètre permet de donner aux utilisateurs la possibilité de contourner MFA pour un nombre défini de jours après une connexion réussie à l’aide de MFA. Cela permet d’améliorer le confort d’utilisation en réduisant le nombre de fois où un utilisateur peut effectuer la vérification en deux étapes sur le même appareil.

Toutefois, si un appareil ou un compte est compromis, la mémorisation MFA des appareils de confiance est susceptible d’affecter la sécurité. En cas de violation d’un compte d’entreprise ou de perte/vol d’un appareil fiable, vous devez [restaurer Multi-Factor Authentication sur tous les appareils](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Cette action a pour effet de révoquer le statut approuvé de tous les appareils et oblige l’utilisateur à procéder de nouveau à la vérification en deux étapes. Vous pouvez également demander à vos utilisateurs de restaurer MFA sur leurs propres appareils en suivant les instructions détaillées dans l’article [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Fonctionnement

La mémorisation de Multi-Factor Authentication consiste à configurer un cookie persistant sur le navigateur lorsqu’un utilisateur coche la case « Ne plus me demander pendant **X** jours » lors de sa connexion. L’utilisateur ne recevra plus aucune invite de vérification MFA sur ce navigateur tant que le cookie ne sera pas arrivé à expiration. Si l’utilisateur ouvre un autre navigateur sur le même appareil ou s’il efface les cookies, il recevra de nouveau l’invite de vérification. 

La case « Ne plus me demander pendant **X** jours » ne s’affiche pas dans les applications non liées à un navigateur, qu’elles prennent ou non en charge les fonctions d’authentification modernes. Ces applications utilisent des jetons d’actualisation qui fournissent de nouveaux jetons d’accès toutes les heures. Lorsqu’un jeton d’actualisation est validé, Azure AD vérifie que la dernière vérification en deux étapes effectuée respecte le délai configuré. 

La mémorisation de MFA sur les appareils fiables réduit donc le nombre d’authentifications sur les applications web (qui déclenchent normalement une invite à chaque fois), mais augmente le nombre d’authentifications pour les clients d’authentification moderne (qui déclenchent normalement une invite tous les 90 jours).

> [!NOTE]
>Cette fonctionnalité n’est pas compatible avec la fonctionnalité « Maintenir la connexion » d’AD FS lorsque l’utilisateur effectue la vérification en deux étapes pour AD FS via le serveur Azure MFA ou via une solution MFA tierce. Si vos utilisateurs cochent la case « Maintenir la connexion » sur AD FS alors qu’ils marquent leur appareil comme digne de confiance pour MFA, ils ne pourront pas procéder à la vérification une fois le délai associé à la mémorisation MFA expiré. Azure AD demande une vérification immédiate en deux étapes, mais AD FS renvoie un jeton avec la revendication MFA d’origine et la date associée, au lieu d’effectuer de nouveau la vérification en deux étapes. Ceci permet de définir une boucle de vérification entre Azure AD et AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Activer Mémoriser Multi-Factor Authentication
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Authentification multifacteur**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans la page Paramètres de service, sous **Mémoriser Multi-Factor Authentication**, cochez la case **Permettre aux utilisateurs de mémoriser l’authentification multifacteur sur des appareils de confiance**.

   ![Mémoriser des appareils](./media/multi-factor-authentication-whats-next/remember.png)

6. Définissez le nombre de jours pendant lesquels vous souhaitez autoriser les appareils approuvés à contourner la vérification en deux étapes. La valeur par défaut est de 14 jours.
7. Sélectionnez **Enregistrer**.

### <a name="mark-a-device-as-trusted"></a>Marquer un appareil en tant qu’appareil de confiance

Une fois cette fonctionnalité activée, les utilisateurs peuvent marquer un appareil comme approuvé lorsqu’ils se connectent en cochant **Ne plus demander**.

![Ne plus demander - Capture d’écran](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Méthodes de vérification sélectionnables
Vous pouvez choisir les méthodes de vérification mises à la disposition de vos utilisateurs. Le tableau ci-dessous présente brièvement chaque méthode.

Lorsque vos utilisateurs inscrivent leurs comptes à MFA, ils choisissent la méthode de vérification qu’ils préfèrent parmi les options que vous avez activées. Vous trouverez de l’aide sur le processus d’inscription dans [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md)

| Méthode | Description |
|:--- |:--- |
| Appel vers le téléphone |Passe un appel vocal automatisé. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory local. |
| Message texte vers le téléphone |Envoie un message texte contenant un code de vérification. L’utilisateur est invité à entrer le code de vérification dans l’interface de connexion. On parle alors « SMS unidirectionnel ». Ce terme signifie que l’utilisateur doit renvoyer par SMS dans un code spécifique. Les SMS bidirectionnels sont déconseillés et ne seront plus pris en charge à compter du 14 novembre 2018. Les utilisateurs pour lesquels les SMS bidirectionnels ont été configurés passeront automatiquement à l’authentification « appel téléphonique » à ce moment-là.|
| Notification via une application mobile |Envoie une notification Push sur votre téléphone ou votre appareil inscrit. L’utilisateur consulte la notification et sélectionne **Vérifier** pour terminer la vérification. <br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Code de vérification de l’application mobile |L’application Microsoft Authenticator génère un nouveau code de vérification OATH toutes les 30 secondes. L’utilisateur entre ce code de vérification dans l’interface de connexion.<br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Comment activer/désactiver les méthodes d'authentification
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Authentification multifacteur**.
4. Sous Multi-Factor Authentication, sélectionnez **Paramètres du service**.
5. Dans les **options de vérification** de la page Paramètres de service, sélectionnez/désélectionnez les options que vous souhaitez utiliser.

   ![Options de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Cliquez sur **Enregistrer**.
