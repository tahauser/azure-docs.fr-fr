---
title: Guide du protocole de connexions hybrides Azure Relay | Microsoft Docs
description: Guide du protocole de connexions hybrides Azure Relay.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 43c40baa74b3f7c1f5c9d6626b25bcd45c2f9a10
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocole de connexions hybrides Azure Relay
Azure Relay est l’une des fonctionnalités clés de la plateforme Azure Service Bus. La nouvelle fonctionnalité *Connexions hybrides* de Relay est une évolution sécurisée, à protocole ouvert, sur HTTP et WebSocket. Elle remplace l’ancienne fonctionnalité, nommée *BizTalk Services*, conçue sur un protocole propriétaire. L’intégration des connexions hybrides dans Azure App Services continue de fonctionner telle quelle.

Les connexions hybrides permettent d’établir une communication de flux binaire bidirectionnel entre deux applications en réseau, durant laquelle une des deux, voire les deux, peuvent se trouver derrière un NAT ou un pare-feu. Cet article décrit les interactions côté client avec le relais de connexions hybrides pour connecter les clients ayant le rôle d’expéditeur ou d’écouteur et la façon dont les écouteurs acceptent de nouvelles connexions.

## <a name="interaction-model"></a>Modèle d’interaction
Le relais Connexions hybrides connecte deux parties en fournissant un point de rencontre détectable par les deux parties dans le cloud Azure, auquel elles peuvent se connecter du point de vue de leur propre réseau. Ce point de rencontre est appelé « Connexion hybride » dans ce document, entre autres, dans les API et dans le Portail Azure. Le point de terminaison de service Connexions hybrides est appelé « service » dans la suite de cet article. Le modèle d’interaction utilise la nomenclature établie par de nombreuses autres API de mise en réseau.

Un écouteur indique tout d’abord qu’il est prêt à gérer des connexions entrantes, puis les accepte dès leur arrivée. De l’autre côté, un client de connexion se connecte à l’écouteur et attend l’acceptation de cette connexion pour établir un chemin de communication bidirectionnelle.
« Se connecter », « écouter » et « accepter » sont les termes que vous trouverez dans la plupart des API de sockets.

Selon les modèles de communication relayée, l’une des parties établit des connexions sortantes vers un point de terminaison de service, ce qui fait que « l’écouteur » est également un « client » dans le langage courant, entre autres surcharges terminologiques. Par conséquent, voici la terminologie précise que nous utilisons pour les connexions hybrides :

Les programmes des deux côtés d’une connexion sont appelés « clients », puisqu’il s’agit de clients du service. Le client qui attend et accepte les connexions est « l’écouteur » ; on peut également dire qu’il a le « rôle d’écouteur ». Le client qui démarre une nouvelle connexion vers un écouteur par le biais du service est appelé « expéditeur » ou considéré comme ayant le « rôle d’expéditeur ».

### <a name="listener-interactions"></a>Interactions de l’écouteur
L’écouteur a quatre interactions avec le service ; tous les détails sont décrits plus loin dans cet article, dans la section des références.

#### <a name="listen"></a>Écouter
Pour indiquer au service qu’il est prêt à accepter les connexions, l’écouteur crée une connexion WebSocket sortante. L’établissement de la connexion porte le nom d’une connexion hybride configurée dans l’espace de noms Relay, et un jeton de sécurité qui confère le droit « d’écoute » sur ce nom.
Quand le WebSocket est accepté par le service, l’inscription est terminée et le WebSocket établi reste actif en tant que « canal de contrôle » autorisant toutes les interactions suivantes. Le service autorise jusqu’à 25 écouteurs simultanés sur une connexion hybride. S’il existe plusieurs écouteurs actifs, les connexions entrantes sont réparties entre tous selon un ordre aléatoire ; la répartition équitable n’est pas garantie.

#### <a name="accept"></a>Acceptation
Quand un expéditeur ouvre une nouvelle connexion sur le service, celui-ci choisit et informe l’un des écouteurs actifs sur la connexion hybride. Cette notification est envoyée à l’écouteur sur le canal de contrôle ouvert sous forme de message JSON contenant l’URL du point de terminaison du WebSocket auquel doit se connecter l’écouteur pour accepter la connexion.

