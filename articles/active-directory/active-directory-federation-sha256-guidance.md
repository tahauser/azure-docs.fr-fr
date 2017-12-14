---
title: "Modifier l’algorithme de hachage de signature pour l’approbation de partie de confiance Office 365 | Microsoft Docs"
description: "Cette page fournit des instructions permettant de modifier l’algorithme SHA pour l’approbation de fédération avec Office 365"
keywords: "SHA1, SHA256, O365, fédération, aadconnect, adfs, ad fs, modifier sha, approbation de fédération, approbation de partie de confiance"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: ec7eee36888d825d65335db590731103aabbf5c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Modifier l’algorithme de hachage de signature pour l’approbation de partie de confiance Office 365
## <a name="overview"></a>Vue d'ensemble
Azure Active Directory Federation Services (AD FS) signe ses jetons dans Microsoft Azure Active Directory pour vous assurer qu’ils sont infalsifiables. Cette signature peut reposer sur SHA1 ou SHA256. Azure Active Directory prend désormais en charge les jetons signés avec un algorithme SHA256 et recommande de configurer l’algorithme de signature de jetons SHA256 pour un niveau de sécurité optimal. Cet article décrit comment configurer l’algorithme de signature de jetons sur le niveau de sécurité le plus élevé, SHA256.

>[!NOTE]
>Microsoft recommande d’utiliser l’algorithme de signature de jetons SHA256, qui est plus sûr que SHA1. Cependant, SHA1 est toujours une option prise en charge.

## <a name="change-the-token-signing-algorithm"></a>Modification de l’algorithme de signature de jetons
Une fois que vous avez défini l’algorithme de signature selon l’un des deux processus ci-dessous, ADFS signe les jetons avec SHA256 pour approuver la partie de confiance Office 365. Aucune autre modification de la configuration n’est requise, et ce changement n’a aucun impact sur votre capacité à accéder à Office 365 ou à d’autres applications Azure AD.

### <a name="ad-fs-management-console"></a>Console de gestion AD FS
1. Ouvrez la console de gestion AD FS sur le serveur AD FS principal.
2. Développez le nœud AD FS et cliquez sur **Approbations de la partie de confiance**.
3. Faites un clic droit sur votre approbation de partie de confiance Office 365/Azure et sélectionnez **Propriétés**.
4. Sélectionnez l’onglet **Avancé** puis l’algorithme de hachage sécurisé SHA256.
5. Cliquez sur **OK**.

![Algorithme de signature SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Applets de commande PowerShell d’AD FS
1. Sur un serveur AD FS, ouvrez PowerShell avec des privilèges d’administrateur.
2. Définissez l’algorithme de hachage sécurisé avec l’applet de commande **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>À lire également
* [Réparation de l’approbation Office 365 avec Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

