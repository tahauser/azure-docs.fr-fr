---
title: "Communication à distance des services dans Service Fabric | Microsoft Docs"
description: "La communication à distance dans Service Fabric permet aux clients et aux services de communiquer avec les services en utilisant un appel de procédure distante."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 3bdd271eff6f6ea5b337d148f661c7eada429991
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="service-remoting-with-reliable-services"></a>Communication à distance des services avec Reliable Services
Pour les services qui ne sont pas liés à une pile ou un protocole de communication particulier, comme WebAPI, Windows Communication Foundation (WCF) ou autres, l’infrastructure Reliable Services fournit un mécanisme de communication à distance pour configurer rapidement et facilement un appel de procédure distante pour les services.

## <a name="set-up-remoting-on-a-service"></a>Configuration de la communication à distance sur un service
La configuration de communication à distance pour un service s'effectue en deux étapes simples :

1. Créez une interface pour implémenter votre service. Cette interface définit les méthodes disponibles pour un appel de procédure distante sur votre service. Ces méthodes doivent être des méthodes asynchrones retournant des tâches. L'interface doit implémenter `Microsoft.ServiceFabric.Services.Remoting.IService` pour signaler que le service dispose d'une interface de communication à distance.
2. Utilisez un écouteur de communication à distance dans votre service. RemotingListener est une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance. L’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime` contient une méthode d’extension `CreateServiceRemotingListener` pour les services avec et sans état qui peuvent être utilisés pour créer un écouteur de communication à distance à l’aide du protocole de transport de communication à distance par défaut.

>[!NOTE]
>L’espace de noms `Remoting` est disponible sous la forme d’un package NuGet distinct appelé `Microsoft.ServiceFabric.Services.Remoting`

Par exemple, le service sans état suivant expose une méthode unique pour obtenir « Hello World » sur un appel de procédure distante :

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Les arguments et les types de retour dans l’interface de service peuvent être de type simple, complexe ou personnalisé, mais ils doivent être sérialisables par l’élément [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).NET.
>
>

## <a name="call-remote-service-methods"></a>Méthodes d’appel de service distant
L’appel de méthodes sur un service à l’aide de la pile de communication à distance est effectué avec un proxy local pour le service via la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . La méthode `ServiceProxy` crée un proxy local à l’aide de l’interface implémentée par le service. Avec ce proxy, vous pouvez appeler à distance des méthodes sur l’interface.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

L'infrastructure de communication à distance propage les exceptions levées par le service au client. Par conséquent, lorsqu’il reçoit `ServiceProxy`, le client est responsable de la prise en charge des exceptions levées par le service.

## <a name="service-proxy-lifetime"></a>Durée de vie du proxy du service
Comme la création de proxy de service est une opération légère, les utilisateurs peuvent en créer autant de fois que nécessaire. Les instances de proxy de service peuvent être réutilisées tant que les utilisateurs en ont besoin. Si un appel de procédure distante lève une exception, les utilisateurs peuvent toujours réutiliser la même instance de proxy. Chaque proxy de service contient un client de communication qui permet d’envoyer des messages sur le réseau. Lors de l’invocation d’appels distants, nous vérifions en interne que le client de communication est valide. Nous recréons le client de communication en fonction du résultat. De fait, si une exception se produit, les utilisateurs n’ont pas à recréer `ServiceProxy` car cela est fait de manière transparente.

### <a name="serviceproxyfactory-lifetime"></a>Durée de vie de la fabrique ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) est une fabrique qui crée des instances de proxy pour différentes interfaces de communication à distance. Si vous utilisez l’API `ServiceProxy.Create` pour créer un proxy, le framework crée un singleton ServiceProxy.
Il est utile d’en créer un manuellement lorsque vous devez remplacer les propriétés [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La création de fabrique est une opération coûteuse. ServiceProxyFactory conserve le cache interne du client de communication.
Il est recommandé de mettre en cache ServiceProxyFactory aussi longtemps que possible.

## <a name="remoting-exception-handling"></a>Gestion des exceptions à distance
Toutes les exceptions à distance levées par l’API de service sont envoyées au client en tant que AggregateException. Les exceptions à distance doivent être des contrats de données sérialisables. Sinon, l’API du proxy lève [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) en indiquant l’erreur de sérialisation.

Le proxy de service gère toutes les exceptions de basculement pour la partition de service pour laquelle il a été créé. Il résout à nouveau les points de terminaison s’il existe des exceptions de basculement (exceptions non temporaires) et retente l’appel avec le point de terminaison correct. Le nombre de nouvelles tentatives pour des exceptions de basculement est indéfini.
En cas d’exceptions transitoires, le proxy retente l’appel.

Les paramètres de nouvelle tentative par défaut sont fournis par [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
L’utilisateur peut configurer ces valeurs en passant l’objet OperationRetrySettings au constructeur ServiceProxyFactory.
## <a name="how-to-use-remoting-v2-stack"></a>Utilisation de la pile Remoting V2
Avec le package 2.8 NuGet Remoting, vous avez la possibilité d’utiliser la pile Remoting V2. La pile Remoting V2 est plus performante et fournit des fonctionnalités telles que des API sérialisables personnalisées et plus enfichables.
Par défaut, si vous n’effectuez pas les modifications suivantes, la pile Remoting V1 continue d’être utilisée.
Remoting V2 n’est pas compatible avec V1 (pile Remoting précédente). Par conséquent, reportez-vous à l’article ci-dessous sur la mise à niveau de la version V1 vers V2 sans affecter la disponibilité du service.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Utilisation de l’attribut d’Assembly pour utiliser la pile V2.

Voici les étapes à suivre pour modifier à la pile V2.

1. Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2 » au manifeste du service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Utilisez la méthode d’extension de Remoting pour créer un écouteur de communication à distance.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Ajoutez l’attribut d’Assembly sur les Interfaces de communication à distance.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Aucune modification n’est requise dans le projet Client.
Générez l’assembly Client avec l’assembly de l’interface, pour faire en sorte que l’attribut d’assembly soit utilisé.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Utilisation des classes Explicit V2 pour créer Listener/ClientFactory
Voici la procédure à suivre :
1.  Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2 » au manifeste du service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Utilisez [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet). Le nom de la ressource de point de terminaison de service par défaut utilisé est « ServiceEndpointV2 » et doit être défini dans le manifeste de service.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Utilisez V2 [Client Factory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Mise à niveau de Remoting V1 vers Remoting V2.
Pour procéder à la mise à niveau de la version V1 vers V2, des mises à niveau en 2 étapes sont requises. Étapes suivantes à exécuter dans l’ordre indiqué.

1. Procédez à la mise à niveau de V1 Service vers V2 Service à l’aide de l’attribut CompactListener.
Cette modification permet de s’assurer que le service écoute sur V1 et V2 Listener.

    a)  Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2 » au manifeste du service.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b)  Utilisez la méthode d’extension suivante pour créer un écouteur de communication à distance.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Ajoutez l’attribut d’Assembly sur les Interfaces de communication à distance pour utiliser CompatListener et V2 Client.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Procédez à la mise à niveau de V1 Client vers V2 Client à l’aide de l’attribut V2 Client.
Cette étape permet de s’assurer de Client utilise la pile V2.
Aucune modification du projet/service Client n’est requise. La création de projets Client avec l’assembly de l’interface mise à jour est suffisante.

3. Cette étape est facultative. Utilisez l’attribut V2Listener, puis mettez à niveau vers le service V2.
Cette étape permet de s’assurer que le service écoute uniquement sur l’écouteur V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Comment utiliser la sérialisation personnalisée avec Remoting V2.
L’exemple suivant utilise la sérialisation Json avec Remoting V2.
1. Implémentez l’interface IServiceRemotingMessageSerializationProvider pour permettre l’implémentation de la sérialisation personnalisée.
    Voici l’extrait de code permettant de voir comment se présente une implémentation.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Remplacez le fournisseur de sérialisation par défaut par JsonSerializationProvider pour Remoting Listener.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Remplacez le fournisseur de sérialisation par défaut par JsonSerializationProvider pour Remoting Client Factory.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Étapes suivantes
* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)