L’URL peut et doit être utilisée directement par l’écouteur sans aucun travail supplémentaire.
Les informations encodées sont valides seulement pendant une courte durée, pour l’essentiel tant que l’expéditeur est disposé à attendre que la connexion soit établie de bout en bout, mais avec un maximum de 30 secondes. L’URL ne peut être utilisée que pour une seule tentative de connexion réussie. Dès que la connexion du WebSocket à l’URL de rencontre est établie, toutes les activités supplémentaires sur ce WebSocket sont relayées à partir de et vers l’expéditeur, sans aucune intervention ni interprétation par le service.

#### <a name="renew"></a>Renouveler
Le jeton de sécurité qui doit être utilisé pour enregistrer l’écouteur et maintenir le canal de contrôle peut expirer alors que l’écouteur est actif. L’expiration du jeton n’affecte pas les connexions en cours, mais elle entraîne l’annulation du canal de contrôle par le service au moment de l’expiration ou juste après. L’opération « renouveler » est un message JSON que peut envoyer l’écouteur pour remplacer le jeton associé au canal de contrôle, afin de conserver le canal de contrôle plus longtemps.

#### <a name="ping"></a>Ping
Si le canal de contrôle reste inactif pendant une longue période, les intermédiaires présents sur l’itinéraire, comme les équilibreurs de charge ou les NAT, peuvent supprimer la connexion TCP. L’opération « ping » permet d’éviter cela en envoyant une petite quantité de données sur le canal, qui rappelle à tous les éléments présents sur l’itinéraire du réseau que la connexion est destinée à rester active ; elle sert également de test d’activité de l’écouteur. Si la commande ping échoue, le canal de contrôle doit être considéré comme inutilisable et l’écouteur doit se reconnecter.

### <a name="sender-interaction"></a>Interaction de l’expéditeur
L’expéditeur n’a qu’une seule interaction avec le service : il se connecte.

#### <a name="connect"></a>Connecter
L’opération « se connecter » ouvre un WebSocket sur le service, fournissant le nom de la connexion hybride et un jeton de sécurité (facultatif, mais obligatoire par défaut) conférant l’autorisation « Envoyer » dans la chaîne de requête. Le service interagit ensuite avec l’écouteur de la façon décrite précédemment, et l’écouteur crée une connexion conjointe avec ce WebSocket. Une fois le WebSocket accepté, toutes les autres interactions sur ce WebSocket se font avec un écouteur connecté.

### <a name="interaction-summary"></a>Résumé de l’interaction
Le résultat de ce modèle d’interaction est que le client expéditeur ressort de la liaison avec un WebSocket « propre », qui est connecté à un écouteur et n’a besoin d’aucun préambule et d’aucune préparation supplémentaire. Avec ce modèle, presque toutes les implémentations client existantes de WebSocket peuvent facilement tirer parti du service Connexions hybrides en fournissant une URL correctement construite dans la couche client de leur WebSocket.

Le WebSocket de connexion jointe qu’obtient l’écouteur par le biais de l’interaction d’acceptation est également propre et peut être transmis à une implémentation serveur de WebSocket existante avec une abstraction supplémentaire minimale qui fait la distinction entre les opérations « accepter » sur les écouteurs du réseau local de leur infrastructure et les opérations « accepter » à distance des Connexions hybrides.

## <a name="protocol-reference"></a>Référence sur le protocole

Cette section fournit les détails des interactions de protocole décrites plus haut.

Toutes les connexions de WebSocket sont effectuées sur le port 443 mis à niveau à partir de HTTPS 1.1, qui est couramment abstrait par les API et les infrastructures de WebSocket. Cette description ne tient pas compte de l’implémentation et ne suggère aucune infrastructure en particulier.

### <a name="listener-protocol"></a>Protocole de l’écouteur
Le protocole de l’écouteur se compose de deux mouvements de connexion et de trois opérations messages.

