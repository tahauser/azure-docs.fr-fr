---
title: "Vue d’ensemble d’Azure Media Services | Microsoft Docs"
description: Cette rubrique offre une vue d'ensemble d'Azure Media Services
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2017
ms.author: juliako;anilmur
ms.openlocfilehash: a1e56a6d31568d1d36e713b9a1000d84c6446bac
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="azure-media-services-overview"></a>Vue d’ensemble d’Azure Media Services 

Microsoft Azure Media Services est une plateforme extensible basée sur le cloud qui permet aux développeurs de créer des applications évolutives de gestion et de diffusion de médias. Media Services est basé sur les API REST qui permettent de télécharger, stocker, encoder et empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

Vous pouvez créer des flux de travail de bout en bout en utilisant uniquement Media Services. Vous pouvez aussi choisir d'utiliser des composants tiers pour certaines parties de votre flux de travail (par exemple, en encodant avec un encodeur tiers). Le contenu est ensuite téléchargé, protégé, empaqueté et remis à l'aide de Media Services.

Vous pouvez choisir de diffuser votre contenu en direct ou de le distribuer à la demande. La rubrique contient également des liens vers d’autres rubriques pertinentes.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

* [Workflow en flux continu AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Workflow de streaming à la demande AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Composants requis

Pour commencer à utiliser Azure Media Services, vous devez disposer des éléments suivants :

* Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com).
* Un compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte](media-services-portal-create-account.md).
* (Facultatif) Un environnement de développement configuré. Choisissez .NET ou API REST comme environnement de développement. Pour plus d’informations, consultez [Configuration d'environnement](media-services-dotnet-how-to-use.md).

    De plus, découvrez comment [vous connecter par programmation à l’API AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Un point de terminaison de streaming standard ou premium à l’état Démarré.  Pour plus d’informations, consultez [Gestion des points de terminaison de streaming](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>Kits de développement logiciel (SDK) et outils

Pour créer des solutions Media Services, vous pouvez utiliser les composants suivants :

* [API REST Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Un des Kits de développement logiciel (SDK) de client disponibles :
    * [Kit de développement logiciel (SDK) Azure Media Services pour .NET](https://github.com/Azure/azure-sdk-for-media-services),
    * [Kit de développement logiciel (SDK) Azure pour Java](https://github.com/Azure/azure-sdk-for-java),
    * [Kit de développement logiciel (SDK) Azure pour PHP](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services pour Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (il s’agit d’une version non Microsoft du Kit de développement logiciel (SDK) Node.js. Il est géré par une communauté et ne fournit pas une couverture à 100 % des API AMS).
* Outils existants :
    * [portail Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) est une application Winforms/C# pour Windows)

## <a name="code-samples"></a>Exemples de code

Vous trouverez plusieurs exemples de code dans la galerie **Azure Code Samples** : [exemples de code Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Concepts

Pour les concepts Azure Media Services, consultez [Concepts](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scénarios pris en charge et disponibilité de Media Services dans les centres de données

Pour plus d’informations, consultez [Scénarios AMS et disponibilité des fonctionnalités et services dans les centres de données](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contrat de Niveau de Service (SLA)

* Pour Media Services, nous garantissons une disponibilité de 99,9 % des transactions d'API REST.
* Pour la diffusion en continu, nous traiterons avec succès les demandes de service avec une garantie de disponibilité de 99,9 % pour le contenu multimédia existant si un point de terminaison de streaming standard ou premium est acheté.
* Pour les Canaux en Direct, nous garantissons que les canaux en cours d’exécution auront une connectivité externe au moins 99,9 % du temps.
* Pour la Protection de Contenu, nous garantissons que nous serons en mesure de traiter les demandes de clé au moins 99,9 % du temps.
* Pour l’Indexeur, nous serons en mesure d’assurer les demandes de tâche d’indexation traitées avec une unité réservée d’encodage 99,9 % du temps.

Pour plus d’informations, consultez le [contrat SLA Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Pour plus d’informations sur la disponibilité dans les centres de données, consultez la section [Disponibilité](scenarios-and-availability.md#availability).

## <a name="support"></a>Support

[support Azure](https://azure.microsoft.com/support/options/) propose des options de support pour Azure, y compris Media Services.

## <a name="provide-feedback"></a>Fournir des commentaires

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
