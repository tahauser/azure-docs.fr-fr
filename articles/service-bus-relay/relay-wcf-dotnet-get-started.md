---
title: Prise en main des relais WCF Azure Relay dans .NET | Microsoft Docs
description: "Découvrez comment utiliser les relais WCF Azure Relay pour connecter deux applications hébergées à des emplacements différents."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Comment utiliser les relais WCF Azure Relay avec .NET
Cet article explique comment utiliser le service Azure Relay. Les exemples sont écrits en C# et utilisent l’API WCF (Windows Communication Foundation) avec les extensions contenues dans l’assembly Service Bus. Pour plus d’informations sur Azure Relay, consultez la page [Vue d’ensemble d’Azure Relay](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Qu’est-ce que WCF Relay ?

Le service Azure [*WCF Relay*](relay-what-is-it.md) vous permet de créer des applications hybrides qui s’exécutent à la fois dans un centre de données Azure et dans votre propre environnement d’entreprise local. Le service Relay facilite ce processus en offrant la possibilité d’exposer en toute sécurité les services WCF (Windows Communication Foundation) qui résident dans un réseau d’entreprise sur le cloud public, sans avoir à ouvrir une connexion de pare-feu ni à exiger des modifications intrusives dans une infrastructure de réseau d’entreprise.