#### <a name="listener-control-channel-connection"></a>Connexion du canal de contrôle de l’écouteur
Le canal de contrôle est ouvert lors de la création d’une connexion de WebSocket à :

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` est le nom de domaine complet de l’espace de noms Azure Relay qui héberge la connexion hybride, généralement de la forme `{myname}.servicebus.windows.net`.

Voici les options de paramètres des chaînes de requête.

| Paramètre | Obligatoire | Description |
| --- | --- | --- |
| `sb-hc-action` |OUI |Pour le rôle d’écouteur, le paramètre doit être **sb-hc-action=listen** |
| `{path}` |OUI |Chemin d’accès de l’espace de noms encodé au format URL de la connexion hybride préconfigurée sur laquelle cet écouteur doit être inscrit. Cette expression est ajoutée à la partie fixe `$hc/` du chemin. |
| `sb-hc-token` |Oui\* |L’écouteur doit fournir un jeton d’accès partagé Service Bus valide et encodé au format URL pour l’espace de noms ou la connexion hybride qui confère le droit **d’écoute**. |
| `sb-hc-id` |Non  |Cet ID facultatif fourni par le client permet un suivi de diagnostic de bout en bout. |

Si la connexion du WebSocket échoue car le chemin de la connexion hybride n’est pas inscrit, en raison d’un jeton non valide ou manquant ou à cause d’une erreur d’un autre type, les commentaires sur l’erreur sont fournis d’après le modèle classique de commentaires d’état HTTP 1.1. La description d’état contient un ID de suivi d’erreur qui peut être communiqué au personnel de support Azure :

| Code | Error | Description |
| --- | --- | --- |
| 404 |Introuvable |Le chemin de la connexion hybride n’est pas valide ou l’URL de base est incorrecte. |
| 401 |Non autorisé |Le jeton de sécurité est manquant, incorrect ou non valide. |
| 403 |Interdit |Le jeton de sécurité n’est pas valide pour ce chemin d’accès et pour cette action. |
| 500 |Erreur interne |Un problème est survenu dans le service. |

Si la connexion du WebSocket est intentionnellement arrêtée par le service après sa configuration initiale, la raison de cette opération est communiquée par le biais d’un code d’erreur approprié du protocole WebSocket avec un message d’erreur descriptif qui inclut également un ID de suivi. Le service ne peut pas arrêter le canal de contrôle sans rencontrer un état d’erreur. Les arrêts normaux sont contrôlés par le client.

| État du SW | Description |
| --- | --- |
| 1001 |Le chemin d’accès de la connexion hybride a été supprimé ou désactivé. |
| 1008 |Le jeton de sécurité a expiré et, par conséquent, la stratégie d’autorisation n’est pas respectée. |
| 1011 |Un problème est survenu dans le service. |

### <a name="accept-handshake"></a>Négociation d’acceptation
La notification d’acceptation est envoyée par le service à l’écouteur sur le canal de contrôle établi précédemment sous forme de message JSON dans un bloc de texte de WebSocket. Il n’y a pas de réponse à ce message.

Le message contient un objet JSON nommé « accept », qui définit les propriétés suivantes à ce jour :

* **address** : chaîne d’URL à utiliser pour que le WebSocket du service accepte une connexion entrante.
* **ID** : identificateur unique de cette connexion. Si l’ID a été fourni par le client expéditeur, il s’agit de la valeur fournie par l’expéditeur, sinon d’une valeur générée par le système.
* **connectHeaders** : tous les en-têtes HTTP qui ont été fournis au point de terminaison Relay par l’expéditeur, dont également les en-têtes Sec-WebSocket-Protocol et Sec-WebSocket-Extensions.

#### <a name="accept-message"></a>Message d’acceptation

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",  
        "connectHeaders" : {                                         
            "Host" : "...",                                                
            "Sec-WebSocket-Protocol" : "...",                              
            "Sec-WebSocket-Extensions" : "..."                             
        }                                                            
     }                                                         
}
```

L’adresse URL fournie dans le message JSON est utilisée par l’écouteur pour que le WebSocket accepte ou rejette le socket de l’expéditeur.

#### <a name="accepting-the-socket"></a>Accepter le socket
Pour accepter, l’écouteur établit une connexion WebSocket à l’adresse fournie.

Si le message d’acceptation comporte un en-tête `Sec-WebSocket-Protocol`, il est attendu que l’écouteur accepte uniquement le WebSocket s’il prend en charge ce protocole. De plus, il définit l’en-tête au moment où le WebSocket est établi.

