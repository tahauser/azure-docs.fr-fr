---
title: "Create and install P2S VPN client configuration files for Azure certificate authentication: PowerShell: Azure (Créer et installer des fichiers de configuration du client VPN pour des connexions P2S : Azure PowerShell) | Microsoft Docs"
description: "Cet article vous aide à créer et à installer un fichier de configuration du client VPN pour des connexions P2S utilisant une authentification par certificat."
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
ms.date: 01/29/2018
ms.author: cherylmc
ms.openlocfilehash: efe5d3db16db83568bb844894198b59a6b39f626
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Créer et installer des fichiers de configuration du client VPN avec des configurations d’authentification par certificat de connexions P2S Azure natives

Les fichiers de configuration du client VPN se trouvent dans un fichier zip. Les fichiers de configuration fournissent les paramètres nécessaires à un client VPN IKEv2 Windows ou Mac natif pour se connecter à un réseau virtuel en point à site avec une authentification par certificat Azure native.

### <a name="workflow"></a>Workflow P2S

  1. Configurez la passerelle VPN Azure pour une connexion P2S.
  2. Générez le certificat racine et les certificats client. Chargez la clé publique du certificat racine dans Azure et installez les certificats client sur les appareils client. Les certificats sont utilisés pour authentifier les utilisateurs qui se connectent.
  3. Obtenez la configuration du client VPN pour l’option d’authentification de votre choix et utilisez-la pour installer le client VPN sur vos appareils Windows et Mac. Cela vous permet de vous connecter à des réseaux virtuels Azure via une connexion point à site.

>[!NOTE]
>Les fichiers de configuration du client sont spécifiques à la configuration VPN du réseau virtuel. Si vous apportez des modifications à la configuration du VPN en point à site après avoir généré les fichiers de configuration du client VPN, comme le type de protocole ou d’authentification du VPN, veillez à générer de nouveaux fichiers de configuration du client VPN pour vos appareils utilisateurs.
>
>

## <a name="generate"></a>Générer les fichiers de configuration du client VPN

Avant de commencer, veillez à ce que tous les utilisateurs qui se connectent disposent d’un certificat valide installer sur l’appareil utilisateur. Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

Vous pouvez générer des fichiers de configuration client à l’aide de PowerShell, ou en utilisant le portail Azure. L’une ou l’autre des méthodes retourne le même fichier zip. Décompressez le fichier pour afficher les dossiers suivants :

  * Les dossiers **WindowsAmd64** et **WindowsX86**, dans lesquels se trouvent respectivement les packages de programme d’installation pour Windows 32 bits et Windows 64 bits. Le package d’installation **WindowsAmd64** est pour tous les clients Windows 64 bits pris en charge, pas seulement Amd.
  * Le dossier **Générique**, dans lequel se trouvent les informations générales utilisées pour créer la configuration de votre client VPN. Ignorez ce dossier. Le dossier Générique est fourni si le protocole IKEv2 ou SSTP+IKEv2 a été configuré sur la passerelle. S’il n’y a que le protocole SSTP qui a été configuré, alors le dossier Générique n’apparaîtra pas.

### <a name="zipportal"></a>Générer les fichiers à l’aide du portail Azure

1. Dans le portail Azure, accédez à la passerelle du réseau virtuel auquel vous souhaitez vous connecter.
2. Dans la page de passerelle de réseau virtuel, cliquez sur **Configuration de point à site**.
3. En haut de la page Configuration de point à site, cliquez sur **Télécharger le client VPN**. La génération du package de configuration du client prend quelques minutes.
4. Votre navigateur indique qu’un fichier zip de configuration du client est disponible. Il porte le même nom que votre passerelle. Décompressez le fichier pour afficher les dossiers.

### <a name="zipps"></a>Générer des fichiers à l’aide de PowerShell

