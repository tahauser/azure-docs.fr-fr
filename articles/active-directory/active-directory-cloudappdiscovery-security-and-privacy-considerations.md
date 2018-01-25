---
title: "Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery | Microsoft Docs"
description: "Cette rubrique décrit les considérations de sécurité et de confidentialité liées à Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 7775bad1503e62e1887e6d85f67c3107f48866bf
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery
Cette rubrique explique comment les données sont collectées, traitées et sécurisées dans Azure Active Directory Cloud App Discovery. Microsoft s’engage à protéger la confidentialité de vos données et à les sécuriser. Microsoft fournit des services dans le respect des pratiques de sécurisation du cycle de vie du développement de logiciels. La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft.

> [!TIP] 
> Découvrez le nouveau service Cloud App Discovery sans agent dans Azure Active Directory (Azure AD), qui est amélioré par [l’intégration à Microsoft Cloud App Security](https://portal.cloudappsecurity.com). 

## <a name="overview"></a>Vue d’ensemble
Cloud App Discovery est une fonctionnalité d’Azure AD hébergée dans Microsoft Azure.  
Un agent Cloud App Discovery Endpoint Agent permet de recueillir des données de détection d’application à partir d’ordinateurs gérés par le service informatique. Les données recueillies sont envoyées en toute sécurité via un canal chiffré au service Azure AD Cloud App Discovery. Les données Cloud App Discovery d’une organisation sont ensuite visibles dans le portail Azure. 

![Fonctionnement de Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

Les sections ci-dessous décrivent le flux sécurisé des informations qui transitent de votre organisation vers le service Cloud App Discovery, puis vers le portail Cloud App Discovery.

## <a name="collecting-data-from-your-organization"></a>Collecte de données de votre organisation
Pour utiliser la fonctionnalité Cloud App Discovery d’Azure Active Directory afin d’obtenir des informations sur les applications utilisées par les employés de votre organisation, vous devez tout d’abord déployer l’agent Cloud App Discovery Endpoint Agent sur les ordinateurs de votre organisation.

Les administrateurs du client Azure Active Directory (ou leur délégué) peuvent télécharger le package d’installation d’agent à partir du portail Azure. Vous pouvez installer l’agent manuellement ou l’installer sur plusieurs ordinateurs de l’organisation à l’aide de SCCM ou de la stratégie de groupe.

Pour plus d’informations sur les options de déploiement, consultez le [Guide de déploiement de Cloud App Discovery avec la stratégie de groupe](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### <a name="data-collected-by-the-agent"></a>Données recueillies par l’agent
Les informations présentées dans la liste suivante sont collectées par l’agent quand des connexions sont établies à des applications web. Les informations sont recueillies uniquement pour les applications que l’administrateur a configurées pour la détection. Vous pouvez modifier la liste des applications cloud que l’agent surveille avec Cloud App Discovery dans Azure AD, à partir du [portail Azure](https://portal.azure.com/), sous **Paramètres**->**Collection de données**->**Liste App Collection**. 

**Catégorie d’informations** : informations sur l’utilisateur  
**Description** : nom d’utilisateur Windows du processus qui a fait une demande à l’application web cible (par exemple, DOMAIN\nom_utilisateur), ainsi que l’identificateur de sécurité Windows (SID) de l’utilisateur.

**Catégorie d’informations** : informations sur le processus  
**Description** : nom du processus qui a envoyé la demande à l’application web cible (par exemple, « iexplore.exe »).

**Catégorie d’informations** : informations sur la machine  
**Description** : nom NetBIOS de la machine sur laquelle l’agent est installé.

**Catégorie d’informations** : Informations sur le trafic d’application  
**Description** : informations de connexion suivantes :

* Adresses IP source (ordinateur local) et de destination et numéros de port
* Adresse IP publique de l’organisation par le biais de laquelle la demande est envoyée
* Heure de la demande
* Volume de trafic envoyé et reçu
* Version IP (4 ou 6)
* Pour les connexions TLS uniquement : nom d’hôte cible tiré de l’extension de l’Indication du nom du serveur ou du certificat de serveur.

Les informations HTTP suivantes :

* Méthode (GET, POST, etc.)
* Protocole (HTTP/1.1, etc.)
* Chaîne d’agent utilisateur
* Nom d’hôte
* URI cible (à l’exclusion de la chaîne de requête)
* Informations de type de contenu
* Informations d’URL de point d’accès (à l’exception de la chaîne de requête)

> [!NOTE]
> Les informations HTTP ci-dessus sont recueillies pour toutes les connexions non chiffrées.
> Pour les connexions TLS, ces informations sont capturées uniquement quand le paramètre « Inspection approfondie » est activé dans le portail. Le paramètre est activé par défaut.
> Pour plus d’informations, consultez la section ci-dessous, ainsi que la page [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 

En plus des données sur l’activité réseau, l’agent collecte les données anonymes suivantes
* Configuration logicielle et matérielle
* Rapports d'erreurs
* Données sur l’utilisation de l’agent


### <a name="how-the-agent-works"></a>Fonctionnement de l’agent
L’installation de l’agent comprend deux composants :

* un composant en mode utilisateur ;
* un composant de pilote en mode noyau (pilote de plateforme de filtrage Windows).

Lors de son installation initiale, l’agent stocke sur l’ordinateur un certificat approuvé spécifique à celui-ci, qu’il utilise ensuite pour établir une connexion sécurisée avec le service Cloud App Discovery. L’agent extrait régulièrement la configuration de la stratégie auprès du service Cloud App Discovery via cette connexion sécurisée. La stratégie comporte des informations sur les applications cloud à surveiller et précise entre autres choses si la mise à jour automatique doit être activée.

Comme le trafic web est envoyé et reçu sur l’ordinateur à partir de Chrome ou Internet Explorer, l’agent Cloud App Discovery analyse le trafic et extrait les métadonnées pertinentes (consultez la section **Données recueillies par l’agent** plus haut).  
Chaque minute, l’agent envoie les métadonnées recueillies au service Cloud App Discovery sur un canal chiffré.

Le composant pilote intercepte le trafic chiffré et s’introduit dans le flux chiffré. Vous trouverez des détails supplémentaires dans la section **Interception des données à partir de connexions chiffrées (inspection approfondie)** ci-après.

### <a name="respecting-user-privacy"></a>Respect de la confidentialité de l’utilisateur
Notre objectif est de fournir aux administrateurs les outils nécessaires pour définir un équilibre entre une optique détaillée de l’utilisation d’application et la confidentialité utilisateur, conformément aux exigences de leur organisation. À cette fin, nous fournissons les boutons suivants dans la page Paramètres du portail :

* **Collecte de données**: les administrateurs peuvent choisir de spécifier les applications ou les catégories d’applications pour lesquelles ils souhaitent obtenir des données de découverte.
* **Inspection approfondie** : les administrateurs peuvent choisir de spécifier si l’agent recueille le trafic HTTP pour les connexions SSL/TLS (c’est ce que l’on appelle **l’inspection approfondie**). Pour plus d'informations à ce sujet, consultez la section suivante.
* **Options de consentement**: les administrateurs peuvent utiliser le portail Cloud App Discovery pour choisir d’avertir ou non les utilisateurs de la collecte de données par l’agent et de demander le consentement de l’utilisateur avant le début de la collecte en question.

L’agent de point de terminaison Cloud App Discovery recueille uniquement les informations décrites dans la section ci-dessus, **Données recueillies par l’agent** .

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Interception des données à partir de connexions chiffrées (inspection approfondie)
Comme indiqué précédemment, les administrateurs peuvent configurer l'agent pour qu’il analyse les données à partir de connexions chiffrées (« inspection approfondie »). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) est l’un des protocoles les plus courants parmi ceux utilisés aujourd’hui sur Internet. En chiffrant les communications avec TLS, un client peut établir un canal de communication sécurisé et privé avec un serveur web ; TLS offre une protection essentielle permettant de passer des informations d’authentification et empêcher la divulgation d’informations confidentielles.

Bien que le canal chiffré et sécurisé de bout en bout fourni par TLS offre une protection importante de la confidentialité et de la sécurité, le protocole est fréquemment utilisé à des fins malveillantes ou mal intentionnées. En fait, le protocole TLS est souvent appelé « protocole de contournement de pare-feu universel ». Le problème vient du fait que la plupart des pare-feu ne peuvent pas inspecter les communications TLS, car les données de la couche Application sont chiffrées avec SSL. Sachant cela, les pirates exploitent fréquemment TLS pour fournir des charges utiles malveillantes à un utilisateur. Ils sont en effet convaincus que même les pare-feu de la couche Application les plus intelligents sont complètement aveugles sur TLS et doivent simplement relayer les communications TLS entre les hôtes. Il est fréquent que les utilisateurs finaux exploitent TLS pour contourner les contrôles d’accès appliqués par leur pare-feu d’entreprise et les serveurs proxy. Ils s’en servent pour se connecter aux proxys publics et pour le tunneling de protocoles non TLS via le pare-feu qui pourraient autrement être bloqués par la stratégie.

L’inspection approfondie permet à l'agent Cloud App Discovery d’agir comme un intercepteur de confiance. Quand un client demande à accéder à une ressource protégée par HTTPS, le pilote de l’agent de point de terminaison intercepte la connexion et en établit une nouvelle auprès du serveur de destination pour récupérer son certificat SSL au nom du client. L'agent vérifie ensuite que le certificat est digne de confiance (en s’assurant qu’il n’a pas été révoqué et en effectuant d’autres vérifications). Si c’est le cas, l’agent de point de terminaison copie les informations du certificat du serveur et crée son propre certificat de serveur (appelé « certificat d’interception ») à partir de ces informations. Le certificat d’interception est signé à la volée par l’agent de point de terminaison avec un certificat racine, qui est installé dans le magasin de certificats approuvés de Windows. Ce certificat racine auto-signé est marqué comme non exportable et une liste ACL répertorie uniquement les administrateurs. Il est censé ne jamais quitter l’ordinateur sur lequel il a été créé. Quand l’application cliente de l’utilisateur final reçoit le certificat d’interception, elle l’approuve, car elle peut valider la chaîne de certificats jusqu’au certificat racine. Ce processus, qui est pour l’essentiel transparent du point de vue de l'utilisateur final, présente quelques inconvénients, comme décrit ci-dessous.

En activant l’inspection approfondie, l’agent de point de terminaison Cloud App Discovery peut déchiffrer et inspecter les communications TLS chiffrées, ce qui permet au service de réduire le bruit et de fournir des informations sur l’utilisation des applications cloud chiffrées.

#### <a name="a-word-of-caution"></a>Avertissement
Avant d'activer l’inspection approfondie, il est fortement recommandé de communiquer vos intentions aux ressources humaines et au département juridique et d'obtenir leur accord. L’inspection des communications chiffrées privées de l’utilisateur final peut être un sujet sensible pour des raisons évidentes. Avant de déployer l’inspection approfondie dans un environnement de production, vérifiez que les règles de sécurité et de bon usage de votre entreprise ont été mises à jour pour indiquer que les communications chiffrées seront inspectées. La notification des utilisateurs et l’exemption de sites contenant des données sensibles (par exemple, des banques et des établissements de santé) peuvent également être nécessaires si vous configurez Cloud App Discovery pour les analyser. Comme indiqué plus haut, les administrateurs peuvent utiliser le portail Cloud App Discovery pour choisir d’avertir ou non les utilisateurs de la collecte de données par l’agent et de demander le consentement de l’utilisateur avant le début de la collecte en question.

### <a name="known-issues-and-drawbacks"></a>Problèmes connus et inconvénients
Dans certains cas, l’interception TLS peut avoir des incidences sur l’expérience de l’utilisateur final :

* Les certificats de validation étendue affichent la barre d’adresse du navigateur web en vert pour donner à l’utilisateur une indication visuelle qu’il visite un site web de confiance. L’inspection TLS ne peut pas dupliquer l’évaluation étendue dans le certificat qu’elle adresse au client. En principe, les sites web qui utilisent des certificats de validation étendue fonctionnent, mais la barre d'adresse ne s’affiche pas en vert.  
* L’épinglage de clé publique (aussi appelé « épinglage de certificat ») vise à protéger les utilisateurs contre les attaques de l’intercepteur et les autorités de certification non autorisées. Quand le certificat racine d’un site épinglé est différent de celui d’une autorité de certification connue, le navigateur rejette la connexion et génère une erreur. Comme l'interception TLS est en fin de compte un intercepteur, ces connexions échouent.
* Si, pour examiner les informations du site, l’utilisateur clique sur l’icône de verrou située dans la barre d’adresse du navigateur, il ne verra pas de chaîne se terminant dans l’autorité de certification utilisée pour signer le certificat du site web, mais plutôt une chaîne de certificats se terminant par le magasin de certificats approuvés de Windows.

Pour réduire la survenue de ces problèmes, nous assurons un suivi des services cloud et des applications clientes censés utiliser la validation étendue ou l’épinglage de clé publique et donnons instruction à l’agent de point de terminaison de ne pas intercepter les connexions concernées. Cependant, même dans ces cas de figure, vous continuerez de recevoir des rapports faisant état de l’utilisation de ces applications cloud et indiquant le volume de données transférées, mais comme elles ne font pas l’objet d’une inspection approfondie, vous n’aurez aucun détail sur la façon dont les applications ont été utilisées.

## <a name="sending-data-to-cloud-app-discovery"></a>Envoi de données à Cloud App Discovery
Une fois que des métadonnées ont été recueillies par l’agent, elles sont mises en cache sur l’ordinateur pendant une minute maximum ou jusqu’à ce que les données mises en cache atteignent une taille de 5 Mo. Elles sont ensuite compressées, puis envoyées via une connexion sécurisée au service Cloud App Discovery.

Si l’agent ne peut pas communiquer avec le service Cloud App Discovery pour une raison quelconque, les métadonnées recueillies sont stockées dans un cache de fichiers local accessible uniquement par les utilisateurs disposant de privilèges sur l’ordinateur (tels que ceux appartenant au groupe Administrateurs).  
L’agent essaie automatiquement de renvoyer les métadonnées mises en cache jusqu’à ce qu’elles aient été correctement reçues par le service Cloud App Discovery.

## <a name="receiving-the-data-at-the-service-end"></a>Réception des données à l’extrémité du service
Les agents s’authentifient auprès du service Cloud App Discovery à l’aide du certificat d’authentification client spécifique à l’ordinateur référencé ci-dessus et transfèrent les données via un canal chiffré.  
Le pipeline d’analyse du service Cloud App Discovery traite les métadonnées de chaque client séparément en les partitionnant logiquement à toutes les étapes du pipeline d’analyse.
Les métadonnées analysées permettent de générer différents rapports dans le portail.

Les métadonnées non traitées et les métadonnées analysées sont stockées pendant 180 jours. En outre, les clients peuvent choisir de capturer les métadonnées analysées dans un compte de stockage d’objets blob Azure de leur choix.
Cela est utile pour effectuer une analyse hors ligne des métadonnées et pour la conservation à long terme des données.

## <a name="accessing-the-data-using-the-azure-portal"></a>Accès aux données à l’aide du portail Azure
Dans le but de protéger les métadonnées recueillies, par défaut seuls les administrateurs globaux du client ont accès à la fonctionnalité Cloud App Discovery dans le portail Azure.  
Cependant, les administrateurs peuvent choisir de déléguer cet accès à d’autres utilisateurs ou groupes.

> [!NOTE]
> Pour plus d’informations, consultez la page [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 


Tout utilisateur accédant aux données dans le portail doit posséder une licence Azure AD Premium.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

