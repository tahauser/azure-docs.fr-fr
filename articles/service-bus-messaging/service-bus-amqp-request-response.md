---
title: "AMQP 1.0 dans les opérations basées sur les requêtes-réponses d’Azure Service Bus | Microsoft Docs"
description: "Liste des opérations basées sur les requêtes-réponses de Microsoft Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d72a4de8591898a55e4225ace154fd5ed53e6f91
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 dans Microsoft Azure Service Bus : opérations basées sur les requêtes-réponses

Cet article définit la liste des opérations basées sur les requêtes-réponses de Microsoft Azure Service Bus. Ces informations sont basées sur la première ébauche d’AMQP Management Version 1.0.  
  
Pour obtenir un guide détaillé sur le protocole AMQP 1.0 au niveau des câbles qui explique comment Service Bus implémente la spécification technique AMQP OASIS et s’appuie sur celle-ci, consultez le [Guide du protocole AMQP 1.0 dans Azure Service Bus et Event Hubs][Guide du protocole AMQP 1.0].  
  
## <a name="concepts"></a>Concepts  
  
### <a name="entity-description"></a>Description d’entité  

Une description d’entité fait référence à un objet Service Bus [classe QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) ou [classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription).  
  
### <a name="brokered-message"></a>Message réparti  

Représente un message Service Bus mappé à un message AMQP. Le mappage est défini dans le [guide du protocole AMQP de Service Bus](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Attacher à un nœud de gestion d’entité  

Toutes les opérations décrites dans ce document suivent un modèle demande/réponse sont limitées à une entité et nécessitent l’attachement à un nœud de gestion d’entité.  
  
### <a name="create-link-for-sending-requests"></a>Créer le lien pour l’envoi des demandes  

Crée un lien vers le nœud de gestion pour envoyer des demandes.  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>Créer le lien pour la réception des réponses  

Crée un lien pour la réception des réponses à partir du nœud de gestion.  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>Transférer un message de demande  

Transfère un message de demande.  
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
        )  
```  
  
### <a name="receive-a-response-message"></a>Recevoir un message de réponse  

Reçoit le message de réponse à partir du lien de réponse.  
  
```  
responseMessage = responseLink.receiveTransfer()  
```  
  
La réponse présente le format suivant :
  
```  
Message(  
properties: {     
        correlation-id: <request id>  
    },  
    application-properties: {  
            "statusCode" -> <status code>,  
            "statusDescription" -> <status description>,  
           },         
)  
  
