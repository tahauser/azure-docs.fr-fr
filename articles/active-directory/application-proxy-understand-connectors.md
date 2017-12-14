---
title: "Présentation des connecteurs de proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fe8d5c40249431be60dc8844adf7efa1b8e87c5f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Présentation des connecteurs de proxy d’application Azure AD

Les connecteurs rendent possible le proxy d’application Azure AD. Ils sont très puissants, simples et faciles à déployer et maintenir. Cet article présente les connecteurs, leur fonctionnement et des suggestions pour optimiser le déploiement. 

## <a name="what-is-an-application-proxy-connector"></a>Qu’est-ce qu’un connecteur de proxy d’application ?

Les connecteurs sont des agents légers présent en local et qui facilitent la connexion sortante vers le service de proxy d’application. Les connecteurs doivent être installés sur un serveur Windows Server qui a accès à l’application principale. Vous pouvez organiser des connecteurs dans les groupes de connecteurs, et chaque groupe gère le trafic vers des applications spécifiques. Les connecteurs équilibrent la charge automatiquement et peuvent aider à optimiser la structure du réseau. 

## <a name="requirements-and-deployment"></a>Exigences et déploiement

Pour déployer le proxy d’application avec succès, vous devez disposer d’au moins un connecteur, mais nous vous recommandons d’en utiliser deux ou plusieurs pour assurer une meilleure résilience. Installez le connecteur sur un serveur Windows Server 2012 R2 ou une machine 2016. Le connecteur doit être en mesure de communiquer avec le service Application Proxy, ainsi que les applications locales que vous publiez. 

