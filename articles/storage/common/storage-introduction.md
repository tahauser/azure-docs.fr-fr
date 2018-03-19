---
title: "Présentation du stockage Azure | Microsoft Docs"
description: "Présentation du stockage Azure, stockage de données de Microsoft dans le cloud."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 799636d0a702407be06bbe8cebae552b34d860db
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Introduction à Microsoft Azure Storage

Le stockage Microsoft Azure est un service cloud géré par Microsoft qui fournit un stockage hautement disponible, sécurisé, durable, évolutif et redondant. Microsoft prend en charge la maintenance et gère les problèmes critiques pour vous.

Le stockage Azure se compose de trois services de données : le stockage d’objets blob, le stockage de fichiers et le stockage de files d’attente. Le stockage d’objets blob prend en charge le stockage Standard et Premium, mais n’utilise que des instances SSD pour une rapidité d’action optimale avec le stockage Premium. Le stockage froid constitue une autre fonctionnalité qui vous permet de stocker d’importants volumes de données rarement sollicitées à un coût faible.

Cet article vous détaillera :
* les services de stockage Azure
* les types de compte de stockage
* comment accéder aux objets blob, aux files d’attente et aux fichiers
* le chiffrement
* la réplication
* comment transférer des données vers le stockage ou hors du stockage
* les nombreuses bibliothèques de client de stockage disponibles.

Pour démarrer et exécuter avec le stockage Azure, consultez la section [Créer un compte de stockage](storage-quickstart-create-account.md).

## <a name="introducing-the-azure-storage-services"></a>Présentation des services Azure Storage

Pour utiliser les services fournis par le stockage Azure (stockage d’objets blob, stockage de fichiers et stockage de files d’attente), créez un compte de stockage, puis transférez les données vers/à partir d’un service spécifique dans ce compte de stockage.

## <a name="blob-storage"></a>Stockage d'objets blob

Les objets blob sont essentiellement des fichiers semblables à ceux que vous stockez sur votre ordinateur (ou tablette, appareil mobile, etc). Il peut s’agir d’images, de fichiers Microsoft Excel, de fichiers HTML, de disques durs virtuels (VHD), de Big Data telle que des journaux ou des sauvegardes de base de données. Les objets blob sont stockés dans des conteneurs, équivalents à des dossiers.

Après avoir stocké les fichiers dans le stockage blob, vous pouvez y accéder depuis n’importe où dans le monde via des URL, l’interface REST ou l’une des bibliothèques clientes de stockage du Kit de développement logiciel (SDK) Azure. Les bibliothèques clientes de stockage sont disponibles dans plusieurs langages, tels que Node.js, Java, PHP, Ruby, Python et .NET.

Il existe trois types de blob : les blobs de blocs, les blobs de pages (utilisés pour les fichiers VHD) et les blobs d’ajout.

* Les objets blob de blocs servent à stocker des fichiers ordinaires d’une taille maximale approximative de 4,7 To.
* Les objets blob de pages servent à stocker les fichiers à accès aléatoire d’une taille maximale de 8 To. Ces objets sont utilisés avec les fichiers VHD qui soutiennent les machines virtuelles.
* Les objets blob d’ajout se composent de blocs, comme les objets blob de blocs, mais sont optimisés pour les opérations d’ajout. Ils servent à effectuer des opérations telles que l’enregistrement d’informations vers le même objet blob, depuis plusieurs machines virtuelles.

Pour les jeux de données très volumineux où les contraintes du réseau rendent irréaliste le téléchargement de données vers/depuis le stockage d’objets blob par le biais du réseau, vous pouvez expédier un ensemble de disques durs à Microsoft pour importer ou exporter les données directement à partir du centre de données. Consultez [Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export](../storage-import-export-service.md).

## <a name="azure-files"></a>Azure Files
[Azure Files](../files/storage-files-introduction.md) vous permet de configurer les partages de fichiers réseau hautement disponibles qui sont accessibles à l’aide du protocole Server Message Block (SMB) standard. Cela signifie que plusieurs machines virtuelles peuvent partager les mêmes fichiers avec accès en lecture et en écriture. Vous pouvez également consulter les fichiers à l’aide de l’interface REST ou des bibliothèques clientes de stockage.

Ce qui distingue Azure Files des fichiers sur un partage de fichiers d’entreprise est le fait de pouvoir accéder aux fichiers depuis n’importe où dans le monde, à l’aide d’une URL qui pointe vers le fichier et inclut un jeton de signature (SAP) d’accès partagé. Vous pouvez générer des jetons SAP, qui autorisent un accès spécifique à une ressource privée pour une durée définie.

