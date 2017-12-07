---
title: "Connexions hybrides d’Azure App Service | Microsoft Docs"
description: "Comment créer et utiliser des connexions hybrides pour accéder aux ressources dans des réseaux hétérogènes"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Connexions hybrides d’Azure App Service #

Les connexions hybrides sont un service dans Azure et une fonctionnalité dans Azure App Service. En tant que service, il exploite et inclut des fonctionnalités qui vont au-delà de celles utilisées dans App Service. Pour en savoir plus sur les connexions hybrides et leur utilisation en dehors d’App Service, consultez [Connexions hybrides Azure Relay][HCService].

Dans App Service, les connexions hybrides peuvent être utilisées pour accéder aux ressources d’application d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Elles n’autorisent pas à une autre fonction d’accéder à votre application. Utilisée dans App Service, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application à condition que vous accédiez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détectent pas et ne prennent pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elles fournissent simplement un accès réseau.  


## <a name="how-it-works"></a>Fonctionnement ##
La fonctionnalité Connexions hybrides se compose de deux appels sortants vers Azure Service Bus Relay. Il existe une connexion à partir d’une bibliothèque sur l’hôte sur lequel votre application est en cours d’exécution dans App Service. Il existe également une connexion entre Hybrid Connection Manager (HCM) et Service Bus Relay. Le GCH est un service de relais que vous déployez dans le réseau hébergeant la ressource à laquelle vous tentez d’accéder. 

Grâce aux deux connexions liées, votre application inclut un tunnel TCP vers une combinaison hôte:port fixe de l’autre côté du GCH. La connexion utilise TLS 1.2 pour la sécurité et des clés de signature d’accès partagé (SAP) pour l’authentification et l’autorisation.    

![Diagramme du flux de haut niveau de la connexion hybride][1]

Lorsque votre application effectue une requête DNS qui correspond à un point de terminaison de connexion hybride configuré, le trafic TCP sortant est redirigé via la connexion hybride.  

> [!NOTE]
> Cela signifie que vous devez toujours utiliser un nom DNS pour votre connexion hybride. Certains logiciels clients n’effectuent une recherche DNS que si le point de terminaison utilise une adresse IP à la place.
>
>

La fonctionnalité Connexions hybrides a deux types : les connexions hybrides proposées en tant que service sous Service Bus Relay et les anciennes connexions hybrides BizTalk Services. Ces dernières sont appelées des connexions hybrides classiques dans le portail. D’autres informations s’y rapportant sont fournies plus loin dans cet article.

### <a name="app-service-hybrid-connection-benefits"></a>Avantages d’une connexion hybride App Service ###

La fonctionnalité de connexions hybrides offre un certain nombre d’avantages, notamment :

- Les applications peuvent accéder en toute sécurité aux systèmes et services locaux.
- La fonctionnalité ne nécessite pas un point de terminaison accessible par Internet.
- sa configuration est simple et rapide ; 
- Chaque connexion hybride correspond à une combinaison hôte:port unique, gage de sécurité.
- Normalement, elle ne nécessite pas de trous de pare-feu. Les connexions sont toutes sortantes via des ports web standard.
- la fonctionnalité se situant au niveau du réseau, elle n’est pas spécifique au langage utilisé par votre application et à la technologie utilisée par le point de terminaison ;
- elle peut être utilisée pour fournir un accès à plusieurs réseaux à partir d’une même application. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Ce que vous ne pouvez pas faire avec les connexions hybrides ###

Vous ne pouvez pas faire certaines opérations avec les connexions hybrides, notamment :

- Montage d’un lecteur.
- Utilisation d’UDP.
- Accès à des services TCP qui utilisent des ports dynamiques tels que le mode FTP passif ou le mode passif étendu.
- Prise en charge de LDAP, car UDP est parfois exigé.
- Prise en charge d’Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Ajouter et créer des connexions hybrides dans votre application ##

Vous pouvez créer des connexions hybrides via votre application App Service ou d’Azure Relay dans le portail Azure. Nous vous recommandons de créer des connexions hybrides via l’application App Service que vous voulez utiliser avec la connexion hybride. Pour créer une connexion hybride, accédez au [portail Azure][portal] et sélectionnez votre application. Sélectionnez **Mise en réseau** > **Configurer vos points de terminaison de connexion hybride**. Vous pouvez alors voir les connexions hybrides configurées pour votre application.  

![Capture d’écran de la liste des connexions hybrides][2]

Pour ajouter une nouvelle connexion hybride, sélectionnez **Ajouter une connexion hybride**.  La liste des connexions hybrides que vous avez déjà créées apparaît. Pour en ajouter une ou plusieurs à votre application, sélectionnez celles de votre choix, puis **Ajouter la connexion hybride sélectionnée**.  

