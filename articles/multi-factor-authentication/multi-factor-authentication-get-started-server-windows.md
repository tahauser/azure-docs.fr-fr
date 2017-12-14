---
title: Authentification Windows et Azure MFA Server | Microsoft Docs
description: "Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de l’authentification Windows et du serveur Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ae4503425d139c31d810f4bcf940b03357ed933a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Authentification Windows et serveur Azure Multi-Factor Authentication
La section Authentification Windows d’Azure Multi-Factor Authentication Server vous permet d’activer et de configurer l’authentification Windows des applications. Avant de configurer l’authentification Windows, gardez à l’esprit la liste suivante :

* Après la configuration, redémarrez Azure Multi-Factor Authentication pour que les services Terminal Server prennent effet.
* Si la case de correspondance d’utilisateur Authentification multifacteur Azure requise est cochée et que vous ne figurez pas dans la liste des utilisateurs, vous ne pourrez pas vous à l'ordinateur après le redémarrage.
* Les adresses IP de confiance varient selon que l'application est en mesure de fournir l'IP du client avec l'authentification. Actuellement, seul Terminal Services est pris en charge.  

> [!NOTE]
> Cette fonctionnalité n'est pas prise en charge pour sécuriser Terminal Services sur Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Pour sécuriser une application avec l'authentification Windows, utilisez la procédure suivante.
1. Sur le serveur Azure Multi-Factor Authentication, cliquez sur l’icône Authentication Windows.
   ![Authentification Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Cochez la case **Activer l’authentification Windows**. Par défaut, cette case est désactivée.
3. L'onglet Applications permet à l'administrateur de configurer une ou plusieurs applications pour l'authentification Windows.
4. Sélectionner un serveur ou une application – permet de spécifier si le serveur ou l'application est activé. Cliquez sur **OK**.
5. Cliquez sur **Ajouter…**.
6. L'onglet Adresses IP de confiance vous permet d'ignorer Azure Multi-Factor Authentication pour les sessions Windows provenant d'adresses IP spécifiques. Par exemple, si les employés utilisent l'application au bureau et à domicile, vous pouvez décider de que vous ne souhaitez pas que leurs téléphones sonnent pour Azure Multi-Factor Authentication au bureau. Pour ce faire, vous devez définir le sous-réseau du bureau comme adresse IP de confiance.
7. Cliquez sur **Ajouter…**.
8. Sélectionnez **Adresse IP unique** si vous souhaitez ignorer une adresse IP unique.
9. Sélectionnez **Plage d’adresses IP** si vous souhaitez ignorer toute une plage d’adresses IP. Exemple 10.63.193.1-10.63.193.100.
10. Sélectionnez **Sous-réseau** si vous souhaitez spécifier une plage d’adresses IP à l’aide de la notation de sous-réseau. Entrez l'adresse IP de début du sous-réseau et choisissez le masque de réseau approprié dans la liste déroulante.
11. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des appareils VPN tiers pour Azure MFA Server](multi-factor-authentication-advanced-vpn-configurations.md)

- [Augmenter votre infrastructure d’authentification existante avec l’extension NPS pour Azure MFA](multi-factor-authentication-nps-extension.md)
