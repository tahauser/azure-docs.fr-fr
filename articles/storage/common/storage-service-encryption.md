---
title: "Azure Storage Service Encryption pour les données au repos | Microsoft Docs"
description: "La fonctionnalité Azure Storage Service Encryption permet de chiffrer votre stockage d’objets blob Azure côté service lors du stockage des données et de le déchiffrer lorsque vous récupérez les données."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: d04752c92218d17b4e90bb03a2ed2ac5a0a11b93
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption pour les données au repos
Azure Storage Service Encryption pour les données au repos vous aide à protéger vos données pour répondre aux engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, Azure Storage chiffre automatiquement vos données avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. La gestion du chiffrement, du déchiffrement et des clés est totalement transparente pour les utilisateurs.

Les sections suivantes fournissent des instructions détaillées sur la façon d’utiliser les fonctionnalités Storage Service Encryption et présentent les scénarios et les expériences utilisateur pris en charge.

## <a name="overview"></a>Vue d'ensemble
Azure Storage propose un ensemble complet de fonctionnalités de sécurité qui, réunies, permettent aux développeurs de créer des applications sécurisées. Les données peuvent être sécurisées en transit entre une application et Azure au moyen du [chiffrement côté client](../storage-client-side-encryption.md), de HTTPs ou de SMB 3.0. La fonctionnalité Storage Service Encryption fournit un chiffrement au repos et se charge de la gestion du chiffrement, du déchiffrement et des clés de façon totalement transparente. Toutes les données sont chiffrées à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256 bits, l’un des algorithmes de chiffrement par blocs les plus puissants disponibles.

SSE fonctionne en chiffrant les données lors de leur écriture dans le stockage Azure et peut être utilisé pour le stockage Blob, Fichier, Table et File d’attente Azure. Il fonctionne pour les éléments suivants :

* Stockage Standard : comptes de stockage à usage général pour le stockage Blob, Fichier, Table et File d’attente Azure
* Stockage Premium 
* Activé par défaut pour les comptes de stockage classique et ARM 
* Tous les niveaux de redondance (LRS, ZRS, GRS, RA-GRS)
* Comptes de stockage Azure Resource Manager (non Classic) 
* Toutes les régions.

Pour plus d’informations, consultez la FAQ.

Pour afficher les paramètres de Storage Service Encryption pour un compte de stockage, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez un compte de stockage. Dans le panneau Paramètres, recherchez la section Service BLOB, comme illustré dans cette capture d’écran, puis cliquez sur Chiffrement.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption/image1.png)
<br/>*Figure 1 : Activer SSE pour le service BLOB (étape 1)*

![Capture d’écran du Portail affichant l’option de chiffrement](./media/storage-service-encryption/image3.png)
<br/>*Figure 2 : Activer SSE pour le service Fichier (étape 1)*

Lorsque vous avez cliqué sur le paramètre Chiffrement, vous pouvez activer ou désactiver Storage Service Encryption.

![Capture d’écran du Portail affichant les propriétés de chiffrement](./media/storage-service-encryption/image2.png)
<br/>*Figure 3 : Activer SSE pour le service Blob et Fichier (étape 2)*

## <a name="encryption-scenarios"></a>Scénarios de chiffrement
Storage Service Encryption est activé au niveau d’un compte de stockage. Il est activé pour tous les services par défaut. Les scénarios clients suivants sont pris en charge :

* Chiffrement du stockage Blob, Fichier, Table et File d’attente pour les comptes classique et Resource Manager.

SSE présente les limites suivantes :

