---
title: API et outils de Azure Batch | Microsoft Docs
description: "Découvrez les API et les outils disponibles pour développer des solutions avec le service Azure Batch."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: get-started-article
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: 0d3720d33de2237b0d8cce2ae56bcc7ccf6acc43
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-batch-apis-and-tools"></a>Vue d’ensemble des outils et API Batch

Le traitement des charges de travail parallèles avec Azure Batch est généralement effectué par programme en utilisant l’une des [API Batch](#batch-development-apis). Votre application cliente ou le service peut utiliser les API Batch pour communiquer avec le service Batch. Avec les API Batch, vous pouvez créer et gérer les pools de nœuds de calcul, des machines virtuelles ou services de cloud. Vous pouvez ensuite planifier des tâches à exécuter sur ces nœuds. 

Vous pouvez efficacement traiter des charges de travail à grande échelle pour votre organisation, ou fournir un service frontal à vos clients afin qu’ils puissent exécuter des travaux et des tâches, à la demande ou selon un calendrier, sur un nœud, des centaines de nœuds, voire des milliers. Vous pouvez également utiliser Azure Batch dans le cadre d’un plus grand workflow géré par des outils comme [Azure Data Factory](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Si vous souhaitez mieux comprendre les fonctionnalités offertes par l’API Batch, consultez la page [Vue d’ensemble des fonctionnalités d’Azure Batch](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Comptes Azure pour le développement de Batch
Lorsque vous développez des solutions Batch, vous devez utiliser les comptes suivants dans Microsoft Azure.

* **Compte et abonnement Azure** : si vous ne possédez pas encore d’abonnement Azure, vous pouvez activer les [avantages de votre abonnement Visual Studio][msdn_benefits] ou vous inscrire pour obtenir un [compte Azure gratuit][free_account]. Lorsque vous créez un compte, un abonnement par défaut est automatiquement créé.
* **Compte Batch** : les ressources Azure Batch, notamment les pools, les nœuds de calcul, les travaux et les tâches, sont associées à un compte Azure Batch. Lorsque votre application effectue une requête auprès du service Batch, il authentifie la requête en utilisant le nom du compte Azure Batch, l’URL du compte et une clé d’accès ou bien un jeton de Azure Active Directory. Vous pouvez [créer un compte Batch](batch-account-create-portal.md) dans le Portail Azure ou par programme.
* **Compte de stockage** : Batch inclut la prise en charge intégrée de l’utilisation des fichiers dans [Azure Storage][azure_storage]. Presque tous les scénarios du service Batch font appel au stockage Blob Azure pour la préparation des programmes exécutés par vos tâches et les données qu’ils traitent ainsi que pour le stockage des données de sortie qu’elles génèrent. Pour créer un compte de stockage, consultez l’article [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).

## <a name="batch-service-apis"></a>API du service Batch

Vos applications et services peuvent émettre des appels directs de l’API REST ou utiliser une ou plusieurs des bibliothèques clientes suivantes pour exécuter et gérer vos charges de travail Azure Batch.

| API | Informations de référence sur l'API | Download | Didacticiel | Exemples de code | En savoir plus |
| --- | --- | --- | --- | --- | --- |
| **REST Batch** |[docs.microsoft.com][batch_rest] |N/A |- |- | [Versions prises en charge](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Didacticiel](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Notes de publication](http://aka.ms/batch-net-dataplane-changelog) |
| **Python Batch** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Didacticiel](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Lisez-moi](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Didacticiel](batch-nodejs-get-started.md) |- | [Lisez-moi](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Java Batch** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Lisez-moi][api_sample_java] | [Lisez-moi](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API de Batch Management

Les API Azure Resource Manager pour le service Batch fournissent un accès par programme aux comptes Batch. À l’aide de ces API, vous pouvez gérer par programme les comptes Batch, les quotas, les packages d’application et d’autres ressources via le fournisseur Microsoft.Batch.  

| API | Informations de référence sur l'API | Download | Didacticiel | Exemples de code |
| --- | --- | --- | --- | --- |
| **REST Batch Management** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Gestion de lots .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Didacticiel](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Python Batch Management** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Node.js Batch Management** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Java Batch Management** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Outils en ligne de commande Batch

Ces outils en ligne de commande fournissent les mêmes fonctionnalités que les API des services Batch et Batch Management : 

* [Applets de commande PowerShell pour Batch][batch_ps] : les applets de commande Azure Batch contenues dans le module [Azure PowerShell](/powershell/azure/overview) vous permettent de gérer les ressources du service Batch avec PowerShell.
* [Azure CLI 2.0](/cli/azure) : l’interface de ligne de commande Azure est un ensemble d’outils multiplateforme qui fournit des commandes d’environnement permettant d’interagir avec de nombreux services Azure, y compris les services Batch et Batch Management. Consultez la page [Gérer les ressources Batch avec Azure CLI](batch-cli-get-started.md) pour plus d’informations sur l’utilisation d’Azure CLI avec Batch.

## <a name="other-tools-for-application-development"></a>Autres outils pour le développement d’applications

Voici quelques outils supplémentaires qui peuvent être utiles pour générer et déboguer vos applications et services Batch :

* [Portail Azure][portal] : vous pouvez créer, surveiller et supprimer des pools Batch, des travaux et des tâches dans le portail Azure. Vous pouvez consulter les informations d’état de ces éléments et d’autres ressources pendant que vous exécutez vos travaux, et même télécharger des fichiers depuis les nœuds de calcul de vos pools. Par exemple, vous pouvez télécharger une tâche ayant échoué `stderr.txt` lors de la résolution des problèmes. Vous pouvez également télécharger des fichiers du Bureau à distance (RDP), à utiliser pour vous connecter aux nœuds de calcul.
* [Azure BatchLabs][batch_labs]: BatchLabs est un outil client autonome, gratuit et doté de nombreuses fonctionnalités, aidant à créer, déboguer et analyser les applications de Azure Batch. Téléchargez un [package d’installation](https://azure.github.io/BatchLabs/) pour Mac, Linux ou Windows.
* [Microsoft Azure Storage Explorer][storage_explorer] : bien qu’il ne s’agisse pas à proprement parler d’un outil Azure Batch, l’Explorateur de stockage est l’un de vos meilleurs alliés pendant le développement et le débogage de vos solutions Batch.

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour en savoir plus sur la journalisation des événements à partir de votre application Batch, consultez [Consigner des événements pour l’analyse et l’évaluation de diagnostic des solutions Batch](batch-diagnostics.md). Pour en savoir plus sur les événements déclenchés par le service Batch, voir [Batch Analytics](batch-analytics.md).
- Pour plus d’informations sur les variables d’environnement des nœuds de calcul, consultez [Variables d’environnement de nœud de calcul Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). Elle contient des informations indispensables pour toute personne se préparant à l’utilisation de Batch. L’article contient des informations plus détaillées sur les ressources du service Batch telles que les pools, les nœuds, les travaux et les tâches, ainsi que sur les nombreuses fonctionnalités API que vous pouvez utiliser lors de la création de votre application Batch.
* [Prise en main de la bibliothèque Azure Batch pour .NET](tutorial-parallel-dotnet.md) pour apprendre à utiliser C# et la bibliothèque .NET Batch pour exécuter une charge de travail simple à l’aide d’un flux de travail Batch courant. Une [version Python](tutorial-parallel-python.md) et un [didacticiel Node.js](batch-nodejs-get-started.md) sont également disponibles.
* Téléchargez les [exemples de code sur GitHub][github_samples] pour voir comment C# et Python peuvent interagir avec Batch pour planifier et traiter des exemples de charge de travail.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch/clientlibrary:
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-mgmt-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/azurerm.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchLabs/
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com
