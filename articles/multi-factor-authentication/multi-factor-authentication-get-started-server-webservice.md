---
title: "Service Web de l’application mobile du serveur Azure MFA | Microsoft Docs"
description: "L'application Microsoft Authenticator offre une option d'authentification hors bande supplémentaire.  Il permet au serveur MFA d’utiliser des notifications Push pour les utilisateurs."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 83b04e48dd528881097bcf16bc03e1a18ea20c43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Activation de l'authentification par application mobile avec le serveur Azure Multi-Factor Authentication

L'application Microsoft Authenticator offre une option de vérification hors bande supplémentaire. Au lieu de passer un appel téléphonique automatisé ou de rédiger un SMS à l'utilisateur lors de la connexion, Azure Multi-Factor Authentication envoie une notification à l'application Microsoft Authenticator sur le smartphone ou la tablette de l'utilisateur. L'utilisateur appuie simplement sur **Vérifier** (ou saisit un code PIN et appuie sur « Authentifier ») dans l'application pour compléter leur inscription.

Il est préférable d’utiliser une application mobile pour la vérification en deux étapes si la réception par téléphone n’est pas fiable. Si vous utilisez l’application comme un générateur de jetons OATH, aucune connexion réseau ou Internet n’est nécessaire.

Selon votre environnement, il peut être nécessaire de déployer le service Web de l’application mobile sur le même serveur que le serveur Azure Multi-Factor Authentication ou sur un autre serveur accessible sur Internet.

## <a name="requirements"></a>Configuration requise

Pour utiliser l'application Microsoft Authenticator, les éléments suivants sont requis afin que l'application puisse communiquer avec le service Web de l’application mobile :

