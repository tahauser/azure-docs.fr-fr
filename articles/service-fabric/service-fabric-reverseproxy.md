---
title: "Proxy inverse Azure Service Fabric | Microsoft Docs"
description: "Utilisez le proxy inverse de Service Fabric pour assurer les communications avec les microservices de l’intérieur et de l’extérieur du cluster."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 55b201842503a879725fa77328a72c83fe0bbade
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy inverse dans Azure Service Fabric
Le proxy inverse intégré à Azure Service Fabric permet aux microservices exécutés dans un cluster Service Fabric de découvrir d’autres services détenant des points de terminaison http et de communiquer avec ces services.

## <a name="microservices-communication-model"></a>Modèle de communication des microservices
Dans Service Fabric, les microservices s’exécutent sur un sous-ensemble de nœuds au sein du cluster et peuvent migrer entre les nœuds, pour des raisons diverses. Ainsi, les points de terminaison associés aux microservices peuvent changer de manière dynamique. Pour découvrir d’autres services dans le cluster et communiquer avec ces derniers, un microservice doit passer par les étapes suivantes :

1. Résoudre l’emplacement du service avec le service de nommage
2. Connectez-vous au service.
3. Encapsuler les étapes précédentes dans une boucle qui implémente la résolution de service, puis réessayer des stratégies à appliquer en cas d’échec de la connexion

Pour plus d’informations, consultez la page [Se connecter et communiquer avec les services](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Communiquer avec le proxy inverse
Le proxy inverse est un service qui s’exécute sur chaque nœud et qui gère la résolution des points de terminaison, les nouvelles tentatives automatiques et autres échecs de connexion pour le compte des services clients. Le proxy inverse peut être configuré pour appliquer différentes stratégies à mesure qu’il gère les demandes émanant des services clients. Utiliser un proxy inverse permet au service client de recourir à toutes les bibliothèques de communication HTTP côté client et ne nécessite aucune résolution spéciale ou logique de nouvelle tentative dans le service. 

Le proxy inverse expose un ou plusieurs points de terminaison sur le nœud local que les services clients peuvent utiliser pour envoyer des demandes à d’autres services.

![Communications internes][1]

> [!NOTE]
> **Plateformes prises en charge**
>
> Le proxy inverse dans Service Fabric prend en charge les plateformes suivantes :
> * *Cluster Windows* : Windows versions 8 et ultérieures ou Windows Server versions 2012 et ultérieures
> * *Cluster Linux* : le proxy inverse n’est pas disponible pour les clusters Linux.
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atteindre les microservices de l’extérieur du cluster
Le modèle de communication externe par défaut des microservices est un modèle d’adhésion, avec lequel aucun service n’est accessible directement à partir des clients externes. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), qui représente une limite réseau située entre les microservices et les clients externes, effectue la traduction des adresses réseau et transfère les requêtes externes vers des points de terminaison IP:port internes. Pour que le point de terminaison d’un microservice soit accessible aux clients externes, vous devez tout d’abord configurer l’équilibreur de charge de sorte qu’il transfère le trafic vers chaque port utilisé par le service au sein du cluster. En outre, la plupart des microservices, en particulier les microservices avec état, ne résident pas sur tous les nœuds du cluster. Les microservices peuvent se déplacer entre les nœuds lors d’un basculement. Dans ce cas, l’équilibreur de charge ne peut pas déterminer efficacement l’emplacement du nœud cible des réplicas auxquels il doit transférer le trafic.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Atteindre les microservices par l’intermédiaire du proxy inverse à partir de l’extérieur du cluster
Au lieu de configurer le port d’un service donné dans l’équilibreur de charge, vous pouvez configurer uniquement le port du proxy inverse dans l’équilibreur de charge. Cette configuration permet aux clients se trouvant en dehors du cluster d’atteindre les services à l’intérieur du cluster avec le proxy inverse sans qu’une configuration supplémentaire soit nécessaire.

![Communications externes][0]

