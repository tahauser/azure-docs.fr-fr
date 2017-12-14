---
title: Authentification RADIUS et Azure MFA Server | Microsoft Docs
description: "Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de l’authentification RADIUS et du serveur Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 07b242699e016550c82f491c1678c6dbc9f7234d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Intégration de l'authentification RADIUS avec le serveur Azure Multi-Factor Authentication

RADIUS est un protocole standard pour accepter les demandes d'authentification et traiter ces demandes. Le serveur Azure Multi-Factor Authentication peut fonctionner comme un serveur RADIUS. Pour ajouter la vérification en deux étapes, insérez-le entre votre client RADIUS (appliance VPN) et la cible d’authentification. Il peut s’agir d’Active Directory, d’un annuaire LDAP, ou bien d’un autre serveur RADIUS. Pour garantir le fonctionnement Azure Multi-Factor Authentication (MFA), vous devez configurer le serveur Azure MFA pour communiquer avec les serveurs clients et la cible de l’authentification. Le serveur Azure MFA accepte les requêtes provenant d'un client RADIUS, valide les informations d'identification par rapport à la cible de l'authentification, ajoute l’authentification multifacteur Azure et renvoie une réponse au client RADIUS. La demande d’authentification est complète uniquement si l'authentification principale et l'authentification multifacteur Azure réussissent.

> [!NOTE]
> Le serveur MFA prend uniquement en charge les protocoles RADIUS PAP (Password Authentication Protocol) et MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) lorsqu'il agit comme un serveur RADIUS.  Les autres protocoles comme EAP (Extensible Authentication Protocol) peuvent être utilisés lorsque le serveur MFA agit comme un proxy RADIUS vers un autre serveur RADIUS prenant en charge ce protocole.
>
> Dans cette configuration, les jetons SMS et OATH unidirectionnels ne fonctionnent pas car le serveur MFA n'est pas capable d'initier correctement une réponse RADIUS Challenge à l'aide d’autres protocoles.

![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>Ajouter un client RADIUS
Pour configurer l'authentification RADIUS, installez le serveur Azure Multi-Factor Authentication sur un serveur Windows. Si vous utilisez un environnement Active Directory, le serveur doit être lié au domaine à l'intérieur du réseau. Utilisez la procédure suivante pour configurer le serveur Azure Multi-Factor Authentication :

1. Sur le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification RADIUS dans le menu de gauche.
2. Cochez la case **Activer l'authentification RADIUS**.
3. Dans l’onglet Clients, modifiez le port d'authentification et le port de gestion si le service Azure MFA RADIUS doit écouter les demandes RADIUS sur des ports non standard.
4. Cliquez sur **Ajouter**.
5. Entrez l'adresse IP de l'appliance ou du serveur qui s'authentifieront auprès du serveur Azure Multi-Factor Authentication, un nom d’application (facultatif) et un secret partagé.

  Le nom de l’application apparaît dans les rapports et peut être affiché dans les messages d’authentification SMS ou d’application mobile.

  Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur l’appliance/serveur.

6. Cochez la case de **correspondance d’utilisateur Authentification multifacteur requise** si tous les utilisateurs ont été importés sur le serveur et soumis à l’authentification multifacteur. Si un grand nombre d’utilisateurs n’ont pas encore été importés sur le serveur ou ne sont pas soumis à la vérification en deux étapes, laissez la case désactivée.
7. Cochez la case **Activer un jeton OATH de secours** si vous souhaitez utiliser les codes d’accès OATH à partir d’applications de vérification mobile comme méthode de sauvegarde.
8. Cliquez sur **OK**.

Répétez les étapes 4 à 8 pour ajouter autant de clients RADIUS que nécessaire.

## <a name="configure-your-radius-client"></a>Configurer votre client RADIUS

1. Cliquez sur l'onglet **Cible**.
2. Si le serveur Azure MFA est installé sur un serveur appartenant à un domaine dans un environnement Active Directory, sélectionnez Domaine Windows.
3. Si les utilisateurs doivent être authentifiés par rapport à un répertoire LDAP, sélectionnez la **liaison LDAP**.

  Sélectionnez l’icône Intégration de répertoires et modifiez la configuration LDAP sous l’onglet Paramètres afin que le serveur puisse se lier à votre répertoire. Vous trouverez des instructions sur la configuration LDAP dans le [guide de configuration du proxy LDAP](multi-factor-authentication-get-started-server-ldap.md).

4. Si les utilisateurs doivent être authentifiés par rapport à un autre serveur RADIUS, sélectionnez ce serveur RADIUS.
5. Cliquez sur **Ajouter** pour configurer le serveur vers lequel le serveur Azure MFA enverra des demandes RADIUS par proxy.
6. Dans la boîte de dialogue Ajouter un serveur RADIUS, entrez l'adresse IP du serveur RADIUS et un secret partagé.

  Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur le serveur RADIUS. Modifiez le port d'authentification et le port de gestion si des ports différents sont utilisés par le serveur RADIUS.

7. Cliquez sur **OK**.
8. Ajoutez le serveur Azure MFA comme client RADIUS dans l'autre serveur RADIUS afin qu'il puisse traiter les demandes d'accès qu’il reçoit du serveur Azure MFA. Utilisez le même secret partagé configuré sur le serveur Azure Multi-Factor Authentication.

Répétez ces étapes pour ajouter des serveurs RADIUS supplémentaires. Configurez l’ordre dans lequel le serveur Azure MFA doit les appeler à l’aide des boutons **Monter** et **Descendre**.

Vous avez configuré avec succès le serveur Azure Multi-Factor Authentication. Le serveur écoute désormais sur les ports configurés pour les demandes d'accès RADIUS à partir de clients configurés.   

## <a name="radius-client-configuration"></a>Configuration du client RADIUS
Pour configurer le client RADIUS, suivez ces indications :

* Configurez votre appliance/serveur pour s’authentifier via RADIUS auprès de l’adresse IP du serveur Azure Multi-Factor Authentication, qui agit en tant que serveur RADIUS.
* Utilisez le même secret partagé configuré précédemment.
* Configurez le délai d'expiration RADIUS sur 30 à 60 secondes pour pouvoir valider les informations d'identification de l'utilisateur, effectuez la vérification en deux étapes, recevez leur réponse et répondez à la demande d'accès RADIUS.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [intégrer l’authentification RADIUS](multi-factor-authentication-nps-extension.md) si Azure Multi-Factor Authentication se trouve dans le cloud. 