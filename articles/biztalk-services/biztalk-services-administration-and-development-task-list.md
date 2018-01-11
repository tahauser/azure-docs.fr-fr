---
title: "Liste des tâches d’administration et de développement dans BizTalk Services | Microsoft Docs"
description: "Documentation de travail et de planification pour le déploiement d'Azure BizTalk Services."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 9edd7261ca62f505ffb4854e3132fae916768f67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Liste des tâches d'administration et de développement dans BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Mise en route
Lorsque vous utilisez Microsoft Azure BizTalk Services, il existe plusieurs composants locaux et cloud à prendre en compte. Pour commencer, prenez en compte le flux de processus suivant :  

| Étape | Qui est responsable | Task | Liens connexes |
| --- | --- | --- | --- |
| 1. |Administrateur |Créer l’abonnement Microsoft Azure à l’aide d’un compte Microsoft ou d’un compte professionnel |[Portail Azure](https://portal.azure.com) |
| 2. |Administrateur |Créer ou approvisionner un BizTalk Service. |[Créer un service BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Administrateur |Enregistrer votre déploiement ou le déploiement de votre entreprise de BizTalk Services |[Enregistrement et mise à niveau d'un déploiement de service BizTalk sur le portail BizTalk Services](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administrateur |S’applique si l’application utilise BizTalk Adapter Service pour se connecter à un système métier (LOB) local ou utilise une file d’attente ou une destination de rubrique.  Créer l'espace de noms Azure Service Bus Donner les valeurs espace de noms, nom de l'émetteur Service Bus et clé de l'émetteur Service Bus au développeur. |[Procédure : Créer ou modifier un espace de noms du service Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) et [Obtenir les valeurs nom et clé de l’émetteur](biztalk-issuer-name-issuer-key.md) |
| 5. |Développeur |Installer le Kit de développement logiciel (SDK) et créer le projet BizTalk Service dans Visual Studio. |[Installer le Kit de développement logiciel (SDK) Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689760.aspx) et [Créer des points de terminaison de messagerie enrichis sur Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Développeur |Déployer votre projet BizTalk Service sur votre BizTalk Service hébergé sur Azure. |[Déploiement et actualisation du projet BizTalk Services](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administrateur |S'applique si vous utilisez EDI.  Vous pouvez ajouter des Partenaires et créer des Accords sur le portail Microsoft Azure BizTalk Services. Lorsque vous créez un accord, vous pouvez ajouter le pont et/ou les transformations créés par le développeur dans les paramètres de l'accord. |[Configuration d'EDI, AS2 et EDIFACT sur le portail BizTalk Services](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administrateur |Avec [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), surveiller l’intégrité de votre service BizTalk, y compris les métriques de performances. |[Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrateur |À l'aide du portail Microsoft Azure BizTalk Services, gérer les artefacts utilisés par les Services BizTalk et suivre les messages lorsqu’ils sont traités par les fichiers de pont. |[Utilisation du portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administrateur |Créer un plan de sauvegarde pour sauvegarder BizTalk Service. |[Continuité des activités et récupération d'urgence dans BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Étapes suivantes
[Didacticiels et exemples](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Créer le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installer le Kit de développement logiciel (SDK) Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Concepts
[Créer le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[Messagerie EDI, AS2 et EDIFACT (entreprise-entreprise)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Autres ressources
[Ajouter des points de terminaison de messagerie source, destination et pont](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Découvrir et créer des tables et des transformations de messages](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Utilisation de BizTalk Adapter Service (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)

