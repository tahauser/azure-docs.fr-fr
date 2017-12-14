---
title: "Sécuriser des communications pour les services dans Azure Service Fabric | Microsoft Docs"
description: "Vue d’ensemble de la sécurisation des communications pour Reliable Services en cours d’exécution dans un cluster Azure Service Fabric."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 5e2f36b3de1dd04c1a3f36ae308af164d10654ea
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Sécurisation des communications pour les services dans Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java sur Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Sécurisation d’un service lors de l’utilisation de la communication à distance des services
Nous allons utiliser un [exemple](service-fabric-reliable-services-communication-remoting-java.md) existant qui explique comment configurer la communication à distance pour Reliable Services. Pour sécuriser un service lors de l’utilisation de la communication à distance des services, procédez comme suit :

1. Créez une interface, `HelloWorldStateless`, qui définit les méthodes disponibles pour l'appel de procédure distante sur votre service. Votre service utilisera `FabricTransportServiceRemotingListener`, qui est déclaré dans le package `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Il s'agit d'une implémentation `CommunicationListener` qui fournit des fonctionnalités de communication à distance.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Ajoutez des paramètres de l’écouteur et des informations d’identification de sécurité.

    Assurez-vous le certificat que vous souhaitez utiliser pour sécuriser les communications de votre service est installé sur tous les nœuds du cluster. Vous pouvez fournir les paramètres de l’écouteur et les informations d’identification de sécurité de deux manières :

   1. À l'aide d’un [package de configuration](service-fabric-application-and-service-manifests.md):

       Ajoutez une section `TransportSettings` dans le fichier settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       Dans ce cas, la méthode `createServiceInstanceListeners` se présente comme suit :

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Si vous ajoutez une section `TransportSettings` dans le fichier settings.xml sans aucun préfixe, `FabricTransportListenerSettings` charge par défaut tous les paramètres de cette section.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Dans ce cas, la méthode `CreateServiceInstanceListeners` se présente comme suit :

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Lorsque vous appelez des méthodes sur un service sécurisé à l’aide de la pile de communication à distance, au lieu d’utiliser la classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` pour créer un proxy de service, utilisez `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Si le code client s’exécute dans le cadre d’un service, vous pouvez charger `FabricTransportSettings` à partir du fichier settings.xml. Créez une section TransportSettings similaire au code de service, comme illustré précédemment. Apportez les modifications suivantes au code du client :

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