* Données existantes : SSE chiffre uniquement les données créées après l’activation du chiffrement. Par exemple, si vous créez un compte de stockage Resource Manager sans activer le chiffrement, puis si vous chargez des objets blob ou des disques durs virtuels archivés dans ce compte de stockage avant d’activer SSE, ces objets blob ne sont pas chiffrés, sauf s’ils sont réécrits ou copiés.
* Prise en charge du Marketplace : activez le chiffrement des machines virtuelles créées à partir du Marketplace à l’aide du [portail Azure](https://portal.azure.com), de PowerShell et de l’interface de ligne de commande Azure. L’image de base du disque dur virtuel reste non chiffrée. Toutefois, les écritures effectuées une fois que la machine virtuelle est opérationnelle sont chiffrées.

## <a name="getting-started"></a>Mise en route
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Étape 1 : [Créer un compte de stockage](../storage-create-storage-account.md).
### <a name="step-2-verify-encryption"></a>Étape 2 : Vérifier le chiffrement.
Vous pouvez vérifier le chiffrement à l’aide du [portail Azure](https://portal.azure.com).

> [!NOTE]
> Si vous souhaitez vérifier la fonctionnalité Storage Service Encryption par programme sur un compte de stockage, vous pouvez utiliser [l’API REST du fournisseur de ressources de stockage Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx), [la bibliothèque cliente des fournisseurs de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou [l’interface de ligne de commande Azure](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Étape 3 : Copier les données dans le compte de stockage
Une fois que SSE est activé pour un compte de stockage, les données écrites dans ce compte de stockage seront chiffrées. Toutes les données qui se trouvent déjà dans ce compte de stockage ne seront pas chiffrées jusqu’à ce qu’elles soient réécrites. Vous pouvez copier des données d’un conteneur à un autre pour vous assurer que les données précédentes sont chiffrées. Vous pouvez utiliser l’un des outils suivants pour effectuer cette opération. Le même comportement s’applique au stockage Fichier, Table et File d’attente.

#### <a name="using-azcopy"></a>Utilisation d’AzCopy
AzCopy est un utilitaire de ligne de commande Windows conçu pour copier des données depuis et vers un objet blob Microsoft Azure, un fichier ou un stockage de table en utilisant les commandes avec une performance optimale. Vous pouvez l’utiliser pour copier vos objets blob ou vos fichiers d’un compte de stockage à un autre pour lequel SSE est activé. 

Pour plus d’informations, voir [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

#### <a name="using-smb"></a>Utilisation de SMB
Azure Files offre des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard. Vous pouvez monter un partage de fichiers à partir d’un client local ou dans Azure. Une fois le partage monté, des outils tels que Robocopy peuvent être utilisés pour copier les fichiers sur des partages de fichiers Azure. Pour plus d’informations, consultez [Montage d’un partage de fichiers Azure et accès au partage dans Windows](../files/storage-how-to-use-files-windows.md) et [Utilisation du stockage de fichiers Azure avec Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>Utilisation des bibliothèques clientes de stockage
Vous pouvez copier des données d’objets blob ou de fichiers vers et à partir du Stockage Blob ou entre comptes de stockage à l’aide de notre vaste choix de bibliothèques clientes de stockage, dont .NET, C++, Java, Android, Node.js, PHP, Python et Ruby.

Pour plus d’informations, consultez la section [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Utilisation d’un explorateur de stockage
Vous pouvez utiliser les explorateurs de stockage pour créer des comptes de stockage, charger et télécharger des données, afficher le contenu des objets blob et parcourir les répertoires. Vous pouvez utiliser l’un de ces explorateurs de stockage pour télécharger des objets blob dans votre compte de stockage avec le chiffrement activé. Avec certains explorateurs de stockage, vous pouvez également copier les données du compte de stockage d’objets blob existant vers un conteneur différent dans le compte de stockage ou un nouveau compte de stockage pour lequel SSE est activé.

Pour en savoir plus, consultez [Explorateurs du stockage Azure](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>Étape 4 : Interroger l’état des données chiffrées
Une version mise à jour des bibliothèques clientes de stockage a été déployée afin que vous puissiez interroger l’état d’un objet pour déterminer s’il est chiffré ou non. Actuellement disponible uniquement pour le Stockage Blob. La prise en charge du Stockage Fichier est prévue. 

En attendant, vous pouvez appeler l’opération [Obtenir les propriétés de compte](https://msdn.microsoft.com/library/azure/mt163553.aspx) afin de vérifier que le chiffrement est activé pour votre compte de stockage ou d’afficher les propriétés du compte de stockage dans le portail Azure.

## <a name="encryption-and-decryption-workflow"></a>Flux de travail de chiffrement et de déchiffrement
Voici une brève description du flux de travail de chiffrement et de déchiffrement :

* Le chiffrement est activé pour un compte de stockage.
* Lorsque le client écrit de nouvelles données (PUT Blob, PUT Block, PUT Page, PUT File, etc.) dans le Stockage Blob ou le Stockage Fichier, chaque écriture est chiffrée à l’aide du [Chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des algorithmes de chiffrement par blocs les plus puissants disponibles.
* Lorsque le client a besoin d’accéder aux données (GET Blob, etc.), celles-ci sont automatiquement déchiffrées avant d’être renvoyées à l’utilisateur.
* Si le chiffrement est désactivé, les nouvelles écritures ne sont plus chiffrées et les données chiffrées existantes restent chiffrées jusqu’à ce qu’elles soient réécrites par l’utilisateur. Lorsque le chiffrement est activé, les écritures dans le Stockage Blob ou le Stockage Fichier sont chiffrées. L’état des données ne change pas lorsque l’utilisateur bascule entre l’activation et la désactivation du chiffrement pour le compte de stockage.
* Toutes les clés de chiffrement sont stockées, chiffrées et gérées par Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Forum Aux Questions Azure Storage Service Encryption pour les données au repos
**Q : Je possède déjà un compte de stockage classique. Puis-je activer SSE dessus ?**

R : SSE est activé par défaut pour tous les comptes de stockage (comptes de stockage classique et Resource Manager).

**Q : Comment chiffrer les données de mon compte de stockage classique ?**

R : Avec le chiffrement activé par défaut, vos nouvelles données sont automatiquement chiffrées par le service de stockage. 

En outre, vous pouvez créer un compte de stockage Resource Manager et copier toutes vos données à l’aide [d’AzCopy](storage-use-azcopy.md) de votre compte de stockage classique existant vers votre nouveau compte de stockage Resource Manager. 

Vous pouvez aussi choisir de migrer votre compte de stockage classique vers un compte de stockage Resource Manager. Cette opération est instantanée, elle modifie votre type de compte, mais n’affecte pas vos données existantes. Les nouvelles données écrites ne seront chiffrées qu’après l’activation du chiffrement. Pour plus d’informations, consultez [Migration prise en charge par la plateforme de ressources IaaS de l’environnement Classic vers Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Notez que cela n’est pris en charge que pour les services Blob et Fichier.

**Q : Je possède déjà un compte de stockage Resource Manager. Puis-je activer SSE dessus ?**

R : SSE est activé par défaut sur tous les comptes de stockage Resource Manager existants. Cela est pris en charge pour le stockage Blob, Table, Fichier et File d’attente. 

**Q : Puis-je chiffrer les données actuelles dans un compte de stockage Resource Manager existant ?**

R : Avec SSE activé par défaut pour tous les comptes de stockage classiques et Resource Manager. Toutefois, les données qui étaient déjà présentes ne seront pas chiffrées. Pour chiffrer des données existantes, vous pouvez les copier sur un autre nom ou un autre conteneur, puis supprimer les versions non chiffrées. 

**Q : J’utilise Stockage Premium. Puis-je utiliser SSE ?**

R : Oui. SSE est pris en charge par le stockage Standard et Premium Storage.  Le Stockage Premium n’est pas pris en charge pour le service Fichier.

**Q : Si je crée un compte de stockage avec SSE activé, puis si je crée une machine virtuelle à l’aide de ce compte de stockage, cela signifie-t-il que ma machine virtuelle est chiffrée ?**

R. : Oui. Les disques créés qui utilisent le nouveau compte de stockage sont chiffrés, à condition qu’ils aient été créés après l’activation de SSE. Si la machine virtuelle a été créée à l’aide d’Azure Marketplace, l’image de base du disque dur virtuel reste non chiffrée. Toutefois, les écritures effectuées une fois que la machine virtuelle est opérationnelle sont chiffrées.

**Q : Puis-je créer des comptes de stockage dans lesquels SSE est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**

R : SSE est activé par défaut au moment de la création d’un compte de stockage (comptes classique et Resource Manager). Vous pouvez vérifier les propriétés du compte à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure.

**Q : Quel est le coût d’Azure Storage si SSE est activé ?**

R : Aucun coût supplémentaire n’est facturé.

**Q : Qui gère les clés de chiffrement ?**

R : Les clés sont gérées par Microsoft.

**Q : Puis-je utiliser mes propres clés de chiffrement ?**

R : Nous travaillons activement à la mise en place d’une fonctionnalité permettant aux clients d’utiliser leurs propres clés de chiffrement.

**Q : Est-il possible de révoquer l’accès aux clés de chiffrement ?**

R : Pas pour l’instant. Les clés sont entièrement gérées par Microsoft.

**Q : Est-ce que SSE est activé par défaut lorsque je crée un compte de stockage ?**

R : Oui, SSE utilisant des clés gérées Microsoft est activé par défaut pour tous les comptes de stockage Azure Resource Manager et classiques. Il est activé pour tous les services également - stockage Blob, Table, File d’attente et Fichier.

**Q : En quoi est-ce différent d’Azure Disk Encryption ?**

R : Cette fonctionnalité permet de chiffrer les données dans Azure Blob Storage. Azure Disk Encryption permet de chiffrer les disques de système d’exploitation et de données dans les machines virtuelles IaaS. Pour plus d’informations, consultez notre [guide de sécurité sur Storage](../storage-security-guide.md).

**Q : Que se passe-t-il si SSE est activé, puis si j’active Azure Disk Encryption sur les disques ?**

R: Cela fonctionne de façon transparente. Vos données sont chiffrées par les deux méthodes.

**Q : Mon compte de stockage est configuré pour être répliqué de manière géo-redondante. Si SSE est activé, ma copie redondante est-elle également chiffrée ?**

R : Oui. Toutes les copies du compte de stockage sont chiffrées et l’ensemble des options de redondance sont prises en charge : stockage localement redondant (LRS), stockage redondant dans une zone (ZRS), stockage géo-redondant (GRS) et stockage géo-redondant avec accès en lecture (RA-GRS).

**Q : Je ne peux pas activer le chiffrement sur mon compte de stockage.**

R : Le chiffrement est activé par défaut et il n’existe pas de configuration pour désactiver le chiffrement de votre compte de stockage. 

**Q : SSE est-il autorisé uniquement dans des régions spécifiques ?**

R : SSE est disponible dans toutes les régions pour tous les services. 

**Q : Comment obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**

R : Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour les problèmes liés au chiffrement du service Stockage.

## <a name="next-steps"></a>Étapes suivantes
Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité sur Stockage](../storage-security-guide.md).
