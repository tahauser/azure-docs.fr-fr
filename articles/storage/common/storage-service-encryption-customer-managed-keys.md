---
title: "Chiffrement du service de stockage Azure à l’aide de clés gérées par le client dans Azure Key Vault | Microsoft Docs"
description: "La fonctionnalité Azure Storage Service Encryption permet de chiffrer votre stockage d’objets blob Azure côté service lors du stockage des données et de le déchiffrer lorsque vous récupérez les données à l’aide de clés gérées par le client."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 0a05a0d28899cc3db11f8fda8aec5bd6ed9bd5f8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault

Microsoft Azure s’engage à vous aider à protéger et préserver vos données pour répondre aux engagements de votre entreprise en matière de sécurité et de conformité.  Pour protéger vos données au repos, vous pouvez utiliser Storage Service Encryption (SSE), qui chiffre automatiquement vos données lors de leur écriture dans le stockage et les déchiffre lors de leur récupération. Le chiffrement et le déchiffrement sont automatiques et entièrement transparents ; ils utilisent la technologie de [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des chiffrements par bloc les plus sécurisés qui soient.

Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec SSE ou utiliser vos propres clés de chiffrement. Cet article aborde ce dernier point. Pour plus d’informations sur l’utilisation de clés gérées par Microsoft, ou sur SSE en général, consultez [Chiffrement du service Stockage Azure pour les données au repos](../storage-service-encryption.md).

Pour fournir la possibilité d’utiliser vos propres clés de chiffrement, le stockage SSE pour le stockage blob est intégré dans Azure Key Vault (AKV). Vous pouvez créer vos propres clés de chiffrement et les stocker dans AKV, ou utiliser les API d’AKV pour générer des clés de chiffrement. Non seulement AKV vous permet de gérer et de contrôler vos clés, mais il vous permet également d’auditer votre utilisation des clés. 

Pourquoi souhaitez-vous créer vos propres clés ? Parce que vous gagnez en flexibilité, c’est vous seul qui créez, changez, désactivez et définissez les contrôles d’accès. Cela vous permet également d’effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.

## <a name="sse-with-customer-managed-keys-preview"></a>SSE avec clés gérées par le client (préversion)

Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Pour utiliser cette fonctionnalité, vous devez créer un compte de stockage. Vous pouvez créer un coffre de clés et une clé, ou vous pouvez utiliser un coffre de clés et une clé existants. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents.

Pour participer à la préversion, contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com). Nous fournirons un lien spécial pour participer à la préversion.

Pour plus d’informations, consultez les [Questions fréquentes](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest) (FAQ).

> [!IMPORTANT]
> Vous devez vous inscrire pour la préversion avant d’effectuer les étapes décrites dans cet article. Sans accès à la préversion, vous ne serez pas en mesure d’activer cette fonctionnalité dans le portail.

