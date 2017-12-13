---
title: "Protéger le contenu HLS Apple FairPlay - Azure hors connexion | Microsoft Docs"
description: "Cette rubrique fournit une vue d’ensemble et montre comment utiliser Azure Media Services pour chiffrer dynamiquement votre contenu HTTP Live Streaming (HLS) avec Apple FairPlay en mode hors connexion."
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), hors connexion, iOS 10
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>FairPlay Streaming hors connexion
Microsoft Azure Media Services fournit un ensemble bien conçu de [services de protection de contenu](https://azure.microsoft.com/services/media-services/content-protection/), couvrant :
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Chiffrement AES-128

Le chiffrement de contenu DRM/AES est exécuté dynamiquement sur demande pour divers protocoles de diffusion en continu. Des services de remise de clés de chiffrement AES/licence DMR sont également fournis par Azure Media Services.

Outre la protection du contenu pour la diffusion en continu en ligne sur plusieurs protocoles de diffusion en continu, le mode hors connexion pour le contenu protégé est également une fonctionnalité fréquemment demandée. La prise en charge du mode hors connexion est nécessaire pour les scénarios suivants :
1. La lecture avec une connexion Internet non disponible, par exemple en voyage ;
2. Certains fournisseurs de contenu peuvent interdire la remise de licence DRM au-delà de la frontière d’un pays. Si un utilisateur souhaite regarder du contenu en voyage à l’étranger, le téléchargement hors connexion est nécessaire.
3. Dans certains pas, la disponibilité Internet et/ou la bande passante est encore limitée. Les utilisateurs peuvent choisir de télécharger d’abord afin de pouvoir regarder le contenu en résolution suffisamment élevée pour une expérience d’affichage satisfaisante. Dans ce cas, le problème ne concerne pas majoritairement la disponibilité du réseau, mais une bande passante réseau limitée. Les fournisseurs OTT/OVP doivent fournir une prise en charge du mode hors connexion.

Cet article couvre la prise en charge du mode hors connexion FairPlay Streaming (FPS) ciblant les appareils exécutant iOS 10 ou version ultérieure. Cette fonctionnalité n’est pas prise en charge pour d’autres plateformes Apple, telles que watchOS, tvOS ou Safari sur macOS.

## <a name="preliminary-steps"></a>Étapes préliminaires
Avant de mettre en œuvre DRM hors ligne pour FairPlay sur un appareil iOS 10+, vous devez, tout d’abord :
1. Vous familiariser avec la protection du contenu en ligne pour Fairplay.
 Vous trouverez toutes les informations nécessaires dans les articles/exemples suivants :
- [Apple FairPlay Streaming pour Azure Media Services généralement disponible](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [Protéger votre contenu HLS avec Apple FairPlay ou Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [Un exemple pour une diffusion en contenu FPS en ligne](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Obtenez le kit de développement logiciel (SDK) FPS de Apple Developer Network. Le kit de développement logiciel (SDK) contient deux composants :
- le kit de développement logiciel (SDK) du serveur FPS, qui contient le module de sécurité de clés (KMS), des exemples clients, une spécification et un ensemble de vecteurs de test ;
- le pack de déploiement FPS, qui contient la Fonction D, une spécification avec des instructions sur le mode de génération du certificat FPS, une clé privée spécifique au client et la clé secrète d’application (ASK). Apple publie un pack de déploiement FPS uniquement aux fournisseurs de contenu sous licence.

## <a name="configuration-in-azure-media-services"></a>Configuration dans Azure Media Services
Pour la configuration du mode hors connexion FPS via [Azure Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), vous devez utiliser Azure Media Services .NET SDK v. 4.0.0.4 ou version ultérieure, qui offre l’API nécessaire pour la configuration du mode hors connexion FPS.
Tel qu’indiqué dans les hypothèses ci-dessous, nous supposons que vous possédez le code de travail pour la configuration de la protection du contenu FPS en mode en ligne. Une fois que vous possédez le code de configuration de la protection de contenu en mode hors connexion pour FPS, seules les deux modifications suivantes sont nécessaires.

## <a name="code-change-in-fairplay-configuration"></a>Modification du code dans la configuration Fairplay
Définissons un booléen « activer le mode hors connexion », nommé objDRMSettings.EnableOfflineMode qui est vrai lors de l’activation du scénario DRM hors connexion. En fonction de cet indicateur, nous procédons la modification suivante de la configuration Fairplay :

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Modification du code dans la configuration de stratégie de remise des éléments multimédia
La deuxième modification consiste à ajouter la troisième clé dans le dictionnaire Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
Le troisième AssetDeliveryPolicyConfigurationKey doit être ajouté comme suit : 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Après cette étape, le Dictionary<AssetDeliveryPolicyConfigurationKey, string> dans la stratégie de remise des éléments multimédia FPS contient les trois entrées suivantes :
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl ou AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl en fonction des facteurs tels que le serveur de clé/KMS FPS utilisé et si nous souhaitons réutiliser la même stratégie de remise des éléments multimédia dans plusieurs éléments multimédia
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Désormais votre compte des services média est configuré pour fournir des licences Fairplay hors ligne.

## <a name="sample-ios-player"></a>Exemple de lecteur iOS
Notons avant tout, que la prise en charge en mode hors connexion FPS est disponible uniquement sur iOS 10 et version ultérieure. Nous devons utiliser le kit de développement logiciel (SDK) du serveur FPS (v3.0 ou ultérieure) qui contient un document ou un exemple pour le mode hors connexion FPS. En particulier, le kit de développement logiciel (SDK) du serveur FPS (v3.0 ou ultérieure) contient les deux éléments suivants associés au mode hors connexion :
1. Document : lecture hors connexion avec FairPlay Streaming et HTTP Live Streaming. Apple, 9/14/2016. Dans le kit de développement logiciel du serveur FPS v 4.0, ce document a été fusionné dans le document de diffusion en continu FPS.
2. Code en exemple : exemple HLSCatalog pour le mode hors connexion FPS dans \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Dans l’application en exemple HLSCatalog, les fichiers de code suivants visent particulièrement à mettre en œuvre des fonctionnalités en mode hors connexion :
- Fichier de code AssetPersistenceManager.swift : AssetPersistenceManager est la première classe de cet exemple qui démontre
    - Comment gérer le téléchargement des flux HLS, telles que les API pour démarrer et annuler le téléchargement, supprimer des éléments multimédia existants hors de l’appareil de l’utilisateur ;
    - Comment contrôler la progression du téléchargement.
- Fichiers de code AssetListTableViewController.swift et AssetListTableViewCell.swift : AssetListTableViewController est l’interface principale de cet exemple. Elle fournit une liste d’éléments multimédia que l’exemple peut lire, télécharger, supprimer ou annuler un téléchargement. 

Vous trouverez ci-dessous les étapes détaillées pour la configuration d’un lecteur exécutant iOS. Supposons que vous démarrez à partir d’un exemple HLSCatalog dans le kit de développement logiciel du serveur FPS v 4.0.1.  Nous devons procéder aux modifications de code suivantes :

Dans HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, mettez en œuvre la méthode `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` à l’aide du code suivant : faites de drmUr une variable affectée à l’URL de diffusion en continu HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Dans HLSCatalog\Shared\Managers\ContentKeyDelegate.swift,mettez en œuvre la méthode `requestApplicationCertificate()`. Cette mise en œuvre dépend de l’incorporation du certificat (clé publique uniquement) à l’appareil ou de l’hébergement du certificat sur le Web. Vous trouverez ci-dessous une mise en œuvre utilisant le certificat d’application hébergé utilisé dans nos exemples de tests. Faites de certUrl une variable contenant l’URL du certificat d’application.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Pour le test intégré final, l’URL de la vidéo et l’URL du certificat d’application sont fournies dans la section de « Test intégré ».

Dans HLSCatalog\Shared\Resources\Streams.plist, ajoutez l’URL de votre vidéo de test et, pour l’ID de la clé de contenu, utilisez uniquement l’URL d’acquisition de licences FairPlay avec le protocole skd comme valeur unique.

![Flux d’applications iOS FairPlay hors connexion](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Pour l’URL de la vidéo test, l’URL d’acquisition de licences et l’URL de certificat d’application Fairplay, vous pouvez utiliser vos propres exemples, si vous les avez configurés, ou vous pouvez passer à la section suivante qui contient les exemples de tests.

## <a name="integrated-test"></a>Test intégré
Trois exemples de tests ont été configurés dans Azure Media Services qui couvre les trois scénarios suivants :
1.  FPS protégé, avec vidéo, audio et autre piste audio ;
2.  FPS protégé, avec vidéo, audio mais pas d’autre piste audio ;
3.  FPS protégé avec vidéo seulement, pas d’audio.

Vous trouverez ces exemples sur ce [site de démo](http://aka.ms/poc#22), avec le certificat d’application correspondant hébergé dans une application Web Azure.
Nous avons remarqué que, avec l’exemple v3 ou v4 sur le kit de développement logiciel (SDK) du serveur FPS, si une liste de lectures principale contient une autre audio, en mode hors connexion, elle ne lit que l’audio. Il est par conséquent nécessaire de supprimer l’autre audio. En d’autres termes, parmi les trois exemples ci-dessus, (2) et (3) fonctionnent en ligne et en mode hors connexion. Mais (1) ne lit l’audio qu’en mode hors connexion tandis que la diffusion en continu en ligne fonctionne bien.

## <a name="faq"></a>Forum Aux Questions
Quelques questions fréquemment posées sur le dépannage :
- **Pourquoi seul l’audio mais pas la vidéo est lu en mode hors connexion ?** Ce comportement semble être conçu tel quel dans l’application en exemple. Lorsqu’une autre piste audio est présente (ce qui est le cas pour HLS), en mode hors connexion, iOS 10 et iOS 11 passent par défaut à l’autre piste audio. Pour compenser ce comportement en mode hors connexion, nous devons supprimer l’autre piste audio du flux. Pour ce faire du côté de Azure Media Services, nous pouvons simplement ajouter le filtre manifeste dynamique «  audio-only=false. » En d’autres termes, une URL HLS se termine par .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Pourquoi l’audio est toujours lu sans vidéo en mode hors connexion après l’ajout de audio-only=false ?** En fonction de la conception de la clé du cache CDN, le contenu peut être en cache. Vous devez vider le cache.
- **Le mode hors connexion FPS est-il également pris en charge sur iOS 11 comme sur iOS 10 ?** Oui, le mode hors connexion FPS est pris en charge sur iOS 10 et iOS 11.
- **Pourquoi ne puis-je pas trouver le document Lecture hors connexion avec FairPlay Streaming et HTTP Live Streaming dans le kit de développement logiciel (SDK) du serveur FPS ?** Depuis le kit de développement du serveur FPS v4, ce document a été fusionné dans le document Guide de programmation Fairplay Streaming.
- **À quoi correspond le dernier paramètre dans l’API suivante pour le mode hors connexion FPS ?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

Vous trouverez la documentation de cette API [ici](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Le paramètre représente la durée de location hors connexion avec l’heure pour unité.
- **Quelle est la structure du fichier téléchargé/hors connexion sur les appareils iOS ?** La structure du fichier téléchargé sur un appareil iOS ressemble à ce qui suit (capture d’écran). Le dossier `_keys` stocke les licences FPS téléchargés, un fichier de magasin pour chaque hôte de service de licence. Le dossier `.movpkg` stocke le contenu audio et vidéo. Le premier dossier avec le nom terminé par un tiret suivi par un numérique contient un contenu vidéo. La valeur numérique est la « PeakBandwidth » des rendus de vidéo. Le deuxième dossier avec le nom terminé par un tiret suivi de 0 contient un contenu audio. Le troisième dossier nommé « Données » contient la liste de lectures principale du contenu FPS. Boot.xml offre une description complète du contenu du dossier `.movpkg` (voir ci-dessous pour un fichier boot.xml exemple).

![Structure de fichier d’application exemple iOS Fairplay hors connexion](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Un fichier boot.xml en exemple :
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>Résumé
Dans ce document, nous avons fourni les étapes détaillées et les informations pour mettre en œuvre le mode hors connexion FPS, y compris :
1. Configuration de la protection du contenu Azure Media Services via l’API .NET AMS. Ceci permet de configurer le chiffrement Fairplay et la remise de licences Fairplay dynamiques dans AMS.
2. Lecteur iOS basé sur l’exemple du kit de développement logiciel (SDK) du serveur FPS Apple. Ceci doit configurer un lecteur iOS pouvant lire le contenu FPS en mode de diffusion en continu en ligne ou un mode hors connexion.
3. Exemple de vidéos FPS pour des tests en mode hors connexion et en diffusion en continu en ligne.
4. FAQ sur le mode hors connexion FPS.
