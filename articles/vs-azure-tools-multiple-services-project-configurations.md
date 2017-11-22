---
title: "Configuration de votre projet Azure à l'aide de plusieurs configurations de service | Microsoft Docs"
description: "Découvrez comment configurer un projet de service cloud Azure en modifiant les fichiers ServiceDefinition.csdef et ServiceConfiguration.cscfg."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 59492ccc64a70680d71ad1de0700ed30f9e45306
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configuration de votre projet Azure à l'aide de plusieurs configurations de service
Un projet de service cloud Azure comprend deux fichiers de configuration : ServiceDefinition.csdef et ServiceConfiguration.cscfg. Ces fichiers sont fournis avec votre application de service cloud Azure et déployés dans Azure.

* Le fichier **ServiceDefinition.csdef** contient les métadonnées requises par l'environnement Azure selon les besoins de votre application de service cloud, notamment les rôles qu'elle contient. Ce fichier contient également les paramètres de configuration qui s'appliquent à toutes les instances. Ces paramètres de configuration peuvent être lus à l'exécution à l'aide de l'API Azure Service Hosting Runtime. Ce fichier ne peut pas être mis à jour lorsque votre service est en cours d’exécution dans Azure.
* Le fichier **ServiceConfiguration.cscfg** définit des valeurs des paramètres de configuration définis dans le fichier de définition de service et spécifie le nombre d'instances à exécuter pour chaque rôle. Ce fichier peut être mis à jour lorsque votre service cloud est en cours d’exécution dans Azure.

Les outils Azure pour Microsoft Visual Studio fournissent des pages de propriétés que vous pouvez utiliser pour définir les paramètres de configuration stockés dans ces fichiers. Pour accéder aux pages de propriétés, double-cliquez sur la référence de rôle sous le projet de service cloud Azure dans l'Explorateur de solutions, ou cliquez avec le bouton droit sur la référence de rôle et sélectionnez **Propriétés**, comme l’illustre la figure suivante.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Pour en savoir plus sur les schémas sous-jacents des fichiers de définition et de configuration de service, consultez les articles relatifs au [schéma XML .csdef](cloud-services/schema-csdef-file.md) et au [schéma XML .cscfg](cloud-services/schema-cscfg-file.md). Pour plus d'informations sur la configuration de service, consultez [Configuration de Cloud Services](cloud-services/cloud-services-how-to-configure-portal.md).

## <a name="configuring-role-properties"></a>Configuration des propriétés de rôle
Les pages de propriétés d’un rôle web et d’un rôle de travail sont similaires, bien qu'il existe quelques différences, décrites dans les sections suivantes.

Dans la page **Mise en cache** , vous pouvez configurer les services de mise en cache Azure.

### <a name="configuration-page"></a>Page de configuration
Sur la page **Configuration** , vous pouvez définir ces propriétés :

**rôle web**

Définir la propriété count de l’ **Instance** au nombre d'instances que le service doit exécuter pour ce rôle.

Définir la propriété **Taille de la machine virtuelle** sur **Très petite**, **Petite**, **Moyenne**, **Grande** ou **Très grande**.  Pour en savoir plus, consultez la rubrique [Tailles de services cloud](cloud-services/cloud-services-sizes-specs.md).

**Action de démarrage** (rôle web uniquement)

Définissez cette propriété pour spécifier que Visual Studio doit lancer un navigateur web pour les points de terminaison HTTP, les points de terminaison HTTPS, ou les deux lorsque vous commencez le débogage.

L'option point de terminaison HTTPS est disponible uniquement si vous avez déjà défini un point de terminaison HTTPS pour votre rôle. Vous pouvez définir un point de terminaison HTTPS sur la page Propriétés des **Points de terminaison** .

Si vous avez déjà ajouté un point de terminaison HTTPS, l'option point de terminaison HTTPS est activée par défaut et Visual Studio lance un navigateur pour ce point de terminaison lorsque vous démarrez le débogage, ainsi qu'un navigateur pour votre point de terminaison HTTP. Cela suppose que les deux options de démarrage soient activées.

**Diagnostics**

