---
title: "Configuration de l’ordinateur pour le développement Media Services avec .NET"
description: "Familiarisez-vous avec les conditions préalables pour Media Services à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET. Apprenez également à créer une application Visual Studio."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: juliako
ms.openlocfilehash: f5dd263a2e925989069c3b0257cfafa4c43e6157
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="media-services-development-with-net"></a>Développement Media Services avec .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Cette rubrique explique comment commencer à développer des applications Media Services à l’aide de .NET.

La bibliothèque du **Kit de développement logiciel (SDK) Azure Media Services pour .NET** permet de programmer pour Media Services à l'aide de .NET. Pour que le développement avec .NET soit encore plus simple, la bibliothèque des **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET** est fournie. Cette bibliothèque contient un ensemble de méthodes d’extension et de fonctions d’assistance qui simplifient votre code .NET. Les deux bibliothèques sont disponibles par le biais de **NuGet** et de **GitHub**.

## <a name="prerequisites"></a>Conditions préalables
* Un compte Media Services dans un abonnement Azure nouveau ou existant. Consultez la rubrique [Comment créer un compte Media Services](media-services-portal-create-account.md).
* Systèmes d’exploitation : Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Framework 4.5.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio
Cette section vous montre comment créer un projet dans Visual Studio et le configurer pour le développement Media Services.  Dans ce cas, le projet est une application console C# Windows, mais les étapes de configuration présentées ici s’appliquent aux autres types de projets que vous pouvez créer pour les applications Media Services (par exemple, une application Windows Forms ou Web ASP.NET).

Cette section montre comment utiliser **NuGet** pour ajouter des extensions de Kit de développement logiciel (SDK) Media Services pour .NET et d’autres bibliothèques dépendantes.

Vous pouvez également obtenir les dernières informations relatives au Kit de développement logiciel (SDK) Media Services pour .NET à partir de GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), générer la solution et ajouter les références au projet client. Toutes les dépendances nécessaires sont téléchargées et extraites automatiquement.

1. Créez une application console C# dans Visual Studio. Entrez le **nom**, l’**emplacement** et le **nom de solution**, puis cliquez sur OK.
2. Générez la solution.
3. Utilisez **NuGet** pour installer et ajouter les **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET** (**windowsazure.mediaservices.extensions**). L'installation de ce package installe également le **Kit de développement logiciel (SDK) Media Services pour .NET** et ajoute toutes les autres dépendances requises.
   
    Assurez-vous que la version la plus récente de NuGet est installée. Pour obtenir des informations supplémentaires et des instructions relatives à l'installation, consultez la page [NuGet](http://nuget.codeplex.com/).

    1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Gérer les packages NuGet**.

    2. La boîte de dialogue Gérer les packages NuGet apparaît.

    3. Dans la galerie en ligne, recherchez Extensions Azure MediaServices, choisissez **Azure Media Services .NET SDK Extensions** (**windowsazure.mediaservices.extensions**), puis cliquez sur le bouton **Installer**.
   
    4. Le projet est modifié et des références aux extensions du SDK Media Services pour .NET, au SDK Media Services pour .NET et à d’autres assemblys dépendants sont ajoutées.
4. Pour promouvoir un environnement de développement plus propre, envisagez d’activer la restauration du package NuGet. Pour plus d'informations, consultez le document [Restauration du package NuGet](http://docs.nuget.org/consume/package-restore).
5. Ajoutez une référence à l'assembly **System.Configuration** . Cet assembly contient la classe System.Configuration.**ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration (par exemple, App.config).
   
    1. Pour ajouter des références à l’aide de la boîte de dialogue Gérer les références, cliquez avec le bouton droit sur le nom du projet dans l’Explorateur de solutions. Ensuite, cliquez sur **Ajouter** puis sur **Référence**.
   
    2. La boîte de dialogue Gérer les références s’affiche.
    3. Sous Assemblys .NET Framework, recherchez et sélectionnez l’assembly System.Configuration et appuyez sur **OK**.
6. Ouvrez le fichier App.config et ajoutez-y une section **appSettings**.     
   
    Définissez les valeurs nécessaires pour se connecter à l’API Media Services. Pour plus d’informations, consultez l’article [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Si vous utilisez [l’authentification utilisateur](media-services-use-aad-auth-to-access-ams-api.md#types-of-authentication), votre fichier de configuration contiendra probablement des valeurs relatives au domaine de votre client Azure AD et au point de terminaison de l’API REST AMS.
    
    >[!Note]
    >La plupart des exemples de code de la documentation Azure Media Services utilisent une authentification de type utilisateur (interactif) pour se connecter à l’API AMS. Cette méthode d’authentification fonctionne bien avec les applications natives de gestion ou de surveillance : applications mobiles, applications Windows et applications de console.
    
    >[!Important]
    > La méthode d’authentification **interactive** ne convient PAS aux applications de type serveur, services web et API. Pour ces types d’applications, utilisez la méthode d’authentification **Principal de service**. Pour plus d’informations, consultez l’article [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

        <configuration>
        ...
            <appSettings>
              <add key="AADTenantDomain" value="YourAADTenantDomain" />
              <add key="MediaServiceRESTAPIEndpoint" value="YourRESTAPIEndpoint" />
            </appSettings>

        </configuration>

7. Remplacez les instructions **using** existantes au début du fichier Program.cs par le code suivant.
           
        using System;
        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Collections.Generic;
        using System.Linq;

À ce stade, vous êtes prêt à commencer le développement d’une application Media Services.    

## <a name="example"></a>Exemple

Ce court exemple permet de se connecter à l’API AMS et de répertorier tous les processeurs multimédias disponibles.
    
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];
    
        private static CloudMediaContext _context = null;
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    
            // List all available Media Processors
            foreach (var mp in _context.MediaProcessors)
            {
                Console.WriteLine(mp.Name);
            }
    
        }

##<a name="next-steps"></a>Étapes suivantes

[Vous pouvez désormais vous connecter à l’API AMS](media-services-use-aad-auth-to-access-ams-api.md) et commencer à [développer](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