Il en va de même pour l’en-tête `Sec-WebSocket-Extensions`. Si le framework prend en charge une extension, il doit définir l’en-tête comme la réponse côté serveur de la liaison `Sec-WebSocket-Extensions` requise pour l’extension.

L’URL doit être utilisée telle quelle pour établir le socket d’acceptation, mais elle contient les paramètres suivants :

| Paramètre | Obligatoire | Description |
| --- | --- | --- |
| `sb-hc-action` |OUI |Pour accepter un socket, le paramètre doit être `sb-hc-action=accept` |
| `{path}` |OUI |(voir le paragraphe suivant) |
| `sb-hc-id` |Non  |Voir la description précédente de **id**. |

`{path}` est le chemin de l’espace de noms encodé au format URL de la connexion hybride préconfigurée sur laquelle cet écouteur doit être inscrit. Cette expression est ajoutée à la partie fixe `$hc/` du chemin. 

L’expression `path` peut être étendue par un suffixe et une expression de chaîne de requête qui suit le nom inscrit après une barre oblique de séparation. Cela permet au client expéditeur de transmettre des arguments à l’écouteur quand il n’est pas possible d’inclure des en-têtes HTTP. L’objectif est que le framework de l’écouteur analyse la partie fixe du chemin et le nom inscrit dans le chemin, et rende le reste (éventuellement sans argument de chaîne de requête préfixé par `sb-`), accessible à l’application pour que celle-ci puisse décider d’accepter ou non la connexion.

Pour plus d’informations, consultez la section suivante, « Protocole de l’expéditeur ».

En cas d’erreur, le service peut répondre ceci :

| Code | Error | Description |
| --- | --- | --- |
| 403 |Interdit |L’URL n’est pas valide. |
| 500 |Erreur interne |Un problème est survenu dans le service. |

Une fois la connexion établie, le serveur arrête le WebSocket quand le WebSocket expéditeur s’arrête, ou avec l’état suivant :

| État du SW | Description |
| --- | --- |
| 1001 |Le client expéditeur arrête la connexion. |
| 1001 |Le chemin d’accès de la connexion hybride a été supprimé ou désactivé. |
| 1008 |Le jeton de sécurité a expiré et, par conséquent, la stratégie d’autorisation n’est pas respectée. |
| 1011 |Un problème est survenu dans le service. |

#### <a name="rejecting-the-socket"></a>Rejeter le socket
Pour rejeter le socket après avoir inspecté le message d’acceptation, une liaison similaire est nécessaire afin que le code et la description de l’état communiquant le motif du rejet puissent être transmis à l’expéditeur.

Le choix de conception du protocole consiste ici à utiliser une liaison WebSocket (qui est conçue pour se terminer avec un état d’erreur défini) afin que les implémentations du client écouteur puissent continuer à s’appuyer sur un client WebSocket sans devoir utiliser un client HTTP supplémentaire sans système d’exploitation.

Pour rejeter le socket, le client prend l’URI d’adresse du message d’acceptation et y ajoute deux paramètres de chaîne de requête, comme suit :

| Paramètre | Obligatoire | Description |
| --- | --- | --- |
| statusCode |OUI |Code d’état HTTP numérique. |
| statusDescription |OUI |Motif du rejet lisible. |

L’URI obtenu est ensuite utilisé pour établir une connexion WebSocket.

En cas de réussite, cette liaison échoue intentionnellement avec un code d’erreur HTTP 410, car aucun WebSocket n’a été établi. Si une erreur survient, les codes suivants la décrivent :

| Code | Error | Description |
| --- | --- | --- |
| 403 |Interdit |L’URL n’est pas valide. |
| 500 |Erreur interne |Un problème est survenu dans le service. |

### <a name="listener-token-renewal"></a>Renouvellement du jeton de l’écouteur
Lorsque le jeton de l’écouteur est sur le point d’expirer, celui-ci peut le remplacer en envoyant un message de bloc de texte au service via le canal de contrôle établi. Le message contient un objet JSON nommé `renewToken`, qui définit la propriété suivante à ce moment donné :

