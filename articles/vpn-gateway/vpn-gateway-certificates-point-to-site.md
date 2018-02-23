---
title: "Générer et exporter des certificats pour les connexions de point à site : PowerShell : Azure | Microsoft Docs"
description: "Créez un certificat racine auto-signé, exportez la clé publique et générez des certificats clients avec PowerShell sous Windows 10 ou Windows Server 2016."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 5e041de12105770a16e43b5ff4f918a1eba92b2a
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell-on-windows-10-or-windows-server-2016"></a>Générer et exporter des certificats pour les connexions de point à site à l’aide de PowerShell sous Windows 10 ou Windows Server 2016

Les connexions de point à site utilisent des certificats pour l’authentification. Cet article explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de PowerShell sous Windows 10 ou Windows Server 2016. Si vous recherchez des étapes de la configuration de point à site, telles que le chargement des certificats racines, sélectionnez l’un des articles « Configuration d’une connexion point à site à un réseau virtuel » dans la liste suivante :

> [!div class="op_single_selector"]
> * [Création de certificat auto-signés - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Création de certificat auto-signés - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configuration d’une connexion de point à site à un réseau virtuel - Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configuration d’une connexion point à site à un réseau virtuel - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configuration d’une connexion de point à site à un réseau virtuel - Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 


Vous devez suivre les étapes de cet article sur un ordinateur exécutant Windows 10 ou Windows Server 2016. Les applets de commande PowerShell que vous utilisez pour générer des certificats font partie du système d’exploitation et ne fonctionnent pas sur d’autres versions de Windows. L’ordinateur Windows 10 ou Windows Server 2016 est nécessaire uniquement pour générer les certificats. Une fois les certificats générés, vous pouvez les charger ou les installer sur n’importe quel système d’exploitation client pris en charge. 

Si vous n’avez pas accès à un ordinateur Windows 10 ou Windows Server 2016, vous pouvez utiliser [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pour générer des certificats. Les certificats générés à l’aide de l’une de ces deux méthodes peuvent être installés sur n’importe quel système d’exploitation client [pris en charge](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

## <a name="rootcert"></a>Créer un certificat racine auto-signé

Utilisez la cmdlet New-SelfSignedCertificate pour créer un certificat racine auto-signé. Pour obtenir des informations sur des paramètres supplémentaires, consultez [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Sur un ordinateur sous Windows 10 ou Windows Server 2016, ouvrez une console Windows PowerShell avec élévation de privilèges.
2. Utilisez l’exemple suivant pour créer le certificat racine auto-signé. L’exemple suivant crée un certificat racine auto-signé nommé « P2SRootCert », automatiquement installé dans « Certificates-Current User\Personal\Certificates ». Vous pouvez afficher le certificat en ouvrant *certmgr.msc* ou *Gérer les certificats utilisateur*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Exporter la clé publique (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Le fichier exported.cer doit être chargé dans Azure. Pour obtenir des instructions, consultez [Configurer une connexion de point à site](vpn-gateway-howto-point-to-site-rm-ps.md#upload). Pour ajouter un certificat racine approuvé supplémentaire, consultez [cette section](vpn-gateway-howto-point-to-site-rm-ps.md#addremovecert) de l’article.

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Exporter le certificat racine auto-signé et la clé privée pour la stocker (facultatif)

Vous souhaiterez peut-être exporter le certificat racine auto-signé et le stocker en toute sécurité. Si besoin est, vous pouvez l’installer ultérieurement sur un autre ordinateur et générer davantage de certificats clients ou exporter un autre fichier .cer. Pour exporter le certificat racine auto-signé au format .pfx, sélectionnez le certificat racine et suivez les étapes décrites dans la section [Exporter un certificat client](#clientexport).

## <a name="clientcert"></a>Générer un certificat client

Chaque ordinateur client qui se connecte à un réseau virtuel avec une connexion de point à site doit avoir un certificat client installé. Vous générez un certificat client à partir du certificat racine auto-signé, puis l’exportez et l’installez. Si le certificat client n’est pas installé, l’authentification échoue. 

Les étapes suivantes vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Lorsque vous générez des certificats clients suivant les étapes ci-dessous, le certificat client est automatiquement installé sur l’ordinateur que vous avez utilisé pour générer le certificat. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous pouvez exporter le certificat.

Les exemples utilisent la cmdlet New-SelfSignedCertificate pour générer un certificat client qui expire au bout d’un an. Pour obtenir des informations sur des paramètres supplémentaires, telles que la définition d’une valeur d’expiration différente pour le certificat client, consultez [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exemple 1

Cet exemple utilise la variable « $cert » déclarée dans la section précédente. Si vous avez fermé la console PowerShell après la création du certificat racine auto-signé, ou que vous créez des certificats clients supplémentaires dans une nouvelle session de console PowerShell, suivez les étapes décrites dans [l’exemple 2](#ex2).

Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ».  Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemple 2

Si vous créez des certificats clients supplémentaires ou que vous n’utilisez pas la même session PowerShell que pour créer votre certificat racine auto-signé, suivez les étapes suivantes :

1. Identifiez le certificat racine auto-signé installé sur l’ordinateur. Cette applet de commande renvoie la liste des certificats installés sur votre ordinateur.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Recherchez le nom du sujet dans la liste renvoyée, puis copiez l’empreinte qui se trouve à côté dans un fichier texte. Dans l’exemple suivant, il y a deux certificats. Le nom CN est le nom du certificat racine auto-signé à partir duquel vous souhaitez générer un certificat enfant. Dans ce cas, « P2SRootCert ».

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Déclarez une variable pour le certificat racine avec l’empreinte de l’étape précédente. Remplacez THUMBPRINT par l’empreinte du certificat racine à partir duquel vous souhaitez générer un certificat enfant.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Par exemple, avec l’empreinte numérique pour P2SRootCert de l’étape précédente, la variable ressemble à ceci :

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ». Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Exporter un certificat client   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

## <a name="install"></a>Installer un certificat client exporté

Pour installer un certificat client, consultez [Installer un certificat client pour des connexions point à site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>étapes suivantes

Poursuivez votre configuration point à site.

* Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez [Configurer P2S à l’aide de l’authentification par certificat Azure native](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Pour connaître les étapes du modèle de déploiement **Classic**, consultez la page [Configurer une connexion VPN de point à site à un réseau virtuel (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Pour plus d’informations sur la résolution des problèmes liés aux connexions point à site, voir [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).