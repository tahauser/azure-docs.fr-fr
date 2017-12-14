---
title: "Configurer le serveur Azure MFA pour la haute disponibilité | Microsoft Docs"
description: "Déployez plusieurs instances du serveur Azure Multi-Factor Authentication dans les configurations qui fournissent une haute disponibilité."
services: multi-factor-authentication
keywords: Azure MFA,
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c2f9a2422ea5a47edd80948073c7f7a6cb0d0945
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurer un serveur Azure Multi-Factor Authentication pour la haute disponibilité

Pour atteindre une haute disponibilité avec votre déploiement de serveur Azure MFA, vous devez déployer plusieurs serveurs MFA. Cette section fournit des informations sur la conception à charge équilibrée permettant d’atteindre vos objectifs de haute disponibilité dans votre déploiement de serveur Azure MFA.

## <a name="mfa-server-overview"></a>Vue d’ensemble du serveur MFA

L’architecture du service de serveur Azure MFA comprend plusieurs composants, comme indiqué dans le diagramme suivant :

 ![Architecture du serveur MFA](./media/mfa-server-high-availability/mfa-ha-architecture.png)

Un serveur MFA est un serveur Windows Server sur lequel le logiciel Azure Multi-Factor Authentication est installé. L’instance du serveur MFA doit être activée par le service MFA dans Azure pour fonctionner. Plusieurs serveurs MFA peuvent être installés localement.

Par défaut, le premier serveur MFA installé devient le serveur MFA maître dès son activation par le service Azure MFA. Le serveur MFA maître possède une copie accessible en écriture de la base de données PhoneFactor.pfdata. Les installations ultérieures d’instances de serveur MFA sont appelées instances subordonnées. Les instances subordonnées MFA ont une copie en lecture seule répliquée de la base de données PhoneFactor.pfdata. Les serveurs MFA répliquent les informations par appel de procédure distante (RPC). Tous les serveurs MFA doivent collectivement être joints à un domaine ou être autonomes pour répliquer les informations.

Les serveurs MFA maître et subordonnés communiquent avec le service MFA quand une authentification à 2 facteurs est requise. Par exemple, quand un utilisateur tente d’accéder à une application qui exige une authentification à 2 facteurs, il est d’abord authentifié par un fournisseur d’identité, comme Active Directory (AD).

Après une authentification réussie auprès d’Active Directory, le serveur MFA communique avec le service MFA. Le service MFA attend une notification provenant du service MFA pour autoriser ou refuser l’accès de l’utilisateur à l’application.

Si le serveur MFA maître est mis hors connexion, les authentifications peuvent toujours être traitées, mais les opérations qui exigent d’apporter des modifications à la base de données MFA ne peuvent pas être traitées. (Exemples : ajout d’utilisateurs, modifications de code confidentiel en libre service et modification des informations de l’utilisateur.)

## <a name="deployment"></a>Déploiement

Considérez les points importants suivants pour équilibrer la charge du serveur Azure MFA et de ses composants associés.

* **Utilisation de la norme RADIUS pour atteindre une haute disponibilité**. Si vous utilisez des serveurs Azure MFA en tant que serveurs RADIUS, vous pouvez potentiellement configurer un seul serveur MFA en tant que cible d’authentification RADIUS principale et les autres serveurs Azure MFA en tant que cibles d’authentification secondaires. Toutefois, cette méthode d’obtention d’une haute disponibilité risque de ne pas être pratique, car il existe un délai d’attente à observer en cas d’échec de l’authentification sur la cible d’authentification principale avant de pouvoir être authentifié sur la cible d’authentification secondaire. Il est plus efficace d’équilibrer la charge du trafic RADIUS entre le client RADIUS et les serveurs RADIUS (dans ce cas, les serveurs Azure MFA jouent le rôle de serveurs RADIUS) afin de pouvoir configurer les clients RADIUS avec une seule URL vers laquelle pointer.
* **Besoin de promouvoir manuellement les serveurs subordonnés MFA**. Si le serveur Azure MFA maître est mis hors connexion, les serveurs Azure MFA secondaires continuent à traiter les demandes MFA. Toutefois, tant qu’un serveur MFA maître n’est pas disponible, les administrateurs ne peuvent pas ajouter d’utilisateurs ni modifier des paramètres MFA, et les utilisateurs ne peuvent pas apporter de modifications en utilisant leur portail. La promotion d’un serveur subordonné MFA au rôle de maître est toujours un processus manuel.
* **Possibilité de séparation des composants**. Le serveur Azure MFA comprend plusieurs composants qui peuvent être installés sur la même instance de Windows Server ou sur différentes instances. Ces composants incluent le portail utilisateur, le service web de l’application mobile et l’adaptateur (agent) ADFS. Cette séparation possible permet d’utiliser le proxy d’application web pour publier le portail utilisateur et le serveur web de l’application mobile à partir du réseau de périmètre. Une telle configuration renforce la sécurité globale de votre conception, comme indiqué dans le diagramme suivant. Le portail utilisateur MFA et le serveur web de l’application mobile peuvent aussi être déployés dans des configurations à charge équilibrée de haute disponibilité.

   ![Serveur MFA avec un réseau de périmètre](./media/mfa-server-high-availability/mfasecurity.png)