* **token** : jeton d’accès partagé Service Bus valide et encodé au format URL pour l’espace de noms ou la connexion hybride qui confère le droit d’**écoute**.

#### <a name="renewtoken-message"></a>Message renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Si la validation du jeton échoue, l’accès est refusé et le service cloud ferme le WebSocket du canal de contrôle avec une erreur. Sinon, il n’y a aucune réponse.

| État du SW | Description |
| --- | --- |
| 1008 |Le jeton de sécurité a expiré et, par conséquent, la stratégie d’autorisation n’est pas respectée. |

## <a name="sender-protocol"></a>Protocole de l’expéditeur
Le protocole de l’expéditeur est identique au mode d’établissement d’un écouteur.
L’objectif est une transparence maximale pour le WebSocket de bout en bout. L’adresse de connexion est la même que pour l’écouteur, mais « l’action » diffère et le jeton a besoin d’une autorisation différente :

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*namespace-address* est le nom de domaine complet de l’espace de noms Azure Relay qui héberge la connexion hybride, généralement de la forme `{myname}.servicebus.windows.net`.

La demande peut contenir des en-têtes HTTP arbitraires supplémentaires, notamment ceux définis par l’application. Tous les en-têtes fournis sont transmis à l’écouteur et se trouvent sur l’objet `connectHeader` du message de contrôle **accept**.

Voici les options de paramètres des chaînes de requête :

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| `sb-hc-action` |OUI |Pour le rôle de l’expéditeur, le paramètre doit être `action=connect`. |
| `{path}` |OUI |(voir le paragraphe suivant) |
| `sb-hc-token` |Oui\* |L’écouteur doit fournir un jeton d’accès partagé Service Bus valide et encodé au format URL pour l’espace de noms ou la connexion hybride qui confère le droit **d’envoi**. |
| `sb-hc-id` |Non  |ID facultatif qui permet le suivi de diagnostic de bout en bout et est accessible à l’écouteur pendant la liaison d’acceptation. |

Le `{path}` est le chemin d’accès de l’espace de noms encodé au format URL de la connexion hybride préconfigurée sur laquelle cet écouteur doit être inscrit. L’expression `path` peut être étendue par un suffixe ou une expression de chaîne de requête pour prolonger la communication. Si la connexion hybride est enregistrée sous le chemin `hyco`, l’expression `path` peut être `hyco/suffix?param=value&...`, suivi des paramètres de chaîne de requête définis ici. Ainsi, l’expression complète peut être celle-ci :

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

L’expression `path` est transmise à l’écouteur dans l’URI d’adresse contenu dans le message de contrôle « accept ».

Si la connexion du WebSocket échoue car le chemin de la connexion hybride n’est pas inscrit, en raison d’un jeton non valide ou manquant ou à cause d’une erreur d’un autre type, les commentaires sur l’erreur sont fournis d’après le modèle classique de commentaires d’état HTTP 1.1. La description d’état contient un ID de suivi d’erreur qui peut être communiqué au personnel de support Azure :

| Code | Error | Description |
| --- | --- | --- |
| 404 |Introuvable |Le chemin de la connexion hybride n’est pas valide ou l’URL de base est incorrecte. |
| 401 |Non autorisé |Le jeton de sécurité est manquant, incorrect ou non valide. |
| 403 |Interdit |Le jeton de sécurité n’est pas valide pour ce chemin et pour cette action. |
| 500 |Erreur interne |Un problème est survenu dans le service. |

Si la connexion du WebSocket est intentionnellement arrêtée par le service après sa configuration initiale, la raison de cette opération est communiquée par le biais d’un code d’erreur approprié du protocole WebSocket avec un message d’erreur descriptif qui inclut également un ID de suivi.

| État du SW | Description |
| --- | --- |
| 1 000 |L’écouteur a fermé le socket. |
| 1001 |Le chemin d’accès de la connexion hybride a été supprimé ou désactivé. |
| 1008 |Le jeton de sécurité a expiré et, par conséquent, la stratégie d’autorisation n’est pas respectée. |
| 1011 |Un problème est survenu dans le service. |

## <a name="next-steps"></a>étapes suivantes
* [FAQ sur Azure Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)