Pour plus d’informations sur la configuration réseau requise pour le serveur du connecteur, consultez [Prise en main du proxy d’application et de l’installation d’un connecteur](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Maintenance 
Les connecteurs et le service se chargent de toutes les tâches de haut niveau de disponibilité. Vous pouvez les ajouter ou supprimer de manière dynamique. Chaque fois qu’une nouvelle requête arrive, elle est acheminée vers un des connecteurs actuellement disponibles. Si un connecteur est temporairement indisponible, il ne répond pas à ce trafic.

Les connecteurs sont sans état et ne disposent d’aucune donnée de configuration sur l’ordinateur. Les seules données qu’ils stockent sont les paramètres de connexion au service et le certificat d’authentification. Lorsqu’ils se connectent au service, ils extraient toutes les données de configuration requises et les actualisent toutes les deux minutes.

Les connecteurs interrogent également le serveur pour déterminer s’il existe une version plus récente du connecteur. S’il en existe une, les connecteurs se mettent à jour.

Vous pouvez surveiller vos connecteurs à partir de l’ordinateur sur lequel ils s’exécutent à l’aide du journal d’événements et des compteurs de performances. Ou vous pouvez afficher leur état à partir de la page du proxy d’application du portail Azure :

 ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Vous n’êtes pas obligé de supprimer manuellement les connecteurs qui ne sont pas utilisés. Lorsqu’un connecteur est en cours d’exécution, il reste actif car il se connecte au service. Les connecteurs inutilisés sont marqués comme _inactifs_ et sont supprimés après 10 jours d’inactivité. Toutefois, si vous souhaitez réellement désinstaller un connecteur, désinstallez le service du connecteur et le service de mise à jour du serveur. Redémarrez votre ordinateur pour supprimer complètement le service.

## <a name="automatic-updates"></a>Mises à jour automatiques

Azure AD fournit les mises à jour automatiques pour tous les connecteurs que vous déployez. Tant que le service de mise à jour du connecteur de proxy d’application est en cours d’exécution, vos connecteurs se mettent automatiquement à jour. Si vous ne voyez pas le service de mise à jour du connecteur sur votre serveur, vous devez [réinstaller votre connecteur](active-directory-application-proxy-enable.md) afin d’obtenir les mises à jour. 

Si vous ne souhaitez pas attendre le chargement d’une mise à jour automatique sur votre connecteur, vous pouvez effectuer une mise à niveau manuelle. Accédez à la [page de téléchargement du connecteur](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) sur le serveur où votre connecteur se trouve et sélectionnez **Télécharger**. Ce processus lance une mise à niveau du connecteur local. 

Pour les abonnés avec plusieurs connecteurs, les mises à jour automatiques ciblent un seul connecteur à la fois dans chaque groupe afin d’éviter les temps d’arrêt dans votre environnement. 

Vous pouvez rencontrer des temps d’arrêt lors de la mise à jour de votre connecteur si :  
- Vous n’avez qu’un seul connecteur. Pour éviter ce temps d’arrêt et optimiser la disponibilité, nous vous recommandons d’installer un second connecteur et de [créer un groupe de connecteurs](active-directory-application-proxy-connectors-azure-portal.md).  
- Un connecteur se trouvait au milieu d’une transaction lorsque la mise à jour a commencé. Bien que la transaction d’origine soit perdue, votre navigateur devrait automatiquement relancer l’opération, ou vous pouvez actualiser votre page. Lorsque la demande est renvoyée, le trafic est acheminé vers un connecteur de secours.

## <a name="creating-connector-groups"></a>Créer des groupe de connecteurs

Les groupes de connecteurs vous permettent d’assigner des connecteurs spécifiques afin de servir des applications spécifiques. Vous pouvez regrouper plusieurs connecteurs, puis assigner chaque application à un groupe. 

Les groupes de connecteurs facilitent la gestion de déploiements à grande échelle. Ils améliorent aussi la latence pour les locataires dont les applications sont hébergées dans différentes régions, car vous pouvez créer des groupes de connecteurs basés sur un emplacement pour servir uniquement des applications locales. 

Pour en savoir plus sur les groupes de connecteurs, consultez [Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Planification de la capacité 

Alors que les connecteurs équilibreront automatiquement la charge au sein d’un groupe de connecteur, il est également important que vous vous assuriez d’avoir planifié une capacité suffisante entre les connecteurs pour gérer le volume de trafic attendue. En général, plus vous avez d’utilisateurs, plus vous aurez besoin d’une machine disposant de capacités importantes. Voici un tableau donnant un plan du volume pouvant être gérer par différentes machines. Veuillez noter qu’il est entièrement basé sur le nombre attendu de transactions par seconde (TPS) plutôt que sur le nombre d’utilisateurs, étant donné que les modèles d’utilisation varient et ne peuvent être utilisés pour prévoir une charge.  Notez également qu’il y aura des différences en fonction de la taille des réponses et du temps de réponse de l’application principal, une réponse volumineuse et un temps de réponse lent entraînant un TPS maximal plus faible.

|Cœurs|RAM|Latence attendue (MS)-P99|TPS max.|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Cette machine a une limite de 800 connexions. Pour toutes les autres machines, nous avons utilisé la limite de 200 connexions par défaut.
 
>[!NOTE]
>L’utilisation d’une machine utilisant 4, 8 ou 16 cœurs n’entraîne pas de grandes différences au niveau des TPS maximales. La principale différence entre ces machines se situe au niveau de la latence attendue.  

## <a name="security-and-networking"></a>Sécurité et mise en réseau

Les connecteurs peuvent être installés n’importe où sur le réseau pourvu qu’ils puissent envoyer des requêtes vers le service de proxy d’application. L’important est que l’ordinateur qui exécute le connecteur dispose également d’un accès à vos applications. Vous pouvez installer les connecteurs à l’intérieur de votre réseau d’entreprise ou sur une machine virtuelle qui s’exécute dans le cloud. Les connecteurs peuvent s’exécuter dans une zone démilitarisée (DMZ), mais ce n’est pas nécessaire car tout le trafic est sortant afin sécuriser votre réseau.

Les connecteurs envoient uniquement des demandes sortantes. Le trafic sortant est envoyé au service de proxy d’application et aux applications publiées. Il n’est pas nécessaire pour ouvrir des ports d’entrée car le trafic passe dans les deux sens une fois qu’une session est établie. Il n’est pas nécessaire de configurer l’équilibrage de charge entre les connecteurs ou de configurer l’accès entrant à travers vos pare-feu. 

Pour plus d’informations sur la configuration des règles sortantes de pare-feu, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md).

Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue. 

## <a name="performance-and-scalability"></a>Performances et évolutivité

La mise à l’échelle pour le proxy d’application est transparente, mais l’échelle est un facteur lorsqu’il s’agit de connecteurs. Vous devez disposer de suffisamment de connecteurs pour gérer les pics de trafic. Toutefois, il n’est pas nécessaire de configurer l’équilibrage des charges, car tous les connecteurs dans un groupe de connecteurs équilibrent automatiquement la charge.

Étant donné que les connecteurs sont sans état, ils ne sont pas affectés par le nombre d’utilisateurs ou de sessions. Ils varient plutôt selon le nombre de requêtes et leur taille de charge utile. Avec le trafic web standard, une machine moyenne peut gérer quelques milliers de requêtes par seconde. La capacité spécifique dépend des caractéristiques exactes de la machine. 

Les performances du connecteur sont liées au processeur et à la mise en réseau. Les performances processeur sont nécessaires pour le chiffrement et le déchiffrement SSL, tandis que la mise en réseau est vitale pour obtenir une connectivité rapide aux applications et au service en ligne dans Azure.

En revanche, la mémoire est moins problématique pour les connecteurs. Le service en ligne s’occupe de la majeure partie du traitement et de tout le trafic non authentifié. Tout ce qui peut être effectué dans le cloud est réalisé dans le cloud. 

L’équilibrage de charge se produit entre les connecteurs d’un groupe de connecteurs donné. Nous faisons une variation de tourniquet pour déterminer quel connecteur du groupe sert une requête particulière. Après avoir choisi un connecteur, nous mettons en place une affinité de session entre cet utilisateur et l’application pour la durée de la session. Si, pour une raison quelconque, la machine ou le connecteur devient indisponible, le trafic commencera à passer à un autre connecteur du groupe. Cette résilience est également la raison pour laquelle nous vous recommandons d’avoir plusieurs connecteurs.

Un autre facteur affectant les performances est la qualité de la connexion réseau entre les connecteurs, y compris : 

* **Le service en ligne** : les connexions à latence faible ou élevée au service du proxy d’application d’Azure influence les performances du connecteur. Pour des performances optimales, connectez votre organisation à Azure avec Express Route. Sinon, assurez-vous que l’équipe réseau garantit que les connexions à Azure sont gérées de la façon la plus efficace. 
* **Applications principales** : dans certains cas, il existe des proxys supplémentaires entre le connecteur et les applications principales capables de ralentir ou d’empêcher des connexions. Pour résoudre les problèmes de ce scénario, ouvrez un navigateur à partir du serveur du connecteur et essayez d’accéder à l’application. Si vous exécutez les connecteurs dans Azure alors que les applications sont locales, l’expérience peut ne pas être celle que vos utilisateurs attendent.
* **Contrôleurs de domaine** : si les connecteurs effectuent l’authentification unique à l’aide de la délégation Kerberos contrainte (KCD), ils contactent les contrôleurs de domaine avant d’envoyer la requête au serveur principal. Les connecteurs ont un cache de tickets Kerberos, mais dans un environnement occupé, la réactivité des contrôleurs de domaine peut affecter les performances. Ce problème est plus courant pour les connecteurs qui s’exécutent dans Azure mais qui communiques avec les contrôleurs de domaine locaux. 

Pour plus d’informations sur l’optimisation de votre réseau, consultez [Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure Active Directory](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Jonction de domaine

Les connecteurs peuvent s’exécuter sur une machine qui n’est pas jointe à un domaine. Toutefois, si vous souhaitez utiliser l’authentification unique (SSO) pour les applications qui utilisent l’authentification Windows intégrée (IWA), vous avez besoin d’un ordinateur joint à un domaine. Dans ce cas, les ordinateurs du connecteur doivent être joints à un domaine qui peut effectuer la délégation [Kerberos](https://web.mit.edu/kerberos) contrainte pour le compte des utilisateurs pour les applications publiées.

Les connecteurs peuvent également être joints à des domaines ou forêts qui disposent d’une approbation partielle ou pour les contrôleurs de domaine en lecture seule.

## <a name="connector-deployments-on-hardened-environments"></a>Déploiements des connecteurs sur les environnements de sécurité renforcés

Généralement, le déploiement de connecteurs est simple et ne nécessite aucune configuration spéciale. Mais certaines conditions uniques doivent être prises en compte :

* Les organisations qui limitent le trafic sortant doivent [ouvrir les ports requis](active-directory-application-proxy-enable.md#open-your-ports).
* Des machines conformes aux normes FIPS peuvent être requises pour modifier leur configuration afin d’autoriser les processus du connecteur à générer et stocker un certificat.
* Les organisations qui verrouillent leur environnement sur la base des processus qui émettent des requêtes réseau doivent s’assurer que les deux services de connecteur sont activés pour accéder à tous les ports et adresses IP nécessaires.
* Dans certains cas, les proxys de transfert sortants peuvent arrêter l’authentification de certificat bidirectionnelle et entraîner un échec de la communication.

## <a name="connector-authentication"></a>Authentification du connecteur

Afin de fournir un service sécurisé, les connecteurs doivent s’authentifier sur le service, et le service doit s’authentifier sur les connecteurs. L’authentification est effectuée à l’aide de certificats client et serveur lorsque les connecteurs établissent la connexion. De cette façon, le nom d’utilisateur et le mot de passe de l’administrateur ne sont pas stockés sur l’ordinateur du connecteur.

Les certificats utilisés sont spécifiques au service de proxy d’application. Ils sont créés lors de l’inscription initiale et sont renouvelés automatiquement par les connecteurs tous les quelques mois. 

Si un connecteur n’est pas connecté au service pendant plusieurs mois, ses certificats ont peut-être expiré. Dans ce cas, désinstallez et réinstallez le connecteur pour déclencher l’inscription. Vous pouvez exécuter les commandes PowerShell suivantes :

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Sous le capot

Comme les connecteurs sont basés sur le proxy d’application web Windows Server, la plupart ont les mêmes outils de gestion, y compris les journaux d’événements Windows

 ![Gérez les journaux d’événements avec l’Observateur d’événements](./media/application-proxy-understand-connectors/event-view-window.png)

et les compteurs de performances Windows. 

 ![Ajouter des compteurs au connecteur avec l’Analyseur de performances](./media/application-proxy-understand-connectors/performance-monitor.png)

Les connecteurs ont des journaux de session et admin. Les journaux admin incluent les événements principaux et leurs erreurs. Les journaux de session incluent toutes les transactions et les détails de traitement. 

Pour afficher les journaux, accédez l’Observateur d’événements, ouvrez le menu **Affichage**, puis activez **Afficher les journaux d’analyse et de débogage**. Ensuite, permettez-leur de lancer la collecte d’événements. Ces journaux n’apparaissent pas dans le proxy d’application web dans Windows Server 2012 R2, car les connecteurs sont basés sur une version plus récente.

Vous pouvez examiner l’état du service dans la fenêtre Services. Le connecteur compose de deux services Windows : le connecteur lui-même et l’outil de mise à jour. Tous deux doivent s’exécuter en permanence.

 ![AzureAD Services Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Étapes suivantes


* [Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs](active-directory-application-proxy-connectors-azure-portal.md)
* [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md)
* [Résoudre les erreurs du proxy d’application et du connecteur](active-directory-application-proxy-troubleshoot.md)
* [Comment installer silencieusement le connecteur du Proxy d'application Azure AD](active-directory-application-proxy-silent-installation.md)

