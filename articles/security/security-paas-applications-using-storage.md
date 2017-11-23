---
title: "Sécurisation des applications PaaS à l’aide du stockage Azure | Microsoft Docs"
description: " Découvrez les bonnes pratiques de sécurité du stockage Azure pour protéger vos applications mobiles et web PaaS. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 16ee6d9d2f02c758d7682626a8b71a3ff17f841c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Sécurisation des applications mobiles et web PaaS à l’aide du stockage Azure
Dans cet article, nous abordons un ensemble de bonnes pratiques de sécurité du stockage Azure pour protéger vos applications mobiles et web PaaS. Ces bonnes pratiques sont issues de notre expérience d’Azure, mais également de celle des clients, comme vous.

Le [Guide de sécurité du Stockage Azure](../storage/common/storage-security-guide.md) constitue une source précieuse pour obtenir plus d’informations sur la sécurité et le Stockage Azure.  Cet article traite de façon générale certains concepts abordés dans le guide de sécurité et propose des liens vers ce dernier, ainsi que vers d’autres sources.

## <a name="azure-storage"></a>Azure Storage
Azure permet de déployer et d’utiliser le stockage plus aisément que dans le cadre d’une configuration locale. Grâce au stockage Azure, vous pouvez atteindre des niveaux élevés de scalabilité et de disponibilité avec relativement peu d’effort. En plus de constituer la base des machines virtuelles Microsoft Azure et Linux, le stockage Azure peut prendre en charge de grandes applications distribuées.

Les services Azure Storage assurent le stockage d’objets blob (Blob Storage), de tables (Table Storage), de files d’attente (Queue Storage) et de fichiers (File Storage). Pour en savoir plus, consultez [Présentation du stockage Microsoft Azure](../storage/storage-introduction.md).

## <a name="best-practices"></a>Meilleures pratiques
Cet article traite les bonnes pratiques suivantes :

- Protection de l’accès :
   - Signatures d’accès partagé (SAP)
   - Disque managé
   - Contrôle d’accès en fonction du rôle

- Chiffrement du stockage :
   - Chiffrement côté client pour les données de valeur élevée
   - Azure Disk Encryption pour les machines virtuelles
   - Storage Service Encryption

## <a name="access-protection"></a>Protection de l’accès
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Utiliser la signature d’accès partagé au lieu d’une clé de compte de stockage

