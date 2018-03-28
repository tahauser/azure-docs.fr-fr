---
title: Azure Storage Service Encryption pour les données au repos | Microsoft Docs
description: La fonctionnalité Azure Storage Service Encryption permet de chiffrer le stockage Blob Azure côté service lors du stockage des données et de le déchiffrer lorsque vous récupérez les données.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: d9df2218acc218a796e502fa4e3b94573af86ca8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption pour les données au repos

Le Chiffrement du service de stockage Azure pour les données au repos vous permet de protéger vos données pour garantir le respect des engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, le stockage Azure Storage chiffre automatiquement vos données avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. La gestion du chiffrement, le chiffrement au repos, le déchiffrement et la gestion des clés dans Storage Service Encryption se font de façon transparente pour les utilisateurs. Toutes les données écrites dans le stockage Azure sont chiffrées à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des chiffrements par blocs parmi les plus puissants disponibles.

Storage Service Encryption est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti de Storage Service Encryption.

La fonctionnalité chiffre automatiquement les données dans :

- Les deux niveaux de performances (Standard et Premium).
- Deux modèles de déploiement : Azure Resource Manager et classique.
- Tous les services de stockage Azure (stockage Blob, stockage File d’attente, stockage Table et fichiers Azure). 

Storage Service Encryption n’affecte pas les performances de Stockage Azure.

Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec Storage Service Encryption ou utiliser vos propres clés de chiffrement. Pour en savoir plus sur l’utilisation de vos propres clés, consultez [Storage Service Encryption avec des clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Afficher des paramètres de chiffrement dans le portail Azure

Pour afficher les paramètres de Storage Service Encryption, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez un compte de stockage. Dans le volet **PARAMÈTRES**, sélectionnez le paramètre **Chiffrement**.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>FAQ relatif à Storage Service Encryption

**Q : J’ai un compte de stockage classique. Puis-je y activer Storage Service Encryption ?**

R : Storage Service Encryption est activé par défaut pour tous les comptes de stockage (comptes de stockage classiques et Resource Manager).

**Q : Comment chiffrer les données de mon compte de stockage classique ?**

R : Lorsque le chiffrement est activé par défaut, le stockage Azure chiffre automatiquement vos nouvelles données. 

**Q : J’ai un compte de stockage Resource Manager. Puis-je y activer Storage Service Encryption ?**

R : Storage Service Encryption est activé par défaut sur tous les comptes de stockage Resource Manager existants. Il est pris en charge pour le stockage Blob, le stockage Table, le stockage File d’attente et les fichiers Azure. 

**Q : comment chiffrer les données dans un compte de stockage Resource Manager ?**

R : Storage Service Encryption est activé par défaut pour tous les comptes de stockage : classiques et Resource Manager. Toutefois, les données existantes ne sont pas chiffrées. Pour chiffrer des données existantes, vous pouvez les copier sous un autre nom ou dans un autre conteneur, puis supprimer les versions non chiffrées. 

**Q : Puis-je créer des comptes de stockage dans lesquels Storage Service Encryption est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**

R : Storage Service Encryption est activé par défaut lors de la création d’un compte de stockage (classique ou Resource Manager). Vous pouvez vérifier les propriétés du compte à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure.

**Q : Quel est le coût supplémentaire du stockage Azure si Storage Service Encryption est activé ?**

R : Aucun coût supplémentaire n’est facturé.

**Q : Puis-je utiliser mes propres clés de chiffrement ?**

R : Oui, vous pouvez utiliser vos propres clés de chiffrement. Pour plus d’informations, consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Q : Est-il possible de révoquer l’accès aux clés de chiffrement ?**

R : Oui, si vous [utilisez vos propres clés de chiffrement](storage-service-encryption-customer-managed-keys.md) dans Azure Key Vault.

**Q : Storage Service Encryption est-il activé par défaut lorsque je crée un compte de stockage ?**

R : Storage Service Encryption (avec des clés gérées par Microsoft) est activé par défaut pour tous les comptes de stockage : Azure Resource Manager et classiques. Il est également activé pour tous les services : stockage Blob, stockage Table, stockage File d’attente et fichiers Azure.

**Q : En quoi est-ce différent d’Azure Disk Encryption ?**

R : Azure Disk Encryption permet de chiffrer des disques de systèmes d’exploitation et de données dans les machines virtuelles IaaS. Pour plus d’informations, consultez le [guide de sécurité Stockage Azure](../storage-security-guide.md).

**Q : Que se passe-t-il si j’active Azure Disk Encryption sur mes disques de données ?**

R: Cela fonctionne de façon transparente. Les deux méthodes chiffreront vos données.

**Q : Mon compte de stockage est configuré pour être répliqué de manière géo-redondante. Avec Storage Service Encryption, ma copie redondante sera-t-elle également chiffrée ?**

R : Oui, toutes les copies du compte de stockage sont chiffrées. Toutes les options de redondance sont prises en charge : stockage localement redondant, stockage redondant dans une zone, stockage géoredondant et stockage géoredondant avec accès en lecture.

**Q : Puis-je activer le chiffrement sur mon compte de stockage ?**

R : Le chiffrement est activé par défaut, et il n’existe pas de configuration pour désactiver le chiffrement de votre compte de stockage. 

**Q : Storage Service Encryption est-il autorisé uniquement dans certaines régions ?**

R : Storage Service Encryption est disponible dans toutes les régions et pour tous les services. 

**Q : Comment obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**

R : Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour les problèmes ou les commentaires liés à Storage Service Encryption.

## <a name="next-steps"></a>Étapes suivantes
Le stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, ensemble, aident les développeurs à créer des applications sécurisées. Pour plus d’informations, consultez le [guide de sécurité Stockage Azure](../storage-security-guide.md).