Les partages de fichiers peuvent être utilisés dans de nombreux scénarios courants :

* Par exemple, de nombreuses applications locales utilisent les partages de fichiers. Cette fonctionnalité simplifie la migration de ces applications qui partagent des données vers Azure. Si vous montez le partage de fichiers sur la même lettre de lecteur que celle utilisée par l’application locale, la partie de votre application qui accède au partage de fichiers doit fonctionner, avec très peu de modifications à apporter (voire aucune).

* Les fichiers de configuration peuvent être stockés sur un partage de fichiers et sont accessibles à partir de plusieurs machines virtuelles. Les outils et utilitaires utilisés par plusieurs développeurs d’un groupe peuvent être stockés sur un partage de fichiers, en veillant à ce que chacun puisse les trouver et à ce qu’ils utilisent la même version.

* Les journaux de diagnostic, les mesures et les vidages sur incident sont trois exemples de données qui peuvent être écrites dans un partage de fichiers et traitées ou analysées ultérieurement.

À ce jour, les listes de contrôle d’accès (ACL) et l’authentification basée sur Active Directory ne sont pas prises en charge, mais elles le seront à l’avenir. Les informations d’identification de compte de stockage sont utilisées pour l’authentification d’accès au partage de fichiers. Cela signifie que toute personne avec le partage monté aura un accès complet en lecture/écriture au partage.

## <a name="queue-storage"></a>Stockage de files d'attente

Le service de files d’attente Azure sert à stocker et à récupérer des messages. La taille maximale des messages de file d’attente est de 64 Ko et une file d’attente peut contenir des millions de messages. Les files d’attente servent en général à stocker des listes de messages qui seront traités de façon asynchrone.

Par exemple, vous souhaitez que vos clients soient en mesure de charger des images, et vous souhaitez créer des miniatures de chaque image. La procédure normale serait de faire patienter votre client, le temps que vous créiez les miniatures tout en chargeant les images. L’utilisation d’une file d’attente est une autre possibilité. Lorsque le client a terminé le chargement d’images, écrivez un message dans la file d’attente. Utilisez ensuite une fonction Azure afin de récupérer le message dans la file d’attente pour créer les miniatures. Chaque étape de ce processus peut être développée séparément, vous offrant ainsi plus de contrôle lorsque vous le réglez.

## <a name="table-storage"></a>Stockage de tables

