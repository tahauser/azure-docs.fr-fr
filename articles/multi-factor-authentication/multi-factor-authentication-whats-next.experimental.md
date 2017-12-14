---
title: "Configuration d’Azure Multi-Factor Authentication | Microsoft Docs"
description: "Voici la page Multi-Factor Authentication qui vous indique ce qu’il faut faire avec MFA.  Cela inclut les rapports, l’alerte de fraude, le contournement à usage unique, les messages vocaux personnalisés, la mise en cache, les adresses IP approuvées et les mots de passe d’application."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: joflore
ms.openlocfilehash: a6064e33dca09a304074efa4029c0f96fc19b2a2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurer les paramètres d’Azure Multi-Factor Authentication
Cet article vous aide à gérer Azure Multi-Factor Authentication, maintenant que vous êtes opérationnel.  Il aborde divers sujets qui vous permettent de tirer le meilleur parti d’Azure Multi-Factor Authentication.  Ces fonctionnalités ne sont pas disponibles dans toutes les versions d’Azure Multi-Factor Authentication.

| Fonctionnalité | Description | 
|:--- |:--- |
| [Alerte de fraude](#fraud-alert) |Une alerte de fraude peut être configurée et installée de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. |
| [Contournement à usage unique](#one-time-bypass) |Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur. |
| [Messages vocaux personnalisés](#custom-voice-messages) |Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. |
| [Mise en cache](#caching-in-azure-multi-factor-authentication) |La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. |
| [Adresses IP approuvées](#trusted-ips) |Les administrateurs d’un locataire géré ou fédéré peuvent utiliser les adresses IP approuvées pour contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. |
| [Mots de passe d'application](#app-passwords) |Un mot de passe d’application permet à une application qui ne prend pas en charge MFA de contourner l’authentification multifacteur et de continuer à fonctionner. |
| [Mémoriser Multi-Factor Authentication pour les appareils et les navigateurs mémorisés](#remember-multi-factor-authentication-for-devices-that-users-trust) |Vous permet de mémoriser des appareils pour un nombre défini de jours après qu’un utilisateur soit parvenu à se connecter à l’aide de MFA. |
| [Méthodes de vérification sélectionnables](#selectable-verification-methods) |Vous permet de choisir les méthodes d'authentification disponibles pour les utilisateurs. |

## <a name="access-the-azure-mfa-management-portal"></a>Accéder au portail de gestion Azure MFA

Les fonctionnalités présentées dans cet article sont configurées dans le portail de gestion Azure Multi-Factor Authentication. Il existe deux façons d’accéder au portail de gestion de l’authentification MFA via le portail Azure Classic. La première consiste à gérer un fournisseur d’authentification multifacteur. La seconde s'effectue par le biais des paramètres du service MFA. 

### <a name="use-an-authentication-provider"></a>Utiliser un fournisseur d’authentification

Si vous utilisez un fournisseur d’authentification multifacteur pour l’authentification MFA basée sur la consommation, utilisez cette méthode pour accéder au portail de gestion.

Pour accéder au portail de gestion MFA par le biais d’un fournisseur d’authentification multifacteur Azure, connectez-vous au portail Azure Classic en tant qu’administrateur, puis sélectionnez l’option Active Directory. Cliquez sur l’onglet **Fournisseurs d’authentification multifacteur**, puis sélectionnez votre annuaire et cliquez sur le bouton **Gérer** en bas.

### <a name="use-the-mfa-service-settings-page"></a>Utiliser la page des paramètres du service de MFA 

Si vous disposez d’une des licences suivantes, vous pouvez utiliser la page Paramètres du service MFA :
- Azure MFA
- Azure AD Premium 
- Enterprise Mobility + Security

Pour accéder au portail de gestion MFA par le biais de la page des paramètres du service MFA, connectez-vous au portail Azure Classic en tant qu’administrateur, puis sélectionnez l’option Active Directory. Cliquez sur votre répertoire, puis sur l'onglet **Configurer** . Dans la section Authentification multifacteur, sélectionnez **Gérer les paramètres du service**. En bas de la page Paramètres du service MFA, cliquez sur le lien **Accéder au portail** .


## <a name="fraud-alert"></a>Alerte de fraude
Une alerte de fraude peut être configurée et installée de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources.  Les utilisateurs peuvent signaler une fraude à l’aide de l'application mobile ou de leur téléphone.

### <a name="set-up-fraud-alert"></a>Configurer l’alerte de fraude
1. Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
2. Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur **Paramètres** dans la section Configurer.
3. Sous la section Alerte fraude de la page Paramètres, cochez la case **Autoriser les utilisateurs à envoyer des alertes de fraude**.
4. Sélectionnez **Enregistrer** pour enregistrer vos modifications. 

### <a name="configuration-options"></a>Options de configuration

- **Bloquer l’utilisateur en cas de signalement de fraude** - Si un utilisateur signale une fraude, son compte est bloqué.
- **Code pour signaler une fraude lors du message d’accueil initial** - Les utilisateurs appuient généralement sur # pour confirmer la vérification en deux étapes. S’ils veulent signaler une fraude, ils doivent saisir un code avant d’appuyer sur #. Ce code est **0** par défaut, mais vous pouvez le personnaliser.

> [!NOTE]
> Le message d'accueil de Microsoft par défaut demande aux utilisateurs d'appuyer sur 0# pour envoyer une alerte de fraude. Si vous souhaitez utiliser un code autre que 0, vous devrez enregistrer et charger vos propres messages d’accueil vocaux personnalisés avec les instructions appropriées.

![Options d’alerte fraude - Capture d’écran](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Procédure de signalement d’une fraude par les utilisateurs 
Une alerte de fraude peut être déclarée de deux façons.  À l’aide de l'application mobile ou de votre téléphone.  

#### <a name="report-fraud-with-the-mobile-app"></a>Signaler une fraude à l’aide de l’application mobile
1. Lorsqu’une vérification est envoyée sur votre téléphone, sélectionnez-la pour ouvrir l’application Microsoft Authenticator.
2. Sélectionnez **Refuser** dans la notification. 
3. Cliquez sur **Signaler une fraude**.
4. Fermez l’application.

#### <a name="report-fraud-with-a-phone"></a>Signaler une fraude à l’aide d’un téléphone
1. Lorsque vous recevez un appel de vérification sur votre téléphone, répondez-y.  
2. Pour signaler une fraude, entrez le code de fraude (0, par défaut), puis appuyez sur le signe #. Vous êtes alors averti qu’une alerte de fraude a été soumise.
3. Terminez l'appel.

### <a name="view-fraud-reports"></a>Afficher les rapports de fraude
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez à gauche Active Directory.
3. En haut, sélectionnez **Fournisseurs d’authentification multifacteur** pour afficher la liste de vos fournisseurs d’authentification multifacteur.
4. Sélectionnez votre fournisseur d’authentification multifacteur et cliquez sur **Gérer** en bas de la page. Le portail de gestion Azure Multi-Factor Authentication s’affiche.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sous Afficher un rapport, cliquez sur **Alerte fraude**.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur, les numéros de téléphone et l’état des utilisateurs.
7. Cliquez sur **Exécuter** pour afficher un rapport des alertes de fraude. Cliquez sur **Exporter au format CSV** si vous voulez exporter le rapport.

## <a name="one-time-bypass"></a>Contournement à usage unique
Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois sans procéder à la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié. Par conséquent, lorsque l’application mobile ou le téléphone ne reçoit pas de notification ou d’appel téléphonique, vous pouvez activer un contournement à usage unique afin que l’utilisateur puisse accéder à la ressource souhaitée.

### <a name="create-a-one-time-bypass"></a>Créer un contournement à usage unique
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
3. Si le nom de votre fournisseur d’authentification multifacteur Azure apparaît à gauche avec un signe **+**, cliquez sur **+**. Différents groupes de réplication de serveur MFA et le groupe Azure Default sont présentés. Sélectionnez le groupe approprié.
4. Sous Administration des utilisateurs, sélectionnez **Contournement à usage unique**.
5. Dans la page Contournement à usage unique, cliquez sur **Nouveau contournement à usage unique**.

  ![Cloud](./media/multi-factor-authentication-whats-next/create1.png)

6. Entrez le nom d’utilisateur, la durée du contournement, ainsi que la raison du contournement. Cliquez sur **Contournement**.
7. La limite de temps entre en vigueur immédiatement, par conséquent, l’utilisateur doit se connecter avant l’expiration du contournement à usage unique. 

### <a name="view-the-one-time-bypass-report"></a>Afficher le rapport de contournement à usage unique
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez à gauche Active Directory.
3. En haut, sélectionnez **Fournisseurs d’authentification multifacteur** pour afficher la liste de vos fournisseurs d’authentification multifacteur.
4. Sélectionnez votre fournisseur d’authentification multifacteur et cliquez sur **Gérer** en bas de la page. Le portail de gestion Azure Multi-Factor Authentication s’affiche.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, sous Afficher un rapport, cliquez sur **Contournement à usage unique**.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur, les numéros de téléphone et l’état des utilisateurs.
7. Cliquez sur **Exécuter** pour afficher un rapport des contournements. Cliquez sur **Exporter au format CSV** si vous voulez exporter le rapport.

## <a name="custom-voice-messages"></a>Messages vocaux personnalisés
Les messages vocaux personnalisés vous permettent d’utiliser vos propres enregistrements ou messages d’accueil pour la vérification en deux étapes. Vous pouvez utiliser des messages vocaux personnalisés pour compléter ou remplacer les enregistrements Microsoft.

Avant de commencer, tenez compte des points suivants :

* Les formats de fichiers pris en charge sont .wav et .mp3.
* La taille limite des fichiers est de 5 Mo.
* Les messages d’authentification doivent durer moins de 20 secondes. Les messages de plus de 20 secondes ne laissent pas un temps de réponse suffisant aux utilisateurs avant le terme de la vérification.

### <a name="set-up-a-custom-message"></a>Configurer un message personnalisé

Il existe deux étapes dans la création d’un message personnalisé. Tout d’abord, vous téléchargez le message, puis vous l’activez pour vos utilisateurs.

Pour télécharger votre message personnalisé :

1. Créer un message vocal personnalisé à l'aide d'un des formats de fichiers pris en charge.
2. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
3. Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
4. Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur **Messages vocaux** dans la section Configurer.
5. Dans la page Configurer : messages vocaux, cliquez sur **Nouveau message vocal**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6. Dans la page Configurer : nouveaux messages vocaux, cliquez sur **Gérer les fichiers audio**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7. Dans la page Configurer : fichiers audio, cliquez sur **Télécharger un fichier audio**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8. Sur la page Configurer : charger un fichier audio, cliquez sur **Parcourir**, accédez à votre message vocal, puis cliquez sur **Ouvrir**.
9. Ajoutez une description et cliquez sur **Télécharger**.
10. Une fois le message vocal chargé, un message confirme que vous avez bien chargé le fichier.

Pour activer le message pour vos utilisateurs :

1. Sur la gauche, cliquez sur **Messages vocaux**.
2. Sous la section Messages vocaux, cliquez sur **Nouveau message vocal**.
3. Dans la liste déroulante des langues, sélectionnez une langue.
4. Si ce message concerne une application spécifique, spécifiez-la dans la zone de l'application.
5. Dans la liste déroulante Type de message, sélectionnez le type de message à remplacer par votre nouveau message personnalisé.
6. Dans la liste déroulante Fichier audio, sélectionnez le fichier audio que vous avez téléchargé dans la première partie.
7. Cliquez sur **Create**. Un message confirme que vous avez créé un message vocal.
    ![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Mise en cache dans Azure Multi-Factor Authentication
La mise en cache vous permet de définir une période spécifique pour que les tentatives d’authentification suivantes au cours de cette période aboutissent automatiquement. La mise en cache intervient quand les systèmes locaux, comme un VPN, envoient de nombreuses demandes de vérification alors que la première demande est toujours en cours. Cela permet aux demandes suivantes d’aboutir automatiquement après que l’utilisateur a réussi la première vérification en cours. 

La mise en cache n’est pas destinée à être utilisée pour les connexions à Azure AD.

### <a name="set-up-caching"></a>Configurer la mise en cache 
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
3. Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur **Mise en cache** dans la section Configurer.
4. Cliquez sur **Nouveau cache** dans la page Configurer la mise en cache.
5. Sélectionnez le type de cache et les secondes du cache. Cliquez sur **Create**.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Adresses IP approuvées
La fonction Adresses IP approuvées d’Azure MFA permet aux administrateurs d’un locataire géré ou fédéré de contourner la vérification en deux étapes, qui sert aux utilisateurs qui se connectent à partir de l’intranet local de l’entreprise. Cette fonctionnalité est disponible avec la version complète d’Azure Multi-Factor Authentication, mais pas dans la version gratuite pour les administrateurs. Pour plus d’informations sur l’obtention de la version complète d’Azure Multi-Factor Authentication, consultez [Présentation d’Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Type de client Azure AD | Options d’Adresses IP approuvées disponibles |
|:--- |:--- |
| Adresses IP gérées |<li>Plages d’adresses IP spécifiques : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise.</li> |
| Adresses IP fédérées |<li>Tous les utilisateurs fédérés : tous les utilisateurs fédérés qui se connectent au sein de l’organisation contourneront la vérification en deux étapes à l’aide d’une revendication émise par AD FS.</li><br><li>Plages d’adresses IP spécifiques : les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner la vérification en deux étapes des utilisateurs qui se connectent à partir de l’intranet de l’entreprise. |

Ce contournement ne fonctionne qu’à partir de l'intranet d'une entreprise. 

**Expérience de l’utilisateur final au sein du réseau d’entreprise :**

Lorsque la fonction Adresses IP approuvées est désactivée, la vérification en deux étapes est requise pour les flux de navigateur et les mots de passe d’application sont requis pour les applications client riches plus anciennes. 

Quand la fonction Adresses IP approuvées est activée, la vérification en deux étapes n’est *pas* nécessaire pour les flux de navigateur. Les mots de passe d’application ne sont *pas* nécessaires pour les applications clientes riches plus anciennes, à condition que l’utilisateur n’a pas déjà créé un mot de passe d’application. Une fois qu’un mot de passe est en cours d’utilisation, il reste requis. 

**Expérience de l’utilisateur final en dehors du réseau d’entreprise :**

Que la fonction Adresses IP approuvées soit activée ou non, la vérification en deux étapes est requise pour les flux de navigateur et les mots de passe d’application sont requis pour les applications client riches plus anciennes. 

### <a name="to-enable-trusted-ips"></a>Pour activer Adresses IP approuvées
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez à la page de paramètres du service d’authentification MFA pour obtenir des instructions au début de cet article.
3. Dans la page Paramètres du service, sous Adresses IP approuvées, vous disposez de deux options :
   
   * **Pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** – Activez la case à cocher. Tous les utilisateurs fédérés qui se connectent à partir du réseau d’entreprise contournent la vérification en deux étapes en utilisant une revendication émise par AD FS.
   * **Pour les demandes provenant d’une plage spécifique d’adresses IP** – Saisissez les adresses IP dans les zones de texte à l’aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez saisir jusqu'à 50 plages d'adresses IP. Les utilisateurs qui se connectent à partir de ces adresses IP contournent la vérification en deux étapes.
4. Cliquez sur **Save**.
5. Une fois les mises à jour appliquées, cliquez sur **Fermer**.

![Adresses IP approuvées](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Mots de passe d'application
Certaines applications, telles qu’Office 2010 ou version antérieure et qu’Apple Mail, ne prennent pas en charge la vérification en deux étapes. Elles ne sont pas configurées pour accepter une deuxième vérification. Pour utiliser ces applications, vous devez utiliser des « mots de passe d’application » à la place de votre mot de passe traditionnel. Le mot de passe d’application permet à l’application de contourner la vérification en deux étapes et de continuer à fonctionner.

> [!NOTE]
> Authentification moderne pour les clients Office 2013
> 
> Les clients Office 2013 (y compris Outlook) et versions ultérieures prennent en charge des protocoles modernes d’authentification et peuvent être activés dans le cadre de la vérification en deux étapes. Une fois activés, les mots de passe d’application ne sont pas requis pour ces clients.  Pour plus d’informations, consultez [Version préliminaire publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Informations à connaître sur les mots de passe d’application
Voici une liste des points importants à retenir sur les mots de passe d’application.

* Les mots de passe d’application doivent être entrés dans la zone d’entrée une fois par application. Les utilisateurs n’ont pas à s’en souvenir et à les entrer à chaque fois.
* Le mot de passe est généré automatiquement et il n'est pas fourni par l'utilisateur. Le mot de passe généré automatiquement est beaucoup plus difficile à pirater et bien mieux sécurisé.
* Un utilisateur peut posséder jusqu’à 40 mots de passe. 
* Les applications qui mettent en cache des mots de passe et les utilisent dans des scénarios locaux peuvent échouer, car le mot de passe d’application n’est pas connu en dehors de l’ID d’organisation. Des courriers électroniques Exchange sont, par exemple, stockés localement, mais la messagerie archivée se trouve dans le cloud. Le même mot de passe ne fonctionne pas.
* Au démarrage de l’authentification multifacteur, vous pouvez utiliser votre mot de passe avec certains clients autres que des navigateurs. Vous ne pouvez pas utiliser les mots de passe d’application pour les tâches d’administration. Vérifiez que vous créez un compte de service avec un mot de passe fort pour exécuter des scripts PowerShell et que vous n’activez pas ce compte pour la vérification en deux étapes.

> [!WARNING]
> Les mots de passe d’application ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec les points de terminaison locaux et les points de terminaison à découverte automatique cloud. Les mots de passe de domaine sont nécessaires pour s’authentifier en local, alors que les mots de passe d’application sont nécessaires pour s’authentifier sur le cloud.

### <a name="naming-guidance-for-app-passwords"></a>Recommandations en matière d'affectation de noms pour les mots de passe d'application
Les noms des mots de passe d’application doivent refléter l’appareil sur lequel ils sont utilisés. Par exemple, si vous disposez d’un ordinateur portable qui contient des applications autres que des navigateurs, créez un mot de passe d’application unique intitulé Ordinateur portable et servez-vous en. Ensuite, créez un autre mot de passe d’application nommé Bureau pour les mêmes applications sur votre ordinateur de bureau. 

Microsoft recommande de créer un mot de passe par appareil, et non un mot de passe d’application par application.

### <a name="federated-sso-app-passwords"></a>Mots de passe d'application fédérés (SSO)
Azure AD prend en charge la fédération (authentification unique) avec les services de domaine Windows Server Active Directory (AD DS) locaux. Si votre organisation est fédérée avec Azure AD et que vous vous apprêtez à utiliser Azure Multi-Factor Authentication, vous avez tout intérêt à consulter les informations relatives aux mots de passe d’application. Cette section s’applique uniquement aux clients fédérés (SSO).

* Les mots de passe d’application sont vérifiés par Azure AD et contournent ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez les mots de passe d’application.
* Pour les utilisateurs fédérés (SSO), nous n’accédons jamais au fournisseur d’identité (IdP), contrairement au flux passif. Les mots de passe sont stockés dans l’ID d’organisation. Si l’utilisateur quitte l’entreprise, cette information doit être transmise en temps réel à l’ID d’organisation via DirSync. La désactivation/suppression de compte peut mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
* Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
* Aucune authentification locale de journalisation/fonctionnalité d’audit n’est disponible pour les mots de passe d’application.
* Certaines conceptions architecturales avancées peuvent exiger une combinaison de noms d’utilisateur, de mots de passe et de mots de passe d’application quand la vérification en deux étapes est utilisée avec les clients. Cependant, cela dépend de là où ils s’authentifient. Pour les clients qui s'authentifient auprès d'une infrastructure locale, vous utiliseriez le nom d'utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.

  Supposons, par exemple, que vous disposez d'une architecture qui se compose des instances suivantes :

  * Vous fédérez votre instance locale d'Active Directory avec Azure AD
  * Vous utilisez Exchange Online
  * Vous utilisez Lync qui est spécifiquement local
  * Vous utilisez Azure Multi-Factor Authentication

  ![Vérification](./media/multi-factor-authentication-whats-next/federated.png)

  Dans ces instances, vous devez suivre ces étapes :

  * Lorsque vous vous connectez à Lync, utilisez le nom d'utilisateur et le mot de passe de votre organisation.
  * Lorsque vous tentez d'accéder au carnet d'adresses via un client Outlook qui se connecte à Exchange Online, utilisez un mot de passe d’application.

### <a name="allow-app-password-creation"></a>Autoriser la création de mots de passe d’application
Par défaut, les utilisateurs ne peuvent pas créer de mots de passe d’application, mais vous pouvez activer la fonctionnalité par vous-même. Pour permettre aux utilisateurs de créer des mots de passe d’application, procédez comme suit :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez à la page de paramètres du service d’authentification MFA pour obtenir des instructions au début de cet article.
3. Sélectionnez la case d’option située à côté de **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

![Création de mots de passe d'application](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Créer des mots de passe d’application
Les utilisateurs peuvent créer des mots de passe d'application lors de leur inscription initiale. Ils ont la possibilité de créer des mots de passe d’application à la fin du processus d’inscription.

Les utilisateurs peuvent aussi créer des mots de passe d’application après l’inscription. Ils peuvent créer les mots de passe d’application en modifiant leurs paramètres dans le portail Azure ou le portail Office 365. Pour plus d’informations et pour connaître les étapes détaillées pour vos utilisateurs, consultez [Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Mémoriser Multi-Factor Authentication pour les appareils utilisateur de confiance
La mémorisation Multi-Factor Authentication pour les appareils et les navigateurs de confiance est une fonctionnalité disponible gratuitement pour tous les utilisateurs MFA. L’authentification multifacteur permet aux utilisateurs de contourner MFA pendant un certain nombre de jours après s’être connectés. Cette fonctionnalité améliore le confort d’utilisation en réduisant le nombre de fois qu’un utilisateur doit effectuer la vérification en deux étapes sur un même appareil.

Toutefois, si un appareil ou un compte est compromis, la mémorisation MFA des appareils de confiance est susceptible d’affecter la sécurité. En cas de violation d’un compte d’entreprise ou de perte/vol d’un appareil de confiance, vous devez [restaurer Multi-Factor Authentication sur tous les appareils](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Cette action a pour effet de révoquer le statut approuvé de tous les appareils et oblige l’utilisateur à procéder de nouveau à la vérification en deux étapes. Vous pouvez également demander à vos utilisateurs de restaurer MFA sur leurs propres appareils en suivant les instructions détaillées dans l’article [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Fonctionnement

La mémorisation de Multi-Factor Authentication consiste à configurer un cookie persistant sur le navigateur lorsqu’un utilisateur coche la case « Ne plus me demander pendant **X** jours » lors de sa connexion. L’utilisateur ne recevra plus aucune invite de vérification MFA sur ce navigateur tant que le cookie ne sera pas arrivé à expiration. Si l’utilisateur ouvre un autre navigateur sur le même appareil ou s’il efface les cookies, il recevra de nouveau l’invite de vérification. 

La case « Ne plus me demander pendant **X** jours » ne s’affiche pas dans les applications non liées à un navigateur, qu’elles prennent ou non en charge les fonctions d’authentification modernes. Ces applications utilisent des jetons d’actualisation qui fournissent de nouveaux jetons d’accès toutes les heures. Quand un jeton d’actualisation est validé, Azure AD vérifie que la dernière vérification en deux étapes a été effectuée dans le délai configuré (en jours). 

En conclusion, la mémorisation MFA sur les appareils de confiance limite le nombre d’authentifications dans les applications web (normalement exigée à chaque fois). Mais elle augmente aussi le nombre d’authentifications pour les clients qui utilisent une authentification moderne (normalement exigée tous les 90 jours).

> [!NOTE]
>Cette fonctionnalité n’est pas compatible avec la fonctionnalité « Maintenir la connexion » d’AD FS lorsque l’utilisateur effectue la vérification en deux étapes pour AD FS via le serveur Azure MFA ou via une solution MFA tierce. Si vos utilisateurs cochent la case « Maintenir la connexion » sur AD FS alors qu’ils marquent leur appareil comme digne de confiance pour MFA, ils ne pourront pas procéder à la vérification une fois le délai associé à la mémorisation MFA expiré. Azure AD demande une vérification immédiate en deux étapes, mais AD FS renvoie un jeton avec la revendication MFA d’origine et la date associée, au lieu d’effectuer de nouveau la vérification en deux étapes. Ce processus déclenche une boucle de vérification entre Azure AD et AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Activer Mémoriser Multi-Factor Authentication
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez à la page de paramètres du service d’authentification MFA pour obtenir des instructions au début de cet article.
3. Dans la page Paramètres de service, sous Gérer les paramètres des appareils de l’utilisateur, cochez la case **Permettre aux utilisateurs de mémoriser l’authentification multifacteur sur des appareils de confiance**.
   ![Mémoriser des appareils](./media/multi-factor-authentication-whats-next/remember.png)
4. Définissez le nombre de jours pendant lesquels vous souhaitez autoriser les appareils approuvés à contourner la vérification en deux étapes. La valeur par défaut est de 14 jours.
5. Cliquez sur **Save**.
6. Cliquez sur **Fermer**.

### <a name="mark-a-device-as-trusted"></a>Marquer un appareil en tant qu’appareil de confiance

Une fois cette fonctionnalité activée, les utilisateurs peuvent marquer un appareil comme approuvé lorsqu’ils se connectent en cochant **Ne plus demander**.

![Ne plus demander - Capture d’écran](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Méthodes de vérification sélectionnables
Vous pouvez choisir les méthodes de vérification mises à la disposition de vos utilisateurs. Le tableau ci-dessous présente brièvement chaque méthode.

Lorsque vos utilisateurs inscrivent leurs comptes à MFA, ils choisissent la méthode de vérification qu’ils préfèrent parmi les options que vous avez activées. Vous trouverez de l’aide sur le processus d’inscription dans [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md)

| Méthode | Description |
|:--- |:--- |
| Appel vers le téléphone |Passe un appel vocal automatisé. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory local. |
| Message texte vers le téléphone |Envoie un message texte contenant un code de vérification. L’utilisateur est invité à répondre au SMS avec le code de vérification ou à entrer le code de vérification dans l’interface de connexion. |
| Notification via une application mobile |Envoie une notification Push sur votre téléphone ou votre appareil inscrit. L’utilisateur consulte la notification et sélectionne **Vérifier** pour terminer la vérification. <br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Code de vérification de l’application mobile |L’application Microsoft Authenticator génère un nouveau code de vérification OATH toutes les 30 secondes. L’utilisateur entre ce code de vérification dans l’interface de connexion.<br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Comment activer/désactiver les méthodes d'authentification
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez à la page de paramètres du service d’authentification MFA pour obtenir des instructions au début de cet article.
3. Dans les options de vérification de la page Paramètres de service, sélectionnez/désélectionnez les options que vous souhaitez utiliser.
   ![Options de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Cliquez sur **Save**.
5. Cliquez sur **Fermer**.