## <a name="getting-started"></a>Mise en route
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Étape 1 : [Créer un compte de stockage](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Étape 2 : Activer le chiffrement
Vous pouvez activer SSE pour le compte de stockage dans le [portail Azure](https://portal.azure.com). Dans le panneau Paramètres, pour le compte de stockage, recherchez la section Service BLOB, comme illustré dans la figure suivante, puis cliquez sur Chiffrement.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Activer SSE pour le service BLOB*

Si vous souhaitez activer ou désactiver la fonctionnalité Storage Service Encryption par programme sur un compte de stockage, vous pouvez utiliser [l’API REST du fournisseur de ressources de stockage Azure](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN), [la bibliothèque cliente des fournisseurs de ressources de stockage pour .NET](https://docs.microsoft.com/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.0.0) ou [la CLI Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Dans cet écran, si vous ne voyez pas la case à cocher Utiliser votre propre clé, cela signifie vous n’avez pas été accepté pour la préversion. Envoyez un e-mail à [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) et demandez une approbation.

![Capture d’écran du portail affichant le chiffrement (préversion)](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Par défaut, SSE utilise les clés gérées par Microsoft. Pour utiliser vos propres clés, cochez cette case. Ensuite, vous pouvez spécifier l’URI de votre clé, ou sélectionner une clé et Key Vault dans le sélecteur.

## <a name="step-3-select-your-key"></a>Étape 3 : Sélectionner votre clé

![Capture d’écran du portail affichant l’option de chiffrement Utiliser votre propre clé](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Capture d’écran du portail affichant l’option de chiffrement avec saisie de l’URI de la clé](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Si le compte de stockage n’a pas accès à Key Vault, vous pouvez exécuter la commande suivante à l’aide d’Azure PowerShell afin d’octroyer l’accès au coffre de clés requis pour les comptes de stockage.

![Capture d’écran du portail affichant l’accès refusé au coffre de clés](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Vous pouvez également accorder l’accès par le biais du portail Azure en accédant à Azure Key Vault dans le portail Azure et en accordant l’accès au compte de stockage.

## <a name="step-4-copy-data-to-storage-account"></a>Étape 4 : Copier les données dans le compte de stockage
Si vous souhaitez transférer des données dans votre nouveau compte de stockage afin de les chiffrer, reportez-vous à l’[Étape 3 de la section Mise en route dans l’article Chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Étape 5 : Interroger l’état des données chiffrées
Pour interroger l’état des données chiffrées, consultez l’[Étape 4 de la section Mise en route dans l’article Chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Forum Aux Questions Azure Storage Service Encryption pour les données au repos
**Q : J’utilise le stockage Premium. Puis-je utiliser SSE avec des clés gérées par le client ?**

R : Oui, SSE avec des clés gérées par Microsoft ou par le client est pris en charge sur le stockage Standard et sur le stockage Premium. 

**Q : Puis-je créer des comptes de stockage dans lesquels SSE avec clés de gérées par le client est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**

R. : Oui.

**Q : Quel est le coût de stockage Azure si SSE avec clés gérées par le client est activé ?**

R : Un coût spécifique est associé à l’utilisation d’Azure Key Vault. Pour plus d’informations, consultez [Tarification d’Azure Key Vault](https://azure.microsoft.com/en-us/pricing/details/key-vault/). L’utilisation de SSE n’entraîne aucun coût supplémentaire.

**Q : Est-il possible de révoquer l’accès aux clés de chiffrement ?**

R : Oui, vous pouvez révoquer l’accès à tout moment. Il existe plusieurs façons de révoquer l’accès à vos clés. Reportez-vous aux pages [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) et [Interface de ligne de commande Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) pour plus d’informations. La révocation de l’accès bloque efficacement l’accès à tous les objets blob dans le compte de stockage, car la clé de chiffrement du compte n’est pas accessible au stockage Azure.

**Q : Puis-je créer un compte de stockage et une clé dans autre région ?**

R : Non, le compte de stockage et le coffre de clé/la clé doivent se trouver dans la même région. 

**Q : Puis-je activer SSE avec des clés gérées par le client lors de la création du compte de stockage ?**

R : Non. Lorsque vous activez SSE au moment de la création du compte de stockage, vous pouvez uniquement utiliser des clés gérées par Microsoft. Si vous souhaitez utiliser des clés gérées par le client, vous devez mettre à jour les propriétés du compte de stockage. Vous pouvez utiliser REST ou l’une des bibliothèques clientes de stockage pour mettre à jour votre compte de stockage par programmation, ou mettre à jour les propriétés du compte de stockage à l’aide du portail Azure après la création du compte.

**Q : Puis-je désactiver le chiffrement lorsque j’utilise SSE avec des clés gérées par le client ?**

R : Non, vous ne pouvez pas désactiver le chiffrement lorsque vous utilisez SSE avec des clés gérées par le client. Pour désactiver le chiffrement, vous devez passer à l’utilisation de clés gérées par Microsoft. Vous pouvez effectuer cette opération dans le portail Azure ou à l’aide de PowerShell.

**Q : Est-ce que SSE est activé par défaut lorsque je crée un compte de stockage ?**

R : SSE n’étant pas activé par défaut, utilisez le portail Azure pour l’activer. Vous pouvez également l’activer par programme à l’aide de l’API REST du fournisseur de ressources de stockage. 

**Q : Je ne peux pas activer le chiffrement sur mon compte de stockage.**

R : S’agit-il d’un compte de stockage Resource Manager ? Les comptes de stockage classiques ne sont pas pris en charge. SSE avec clés gérées par le client peut aussi être activé, mais uniquement sur les comptes de stockage de Gestionnaire des ressources nouvellement créés.

**Q : SSE avec clés gérées par le client est-il uniquement autorisé dans des régions spécifiques ?**

R : SSE est disponible uniquement dans certaines régions pour le stockage d’objets blob dans le cadre de cette préversion. Envoyez un e-mail à l’adresse [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour vérifier la disponibilité et les informations relatives à la préversion. 

**Q : Comment obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**

R : Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour les problèmes liés au chiffrement du service Stockage. 

## <a name="next-steps"></a>Étapes suivantes

*   Pour plus d’informations sur l’ensemble complet des fonctionnalités de sécurité qui aident les développeurs à créer des applications sécurisées, consultez le [Guide sur la sécurité du stockage](https://docs.microsoft.com/azure/storage/storage-security-guide).
*   Pour plus d’informations générales sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).
*   Pour prendre en main Azure Key Vault, consultez [Prise en main d’Azure Key Vault](../../key-vault/key-vault-get-started.md).