Stockage de tables Azure fait maintenant partie d’Azure Cosmos DB. Pour consulter la documentation Stockage de tables Azure, consultez [Vue d’ensemble du stockage de table Azure](../../cosmos-db/table-storage-overview.md). En plus du service Stockage de tables Azure existant, il existe une nouvelle API de Table d’Azure Cosmos DB qui propose des tables optimisées pour le débit, la distribution globale et les index secondaires automatiques. Pour en savoir plus et essayer la nouvelle expérience premium, consultez [API Table d’Azure Cosmos DB](https://aka.ms/premiumtables).

## <a name="disk-storage"></a>Stockage sur disque

Le stockage Azure comprend également des fonctionnalités de disque géré et non géré utilisées par des machines virtuelles. Pour en savoir plus sur ces fonctionnalités, consultez la [documentation Compute Service](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

Ce tableau détaille les différents types de comptes de stockage et les objets qu’ils peuvent utiliser.

|**Type de compte de stockage**|**Édition Standard à usage général**|**Édition Premium à usage général**|**Stockage d’objets blob, niveaux d’accès froid et chaud**|
|-----|-----|-----|-----|
|**Services pris en charge**| Services d’objet blob, de fichier et de file d’attente | Service blob | Service blob|
|**Types d’objets blob pris en charge**|Objets blob de blocs, objets blob de pages et objets blob d’ajout | Objets blob de pages | Objets blob de blocs et objets blob d’ajout|

### <a name="general-purpose-storage-accounts"></a>Comptes de stockage à usage général

Il existe deux types de comptes de stockage à usage général.

#### <a name="standard-storage"></a>Stockage Standard

Les comptes de stockage Standard sont les plus couramment utilisés. Ils peuvent être utilisés avec n’importe quel type de données. Les comptes de stockage Standard se servent de médias magnétiques pour stocker des données.

#### <a name="premium-storage"></a>Stockage Premium

Le service de stockage Premium offre un stockage hautes performances pour les objets blob de pages, principalement utilisés avec les fichiers VHD. Les comptes de stockage Premium se servent du chiffrement SSD pour stocker des données. Microsoft recommande l’utilisation du service de stockage Premium pour toutes vos machines virtuelles.

### <a name="blob-storage-accounts"></a>Comptes de stockage d’objets blob

Le compte de stockage d’objets blob est un compte de stockage spécialisé, utilisé pour le stockage d’objets blob de blocs et d’ajout. Il est impossible d’y stocker des objets blob de pages et donc d’y stocker des fichiers VHD. Ces comptes permettent de définir un niveau d’accès chaud ou froid, et de le modifier à tout moment.

Le niveau d’accès « chaud » désigne les fichiers souvent sollicités : le coût de stockage est plus élevé, tandis que le coût d’accès aux objets blob est beaucoup plus faible. En ce qui concerne les objets blob stockés au niveau d’accès « froid », le coût pour y accéder est plus élevé, tandis que le coût de stockage est beaucoup plus faible.

## <a name="accessing-your-blobs-files-and-queues"></a>Accéder aux objets blob, aux fichiers et aux files d’attente

Chaque compte de stockage dispose deux clés d’authentification, qu’il est possible d’utiliser pour n’importe quelle opération. Vous pouvez ainsi passer de temps en temps d’une clé à l’autre et améliorer la sécurité. Il est essentiel que ces clés soient sécurisées, car leur possession, avec le nom du compte, offre un accès illimité à toutes les données du compte de stockage.

Cette section présente deux façons de sécuriser le compte de stockage et ses données. Pour en savoir plus sur la sécurisation de votre compte de stockage et de vos données, consultez le [guide de sécurité pour le stockage Azure](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Sécuriser l’accès aux comptes de stockage à l’aide d’Azure AD

Contrôler l’accès aux clés du compte de stockage permet de sécuriser l’accès à vos données de stockage. Avec le contrôle d’accès en fonction du rôle (RBAC) de Resource Manager, vous pouvez attribuer des rôles aux utilisateurs, aux groupes ou aux applications. Ces rôles sont liés à un ensemble spécifique d’actions, autorisées ou non. L’utilisation du service RBAC pour accorder l’accès à un compte de stockage ne permet de gérer que les opérations de gestion pour ce compte de stockage, telles que la modification du niveau d’accès. Vous ne pouvez pas utiliser le service RBAC pour accorder l’accès à des objets de données tels qu’un conteneur ou un partage de fichier spécifique. Toutefois, vous pouvez utiliser le service RBAC pour accorder l’accès aux clés du compte de stockage, qu’il sera alors possible d’utiliser pour lire les objets de données.

### <a name="securing-access-using-shared-access-signatures"></a>Sécuriser l’accès à l’aide des signatures d’accès partagé

Vous pouvez utiliser les signatures d’accès partagé et les stratégies d’accès stockées pour sécuriser vos objets de données. Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité qui peut être associé à l’URI pour une ressource et vous permet de déléguer l’accès aux objets de stockage et de spécifier des restrictions telles que les autorisations et la plage des dates/heures d’accès. Cette fonctionnalité dispose de fonctionnalités étendues. Pour en savoir plus, consultez [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="public-access-to-blobs"></a>Accès public aux objets blob

Le service BLOB vous permet d’offrir un accès public à un conteneur et ses objets blob, ou à un objet blob spécifique. Lorsque vous indiquez qu'un conteneur ou un objet blob est public, n'importe qui peut le lire de manière anonyme ; aucune authentification n'est requise. Vous pouvez offrir un accès public lorsque vous disposez d’un site Web qui utilise des images, des vidéos ou des documents provenant d’un stockage d’objets blob. Pour en savoir plus, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Chiffrement

Il existe deux types de chiffrement de base pour les services de stockage. Pour en savoir plus sur la sécurité et le chiffrement, consultez le [guide de sécurité pour le stockage Azure](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Chiffrement au repos

Le Chiffrement du service de stockage Azure au repos permet de protéger vos données pour garantir le respect des engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, Azure Storage chiffre automatiquement vos données avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. La gestion du chiffrement, du déchiffrement et des clés est totalement transparente pour les utilisateurs.

Le chiffrement du service de stockage chiffre automatiquement les données pour tous les niveaux de performance (Standard ou Premium), tous les modèles de déploiement (Azure Resource Manager et Classic) et tous les services de Stockage Azure (blob, file d’attente, table et fichier). Le chiffrement du service de stockage n’affecte pas les performances de Stockage Azure.

Pour plus d’informations sur le chiffrement du service de stockage, consultez [Azure Storage Service Encryption pour les données au repos](storage-service-encryption.md).

### <a name="client-side-encryption"></a>chiffrement côté client

Les bibliothèques clientes de stockage ont des méthodes que vous pouvez appeler pour chiffrer des données par programme avant de les envoyer du client vers Azure. Les données sont chiffrées et stockées, ce qui signifie qu’elles sont également chiffrées au repos. À la lecture des données au retour, vous déchiffrez les informations après réception.

Pour plus d’informations sur le chiffrement côté client, consultez [Chiffrement côté client avec .NET pour Stockage Microsoft Azure](storage-client-side-encryption.md).

## <a name="replication"></a>Réplication

Pour assurer la durabilité de vos données, le stockage Azure conserve (et gère) plusieurs copies de vos données. On parle alors de réplication, ou parfois de redondance. Lorsque vous configurez votre compte de stockage, vous choisissez un type de réplication. Dans la plupart des cas, ce paramètre peut être modifié après avoir configuré le compte de stockage.

**Stockage localement redondant (LRS)**

Le stockage localement redondant (LRS) est conçu pour fournir une durabilité des objets d’au moins 99,999999999 % (11 neuf) sur une année donnée. Cela signifie que de nombreuses copies de vos données sont gérées par le stockage Azure dans le centre de données spécifié lors de la configuration du compte de stockage. Lorsque les modifications sont validées, toutes les copies sont mises à jour avant de retourner les cas de réussite. Cela signifie que les réplicas sont toujours synchronisés. En outre, les copies résident dans des domaines d’erreur et des domaines de mise à niveau distincts, ce qui signifie que vos données sont disponibles même si un nœud de stockage contenant vos données tombe en panne ou est mis hors ligne en vue d’une mise à jour.

**Stockage redondant dans une zone (ZRS) (préversion)**

Le stockage redondant dans une zone (ZRS) est conçu pour simplifier le développement d’applications hautement disponibles. Le stockage ZRS offre une durabilité des objets de stockage d’au moins 99,9999999999 % (12 neuf) sur une année donnée. Le stockage ZRS réplique vos données de façon synchrone sur plusieurs zones de disponibilité. Envisagez le stockage ZRS pour des scénarios tels que des applications transactionnelles dans lesquelles un temps d’arrêt n’est pas acceptable. Le stockage ZRS permet aux clients de lire et d’écrire les données même si une zone est indisponible ou irrécupérable. Les insertions et mises à jour de données sont effectuées de façon synchrone et sont hautement cohérentes.    

L’ancienne fonctionnalité ZRS est maintenant appelée ZRS classique. Les comptes ZRS classiques sont disponibles uniquement pour les objets Blob de bloc dans des comptes de stockage V1 à usage général. ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il est possible qu’un réplica ne soit disponible que si Microsoft lance le basculement vers la région secondaire. Un compte ZRS classique ne peut pas être converti vers ou depuis le stockage LRS ou GRS et n’a pas de fonctionnalité de mesures ou de journalisation.

**Stockage géo-redondant (GRS)**

Le stockage géo-redondant (GRS) est conçu pour fournir une durabilité des objets d’au moins 99,9999999999999999 % (16 neuf) sur une année donnée en conservant les copies locales de vos données dans une région principale, et un autre jeu de copies de vos données dans une région secondaire située à des centaines de kilomètres de la région principale. En cas de défaillance dans la région principale, le stockage Azure bascule vers la région secondaire.

**Stockage géo-redondant avec accès en lecture (RA-GRS)**

Un stockage géo-redondant avec accès en lecture est similaire au stockage GRS, à la différence que vous avez un accès en lecture aux données situées à l’emplacement secondaire. Si le centre de données principal devient temporairement indisponible, vous pouvez continuer à lire les données à partir de l’emplacement secondaire. Cela peut être très utile. Par exemple, une application Web pourrait passer en lecture seule et désigner la copie secondaire, vous laissant un certain degré d’accès même si aucune mise à jour n’est disponible.

> [!IMPORTANT]
> Vous pouvez modifier le mode de réplication de vos données après la création de votre compte de stockage. Toutefois, il vous faudra éventuellement subir un coût forfaitaire supplémentaire lié au transfert de données si vous passez de LRS ou ZRS à GRS ou RA-GRS.
>

Pour en savoir plus sur les options de réplication, consultez [Réplication de Stockage Azure](storage-redundancy.md).

Pour en savoir plus sur la récupération d’urgence, consultez [Que faire en cas de panne de stockage Azure](storage-disaster-recovery-guidance.md).

Si vous voulez un exemple montrant comment exploiter le stockage de RA-GRS pour garantir une haute disponibilité, consultez [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transfert de données vers et depuis Azure Storage

Vous pouvez utiliser l’utilitaire de ligne de commande AzCopy pour copier des données d’objets blob et de fichiers au sein de votre compte de stockage ou entre des comptes de stockage. Pour plus d’informations, consultez l’un des articles suivants :

* [Transférer des données avec AzCopy pour Windows](storage-use-azcopy.md)
* [Transférer des données avec AzCopy pour Linux](storage-use-azcopy-linux.md)

AzCopy repose sur la [bibliothèque de déplacement de données Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), actuellement disponible en version préliminaire.

Le service d’importation/exportation d’Azure peut être utilisé pour importer ou exporter de grandes quantités de données d’objets blob vers ou depuis votre compte de stockage. Vous préparez et envoyez par courrier électronique plusieurs disques durs vers un centre de données Azure, puis ils transféreront les données vers ou depuis les disques durs avant de vous renvoyer les disques durs. Pour plus d’informations sur le service Import/Export, voir [Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export](../storage-import-export-service.md).

## <a name="pricing"></a>Tarifs

Pour en savoir plus sur la tarification du stockage Azure, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Outils, bibliothèques et API de stockage
Les ressources Azure Storage sont accessibles par n’importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, Stockage Azure offre des bibliothèques de programmation pour plusieurs langages répandus. Ces bibliothèques simplifient l’utilisation du stockage Azure sous de nombreux aspects en gérant des détails tels que l’invocation synchrone et asynchrone, le traitement par lots des opérations, la gestion des exceptions, les nouvelles tentatives automatiques, le comportement opérationnel, etc. Des bibliothèques sont actuellement disponibles pour les langages et les plateformes suivants (d’autres sont à l’étude) :

### <a name="azure-storage-data-services"></a>Services de données Azure Storage
* [API REST des services de stockage](/rest/api/storageservices/)
* [Bibliothèque cliente de stockage pour .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Bibliothèque cliente de stockage pour C++](https://github.com/Azure/azure-storage-cpp)
* [Bibliothèque cliente de stockage pour Java/Android](https://azure.microsoft.com/develop/java/)
* [Bibliothèque cliente de stockage pour Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Bibliothèque cliente de stockage pour PHP](https://azure.microsoft.com/develop/php/)
* [Bibliothèque cliente de stockage pour Python](https://azure.microsoft.com/develop/python/)
* [Bibliothèque cliente de stockage pour Ruby](https://azure.microsoft.com/develop/ruby/)
* [Cmdlets de stockage pour PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Commandes de stockage pour CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le stockage d’objets blob](../blobs/storage-blobs-introduction.md)
* [En savoir plus sur le stockage de fichiers](../storage-files-introduction.md)
* [En savoir plus sur le stockage de file d’attente](../queues/storage-queues-introduction.md)

Pour démarrer et exécuter avec le stockage Azure, consultez la section [Créer un compte de stockage](storage-quickstart-create-account.md).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Pour les administrateurs
* [Utilisation d'Azure PowerShell avec Azure Storage](storage-powershell-guide-full.md)
* [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Pour les développeurs .NET
* [Prise en main d’Azure Blob Storage à l’aide de .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Développer pour Azure Files avec .NET](../files/storage-dotnet-how-to-use-files.md)
* [Prise en main d’Azure Table Storage à l’aide de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Prise en main du stockage de files d’attente Azure à l’aide de .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Pour les développeurs Java/Android
* [Utilisation du stockage d'objets blob à partir de Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Développer pour Azure Files avec Java](../files/storage-java-how-to-use-file-storage.md)
* [Utilisation du stockage de tables à partir de Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Utilisation du stockage de files d'attente à partir de Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Pour les développeurs Node.js
* [Utilisation du stockage d'objets blob à partir de Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Utilisation du stockage de tables à partir de Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Utilisation du stockage de files d'attente à partir de Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Pour les développeurs PHP
* [Utilisation du stockage d'objets blob à partir de PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Utilisation du stockage de tables à partir de PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Utilisation du stockage de files d'attente à partir de PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Pour les développeurs Ruby
* [Utilisation du stockage d'objets blob à partir de Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Utilisation du stockage de tables à partir de Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Utilisation du stockage de files d'attente à partir de Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Pour les développeurs Python
* [Utilisation du stockage d'objets blob à partir de Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Développer pour Azure Files avec Python](../files/storage-python-how-to-use-file-storage.md)
* [Utilisation du stockage de tables à partir de Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Utilisation du stockage de files d'attente à partir de Python](../storage-python-how-to-use-queue-storage.md)
