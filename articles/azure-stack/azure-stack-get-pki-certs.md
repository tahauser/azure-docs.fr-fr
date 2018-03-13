---
title: "Générer des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs"
description: "Décrit le processus de déploiement de certificat pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 991a94e4ca41bad438a3c8d06e4e1f691cff91bc
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Générer des certificats pour infrastructure à clé publique pour le déploiement Azure Stack
Maintenant que vous connaissez [les exigences de certificat pour infrastructure à clé publique](azure-stack-pki-certs.md) pour les déploiements Azure Stack, vous devez obtenir ces certificats auprès de l’autorité de certification (AC) de votre choix. 

## <a name="request-certificates-using-an-inf-file"></a>Demander des certificats à l’aide d’un fichier INF
Une méthode pour demander des certificats à une autorité de certification publique ou une autorité de certification interne consiste à utiliser un fichier INF. L’utilitaire certreq.exe intégré à Windows peut utiliser un fichier INF spécifiant les détails du certificat afin de générer un fichier de requête, comme décrit dans cette section. 

### <a name="sample-inf-file"></a>Exemple de fichier INF 
L’exemple de fichier INF de demande de certificat peut être utilisé pour créer un fichier de demande de certificat hors connexion pour l’envoi à une autorité de certification (interne ou publique). Le fichier INF couvre tous les points de terminaison requis (y compris les services PaaS facultatifs) dans un certificat générique unique. 

L’exemple de fichier INF part du principe que la région est égale à **sea** et que la valeur de nom de domaine complet externe est **sea&#46;contoso&#46;com**. Modifiez ces valeurs pour qu’elles correspondent à votre environnement avant de générer un fichier .INF pour votre déploiement. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Générer et envoyer une requête à l’autorité de certification
Le workflow suivant décrit comment vous pouvez personnaliser et utiliser l’exemple de fichier INF généré précédemment pour demander un certificat à une autorité de certification :

1. **Modifiez et enregistrez le fichier INF**. Copiez l’exemple fourni et enregistrez-le dans un nouveau fichier texte. Remplacez le nom de l’objet et le nom de domaine complet externe par les valeurs qui correspondent à votre déploiement, et enregistrez le fichier sous un fichier .INF.
2. **Générez une requête à l’aide de certreq**. À l’aide d’un ordinateur Windows, lancez une invite de commandes en tant qu’administrateur et exécutez la commande suivante pour générer un fichier de requête (.req) : `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Envoyez à l’autorité de certification**. Envoyez le fichier .REQ généré à votre autorité de certification (elle peut être interne ou publique).
4. **Importez le fichier .CER**. L’autorité de certification renvoie un fichier .CER. À l’aide du même ordinateur Windows que celui à partir duquel vous avez généré le fichier de requête, importez le fichier .CER retourné dans le magasin de l’ordinateur/personnel. 
5. **Exportez et copiez le fichier . PFX dans les dossiers de déploiement**. Exportez le certificat (y compris la clé privée) au format de fichier .PFX, puis copiez le fichier .PFX dans les dossiers de déploiement décrits dans les [exigences d’infrastructure à clé publique de déploiement Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Étapes suivantes
[Préparer des certificats PKI Azure Stack](prepare-pki-certs.md)