![Concepts du service WCF Relay](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure Relay vous permet d’héberger des services WCF au sein de votre environnement d’entreprise actuel. Vous pouvez ensuite déléguer au service Relay s’exécutant dans Azure l’écoute des sessions et des demandes entrantes adressées à ces services WCF. Cela vous permet d'exposer ces services au code d'application s'exécutant dans Azure, à des travailleurs itinérants ou à des environnements extranet de partenaires. Relay vous permet de contrôler de manière précise et sécurisée les utilisateurs autorisés à accéder à ces services. Il offre un moyen efficace et sûr d'exposer les fonctionnalités et les données applicatives de vos solutions d'entreprise existantes et d'en tirer profit depuis le cloud.

Cet article explique comment utiliser Azure Relay pour créer un service web WCF, exposé à l’aide d’une liaison de canal TCP, qui implémente une conversation sécurisée entre deux parties.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtention du package NuGet Service Bus
Le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple de se procurer l’API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. Pour installer le package NuGet dans votre projet, procédez comme suit :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Gérer les packages NuGet**.
2. Recherchez « Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus** . Cliquez sur **Installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante :
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Exposer et consommer un service web SOAP avec TCP
Pour exposer un service Web SOAP WCF existant pour une consommation externe, vous devez apporter des modifications aux liaisons et aux adresses du service. Vous pouvez ainsi être amené à modifier votre fichier de configuration ou votre code, selon la façon dont vous avez défini et configuré vos services WCF. Notez que WCF vous permet de disposer de plusieurs points de terminaison réseau sur un même service. Vous pouvez donc conserver les points de terminaison internes existants et ajouter en même temps des points de terminaison de relais pour l’accès externe.

Dans cette tâche, vous créez un service WCF simple et y ajoutez un écouteur de relais. Cet exercice part du principe que vous utilisez déjà Visual Studio, vous ne serez donc pas guidé pour la création de projet. Il porte essentiellement sur le code.

Avant d’effectuer ces étapes, exécutez la procédure suivante pour configurer votre environnement :

1. Dans Visual Studio, créez une application de console contenant deux projets (« Client » et « Service ») dans la solution.
2. Ajoutez le package Service Bus NuGet pour les deux projets. Ce package ajoute toutes les références d’assembly nécessaires à vos projets.

### <a name="how-to-create-the-service"></a>Création du service
Pour commencer, créez le service proprement dit. Un service WCF se compose d'au moins trois parties distinctes :

* Définition d'un contrat décrivant la nature des messages échangés et des opérations à appeler.
* Implémentation de ce contrat.
* Hôte hébergeant ce service WCF et exposant plusieurs points de terminaison.

Les exemples de code présentés dans cette section portent sur chacun de ces composants.

Le contrat définit une seule opération, `AddNumbers`, qui ajoute deux nombres et renvoie le résultat. L'interface `IProblemSolverChannel` permet au client de gérer plus facilement la durée de vie du proxy. Il est recommandé de créer une interface de ce type. De même, vous avez tout intérêt à placer cette définition de contrat dans un fichier distinct de façon à pouvoir y faire référence dans les deux projets (« Client » et « Service »). Vous pouvez également copier le code dans les deux projets.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Une fois que le contrat est en place, l’implémentation se déroule de la façon suivante :

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurer un hôte de service par programme
Maintenant que le contrat et l'implémentation sont en place, vous pouvez héberger le service. L’hébergement se situe à l’intérieur d’un objet [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), qui se charge de gérer les instances du service et héberge les points de terminaison qui écoutent les messages. Le code suivant configure le service avec, d’une part, un point de terminaison local normal et, d’autre part, un point de terminaison de relais, pour illustrer la mise en correspondance, côte à côte, des points de terminaison internes et externes. Remplacez la chaîne *namespace* par le nom de votre espace de noms et *yourKey* par la clé SAP que vous avez obtenue à l’étape de configuration précédente.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Dans l'exemple, vous créez deux points de terminaison dans le cadre de l'implémentation du même contrat. L’un est local et l’autre projeté via Azure Relay. Leurs principales différences sont les liaisons : [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) pour le point de terminaison local, et [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) pour les adresses et le point de terminaison de relais. Le point de terminaison local possède une adresse réseau locale avec un port distinct. Le point de terminaison de relais possède une adresse de point de terminaison constituée de la chaîne `sb`, du nom de votre espace de noms et du chemin « solver ». L’URI obtenu est le suivant : `sb://[serviceNamespace].servicebus.windows.net/solver`, lequel identifie le point de terminaison du service comme un point de terminaison TCP Service Bus (relais) associé à un nom DNS externe complet. Si vous insérez le code en remplaçant les espaces réservés dans la fonction `Main` de l’application **Service**, vous obtiendrez un service fonctionnel. Si vous voulez que votre service écoute exclusivement sur le relais, supprimez la déclaration du point de terminaison local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurer un hôte de service dans le fichier App.config
Vous pouvez également configurer l'hôte à l'aide du fichier App.config. Dans ce cas, le code d’hébergement de service s’affiche dans l’exemple suivant.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Les définitions de point de terminaison se déplacent dans le fichier App.config. Le package NuGet a déjà ajouté au fichier App.config une plage de définitions qui sont les extensions de la configuration nécessaire pour Azure Relay. L’exemple suivant, équivalent exact du code précédent, doit figurer juste en dessous de l’élément **system.serviceModel**. Cet exemple de code suppose que l’espace de noms C# de votre projet se nomme **Service**.
Remplacez les espaces réservés par le nom de votre espace de noms de relais et la clé SAS associée.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Une fois ces modifications effectuées, le service démarre comme précédemment, mais avec deux points de terminaison actifs : l'un local et l'autre écoutant dans le cloud.

### <a name="create-the-client"></a>Création du client
#### <a name="configure-a-client-programmatically"></a>Configuration d’un client par programme
Pour consommer le service, vous pouvez construire un client WCF à l’aide d’un objet [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Service Bus utilise un modèle de sécurité basée sur un jeton, implémenté à l'aide de SAS. La classe [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) représente un fournisseur de jetons de sécurité dont les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus. L’exemple suivant utilise la méthode [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) pour gérer l’acquisition du jeton SAP approprié. Le nom et la clé sont ceux obtenus sur le portail comme indiqué dans la section précédente.

Tout d'abord, référencez ou copiez le code du contrat `IProblemSolver` du service dans votre projet client.

Ensuite, remplacez le code dans la méthode `Main` du client, en remplaçant de nouveau le texte d’espace réservé par votre espace de noms de relais et la clé SAP associée.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Vous pouvez maintenant créer le client et le service, les exécuter (en commençant par le service) ; le client appelle alors le service et imprime **9**. Vous pouvez exécuter le client et le serveur sur différents ordinateurs, voire sur des réseaux, la communication fonctionnera toujours. Le code client peut également être exécuté dans le cloud ou en local.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configuration d’un client dans le fichier App.config
Vous pouvez également configurer le client à l’aide du fichier App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Les définitions de point de terminaison se déplacent dans le fichier App.config. L’exemple suivant, identique au code présenté précédemment, doit figurer juste au-dessous de l’élément `<system.serviceModel>`. Comme précédemment, vous devez remplacer les espaces réservés par l’espace de noms de relais et la clé SAS associée.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les principes de base d’Azure Relay, suivez ces liens pour en savoir plus.

* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Téléchargez des exemples Service Bus à la page [Exemples Azure][Azure samples] ou consultez la [vue d’ensemble des exemples Service Bus][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