* **Le mot de passe à usage unique par SMS (également appelé SMS unidirectionnel) requiert l’utilisation de sessions rémanentes si le trafic est à charge équilibrée**. Un SMS unidirectionnel est une option d’authentification qui amène le serveur MFA à envoyer aux utilisateurs un message texte contenant un mot de passe à usage unique. L’utilisateur entre le mot de passe à usage unique dans une fenêtre d’invite pour répondre à la demande MFA. Si vous équilibrez la charge des serveurs Azure MFA, le même serveur que celui qui a traité la demande d’authentification initiale doit recevoir le message du mot de passe à usage unique de la part de l’utilisateur. Si un autre serveur MFA reçoit la réponse contenant le mot de passe à usage unique, la demande d’authentification échoue. Pour plus d’informations, consultez [Mot de passe à usage unique par SMS ajouté à un serveur Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Les déploiements à charge équilibrée du portail utilisateur et du service web de l’application mobile nécessitent des sessions rémanentes**. Si vous équilibrez la charge du portail utilisateur MFA et du service web de l’application mobile, chaque session doit rester sur le même serveur.

## <a name="high-availability-deployment"></a>Déploiement de la haute disponibilité

Le diagramme suivant illustre une implémentation à charge équilibrée de haute disponibilité complète d’Azure MFA et ses composants, avec les services AD FS à des fins de référence.

 ![Implémentation de la haute disponibilité du serveur Azure MFA](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Remarques les éléments suivants dans la zone numérotée en conséquence du diagramme précédent.

1. Les deux serveurs Azure MFA (MFA1 et MFA2) sont à charge équilibrée (mfaapp.contoso.com) et sont configurés pour utiliser un port statique (4443) pour répliquer la base de données PhoneFactor.pfdata. Le SDK du service web est installé sur chaque serveur MFA pour activer la communication sur le port TCP 443 avec les serveurs AD FS. Les serveurs MFA sont déployés dans une configuration à charge équilibrée sans état. Toutefois, si vous voulez utiliser un mot de passe à usage unique par SMS, vous devez utiliser l’équilibrage de charge avec état.
   ![Serveur Azure MFA - Haute disponibilité du serveur d’applications](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Étant donné que RPC utilise des ports dynamiques, il est déconseillé d’ouvrir des pare-feu jusqu’à la plage de ports dynamiques que RPC peut éventuellement utiliser. Si vous avez un pare-feu **entre** vos serveurs d’applications MFA, vous devez configurer le serveur MFA afin de communiquer sur un port statique pour le trafic de réplication entre les serveurs maîtres et subordonnés et ouvrir ce port sur votre pare-feu. Vous pouvez forcer le port statique en créant une valeur de Registre DWORD sur ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` appelée ```Pfsvc_ncan_ip_tcp_port``` et en définissant sa valeur sur un port statique disponible. Les connexions sont toujours lancées par les serveurs MFA subordonnés vers le maître, le port statique est seulement exigé sur le maître, mais étant donné que vous pouvez promouvoir un serveur subordonné au rôle de maître à tout moment, vous devez définir le port statique sur tous les serveurs MFA.

2. Les deux serveurs du portail utilisateur/d’applications mobiles MFA (MFA-UP-MAS1 et MFA-UP-MAS2) sont à charge équilibrée dans une configuration **avec état** (mfa.contoso.com). N’oubliez pas que les sessions rémanentes sont indispensables pour équilibrer la charge du portail utilisateur et du service d’applications mobiles MFA.
   ![Serveur Azure MFA - Haute disponibilité du portail utilisateur et du service d’applications mobiles](./media/mfa-server-high-availability/mfaportal.png)
3. La batterie de serveurs AD FS est à charge équilibrée et publiée sur Internet par le biais de proxys AD FS à charge équilibrée dans le réseau de périmètre. Chaque serveur AD FS utilise l’agent AD FS pour communiquer avec les serveurs Azure MFA à l’aide d’une URL unique à charge équilibrée (mfaapp.contoso.com) sur le port TCP 443.

## <a name="next-steps"></a>Étapes suivantes

* [Installer et configurer un serveur Azure MFA](multi-factor-authentication-get-started-server.md)