* Azure Multi-Factor Authentication Server 6.0 ou version ultérieure
* Installez le service Web de l’application mobile sur un serveur Web sur Internet exécutant Microsoft® [Internet Information Services (IIS) 7.x ou version ultérieure](http://www.iis.net/)
* ASP.NET v4.0.30319 est installé, inscrit et autorisé
* Les services de rôle requis incluent ASP.NET et une compatibilité avec la métabase IIS 6.
* Le service Web de l’application mobile est accessible via une URL publique
* Le service Web de l’application mobile est sécurisé avec un certificat SSL.
* Installez le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service dans IIS 7.x ou version ultérieure sur le **même serveur sur lequel Azure Multi-Factor Authentication est installé**
* Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service est sécurisé avec un certificat SSL.
* Le service Web de l’application mobile peut se connecter au Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service via SSL
* Mobile App Web Service peut s'authentifier auprès du Kit de développement logiciel (SDK) Azure MFA Web Service en utilisant les informations d'identification d'un compte de service qui est membre du groupe de sécurité « Administrateurs PhoneFactor ». Ce compte et ce groupe de service existent dans Active Directory si le serveur Azure Multi-Factor Authentication s'exécute sur un serveur appartenant à un domaine. Ce compte et ce groupe de service existent localement sur le serveur Azure Multi-Factor Authentication s'il n'est pas joint à un domaine.

## <a name="install-the-mobile-app-web-service"></a>Installation du service Web de l’application mobile

Avant d'installer le service Web de l’application mobile, tenez compte des éléments suivants :

* Il vous faut un compte de service faisant partie du groupe « PhoneFactor Admins ». Il peut s’agir du même compte utilisé lors de l’installation du portail utilisateur.
* Il est utile d'ouvrir un navigateur web sur le serveur web sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service Web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
* Si un pare-feu ou un proxy inverse est assis devant le serveur Web du service Web de l’application mobile et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du service Web de l’application mobile afin que le service Web de l’application mobile puisse utiliser http au lieu de https. SSL est toujours requis depuis l’application mobile vers le pare-feu/proxy inverse. Ajoutez la clé suivante à la section \<appSettings\> :

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Installation du Kit de développement logiciel (SDK) du service web

Quel que soit le scénario, si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service **n’est pas déjà installé** sur le serveur Azure Multi-Factor Authentication (MFA), effectuez les étapes suivantes.

1. Ouvrez la console du serveur Azure Multi-Factor Authentication.
2. Accédez au **Kit de développement logiciel (SDK) du service Web**, puis cliquez sur **Installer le kit de développement logiciel du service Web**.
3. À moins que vous n’ayez besoin de les modifier, terminez l’installation à l’aide des paramètres par défaut.
4. Lier un certificat SSL personnalisé à un site avec IIS.

Si vous avez des questions à propos de la configuration du certificat SSL personnalisé sur un serveur IIS, consultez l’article [Comment configurer SSL sur IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat auto-signé peut être ajouté à cet effet. Importez le certificat dans le magasin « Racine des autorités de certification approuvée » du compte Ordinateur local sur le serveur Web du portail de l’utilisateur afin qu’il approuve ce certificat lors de l’initialisation de la connexion SSL.

![Kit de développement logiciel (SDK) du Service Web d’installation du serveur MFA](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Installer le service

1. **Sur le serveur MFA**, parcourez le chemin d’installation.
2. Accédez au dossier où est installé le serveur Azure MFA, par défaut **C:\Program Files\Azure Multi-Factor Authentication**.
3. Localisez le fichier d’installation **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Si le serveur **n’est pas** accessible sur Internet, copiez le fichier d’installation sur le serveur sur Internet.
4. Si le serveur MFA**n’est pas** accessible sur Internet, passez sur **le serveur sur Internet**.
5. Exécutez le fichier d’installation **MultiFactorAuthenticationMobileAppWebServiceSetup64** en tant qu’administrateur, modifiez le site si vous le souhaitez et modifiez le répertoire virtuel pour un nom court si vous le souhaitez.
6. Après avoir terminé l’installation, accédez à **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.

   * Recherchez la clé **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** et modifiez **value=""** en **value="DOMAIN\User"** où DOMAIN\User est un compte de service faisant partie du groupe « PhoneFactor Admins ».
   * Recherchez la clé **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** et modifiez **value=""** en **value="Password"** où Password est le mot de passe pour le compte de service entré dans la ligne précédente.
   * Rechercher le paramètre **pfMobile App Web Service_pfwssdk_PfWsSdk** et remplacez la valeur de **http://localhost:4898/PfWsSdk.asmx** par l’URL du kit de développement logiciel du service Web (exemple : https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Enregistrez le fichier config Web et fermez le Bloc-notes.

   > [!NOTE]
   > Le protocole SSL étant utilisé pour cette connexion, vous devez référencer le Kit de développement logiciel (SDK) du service Web avec le **nom de domaine complet (FQDN)** et **non l’adresse IP**. Le certificat SSL aura été émis pour le nom de domaine complet, et l’URL utilisée doit correspondre au nom sur le certificat.

7. Si le site Web sur lequel le service Web de l’application mobile a été installé n’a pas encore été lié avec un certificat signé publiquement, installez le certificat sur le serveur, ouvrez le gestionnaire IIS et liez le certificat au site Web.
8. Ouvrez un navigateur web sur un ordinateur et accédez à l’URL où le service Web de l’application mobile a été installé (par exemple, https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
9. Pour plus d’informations sur les méthodes disponibles dans les kits de développement logiciel de services web, consultez le fichier d’aide de l’authentification multifacteur serveur.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication

Maintenant que le service Web de l’application mobile est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour qu’il fonctionne avec le portail.

1. Dans la console du serveur MFA, cliquez sur l’icône Portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, sélectionnez **Application mobile** dans l'onglet Paramètres, sous **Autoriser les utilisateurs à sélectionner la méthode**. Sans cette fonctionnalité activée, les utilisateurs finaux doivent contacter votre support technique pour effectuer l’activation pour l’application mobile.
2. Cochez la case **Autoriser les utilisateurs à activer l'application mobile**.
3. Cochez la case **Autoriser l'inscription utilisateur**.
4. Cliquez sur l’icône de l’**application mobile**.
5. Entrez l’URL utilisé avec le répertoire virtuel qui a été créé lors de l’exécution du fichier d’installation MultiFactorAuthenticationMobileAppWebServiceSetup64 (par exemple : https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) dans le champ **URL du service Web de l’application mobile :**.
6. Remplissez le champ **Nom de compte** avec le nom de la société ou de l’organisation à afficher dans l’application mobile de ce compte.
   ![Configuration du serveur MFA et paramètres de l’application mobile](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Étapes suivantes

- [Scénarios avancés avec Azure Multi-Factor Authentication et des VPN tiers](multi-factor-authentication-advanced-vpn-configurations.md).