![Capture d’écran du portail des connexions hybrides][3]

Si vous souhaitez créer une nouvelle connexion hybride, sélectionnez **Créer une connexion hybride**. Spécifiez les éléments suivants : 

- Nom du point de terminaison.
- Nom d’hôte du point de terminaison.
- Port du point de terminaison.
- Espace de noms Service Bus que vous voulez utiliser.

![Capture d’écran de la boîte de dialogue de création d’une connexion hybride][4]

Chaque connexion hybride est liée à un espace de noms Service Bus et chaque espace de noms Service Bus se trouve dans une région Azure. Il est important d’essayer d’utiliser un espace de noms Service Bus dans la même région que votre application pour éviter une latence du réseau.

Si vous souhaitez supprimer votre connexion hybride de votre application, cliquez avec le bouton droit dessus et sélectionnez **Déconnecter**.  

Lorsqu’une connexion hybride est ajoutée à votre application, vous pouvez afficher ses détails simplement en la sélectionnant. 

![Capture d’écran des détails des connexions hybrides][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Créer une connexion hybride dans le portail Azure Relay ###

En plus d’utiliser le portail à partir de votre application, vous pouvez créer des connexions hybrides depuis le portail Azure Relay. Pour qu’App Service utilise une connexion hybride, celle-ci doit :

* Exiger une autorisation du client.
* Comporter un élément de métadonnées, un point de terminaison nommé contenant une combinaison hôte:port comme valeur.

## <a name="hybrid-connections-and-app-service-plans"></a>Connexions hybrides et plans App Service ##

La fonctionnalité Connexions hybrides est uniquement disponible dans les références SKU des tarifs De base, Standard, Premium et Isolé. Des limites sont liées au plan de tarification.  

> [!NOTE] 
> Vous pouvez uniquement créer de nouvelles connexions hybrides basées sur Azure Relay. Il n’est pas possible de créer de nouvelles connexions hybrides BizTalk.
>

| Plan tarifaire | Nombre de connexions hybrides utilisables dans le plan |
|----|----|
| De base | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolé | 200 |

Notez que le plan App Service vous indique combien de connexions hybrides sont utilisées et par quelles applications.  

![Capture d’écran des propriétés du plan App Service][6]

Sélectionnez la connexion hybride pour voir les détails. Vous pouvez voir toutes les informations que vous avez vues dans la vue de l’application. Vous pouvez également voir combien d’autres applications utilisent cette connexion hybride dans le même plan.

Le nombre de points de terminaison de connexion hybride utilisables dans un plan App Service est limité. Chaque connexion hybride utilisée peut, toutefois, être utilisée sur autant d’applications que souhaité dans ce plan. Par exemple, une même connexion hybride utilisée dans cinq applications distinctes dans un plan App Service compte comme une seule connexion hybride.

L’utilisation des connexions hybrides fait l’objet de frais supplémentaires. Pour plus d’informations, consultez les [tarifs de Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestionnaire de connexion hybride ##

La fonctionnalité Connexions hybrides exige un agent de relais dans le réseau qui héberge votre point de terminaison de connexion hybride. Cet agent de relais est appelé le Gestionnaire de connexion hybride (GCH). Pour télécharger HCM, à partir de votre application dans le [portail Azure][portal], sélectionnez **Mise en réseau** > **Configurer vos points de terminaison de connexion hybride**.  

Cet outil s’exécute sur Windows Server 2012 et version ultérieure. Une fois installé, HCM s’exécute en tant que service qui se connecte à Service Bus Relay, en fonction des points de terminaison configurés. Les connexions à partir de HCM accèdent à Azure par le biais du port 443.    

Après avoir installé HCM, vous pouvez exécuter HybridConnectionManagerUi.exe pour utiliser l’interface utilisateur de l’outil. Ce fichier se trouve dans le répertoire d’installation de Hybrid Connection Manager. Dans Windows 10, vous pouvez aussi simplement rechercher *Hybrid Connection Manager UI* dans votre zone de recherche.  

![Capture d’écran de Hybrid Connection Manager][7]

Quand vous démarrez l’interface utilisateur HCM, vous voyez tout d’abord un tableau qui répertorie toutes les connexions hybrides configurées avec cette instance de HCM. Pour apporter des modifications, commencez par vous authentifier auprès d’Azure. 

Pour ajouter une ou plusieurs connexions hybrides à votre GCH :

1. Démarrez l’interface utilisateur de HCM.
1. Sélectionnez **Configurer une autre connexion hybride**.
![Capture d’écran de la configuration de nouvelles connexions hybrides][8]

1. Connectez-vous à votre compte Azure.
1. Choisissez un abonnement.
1. Sélectionnez les connexions hybrides à faire relayer par HCM.
![Capture d’écran des connexions hybrides][9]

1. Sélectionnez **Enregistrer**.

Vous pouvez maintenant voir les connexions hybrides que vous avez ajoutées. Vous pouvez également sélectionner la connexion hybride configurée pour en afficher les détails.

![Capture d’écran des détails de la connexion hybride][10]

Pour prendre en charge les connexions hybrides avec lesquelles il est configuré, HCM exige les éléments suivants :

- Accès TCP à Azure sur les ports 80 et 443.
- Accès TCP au point de terminaison de connexion hybride.
- Possibilité de recherches DNS sur l’hôte du point de terminaison et l’espace de noms Service Bus.

HCM prend en charge les nouvelles connexions hybrides et les connexions hybrides BizTalk.

> [!NOTE]
> Azure Relay s’appuie sur les sockets web pour assurer la connectivité. Cette fonctionnalité est uniquement disponible sur Windows Server 2012 ou version ultérieure. C’est pourquoi HCM n’est pas pris en charge sur les systèmes antérieurs à Windows Server 2012.
>

### <a name="redundancy"></a>Redondance ###

Chaque GCH peut prendre en charge plusieurs connexions hybrides. De plus, toute connexion hybride peut être prise en charge par plusieurs GCH. Le comportement par défaut consiste à acheminer le trafic entre les HCM configurés pour n’importe quel point de terminaison donné. Si vous souhaitez une haute disponibilité sur vos connexions hybrides à partir de votre réseau, exécutez plusieurs HCM sur des ordinateurs distincts. 

### <a name="manually-add-a-hybrid-connection"></a>Ajouter manuellement une connexion hybride ###

Pour permettre à une personne extérieure à votre abonnement d’héberger une instance HCM pour une connexion hybride donnée, communiquez-lui la chaîne de connexion de passerelle de la connexion hybride. Vous la trouverez dans les propriétés d’une connexion hybride dans le [portail Azure][portal]. Pour utiliser cette chaîne, sélectionnez **Saisir manuellement** dans HCM et collez la chaîne de connexion de passerelle.


## <a name="troubleshooting"></a>Résolution des problèmes ##

L’état « Connecté » signifie qu’au moins un HCM est configuré avec cette connexion hybride et qu’il est en mesure d’atteindre Azure. Si l’état de votre connexion hybride n’indique pas **Connecté**, votre connexion hybride n’est configurée sur aucun HCM ayant accès à Azure.

La principale raison pour laquelle les clients ne peuvent pas se connecter à leur point de terminaison est que le point de terminaison a été spécifié à l’aide d’une adresse IP au lieu d’un nom DNS. Si votre application ne peut pas accéder au point de terminaison souhaité et que vous avez utilisé une adresse IP, utilisez un nom DNS valide sur l’hôte sur lequel le GCH est exécuté. Vérifiez également que le nom DNS est correctement résolu sur l’hôte sur lequel le HCM est en cours d’exécution. Vérifiez qu’il existe une connectivité à partir de l’hôte où le HCM est en cours d’exécution vers le point de terminaison de connexion hybride.  

Dans App Service, l’outil tcpping peut être appelé à partir de la console Outils avancés (Kudu). Cet outil peut vous indiquer si vous avez accès à un point de terminaison TCP, mais ne vous dit pas si vous avez accès à un point de terminaison de connexion hybride. Lorsque vous utilisez l’outil dans la console par rapport à un point de terminaison de connexion hybride, vous confirmez seulement qu’il utilise une combinaison hôte:port.  

## <a name="biztalk-hybrid-connections"></a>Connexions hybrides BizTalk ##

L’ancienne fonctionnalité Connexions hybrides BizTalk a été désactivée pour ne plus permettre de créer de connexion hybride BizTalk supplémentaire. Vous pouvez continuer à utiliser vos connexions hybrides BizTalk existantes avec vos applications, mais vous devez migrer vers les nouvelles connexions hybrides utilisant Azure Relay. Les avantages du nouveau service par rapport à la version BizTalk incluent entre autres :

- aucun compte BizTalk supplémentaire n’est requis ;
- Le protocole TLS utilisé correspond à la version 1.2 au lieu de la version 1.0.
- La communication se fait sur les ports 80 et 443 et utilise un nom DNS pour accéder à Azure, au lieu d’adresses IP et d’une plage de ports supplémentaires.  

Pour ajouter une connexion hybride BizTalk existante à votre application, accédez à votre application dans le [portail Azure][portal], puis sélectionnez **Mise en réseau** > **Configurer vos points de terminaison de connexion hybride**. Dans la table des connexions hybrides classiques, cliquez sur **Ajouter la connexion hybride classique**. La liste de vos connexions hybrides BizTalk s’affiche.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