1. Lorsque vous générez les fichiers de configuration du client VPN, la valeur de la méthode « -AuthenticationMethod » est « EapTls ». Générez les fichiers de configuration du client VPN à l’aide de la commande suivante :

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Copiez l’URL dans votre navigateur pour télécharger le fichier zip, puis décompressez le fichier pour afficher les dossiers.

## <a name="installwin"></a>Installer un package de configuration du client VPN Windows

Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client Windows, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section Point à site de la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#P2S).

Suivez les étapes suivantes pour configurer le client VPN Windows natif pour une authentification par certificat :

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». 
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte. 

## <a name="installmac"></a>Configuration du client VPN sur Mac (OSX)

Azure ne fournit pas de fichier mobileconfig pour l’authentification par certificat Azure native. Vous devez configurer manuellement le client VPN IKEv2 natif sur chaque Mac qui se connectera à Azure. Le dossier **Générique** contient toutes les informations dont vous avez besoin pour la configuration. Si vous ne voyez pas le dossier Générique dans votre téléchargement, il est probable qu’IKEv2 n’était pas sélectionné comme type de tunnel. Une fois IKEv2 sélectionné, générez à nouveau le fichier zip pour récupérer le dossier Générique. Ce dossier contient les fichiers suivants :

* Le fichier **VpnSettings.xml**, qui contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur. 
* Le fichier **VpnServerRoot.cer**, qui contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S.

Suivez les étapes ci-dessous afin de configurer le client VPN Mac natif pour une authentification par certificat. Vous devez effectuer ces étapes sur chaque Mac qui se connectera à Azure :

1. Importez le certificat racine **VpnServerRoot** sur votre Mac. Vous pouvez les importer en copiant les fichiers sur votre Mac, puis en double-cliquant dessus.  
Cliquez sur **Ajouter** pour importer.

  ![ajouter le certificat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Il se peut qu’un double-clic sur le certificat n’affiche pas la boîte de dialogue **Ajouter**, mais le certificat est installé dans le magasin approprié. Vous pouvez vérifier le certificat dans le trousseau de connexion sous la catégorie des certificats.
  
2. Ouvrez la boîte de dialogue **Réseau** dans **Préférences réseau**, puis cliquez sur l’icône **+** pour créer un profil de connexion du client VPN pour une connexion P2S au réseau virtuel Azure.

  La valeur **Interface** est « VPN » et la valeur **VPN Type** est « IKEv2 ». Spécifiez un nom pour le profil dans le champ **Nom du service**, puis cliquez sur **Créer** pour créer le profil de connexion du client VPN.

  ![réseau](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. Dans le dossier **Générique**, depuis le fichier **VpnSettings.xml**, copiez la valeur de la balise **VpnServer**. Collez cette valeur dans les champs **Adresse du serveur** et **ID distant** du profil.

  ![informations du serveur](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. Cliquez sur **Paramètres d’authentification** et sélectionnez **Certificat**. 

  ![paramètres d’authentification](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. Cliquez sur **Sélectionner** pour choisir le certificat client que vous souhaitez utiliser pour l’authentification. Normalement, un certificat client est déjà installé sur la machine (voir l’étape 2 de la section **Workflow P2S** ci-dessus).

  ![certificat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. **Choisir une identité** affiche une liste de certificats à choisir. Sélectionnez le certificat approprié, puis cliquez sur **Continuer**.

  ![identité](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. Dans le champ **ID local**, spécifiez le nom du certificat (renseigné à l’étape 6). Dans cet exemple, nous lui avons donné le nom « ikev2Client.com ». Cliquez ensuite sur le bouton **Appliquer** pour enregistrer les modifications.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. Dans la boîte de dialogue **Réseau**, cliquez sur **Appliquer** pour enregistrer toutes les modifications. Cliquez ensuite sur **Connexion** pour lancer la connexion P2S au réseau virtuel Azure.

## <a name="next-steps"></a>Étapes suivantes

Revenez à l’article pour [terminer la configuration P2S](vpn-gateway-howto-point-to-site-rm-ps.md).
