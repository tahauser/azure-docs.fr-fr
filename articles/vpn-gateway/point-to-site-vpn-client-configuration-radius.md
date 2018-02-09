---
title: "Create and install VPN client configuration files for P2S RADIUS connections: PowerShell: Azure (Créer et installer des fichiers de configuration du client VPN pour des connexions P2S RADIUS : Azure PowerShell) | Microsoft Docs"
description: "Cet article vous aide à créer un fichier de configuration du client VPN pour des connexions P2S utilisant une authentification RADIUS."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: cherylmc
ms.openlocfilehash: 838065287279f1c17e7f294bc919c4a0421e2a58
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Créer et installer les fichiers de configuration du client VPN pour une authentification P2S RADIUS

Les fichiers de configuration du client VPN se trouvent dans un fichier zip. Les fichiers de configuration fournissent les paramètres nécessaires à un client VPN IKEv2 Windows ou Mac pour se connecter à un réseau virtuel en point à site. Le serveur RADIUS fournit plusieurs options d’authentification. La configuration du client VPN varie ainsi d’une option à une autre.

### <a name="workflow"></a>Workflow

  1. [Configurez la passerelle VPN Azure pour une connectivité P2S](point-to-site-how-to-radius-ps.md).
  2. [Configurez votre serveur RADIUS pour l’authentification](point-to-site-how-to-radius-ps.md#radius). 
  3. (Cet article) - Obtenez la configuration du client VPN pour l’option d’authentification de votre choix et utilisez-la pour installer le client VPN sur votre appareil Windows. Cela vous permet de vous connecter à des réseaux virtuels Azure via une connexion P2S.
  4. [Effectuez votre configuration P2S et connectez-vous](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Si vous apportez des modifications à la configuration du VPN en point à site après avoir généré le profil de configuration du client VPN, comme le type de protocole ou d’authentification du VPN, vous devez générer et installer une nouvelle configuration du client VPN sur les appareils de vos appareils utilisateur.
>
>

## <a name="adeap"></a>À propos de l’authentification par nom d’utilisateur/mot de passe

* **Authentification Active Directory** : un scénario courant est l’authentification AD Domain. Dans ce scénario, les utilisateurs utilisent leurs informations d’identification de domaine pour se connecter à des réseaux virtuels Azure. Vous pouvez créer les fichiers de configuration du client VPN pour une authentification AD RADIUS.

* **Authentification sans AD** : vous pouvez également configurer ce scénario d’authentification RADIUS par nom d’utilisateur/mot de passe sans AD.

Veillez à ce que tous les utilisateurs qui se connectent disposent d’informations d’identification (nom d’utilisateur et mot de passe) qui peuvent être authentifiées via RADIUS. Vous pouvez seulement créer une configuration pour le protocole d’authentification par nom d’utilisateur/mot de passe EAP-MSCHAPv2. La méthode « -AuthenticationMethod » est spécifiée en tant que « EapMSChapv2 ».

## <a name="usernamefiles"></a> 1. Générer les fichiers de configuration du client VPN

Générez les fichiers de configuration du client VPN à utiliser avec l’authentification par nom d’utilisateur/mot de passe. Vous pouvez générer les fichiers de configuration du client VPN à l’aide de la commande suivante :

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
L’exécution de la commande renvoie un lien. Copiez et collez ce lien dans un navigateur web pour télécharger « VpnClientConfiguration.zip ». Décompressez le fichier pour afficher les dossiers suivants : 
 
* **WindowsAmd64** et **WindowsX86** : ces dossiers contiennent les packages de programme d’installation pour Windows 64 bits et Windows 32 bits, respectivement. 
* **Générique** : les informations générales utilisées pour créer la configuration de votre client VPN se trouvent dans ce dossier. Ce dossier n’est pas nécessaire pour les configurations d’authentification par nom d’utilisateur/mot de passe.
* **Mac** : si le protocole IKEv2 a été configuré lors de la création de la passerelle du réseau virtuel, un dossier nommé « Mac » s’affiche, dans lequel se trouve un fichier **mobileconfig**. Ce fichier sert à configurer les clients Mac.

Si vous avez déjà créé les fichiers de configuration du client, vous pouvez les récupérer à l’aide de la cmdlet « Get-AzureRmVpnClientConfiguration ». Toutefois, si vous apportez des modifications à votre configuration VPN P2S, comme le type de protocole ou d’authentification VPN, elle ne se met pas à jour automatiquement. Vous devez exécuter la cmdlet « New-AzureRmVpnClientConfiguration » pour créer un nouveau téléchargement de configuration.

Pour récupérer les fichiers de configuration du client générés précédemment, exécutez la commande suivante :

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

## <a name="setupusername"></a> 2. Configurer des clients VPN Windows et Mac
 
### <a name="adwincli"></a>Configuration d’un client VPN Windows

Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client Windows, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section Point à site de la [FAQ](vpn-gateway-vpn-faq.md#P2S).

Suivez les étapes suivantes pour configurer le client VPN Windows natif pour une authentification par certificat :

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». 
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte. 

### <a name="admaccli"></a>Configuration d’un client VPN Mac (OSX)

1. Sélectionnez le fichier **VpnClientSetup mobileconfig** et envoyez-le à chaque utilisateur. Vous pouvez l’envoyer par e-mail, ou par n’importe quelle autre méthode.

2. Recherchez le fichier **mobileconfig** sur l’ordinateur Mac.

  ![rechercher le fichier mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Double-cliquez sur le profil pour l’installer, puis sur **Continuer**. Le nom du profil correspond à celui de votre réseau virtuel.

  ![installer](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Cliquez sur **Continuer** pour faire confiance à l’expéditeur du profil et poursuivre l’installation.

  ![continuer](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Pendant l’installation du profil, vous avez la possibilité de spécifier le nom d’utilisateur et le mot de passe utilisés pour l’authentification VPN. Vous n’êtes pas obligé de renseigner ces informations. Si spécifiées, les informations sont enregistrées et utilisées automatiquement à la connexion. Cliquez sur **Installer** pour continuer.

  ![paramètres](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Entrez un nom d’utilisateur et un mot de passe pour les privilèges nécessaires requis pour installer le profil sur votre ordinateur. Cliquez sur **OK**.

  ![nom d’utilisateur et mot de passe](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Une fois installé, le profil est visible dans la boîte de dialogue **Profils**. Cette boîte de dialogue peut également être ouverte ultérieurement dans **Préférences système**.

  ![Préférences système](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Pour accéder à la connexion VPN, ouvrez la boîte de dialogue **Réseau** dans **Préférences système**.

  ![réseau](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. La connexion VPN apparaît en tant que **IkeV2-VPN**. Le nom peut être modifié en mettant à jour le fichier **mobileconfig**.

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Cliquez sur **Authentication Settings**. Choisissez **Nom d’utilisateur** dans la liste déroulante et entrez vos informations d’identification. Si vous avez entré les informations d’identification précédemment, alors **Nom d’utilisateur** est automatiquement sélectionné dans la liste déroulante et le nom d’utilisateur et le mot de passe sont déjà renseignés. Cliquez sur **OK** pour enregistrer les paramètres. Vous revenez à la boîte de dialogue Réseau.

  ![authentifier](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Cliquez sur **Appliquer** pour enregistrer les modifications. Pour lancer la connexion, cliquez sur **Connexion**.

## <a name="certeap"></a>À propos de l’authentification par certificat
 
Vous pouvez créer les fichiers de configuration du client VPN pour une authentification par certificat RADIUS qui utilise le protocole EAP-TLS. En règle générale, un certificat émis par l’entreprise est utilisé pour authentifier un utilisateur pour un VPN. Veillez à ce que tous les utilisateurs qui se connectent disposent d’un certificat installé sur les appareils utilisateur qui peut être validé par votre serveur RADIUS.
 
* La méthode « -AuthenticationMethod » est « EapTls ».
* Lors de l’authentification par certificat, le client valide le serveur RADIUS en validant son certificat. Le fichier .cer « -RadiusRootCert » contient le certificat racine utilisé pour valider le serveur RADIUS.  
* Un appareil Windows peut posséder plusieurs certificats de client. Lors de l’authentification, cela peut entraîner l’affichage d’une boîte de dialogue contextuelle qui répertorie tous les certificats. L’utilisateur doit alors choisir le certificat à utiliser. Le certificat approprié peut être filtré en spécifiant le certificat racine auquel le certificat client doit être attaché. Le fichier .cer « -ClientRootCert » contient le certificat racine. Ce paramètre est facultatif. Si l’appareil à partir duquel vous souhaitez vous connecter ne dispose que d’un certificat client, ce paramètre n’a pas besoin d’être spécifié.

## <a name="certfiles"></a>1. Générer les fichiers de configuration du client VPN

Générez les fichiers de configuration du client VPN à utiliser avec l’authentification par certificat. Vous pouvez générer les fichiers de configuration du client VPN à l’aide de la commande suivante :
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

L’exécution de la commande renvoie un lien. Copiez et collez ce lien dans un navigateur web pour télécharger « VpnClientConfiguration.zip ». Décompressez le fichier pour afficher les dossiers suivants :

* **WindowsAmd64** et **WindowsX86** : ces dossiers contiennent les packages de programme d’installation pour Windows 64 bits et Windows 32 bits, respectivement. 
* **GenericDevice** : les informations générales utilisées pour créer la configuration de votre client VPN se trouvent dans ce dossier.

Si vous avez déjà créé les fichiers de configuration du client, vous pouvez les récupérer à l’aide de la cmdlet « Get-AzureRmVpnClientConfiguration ». Toutefois, si vous apportez des modifications à votre configuration VPN P2S, comme le type de protocole ou d’authentification VPN, elle ne se met pas à jour automatiquement. Vous devez exécuter la cmdlet « New-AzureRmVpnClientConfiguration » pour créer un nouveau téléchargement de configuration.

Pour récupérer les fichiers de configuration du client générés précédemment, exécutez la commande suivante :

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
## <a name="setupusername"></a> 2. Configurer des clients VPN Windows et Mac

### <a name="certwincli"></a>Configuration d’un client VPN Windows

1. Sélectionnez un package de configuration et installez-le sur l’appareil client. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**. Vous pouvez également enregistrer le package pour l’installer sur d’autres ordinateurs clients.
2. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.

### <a name="certmaccli"></a>Configuration d’un client VPN Mac (OSX)

Vous devez créer un profil distinct pour chaque appareil Mac connecté au réseau virtuel Azure. Les appareils Mac nécessitent la spécification des certificats client pour l’authentification. Le dossier **Générique** contient toutes les informations requises pour créer un profil.

  * Le fichier **VpnSettings.xml** contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur.
  * Le fichier **VpnServerRoot.cer** contient le certificat racine requis pour valider la passerelle VPN lors de la configuration de la connexion P2S.
  * Le fichier **RadiusServerRoot.cer** contient le certificat racine requis pour valider le serveur RADIUS lors de l’authentification.

Suivez les étapes suivantes pour configurer le client VPN Mac natif pour une authentification par certificat :

1. Importez les certificats racine **VpnServerRoot** et **RadiusServerRoot** sur votre Mac. Vous pouvez les importer en copiant les fichiers sur votre Mac, puis en double-cliquant dessus.  
Cliquez sur **Ajouter** pour importer.

  **Ajouter VpnServerRoot**

  ![ajouter le certificat](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **Ajouter RadiusServerRoot**

  ![ajouter le certificat](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Ouvrez la boîte de dialogue **Réseau** dans **Préférences réseau**, puis cliquez sur l’icône **+** pour créer un profil de connexion du client VPN pour une connexion P2S au réseau virtuel Azure.

  La valeur **Interface** est « VPN » et la valeur **VPN Type** est « IKEv2 ». Spécifiez un nom pour le profil dans le champ **Nom du service**, puis cliquez sur **Créer** pour créer le profil de connexion du client VPN.

  ![réseau](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. Dans le dossier **Générique**, depuis le fichier **VpnSettings.xml**, copiez la valeur de la balise **VpnServer**. Collez cette valeur dans les champs **Adresse du serveur** et **ID distant** du profil. Laissez le champ **ID local** vide.

  ![informations du serveur](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. Cliquez sur **Paramètres d’authentification** et sélectionnez **Certificat**. 

  ![paramètres d’authentification](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. Cliquez sur **Sélectionner** pour choisir le certificat que vous souhaitez utiliser pour l’authentification.

  ![certificat](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **Choisir une identité** affiche une liste de certificats à choisir. Sélectionnez le certificat approprié, puis cliquez sur **Continuer**.

  ![identité](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. Dans le champ **ID local**, spécifiez le nom du certificat (renseigné dans l’étape 5). Dans cet exemple, nous lui avons donné le nom « ikev2Client.com ». Cliquez ensuite sur le bouton **Appliquer** pour enregistrer les modifications.

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. Dans la boîte de dialogue **Réseau**, cliquez sur **Appliquer** pour enregistrer toutes les modifications. Cliquez ensuite sur **Connexion** pour lancer la connexion P2S au réseau virtuel Azure.

## <a name="otherauth"></a>Utilisation d’autres types ou protocoles d’authentification

Pour utiliser un type d’authentification différent (par mot de passe à usage unique, par exemple) d’une authentification par nom d’utilisateur/mot de passe ou par certificats, ou pour utiliser un protocole d’authentification différent (PEAP-MSCHAPv2 à la place d’un protocole EAP-MSCHAPv2), vous devez créer votre propre profil de configuration du client VPN. Pour créer le profil, vous avez besoin d’informations telles que l’adresse IP, du type de tunnel et des itinéraires de tunnel fractionné de la passerelle du réseau virtuel. Vous pouvez obtenir ces informations en procédant comme suit :

1. Utilisez la cmdlet « Get-AzureRmVpnClientConfiguration » pour générer la configuration du client VPN pour le protocole EapMSChapv2. Pour obtenir des instructions, consultez [cette section](#ccradius) de l’article.

2. Décompressez le fichier VpnClientConfiguration.zip et recherchez le dossier GenericDevice. Ignorez les dossiers contenant les programmes d’installation Windows pour architecture 64 bits et 32 bits.
 
3. Le dossier GenericDevice contient un fichier XML nommé VpnSettings. Ce fichier contient toutes les informations nécessaires.

  * VpnServer : le nom de domaine complet de la passerelle VPN Azure. Il s’agit de l’adresse à laquelle le client se connecte.
  * VpnType : le type de tunnel que vous utilisez pour vous connecter.
  * Itinéraires : itinéraires que vous devez configurer dans votre profil afin que seul le trafic en direction du réseau virtuel Azure ne passe par le tunnel P2S.
  * Le dossier GenericDevice contient aussi un fichier .cer nommé VpnServerRoot. Ce fichier contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S. Installez le certificat sur tous les appareils qui se connecteront au réseau virtuel Azure. 
 
## <a name="next-steps"></a>étapes suivantes

Revenez à l’article pour [terminer la configuration P2S](point-to-site-how-to-radius-ps.md).
