---
title: "Sécuriser des communications pour les services dans Azure Service Fabric | Microsoft Docs"
description: "Vue d’ensemble de la sécurisation des communications pour Reliable Services en cours d’exécution dans un cluster Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 0804e43c3f1bb13bea92ebd661ca52c799ff2332
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Sécurisation des communications pour les services dans Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java sur Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

La sécurité est un des aspects les plus importants de la communication. L’infrastructure d’application Reliable Services fournit quelques piles et outils de communication prédéfinis afin d’améliorer la sécurité. Cet article vous explique comment améliorer la sécurité quand vous utilisez la communication à distance des services et la pile de communication Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Sécurisation d’un service lors de l’utilisation de la communication à distance des services
Nous utilisons un [exemple](service-fabric-reliable-services-communication-remoting.md) existant qui explique comment configurer la communication à distance pour Reliable Services. Pour sécuriser un service lors de l’utilisation de la communication à distance des services, procédez comme suit :

1. Créez une interface, `IHelloWorldStateful`, qui définit les méthodes disponibles pour l'appel de procédure distante sur votre service. Votre service utilisera `FabricTransportServiceRemotingListener`, qui est déclaré dans l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Il s'agit d'une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Ajoutez des paramètres de l’écouteur et des informations d’identification de sécurité.

    Assurez-vous le certificat que vous souhaitez utiliser pour sécuriser les communications de votre service est installé sur tous les nœuds du cluster. Vous pouvez fournir les paramètres de l’écouteur et les informations d’identification de sécurité de deux manières :

   1. Directement dans le code de service :

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. À l'aide d’un [package de configuration](service-fabric-application-and-service-manifests.md):

       Ajoutez une section `TransportSettings` dans le fichier settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       Dans ce cas, la méthode `CreateServiceReplicaListeners` se présente comme suit :

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Si vous ajoutez une section `TransportSettings` dans le fichier settings.xml, `FabricTransportRemotingListenerSettings ` charge par défaut tous les paramètres de cette section.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Dans ce cas, la méthode `CreateServiceReplicaListeners` se présente comme suit :

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Lorsque vous appelez des méthodes sur un service sécurisé à l’aide de la pile de communication à distance, au lieu d’utiliser la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` pour créer un proxy de service, utilisez `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Transmettez `FabricTransportRemotingSettings`, qui contient `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le code client s’exécute dans le cadre d’un service, vous pouvez charger `FabricTransportRemotingSettings` à partir du fichier settings.xml. Créez une section HelloWorldClientTransportSettings similaire au code de service, comme illustré précédemment. Apportez les modifications suivantes au code du client :

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le client n’est pas exécuté dans le cadre d’un service, vous pouvez créer un fichier client_name.settings.xml dans le même emplacement que le fichier client_name.exe. Créez ensuite une section TransportSettings dans ce fichier.

    Comme pour le service, si vous ajoutez une section `TransportSettings` dans le fichier client settings.xml/client_name.settings.xml, `FabricTransportRemotingSettings` charge par défaut tous les paramètres de cette section.

    Dans ce cas, le code précédent est encore davantage simplifié :  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF
Nous utilisons un [exemple](service-fabric-reliable-services-communication-wcf.md) existant qui explique comment configurer une pile de communication basée sur WCF pour Reliable Services. Pour sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF, procédez comme suit :

1. Pour le service, vous devez sécuriser l'écouteur de communication WCF (`WcfCommunicationListener`) que vous créez. Pour cela, modifiez la méthode `CreateServiceReplicaListeners` .

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Dans le client, la classe `WcfCommunicationClient` que nous avons créée dans l’ [exemple](service-fabric-reliable-services-communication-wcf.md) précédent reste inchangée. Cependant, vous devez remplacer la méthode `CreateClientAsync` de `WcfCommunicationClientFactory` :

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utilisez `SecureWcfCommunicationClientFactory` pour créer un client de communication WCF (`WcfCommunicationClient`). Utilisez le client pour appeler les méthodes de service.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Étapes suivantes
* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