> [!WARNING]
> Lorsque vous configurez le port du proxy inverse dans l’équilibreur de charge, les systèmes se trouvant à l’extérieur du cluster peuvent atteindre tous les microservices du cluster exposant un point de terminaison HTTP.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Format d’URI pour l’adressage des services avec le proxy inverse
Le proxy inverse utilise un format d’URI (Uniform Resource Identifier) spécifique pour identifier la partition de service vers laquelle une requête entrante doit être transférée :

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s) :** le proxy inverse peut être configuré pour accepter le trafic HTTP ou HTTPS. Pour le transfert HTTPS, reportez-vous à [Se connecter à un service sécurisé avec le proxy inverse](service-fabric-reverseproxy-configure-secure-communication.md) une fois que vous avez configuré le proxy inverse pour écouter sur HTTPS.
* **Cluster fully qualified domain name (FQDN) | internal IP :** pour les clients externes, vous pouvez configurer le proxy inverse de façon à le rendre accessible par le biais du domaine du cluster, par exemple mycluster.eastus.cloudapp.azure.com. Par défaut, le proxy inverse s’exécute sur chacun des nœuds. Pour le trafic interne, le proxy inverse est accessible sur l’hôte local ou sur n’importe quelle adresse IP de nœud interne, par exemple 10.0.0.1.
* **Port :** port, par exemple 19081, spécifié pour le proxy inverse.
* **ServiceInstanceName :** nom complet de l’instance de service déployée que vous tentez d’atteindre sans le schéma « fabric:/ ». Par exemple, pour atteindre le service *fabric:/myapp/myservice/*, vous pouvez utiliser *myapp/myservice*.

    Le nom d’instance de service respecte la casse. L’utilisation d’une casse différente pour le nom d’instance de service dans l’URL entraîne l’échec des demandes avec l’erreur 404 (Introuvable).
* **Suffix path :** chemin d’accès réel à l’URL, par exemple *myapi/values/add/3*, associée au service auquel vous souhaitez vous connecter.
* **PartitionKey :** pour un service partitionné, il s’agit de la clé calculée associée à la partition que vous souhaitez atteindre. Il ne s’agit *pas* du GUID d’ID de la partition. Ce paramètre n’est pas obligatoire pour les services qui utilisent le schéma de partition de singleton.
* **PartitionKind :** schéma de partition du service. Il peut s’agir de Named ou Int64Range. Ce paramètre n’est pas obligatoire pour les services qui utilisent le schéma de partition de singleton.
* **ListenerName :** les points de terminaison du service se présentent sous la forme {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2"...}}. Lorsque le service expose plusieurs points de terminaison, cela permet d’identifier le point de terminaison auquel la demande client doit être transférée. Ce paramètre peut être omis si le service n’a qu’un seul écouteur.
* **TargetReplicaSelector :** spécifie le mode de sélection à utiliser pour le réplica cible ou l’instance.
  * Lorsque le service cible est avec état, le paramètre TargetReplicaSelector peut être défini sur « PrimaryReplica », « RandomSecondaryReplica » ou « RandomReplica ». Lorsque ce paramètre n’est pas spécifié, la valeur par défaut est « PrimaryReplica ».
  * Lorsque le service cible est sans état, le proxy inverse transfère la demande à une instance aléatoire de la partition de service.
* **Timeout :** spécifie le délai d’attente de la requête HTTP créée par le proxy inverse pour le service, pour le compte de la requête du client. La valeur par défaut est de 60 secondes. Paramètre facultatif.

### <a name="example-usage"></a>Exemple d’utilisation
Prenons l’exemple du service *fabric:/MyApp/MyService*, qui ouvre un écouteur HTTP sur l’URL suivante :

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Voici les ressources du service :

* `/index.html`
* `/api/users/<userId>`

Si le service utilise le schéma de partitionnement singleton, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* ne sont pas requis ; le service est accessible avec la passerelle de la façon suivante :

* En externe : `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* En interne : `http://localhost:19081/MyApp/MyService`

Si le service utilise le schéma de partitionnement Int64 uniforme, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* doivent être utilisés pour atteindre une partition du service :

* En externe : `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* En interne : `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Pour atteindre les ressources exposées par le service, il vous suffit de placer le chemin d’accès à la ressource après le nom du service dans l’URL :

* En externe : `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* En interne : `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

La passerelle transmet ensuite ces requêtes à l’URL du service :

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Traitement spécial des services de partage de port
Le proxy inverse Service Fabric tente à nouveau de résoudre une adresse de service, et réexécute la requête lorsqu’un service est inaccessible. En règle générale, lorsqu’un service est inaccessible, le réplica ou l’instance de service ont été déplacés vers un autre nœud dans le cadre de leur cycle de vie normal. Dans ce cas, le proxy inverse peut recevoir une erreur de connexion au réseau, qui indique qu’un point de terminaison n’est plus ouvert sur l’adresse résolue à l’origine.

Toutefois, les instances de service ou réplicas peuvent partager un processus hôte, voire un port, lorsqu’ils sont hébergés par un serveur web basé sur HTTP.sys. Cela inclut :

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Dans cette situation, le serveur web est probablement disponible dans le processus hôte et répond aux requêtes, mais le réplica ou l’instance de service résolue ne sont plus accessibles sur l’hôte. Dans ce cas, la passerelle reçoit une réponse HTTP 404 du serveur web. Par conséquent, une réponse HTTP 404 peut avoir deux significations :

- 1er cas : l’adresse du service est correcte, mais la ressource demandée par l’utilisateur n’existe pas.
- 2e cas : l’adresse du service est incorrecte, et la ressource demandée par l’utilisateur peut exister sur un autre nœud.

Le premier cas est une erreur HTTP 404 normale, considérée comme une erreur de l’utilisateur. Toutefois, dans le deuxième cas, l’utilisateur a demandé une ressource qui existe réellement. Le proxy inverse n’a pas réussi à la localiser parce que le service a été déplacé. Le proxy inverse doit résoudre à nouveau l’adresse et réexécuter la requête.

Le proxy inverse doit donc faire la distinction entre ces deux cas. Le serveur doit lui fournir une indication lui permettant d’y parvenir.

* Par défaut, le proxy inverse part du principe qu’il s’agit du deuxième cas et tente à nouveau de résoudre le problème, puis de renvoyer la requête.
* Pour indiquer au proxy inverse qu’il s’agit du premier cas, le service doit retourner l’en-tête de réponse HTTP suivant :

  `X-ServiceFabric : ResourceNotFound`

Cet en-tête indique une situation HTTP 404 normale, dans laquelle la ressource demandée n’existe pas, et le proxy inverse ne tente pas de résoudre à nouveau l’adresse du service.

## <a name="setup-and-configuration"></a>Installation et configuration

### <a name="enable-reverse-proxy-via-azure-portal"></a>Activer le proxy inverse via le portail Azure

Le portail Azure fournit une option permettant d’activer le proxy inverse lors de la création d’un cluster Service Fabric.
Sous **Créer un cluster Service Fabric** (étape 2 : Configuration de cluster, Configuration du type de nœud), cochez la case « Activer le proxy inverse ».
Dans le cadre de la configuration du proxy inverse sécurisé, le certificat SSL peut être indiqué au cours de l’étape 3 : Sécurité, Configurer les paramètres de sécurité du cluster ; pour cela, cochez la case permettant d’inclure un certificat SSL pour le proxy inverse, puis indiquez les informations sur ce certificat.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Activer le proxy inverse via les modèles Azure Resource Manager

Vous pouvez utiliser le [modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) pour activer le proxy inverse de Service Fabric pour le cluster.

Consultez la page [Configurer le Proxy inverse HTTPS dans un cluster sécurisé](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) pour voir des exemples de modèles Azure Resource Manager permettant de configurer un proxy inverse sécurisé avec un certificat et gérant la substitution de certificat.

Tout d’abord, vous récupérez le modèle du cluster que vous souhaitez déployer. Vous pouvez soit utiliser les exemples de modèles, soit créer un modèle Resource Manager personnalisé. Ensuite, vous pouvez activer le proxy inverse en suivant les étapes suivantes :

1. Définissez un port pour le proxy inverse, dans la [section des paramètres](../azure-resource-manager/resource-group-authoring-templates.md) du modèle.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Spécifiez le port de chaque objet nodetype dans la **section du type de ressource** [Cluster](../azure-resource-manager/resource-group-authoring-templates.md).

    Le port est identifié par le nom de paramètre, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Pour contacter le proxy inverse de l’extérieur du cluster Azure, configurez les règles Azure Load Balancer pour le port spécifié à l’étape 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Pour configurer des certificats SSL sur le port du proxy inverse, ajoutez le certificat à la propriété ***reverseProxyCertificate*** dans la [section du type de ressource](../resource-group-authoring-templates.md) **Cluster**.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Prise en charge d’un certificat de proxy inverse différent du certificat de cluster
 Si le certificat du proxy inverse est différent du certificat qui sécurise le cluster, le certificat spécifié précédemment doit être installé sur la machine virtuelle et ajouté à la liste de contrôle d’accès (ACL) afin que Service Fabric puisse y accéder. Pour cela, vous pouvez utiliser la [section du type de ressource](../resource-group-authoring-templates.md) **virtualMachineScaleSets**. Pour l’installation, ajoutez ce certificat au profil osProfile. La section d’extension du modèle peut mettre à jour le certificat dans l’ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Lorsque vous utilisez des certificats différents du certificat de cluster pour activer le certificat du proxy inverse sur un cluster existant, installez le proxy inverse et mettez à jour l’ACL sur le cluster avant d’activer le proxy inverse. Terminez le déploiement du [modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) avec les paramètres mentionnés précédemment avant de commencer un déploiement pour activer le proxy inverse en suivant les étapes 1 à 4.

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir un exemple de communication HTTP entre services, consultez cet [exemple de projet sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Transfert vers un service HTTP sécurisé avec le proxy inverse](service-fabric-reverseproxy-configure-secure-communication.md)
* [Appels de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)
* [API Web qui utilise OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Communication WCF à l’aide de Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* Pour les options de configuration supplémentaires du proxy inverse, reportez-vous à la section ApplicationGateway/Http dans [Personnaliser les paramètres de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