```  
  
### <a name="service-bus-entity-address"></a>Adresse d’entité Service Bus  

Les entités Service Bus doivent être traitées comme suit :  
  
|Type d’entité|Adresse|exemples|  
|-----------------|-------------|-------------|  
|file d'attente|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|rubrique|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Opérations de message  
  
### <a name="message-renew-lock"></a>Verrouillage du renouvellement du message  

Étend le verrouillage d’un message à la durée spécifiée dans la description d’entité.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
 Le corps du message de requête doit contenir une section amqp-value comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|tableau d’uuid|OUI|Jetons de verrouillage de message à renouveler.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec.|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section amqp-value comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|expirations|tableau d’horodatage|OUI|La nouvelle expiration du jeton de verrouillage de message correspond désormais aux jetons de verrouillage des demandes.|  
  
### <a name="peek-message"></a>Lire furtivement un message  

Lit furtivement les messages sans les verrouiller.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|OUI|Numéro de séquence à partir duquel commencer la lecture furtive.|  
|`message-count`|int|OUI|Nombre maximal de messages à lire furtivement.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK : a plus de messages<br /><br /> 0xcc : aucun contenu – plus de messages|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Cloud vers appareil|liste des mappages|OUI|Liste de messages dans laquelle chaque mappage représente un message.|  
  
Le mappage représentant un message doit contenir les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Message|tableau d’octets|OUI|Message codé en filaire AMPQ 1.0.|  
  
### <a name="schedule-message"></a>Message de planification  

Messages de planification.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Cloud vers appareil|liste des mappages|OUI|Liste de messages dans laquelle chaque mappage représente un message.|  
  
Le mappage représentant un message doit contenir les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|message-id|chaîne|OUI|`amqpMessage.Properties.MessageId` comme chaîne|  
|session-id|chaîne|OUI|`amqpMessage.Properties.GroupId as string`|  
|partition-key|chaîne|OUI|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|Message|tableau d’octets|OUI|Message codé en filaire AMPQ 1.0.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec.|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|tableau de type long|OUI|Numéro de séquence des messages planifiés. Le numéro de séquence est utilisé pour annuler.|  
  
### <a name="cancel-scheduled-message"></a>Annuler le message planifié  

Annule les messages planifiés.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|tableau de type long|OUI|Numéros de séquence des messages planifiés pour annuler.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec.|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|tableau de type long|OUI|Numéro de séquence des messages planifiés. Le numéro de séquence est utilisé pour annuler.|  
  
## <a name="session-operations"></a>Opérations de session  
  
### <a name="session-renew-lock"></a>Verrouillage de renouvellement de session  

Étend le verrouillage d’un message à la durée spécifiée dans la description d’entité.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|session-id|chaîne|OUI|ID de la session.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK : a plus de messages<br /><br /> 0xcc : aucun contenu – plus de messages|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|OUI|Nouvelle expiration.|  
  
### <a name="peek-session-message"></a>Message de session de lecture furtive  

Lit furtivement les messages de session sans les verrouiller.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|OUI|Numéro de séquence à partir duquel commencer la lecture furtive.|  
|message-count|int|OUI|Nombre maximal de messages à lire furtivement.|  
|session-id|chaîne|OUI|ID de la session.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK : a plus de messages<br /><br /> 0xcc : aucun contenu – plus de messages|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un mappage avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Cloud vers appareil|liste des mappages|OUI|Liste de messages dans laquelle chaque mappage représente un message.|  
  
 Le mappage représentant un message doit contenir les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Message|tableau d’octets|OUI|Message codé en filaire AMPQ 1.0.|  
  
### <a name="set-session-state"></a>Définir l’état d’une session  

Définit l’état d’une session.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|session-id|chaîne|OUI|ID de la session.|  
|session-state|tableau d’octets|OUI|Données binaires opaques.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
### <a name="get-session-state"></a>Obtenir l’état d’une session  

Obtient l’état d’une session.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|session-id|chaîne|OUI|ID de la session.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|session-state|tableau d’octets|OUI|Données binaires opaques.|  
  
### <a name="enumerate-sessions"></a>Énumérer les sessions  

Énumère les sessions sur une entité de messagerie.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|OUI|Filtre permettant d’inclure uniquement les sessions mises à jour après une durée donnée.|  
|skip|int|OUI|Ignore un certain nombre de sessions.|  
|top|int|OUI|Nombre maximal de sessions.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK : a plus de messages<br /><br /> 0xcc : aucun contenu – plus de messages|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|skip|int|OUI|Nombre de sessions ignorées si le code d’état est 200.|  
|sessions-ids|tableau de chaînes|OUI|Tableau d’ID de session si le code d’état est 200.|  
  
## <a name="rule-operations"></a>Opérations relatives aux règles  
  
### <a name="add-rule"></a>Ajout de règle  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|rule-name|chaîne|OUI|Nom de règle, à l’exception des noms d’abonnement et de rubrique.|  
|rule-description|map|OUI|Description de la règle comme indiqué dans la section suivante.|  
  
Le mappage **rule-description** doit inclure les entrées suivantes, où **sql-filter** et **correlation-filter** s’excluent mutuellement :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|OUI|`sql-filter`, comme indiqué dans la section suivante.|  
|correlation-filter|map|OUI|`correlation-filter`, comme indiqué dans la section suivante.|  
|sql-rule-action|map|OUI|`sql-rule-action`, comme indiqué dans la section suivante.|  
  
Le mappage sql-filter doit inclure les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|expression|chaîne|OUI|Expression de filtre Sql.|  
  
Le mappage **correlation-filter** doit inclure au moins l’une des entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|correlation-id|chaîne|Non ||  
|message-id|chaîne|Non ||  
|to|chaîne|Non ||  
|reply-to|chaîne|Non ||  
|label|chaîne|Non ||  
|session-id|chaîne|Non ||  
|reply-to-session-id|chaîne|Non ||  
|content-type|chaîne|Non ||  
|properties|map|Non |Mappage sur [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) de Service Bus.|  
  
Le mappage **sql-rule-action** doit inclure les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|expression|chaîne|OUI|Expression d’action SQL.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
### <a name="remove-rule"></a>Supprimer la règle  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|rule-name|chaîne|OUI|Nom de règle, à l’exception des noms d’abonnement et de rubrique.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
## <a name="deferred-message-operations"></a>Opérations relatives aux messages différés  
  
### <a name="receive-by-sequence-number"></a>Recevoir par numéro de séquence  

Reçoit des messages différés par numéro de séquence.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|tableau de type long|OUI|Numéros de séquence.|  
|receiver-settle-mode|ubyte|OUI|Mode de **réglage du récepteur** tel que spécifié dans AMQP core v1.0.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|  
  
Le corps du message de réponse doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|Cloud vers appareil|liste des mappages|OUI|Liste de messages dans laquelle chaque mappage représente un message.|  
  
Le mappage représentant un message doit contenir les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|OUI|Jeton de verrouillage si `receiver-settle-mode` est 1.|  
|Message|tableau d’octets|OUI|Message codé en filaire AMPQ 1.0.|  
  
### <a name="update-disposition-status"></a>Mettre à jour le statut de disposition  

Met à jour le statut de disposition des messages différés.  
  
#### <a name="request"></a>Requête  

Le message de requête doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|operation|chaîne|OUI|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Non |Délai d’expiration du serveur de l’opération en millisecondes.|  
  
Le corps du message de requête doit contenir une section **amqp-value** comprenant un **mappage** avec les entrées suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|disposition-status|chaîne|OUI|Terminé<br /><br /> abandonné<br /><br /> interrompu|  
|lock-tokens|tableau d’uuid|OUI|Jetons de verrouillage de message pour mettre à jour le statut de disposition.|  
|deadletter-reason|chaîne|Non |Peut être défini si le statut de disposition est défini sur **interrompu**.|  
|deadletter-description|chaîne|Non |Peut être défini si le statut de disposition est défini sur **interrompu**.|  
|properties-to-modify|map|Non |Liste des propriétés de message réparti Service Bus à modifier.|  
  
#### <a name="response"></a>response  

Le message de réponse doit inclure les propriétés d’application suivantes :  
  
|Clé|Type de valeur|Obligatoire|Contenu de la valeur|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|OUI|Code de réponse HTTP [RFC2616]<br /><br /> 200 : OK-réussite, sinon échec|  
|statusDescription|chaîne|Non |Description de l’état.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur AMQP et Service Bus, consultez les liens suivants :

* [Vue d’ensemble d’AMQP de Service Bus]
* [Guide du protocole AMQP 1.0]
* [AMQP in Service Bus for Windows Server (AMQP dans Service Bus pour Windows Server)]

[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md
[Guide du protocole AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP in Service Bus for Windows Server (AMQP dans Service Bus pour Windows Server)]: https://msdn.microsoft.com/library/dn574799.asp