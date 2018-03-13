---
title: "Considérations de sécurité pour le proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les considérations de sécurité lors de l’utilisation du proxy d’application Azure AD"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2370c4717e2cff6b4b8113b09624ef873b309647
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considérations de sécurité pour l’accès aux applications à distance avec le proxy d’application Azure AD

Cet article décrit les composants qui facilitent la protection des utilisateurs et des applications quand vous utilisez le proxy d’application Azure Active Directory.

Le diagramme ci-dessous illustre la façon dont Azure AD permet un accès à distance sécurisé à vos applications locales.

 ![Diagramme illustrant l’accès sécurisé à distance via le proxy d’application Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Avantages en matière de sécurité

Le proxy d’application Azure AD offre les avantages de sécurité suivants :

### <a name="authenticated-access"></a>Accès authentifié 

Si vous choisissez d’utiliser la préauthentification Azure Active Directory, seules les connexions authentifiées peuvent accéder à votre réseau.

Le proxy d’application Azure AD s’appuie sur le service d’émission de jeton de sécurité (STS) Azure AD pour toutes les authentifications.  L’authentification préalable, par sa nature, bloque un nombre conséquent d’attaques anonymes, car seules les identités authentifiées sont autorisées à accéder à l’application principale.

Si vous choisissez Passthrough comme méthode de préauthentification, vous ne bénéficiez pas de cet avantage. 

### <a name="conditional-access"></a>Accès conditionnel

Appliquez des contrôles de stratégie plus riches avant que les connexions à votre réseau soient établies.

Avec [l’accès conditionnel](active-directory-conditional-access-azure-portal-get-started.md), vous pouvez définir les restrictions sur le trafic qui est autorisé à accéder à vos applications principales. Vous pouvez créer des stratégies qui limitent les connexions en fonction de l’emplacement, de la force de l’authentification et du profil de risque de l’utilisateur.

Vous pouvez également utiliser l’accès conditionnel pour configurer des stratégies Multi-Factor Authentication, qui ajoutent une couche de sécurité supplémentaire aux authentifications des utilisateurs. 

### <a name="traffic-termination"></a>Arrêt du trafic

Tout le trafic est arrêté dans le cloud.

Étant donné que le proxy d’application Azure AD est un proxy inversé, tout le trafic vers les applications principales est arrêté au niveau du service. La session peut être réétablie uniquement avec le serveur principal, ce qui signifie que vos serveurs principaux ne sont pas exposés au trafic HTTP direct. Cette configuration signifie que vous êtes mieux protégé contre les attaques ciblées.

### <a name="all-access-is-outbound"></a>Tous les accès sont sortants 

Vous n’avez pas besoin d’ouvrir de connexion sortante sur le réseau d’entreprise.

Les connecteurs de proxy d’application utilisent uniquement des connexions sortantes vers le service de proxy d’application Azure AD, ce qui signifie qu’il n’est pas nécessaire d’ouvrir des ports de pare-feu pour les connexions entrantes. Les proxys traditionnels exigeaient un réseau de périmètre (également appelé *DMZ*, *zone démilitarisée* et *sous-réseau filtré*) et autorisaient l’accès à des connexions non authentifiées sur le périmètre du réseau. Ce scénario nécessitait d’investir dans des produits de pare-feu d’application web pour analyser le trafic et protéger l’environnement. Avec le proxy d’application, vous pouvez même vous passer de réseau de périmètre, car toutes les connexions sont sortantes et sur un canal sécurisé.

Pour plus d’informations sur les connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analyse à l’échelle du cloud et apprentissage automatique 

Bénéficiez d’une protection de sécurité de pointe.

Comme il fait partie d’Azure Active Directory, le proxy d’application peut tirer parti [d’Azure AD Identity Protection](active-directory-identityprotection.md), grâce aux données du MSRC (Microsoft Security Response Center) et de la DCU (Digital Crimes Unit). Ensemble, nous identifions de façon proactive les comptes compromis et offrons une protection contre les connexions à haut risque. Nous prenons en compte plusieurs facteurs pour déterminer les tentatives de connexion à haut risque. Ces facteurs incluent le marquage des appareils infectés, l’anonymisation des réseaux et les emplacements atypiques ou peu probables.

Nombre de ces rapports et événements sont déjà disponibles via une API pour l’intégration avec vos systèmes de gestion des événements et des informations de sécurité.

### <a name="remote-access-as-a-service"></a>Accès à distance en tant que service

Vous n’avez pas à vous soucier de la maintenance et de l’application de correctifs sur vos serveurs locaux.

Les logiciels sans correctifs sont toujours responsables d’un grand nombre d’attaques. Le proxy d’application Azure AD est un service Internet contrôlé par Microsoft ; vous obtenez donc toujours les derniers correctifs de sécurité et mises à niveau.

Pour améliorer la sécurité des applications publiées par le proxy d’application Azure AD, nous empêchons les robots web d’indexer et d’archiver vos applications. Chaque fois qu’un robot web tente de récupérer les paramètres de robots d’une application publiée, le proxy d’application répond avec un fichier robots.txt qui inclut `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Prévention des attaques DDoS

Les applications publiées via le proxy d’application sont protégées contre les attaques par déni de service distribué (DDoS).

Le service de proxy d’application surveille la quantité de trafic qui tente d’atteindre vos applications et réseau. Si le nombre d’appareils qui demandent un accès à distance à vos applications augmente nettement, Microsoft limite l’accès à votre réseau. 

Microsoft surveille des modèles de trafic pour les applications individuelles et pour votre abonnement dans son ensemble. Si une application reçoit une quantité de demandes anormalement élevée, les demandes d’accès à cette application sont refusées pendant une courte période de temps. Si vous recevez une quantité de demandes anormalement élevée pour l’ensemble de votre abonnement, les demandes d’accès à toutes vos applications sont refusées. Avec cette mesure préventive, vos serveurs d’applications ne sont pas surchargés de demandes d’accès à distance, ce qui permet à vos utilisateurs locaux de continuer à accéder à leurs applications. 

## <a name="under-the-hood"></a>Sous le capot

Le proxy d’application Azure Active Directory se compose de deux parties :

* Le service basé sur le cloud : ce service s’exécute dans Azure, et c’est là que sont effectuées les connexions client/utilisateur externes.
* [Le connecteur local](application-proxy-understand-connectors.md) : un composant local, le connecteur écoute les demandes du service de proxy d’application Azure AD et gère les connexions vers les applications internes. 

Un flux entre le connecteur et le service de proxy d’application est établi lorsque :

* Le connecteur est configuré pour la première fois.
* Le connecteur extrait les informations de configuration du service de proxy d’application.
* Un utilisateur accède à une application publiée.

>[!NOTE]
>Toutes les communications se produisent sur le protocole SSL et proviennent du connecteur vers le service de proxy d’application. Le service est uniquement sortant.

Le connecteur utilise un certificat client pour authentifier le service de proxy d’application pour presque tous les appels. La seule exception est l’étape initiale d’installation, au cours de laquelle le certificat client est établi.

### <a name="installing-the-connector"></a>Installation du connecteur

Le flux suivant se déroule lorsque le connecteur est configuré pour la première fois :

1. L’inscription du connecteur sur le service se produit dans le cadre de l’installation du connecteur. Les utilisateurs sont invités à saisir leurs informations d’identification administrateur Azure AD. Le jeton obtenu à partir de cette authentification est ensuite présenté au service de proxy d’application Azure AD.
2. Le service de proxy d’Application évalue le jeton. Il vérifie si l’utilisateur est un administrateur d’entreprise dans le locataire. Si l’utilisateur n’est pas un administrateur, le processus se termine.
3. Le connecteur génère une demande de certificat client et la transmet, ainsi que le jeton, au service de proxy d’application. À son tour, le service vérifie le jeton et signe la demande de certificat client.
4. Le connecteur utilise le certificat client pour les communications ultérieures avec le service de proxy d’application.
5. Le connecteur effectue une extraction initiale des données de configuration système à partir du service à l’aide de son certificat client et est désormais prêt à recevoir des demandes.

### <a name="updating-the-configuration-settings"></a>Mise à jour des paramètres de configuration

Chaque fois que le service de proxy d’application met à jour les paramètres de configuration, les événements de flux suivants ont lieu :

1. Le connecteur se connecte au point de terminaison de configuration dans le service de proxy d’application à l’aide de son certificat client.
2. Une fois que le certificat client est validé, le service de proxy d’application renvoie les données de configuration au connecteur (par exemple, le groupe auquel le connecteur appartient).
3. Le connecteur génère une nouvelle demande de certificat si le certificat actuel date de plus de 180 jours. Ainsi, dans les faits, le certificat client est mis à jour tous les 180 jours.

### <a name="accessing-published-applications"></a>Accès aux applications publiées

Lorsque les utilisateurs accèdent à une application publiée, les événements suivants se produisent entre le service de proxy d’application et le connecteur de proxy d’application :

1. [Le service authentifie l’utilisateur pour l’application.](#the-service-checks-the-configuration-settings-for-the-app)
2. [Le service place une demande dans la file d’attente du connecteur.](#The-service-places-a-request-in-the-connector-queue)
3. [Un connecteur traite la demande provenant de la file d’attente.](#the-connector-receives-the-request-from-the-queue)
4. [Le connecteur attend une réponse.](#the-connector-waits-for-a-response)
5. [Le service diffuse les données à l’utilisateur.](#the-service-streams-data-to-the-user)

Pour plus d’informations sur ce qui se passe dans chacune de ces étapes, poursuivez votre lecture.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Le service authentifie l’utilisateur pour l’application.

Si vous avez configuré l’application pour utiliser Passthrough comme méthode de préauthentification, les étapes décrites dans cette section sont ignorées.

Si vous avez configuré l’application pour une préauthentification avec Azure AD, les utilisateurs sont redirigés vers Azure AD STS pour s’authentifier et les étapes suivantes se produisent :

1. Le proxy d’application vérifie les éventuelles exigences de stratégie d’accès conditionnel pour l’application spécifique. Cette étape permet de vérifier que l’utilisateur a été attribué à l’application. Si une vérification en deux étapes est requise, la séquence d’authentification invite l’utilisateur à s’authentifier avec un second facteur.

2. Une fois toutes les vérifications réussies, Azure AD STS émet un jeton signé pour l’application et redirige l’utilisateur vers le service de proxy d’application.

3. Le proxy d’application vérifie que le jeton a été émis pour corriger l’application. Il effectue également d’autres vérifications : il vérifie par exemple que le jeton a bien été signé par Azure AD et qu’il est toujours dans la plage d’utilisation valide.

4. Le proxy d’application définit un cookie d’authentification chiffré pour indiquer que l’authentification à l’application a eu lieu. Le cookie contient un horodatage d’expiration basé sur le jeton d’Azure AD et d’autres données, comme le nom d’utilisateur sur lequel l’authentification est basée. Le cookie est chiffré à l’aide d’une clé privée connue uniquement du service de proxy d’application.

5. Le proxy d’application redirige l’utilisateur vers l’URL demandée à l’origine.

Si une partie de la procédure d’authentification préalable échoue, la demande de l’utilisateur est refusée et l’utilisateur voit un message indiquant la source du problème.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Le service place une demande dans la file d’attente du connecteur.

Les connecteurs gardent une connexion sortante ouverte vers le service de proxy d’application. Lorsqu’une demande arrive, le service met en file d’attente la demande sur une des connexions ouvertes pour récupération par le connecteur.

La demande comprend des éléments de l’application, comme les en-têtes de la demande et des données du cookie chiffré, l’utilisateur qui effectue la demande et l’ID de la demande. Bien que les données du cookie chiffré soient envoyées avec la demande, le cookie d’authentification en lui-même ne l’est pas.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Le connecteur traite la demande provenant de la file d’attente. 

En fonction de la demande, le proxy d’application effectue l’une des actions suivantes :

* Si la demande est une opération simple (par exemple s’il n’y a aucune donnée dans le corps comme pour une requête RESTful *GET*), le connecteur établit une connexion à la ressource interne cible et attend une réponse.

* Si la demande possède des données associées dans le corps (par exemple une opération RESTful *POST*), le connecteur établit une connexion sortante à l’aide du certificat client vers l’instance du proxy d’application. Il établit cette connexion pour demander les données et ouvrir une connexion vers la ressource interne. Après réception de la demande à partir du connecteur, le service de proxy d’application commence à accepter le contenu de l’utilisateur et transfère les données au connecteur. Le connecteur transfère à son tour les données vers la ressource interne.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Le connecteur attend une réponse.

Une fois la demande et la transmission de tout le contenu vers le serveur principal terminées, le connecteur attend une réponse.

Après obtention d’une réponse, le connecteur établit une connexion sortante vers le service de proxy d’application, afin de renvoyer les détails de l’en-tête et commencer la diffusion des données renvoyées.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Le service diffuse les données à l’utilisateur. 

Une partie du traitement de l’application peut se produire ici. Si vous avez configuré le proxy d’application pour traduire les en-têtes ou les URL de votre application, cette opération se produit si nécessaire au cours de cette étape.


## <a name="next-steps"></a>Étapes suivantes

[Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure AD](application-proxy-network-topology-considerations.md)

[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)
