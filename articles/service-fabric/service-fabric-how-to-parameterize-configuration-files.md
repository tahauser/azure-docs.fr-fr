---
title: "Guide pratique pour paramétrer les fichiers de configuration dans Azure Service Fabric | Microsoft Docs"
description: "Vous montre comment paramétrer les fichiers de configuration dans Service Fabric"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 1e7d59ecb231440711b8ed3dc0b27a2b105890c4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Guide pratique pour paramétrer les fichiers de configuration dans Service Fabric

Cet article vous explique comment paramétrer un fichier de configuration dans Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Procédure de paramétrage des fichiers de configuration

Dans cet exemple, vous remplacez une valeur de configuration à l’aide de paramètres dans le déploiement de votre application.

1. Ouvrez le fichier Config\Settings.xml.
1. Définissez un paramètre de configuration, en ajoutant le code XML suivant :

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Enregistrez et fermez le fichier.
1. Ouvrez le fichier `ApplicationManifest.xml` .
1. Ajoutez un élément `ConfigOverride`, qui référence le package de configuration, la section et le paramètre.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Toujours dans le fichier ApplicationManifest.xml, spécifiez ensuite le paramètre dans l’élément `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. Puis définissez un élément `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> Si vous ajoutez un élément ConfigOverride, Service Fabric choisit toujours les paramètres d’application ou la valeur par défaut spécifiée dans le manifeste d’application.
>
>

Lors de la publication de votre application depuis Visual Studio à l’aide du profil de publication Cloud.xml, votre service est configuré pour utiliser le port 80. Si vous déployez l’application sans spécifier le paramètre MyWebAPI_PortNumber, le service utilise le port 8080.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur certains des principaux concepts abordés dans cet article, consultez les articles [Gérer des applications pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).