Dans une solution IaaS, qui exécute généralement des machines virtuelles Windows Server ou Linux, les fichiers sont protégés contre la divulgation d’informations et les menaces de falsification à l’aide de mécanismes de contrôle d’accès. Sur Windows, vous utiliseriez des [listes de contrôle d’accès (ACL)](../virtual-network/virtual-networks-acl.md), tandis que sur Linux, vous utiliseriez sans doute [chmod](https://en.wikipedia.org/wiki/Chmod). Pour l’essentiel, c’est exactement ce que vous feriez pour protéger des fichiers sur un serveur dans votre propre centre de données aujourd’hui.

PaaS est différent. Une des méthodes les plus courantes pour stocker des fichiers dans Microsoft Azure consiste à utiliser le [stockage Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Une différence entre le stockage Blob et d’autres systèmes de stockage de fichier est l’E/S de fichier et les méthodes de protection qui l’accompagnent.

Le contrôle d’accès est critique. Pour vous aider à contrôler l’accès au stockage Azure, le système génère deux clés de compte de stockage de 512 bits quand vous [créez un compte de stockage](../storage/common/storage-create-storage-account.md). Le niveau de redondance des clés vous permet d’éviter l’interruption de services pendant une rotation des clés de routine.

Les clés d’accès de stockage sont des secrets de priorité élevée et ne doivent être accessibles qu’aux personnes responsables du contrôle d’accès de stockage. Si des personnes non autorisées accèdent à ces clés, elles ont un contrôle complet du stockage et peuvent remplacer, supprimer ou ajouter des fichiers au stockage. Cela inclut les logiciels malveillants et autres types de contenu qui peuvent nuire à vos clients ou à votre organisation.

Vous devez toujours avoir un moyen de fournir l’accès aux objets dans le stockage. Pour fournir un accès plus précis, vous pouvez tirer parti de la [signature d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAP). Vous pouvez utiliser la SAP pour partager des objets spécifiques dans le stockage pendant un intervalle de temps prédéfini et avec des autorisations spécifiques. Une signature d’accès partagé vous permet de définir :

- L’intervalle pendant lequel la SAP est valide, y compris l’heure de début et l’heure d’expiration.
- Les autorisations accordées par la SAP. Par exemple, une SAP sur un objet blob peut accorder à un utilisateur des autorisations en lecture et en écriture sur cet objet blob, mais pas d’autorisations de suppression.
- Une adresse IP ou plage d’adresses IP facultative à partir de laquelle le stockage Azure accepte la signature d’accès partagé. Par exemple, vous pouvez spécifier une plage d’adresses IP appartenant à votre organisation. Cela fournit une autre mesure de sécurité à votre SAP.
- Le protocole sur lequel le stockage Azure accepte la SAP. Vous pouvez utiliser ce paramètre facultatif pour restreindre l’accès aux clients à l’aide de HTTPS.

La SAP vous permet de partager du contenu comme bon vous semble sans communiquer vos clés de compte de stockage. Utiliser systématiquement la SAP dans vos applications est un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte de stockage.

Pour plus d’informations, consultez [Présentation des signatures d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Pour plus d’informations sur les risques potentiels et sur les recommandations visant à réduire ces derniers, consultez [Bonnes pratiques lors de l’utilisation de SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Utiliser des disques managés pour les machines virtuelles

Lorsque vous choisissez [Azure Managed Disks](../storage/storage-managed-disks-overview.md), Azure gère les comptes de stockage que vous utilisez pour vos disques de machine virtuelle. Choisissez simplement le type de disque (Premium ou Standard) et la taille du disque, le stockage Azure s’occupe du reste. Vous n’avez pas à vous soucier des limites de scalabilité qui vous auraient obligé à utiliser plusieurs comptes de stockage.

Pour plus d’informations, consultez [Forum aux questions sur les disques Premium managés et non managés](../storage/storage-faq-for-disks.md).

### <a name="use-role-based-access-control"></a>Utiliser le contrôle d’accès en fonction du rôle

Nous avons vu plus haut la possibilité d’utiliser une signature d’accès partagé (SAP) pour octroyer aux autres clients un accès limité aux objets dans votre compte de stockage, sans exposer votre clé de compte de stockage. Parfois, les risques associés à une opération particulière sur votre compte de stockage l'emportent sur les avantages offerts par la signature d'accès partagé. Parfois, il est plus simple de gérer l’accès par d’autres moyens.

Une autre façon de gérer l’accès consiste à utiliser le [contrôle d’accès en fonction du rôle dans Azure](../active-directory/role-based-access-control-what-is.md) (RBAC). Avec RBAC, vous vous concentrez sur l’octroi aux employés des autorisations exactes dont ils ont besoin, selon les principes de sécurité de la séparation des privilèges et du besoin de connaître. Un trop grand nombre d’autorisations peut exposer un compte aux attaquants. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. RBAC permet de résoudre ce problème en offrant une gestion précise de l’accès pour Azure. Ces principes sont impératifs pour les organisations qui veulent appliquer des stratégies de sécurité portant sur l’accès aux données.

Vous pouvez tirer parti des rôles RBAC intégrés dans Azure pour assigner des privilèges aux utilisateurs. Envisagez l’utilisation du rôle Contributeur de comptes de stockage pour les opérateurs de cloud qui ont besoin de gérer des comptes de stockage, et du rôle Contributeur de comptes de stockage classiques pour ceux qui gèrent des comptes de stockage classiques. Concernant les opérateurs de cloud qui ont besoin de gérer des machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées, envisagez de les ajouter au rôle Collaborateur de machine virtuelle.

Les organisations qui n’appliquent aucun contrôle d’accès aux données via des fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. Cela risque d’entraîner la compromission des données en accordant à certains utilisateurs un accès non justifié à certaines données.

Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez :

- [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)
- [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md)
- [Guide de sécurité du Stockage Azure](../storage/common/storage-security-guide.md) pour plus de détails sur la sécurisation de votre compte de stockage avec RBAC

## <a name="storage-encryption"></a>Chiffrement du stockage
### <a name="use-client-side-encryption-for-high-value-data"></a>Utiliser le chiffrement côté client pour les données de valeur élevée

Avec le chiffrement côté client, vous pouvez chiffrer par programmation des données en transit avant leur chargement sur le stockage Azure et déchiffrer des données par programmation quand vous les récupérez du stockage.  Ce dispositif fournit le chiffrement des données en transit, mais également le chiffrement des données au repos.  Le chiffrement côté client est la méthode de chiffrement des données la plus sûre, mais il vous oblige à effectuer des modifications par programmation dans votre application et à mettre en place des processus de gestion des clés.

Le chiffrement côté client vous permet également d’avoir un contrôle exclusif de vos clés de chiffrement.  Vous pouvez créer et gérer vos propres clés de chiffrement.  Le chiffrement côté client utilise une technique d’enveloppe où la bibliothèque cliente de stockage Azure génère une clé de chiffrement de contenu qui est ensuite incluse dans un wrapper (chiffrée) à l’aide de la clé de chiffrement de clés. La clé de chiffrement de clés est identifiée par un identificateur de clé et peut être une paire de clés asymétriques ou une clé symétrique pouvant être gérée localement ou stockée dans [Azure Key Vault](../key-vault/key-vault-whatis.md).

Le chiffrement côté client est intégré aux bibliothèques clientes de stockage Java et .NET.  Pour plus d’informations sur le chiffrement de données dans les applications clientes et sur la génération et la gestion de vos propres clés de chiffrement, consultez [Chiffrement côté client et Azure Key Vault pour Stockage Microsoft Azure](../storage/storage-client-side-encryption.md).

### <a name="azure-disk-encryption-for-vms"></a>Azure Disk Encryption pour les machines virtuelles
Azure Disk Encryption est une fonctionnalité qui vous permet de chiffrer vos disques de machine virtuelle IaaS Windows et Linux. Azure Disk Encryption s’appuie sur la fonctionnalité standard BitLocker Windows et la fonctionnalité DM-Crypt de Linux pour assurer le chiffrement de volume du système d’exploitation et des disques de données. La solution est intégrée à Azure Key Vault, ce qui vous permet de contrôler et de gérer les clés et les secrets de chiffrement de disque dans votre abonnement Key Vault. Elle garantit également que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans votre stockage Azure.

Consultez [Azure Disk Encryption pour des machines virtuelles Windows et Linux IaaS](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Storage Service Encryption
Quand le [chiffrement du service de stockage](../storage/storage-service-encryption.md) pour le stockage de fichiers est activé, les données sont chiffrées automatiquement à l’aide du chiffrement AES-256. Microsoft gère en totalité le chiffrement, le déchiffrement et la gestion des clés. Cette fonctionnalité est disponible pour les types de redondance LRS et GRS.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté un ensemble de bonnes pratiques de sécurité du stockage Azure pour protéger vos applications mobiles et web PaaS. Pour en savoir plus sur la sécurisation de vos déploiements PaaS, consultez :

- [Sécurisation des déploiements PaaS](security-paas-deployments.md)
- [Sécurisation des applications mobiles et web PaaS à l’aide d’Azure App Services](security-paas-applications-using-app-services.md)
- [Sécurisation des bases de données PaaS dans Azure](security-paas-applications-using-sql.md)