Par défaut, le diagnostic est activé pour le rôle web. Le projet de service cloud Azure et le compte de stockage sont configurés pour utiliser l'émulateur de stockage local. Lorsque vous êtes prêt à déployer sur Azure, vous pouvez sélectionner le bouton de génération (**...**) pour que le compte de stockage utilise Azure Storage dans le cloud. Vous pouvez transférer les données de diagnostic au compte de stockage à la demande ou à intervalles planifiés automatiquement. Pour plus d'informations sur les diagnostics Azure, consultez la page [Activation de Diagnostics dans Azure Cloud Services et Azure Virtual Machines](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Page Paramètres
Sur la page **Paramètres** , vous pouvez ajouter des paramètres de configuration à votre service. Les paramètres de configuration sont des paires nom-valeur. Le code exécuté dans le rôle peut lire les valeurs de vos paramètres de configuration à l'exécution à l'aide des classes fournies par la [bibliothèque managée Azure](http://go.microsoft.com/fwlink?LinkID=171026). Plus précisément, la méthode [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) renvoie la valeur d'un paramètre de configuration nommé à l'exécution.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configuration d’une chaîne de connexion à un compte de stockage
Une chaîne de connexion est un paramètre de configuration qui fournit des informations de connexion et d'authentification à l'émulateur de stockage ou à un compte de stockage Azure. Chaque fois que votre code doit accéder aux données de services de stockage Azure (données Blob, File d'attente ou Table) à partir du code en cours d’exécution dans un rôle, vous devez définir une chaîne de connexion pour ce compte de stockage.

Une chaîne de connexion qui pointe vers un compte de stockage Azure doit utiliser un format défini. Pour plus d’informations sur la création de chaînes de connexion, consultez [Configuration des chaînes de connexion Stockage Azure](storage/common/storage-configure-connection-string.md).

Lorsque vous êtes prêt à tester votre service avec les services de stockage Azure, ou lorsque vous êtes prêt à déployer votre service cloud dans Azure, vous pouvez modifier la valeur des chaînes de connexion pour qu’elles pointent vers votre compte de stockage Azure. Sélectionnez (**…**) et **Entrer les informations d’identification du compte de stockage**. Entrez vos informations de compte, notamment le nom et la clé de votre compte. Dans la boîte de dialogue **Chaîne de connexion au compte de stockage** , vous pouvez également indiquer si vous souhaitez utiliser des points de terminaison HTTPS par défaut (option par défaut), des points de terminaison HTTP par défaut ou des points de terminaison personnalisés. Vous pouvez décider d’utiliser des points de terminaison personnalisés si vous avez inscrit un nom de domaine personnalisé pour votre service, comme décrit dans [Configuration d’un nom de domaine personnalisé pour des données d’objets blob dans un compte de stockage Azure](storage/blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Vous devez modifier vos chaînes de connexion pour qu’elles pointent vers un compte de stockage Azure avant de déployer votre service. Dans le cas contraire, le rôle pourrait ne pas démarrer, ou alterner entre les états initialisation, occupé et arrêt.
> 
> 

## <a name="endpoints-page"></a>Page des points de terminaison
Un rôle de travail peut avoir un nombre quelconque de points de terminaison HTTP, HTTPS ou TCP. Les points de terminaison peuvent être des points de terminaison d'entrée, accessibles aux clients externes, ou des points de terminaison internes, accessibles aux autres rôles qui s'exécutent dans le service.

* Pour rendre un point de terminaison HTTP accessible aux clients externes et aux navigateurs web, spécifiez « Entrée » comme type de point de terminaison et spécifiez un nom et un numéro de port public.
* Pour rendre un point de terminaison HTTPS accessible aux clients externes et aux navigateurs web, spécifiez **Entrée**comme type de point de terminaison et spécifiez un nom, un numéro de port public et un nom de certificat de gestion.
  
    Pour pouvoir spécifier un certificat de gestion, vous devez définir le certificat sur la page de propriétés des **certificats** .
* Pour rendre un point de terminaison accessible en interne par les autres rôles du service cloud, spécifiez « Interne » comme type de point de terminaison et spécifiez un nom et des ports privés potentiels pour ce point de terminaison.

## <a name="local-storage-page"></a>Page de stockage local
Vous pouvez utiliser la page de propriétés du **Stockage local** pour réserver une ou plusieurs ressources de stockage local pour un rôle. Une ressource de stockage local est un répertoire réservé dans le système de fichiers de la machine virtuelle Azure dans lequel s’exécute l’instance d’un rôle.

## <a name="certificates-page"></a>Page Certificats
Dans la page **Certificats** , vous pouvez associer des certificats à votre rôle. Les certificats que vous ajoutez peuvent être utilisés pour configurer vos points de terminaison HTTPS dans la page de propriétés **Points de terminaison** .

La page de propriétés **Certificats** ajoute des informations sur vos certificats à votre configuration de service. Notez que vos certificats ne sont pas empaquetés avec votre service. Vous devez les charger séparément sur Azure, via le [portail Azure](http://portal.azure.com).

Pour associer un certificat à votre rôle, donnez un nom au certificat. Ce nom permet de faire référence au certificat lorsque vous configurez un point de terminaison HTTPS dans la page de propriétés **Points de terminaison** . Ensuite, spécifiez si le magasin de certificats est **Machine locale** ou **Utilisateur actuel** et le nom du magasin. Enfin, entrez l'empreinte du certificat. Si le certificat est dans Utilisateur actuel\Mon magasin, vous pouvez entrer l'empreinte du certificat en sélectionnant le certificat à partir d'une liste. S'il se trouve à un autre emplacement, entrez la valeur de l'empreinte numérique à la main.

Lorsque vous ajoutez un certificat à partir du magasin de certificats, les éventuels certificats intermédiaires sont automatiquement ajoutés aux paramètres de configuration. Ces certificats intermédiaires doivent également être chargés sur Azure afin de configurer correctement votre service pour SSL.

Les certificats de gestion que vous associez à votre service ne s'appliquent à votre service que lorsqu'il s'exécute dans le cloud. Lorsque votre service s'exécute dans l'environnement de développement local, il utilise un certificat standard géré par l'émulateur de calcul.

## <a name="configuring-the-azure-cloud-service-project"></a>Configuration du projet de service cloud Azure
Pour configurer les paramètres qui s'appliquent à un projet de service cloud Azure tout entier, ouvrez le menu contextuel de ce nœud de projet, puis sélectionnez Propriétés pour ouvrir ses pages de propriétés. Le tableau suivant montre ces pages de propriétés.

| Page Propriété | Description |
| --- | --- |
| Application |Sur cette page, vous pouvez afficher des informations sur la version des outils Azure que ce projet de service cloud utilise et vous pouvez passer à la version actuelle des outils. |
| Événements de build |Sur cette page, vous pouvez définir des événements pré-build et post-build. |
| Développement |Sur cette page, vous pouvez spécifier des instructions de configuration de build et les conditions dans lesquelles les événements post-build s’exécutent. |
| Web |Sur cette page, vous pouvez configurer les paramètres relatifs au serveur web. |

