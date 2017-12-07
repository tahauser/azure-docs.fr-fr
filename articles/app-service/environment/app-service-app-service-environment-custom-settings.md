---
title: "Paramètres personnalisés pour les environnements App Service"
description: "Paramètres de configuration personnalisés pour les environnements App Service"
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Paramètres de configuration personnalisés pour les environnements App Service
## <a name="overview"></a>Vue d'ensemble
Les environnements App Service étant isolés pour chaque client, certains paramètres de configuration peuvent être appliqués exclusivement à des environnements App Service. Cet article décrit les différentes personnalisations pour les environnements App Service disponibles.

Si vous ne possédez pas d’environnement App Service, voir [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Vous pouvez stocker les personnalisations de l’environnement App Service (App Service Environment) à l’aide d’un tableau dans le nouvel attribut **clusterSettings** . Cet attribut se trouve dans le dictionnaire des « Propriétés » de l’entité Azure Resource Manager *hostingEnvironments* .

L’extrait de code abrégé de modèle Resource Manager suivant indique l’attribut **clusterSettings** :

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L’attribut **clusterSettings** peut être inclus dans un modèle Resource Manager pour mettre à jour l’environnement App Service.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilisation de l’Explorateur de ressources Azure pour mettre à jour un environnement App Service
Vous pouvez également mettre à jour l’environnement App Service à l’aide de [l’Explorateur de ressources Azure](https://resources.azure.com).  

1. Dans l’Explorateur de ressources, accédez au nœud de l’environnement App Service (**abonnements** > **resourceGroups** > **fournisseurs** > **Microsoft.Web** > **hostingEnvironments**). Cliquez ensuite sur l’environnement App Service spécifique que vous souhaitez mettre à jour.
2. Dans le volet de droite, cliquez sur **Lecture/écriture** dans la barre d’outils du haut pour autoriser la modification interactive dans l’Explorateur de ressources.  
3. Cliquez sur le bouton bleu **Modifier** pour permettre la modification du modèle Resource Manager.
4. Faites défiler jusqu'au bas du panneau de droite. L’attribut **clusterSettings** , dont vous pouvez entrer ou mettre à jour la valeur, se trouve tout en bas.
5. Entrez (ou copiez et collez) le tableau de valeurs de configuration souhaité dans l’attribut **clusterSettings** .  
6. Cliquez sur le bouton vert **PUT** situé en haut du panneau de droite pour valider la modification apportée à l’environnement App Service.

Quelle que soit la manière dont vous soumettez les modifications, environ 30 minutes (multipliées par le nombre de serveurs frontaux présents dans l’environnement App Service) pour qu’elles prennent effet.
Par exemple, si un environnement App Service a quatre serveurs frontaux, la mise à jour de la configuration prendra environ deux heures. Tant que la modification de la configuration n’est pas déployée, aucune autre opération de mise à l’échelle ou de modification de configuration n’est possible dans l’environnement App Service.

## <a name="disable-tls-10"></a>Désactivation de TLS 1.0
Il est fréquent que des clients, en particulier ceux faisant l’objet d’audits de conformité PCI, demandent à ce qu’il soit possible de désactiver explicitement TLS 1.0 pour leurs applications.

TLS 1.0 peut être désactivé par le biais de l’entrée **clusterSettings** suivante :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Modifier l’ordre des suites de chiffrement TLS
Les clients demandent également s’ils peuvent modifier la liste des chiffrements négociés par leur serveur. Ils peuvent le faire en modifiant l’attribut **clusterSettings** comme indiqué ci-dessous. Vous trouverez la liste des suites de chiffrement disponibles dans [cet article MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Si des valeurs incorrectes sont définies pour la suite de chiffrement et incompréhensibles pour SChannel, l’ensemble de la communication TLS avec votre serveur peut cesser de fonctionner. Dans ce cas, vous devrez supprimer l’entrée *FrontEndSSLCipherSuiteOrder* des **clusterSettings** et envoyer le modèle Resource Manager mis à jour pour rétablir les paramètres de suite de chiffrement par défaut.  Utilisez cette fonctionnalité avec précaution.
> 
> 

## <a name="get-started"></a>Prise en main
Le site de modèles Azure Quickstart Resource Manager comprend un modèle dont la définition de base permet de [créer un environnement App Service](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
