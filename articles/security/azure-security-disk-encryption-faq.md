---
title: FAQ Azure Disk Encryption | Microsoft Docs
description: "Cet article offre des réponses au forum aux questions sur Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux."
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: f3cb11bc13ad2561e62c096d840c0b8e4471d271
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-disk-encryption-faq"></a>Forum aux questions (FAQ) Azure Disk Encryption

Cet article offre des réponses au forum aux questions (FAQ) sur Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux. Pour plus d’informations sur ce service, consultez [Azure Disk Encryption pour machines virtuelles IaaS Windows et Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Questions générales
**Q :** où se trouve Azure Disk Encryption en disponibilité générale (GA) ?

**R :** Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure.

**Q :** Avec quelles autres expériences utilisateur Azure Disk Encryption est-il disponible ?

**R :** la disponibilité générale (GA) Azure Disk Encryption prend en charge les modèles Azure Resource Manager, Azure PowerShell et Azure CLI. Cela vous donne une grande flexibilité. Vous disposez de trois options différentes pour activer le chiffrement des disques sur vos machines virtuelles IaaS. Pour plus d’informations et d’instructions étape par étape sur l’expérience utilisateur disponibles dans Azure Disk Encryption, consultez les scénarios et expériences de déploiement Azure Disk Encryption.

**Q :** Combien coûte Azure Disk Encryption ?

**R :** Le chiffrement des disques de machines virtuelles avec Azure Disk Encryption est gratuit.

**Q :** quels niveaux de machines virtuelles prennent en charge Azure Disk Encryption ?

**R :** Azure Disk Encryption est disponible sur les machines virtuelles du niveau standard, y compris les machines virtuelles IaaS de série [A, D, DS, G, GS et F](https://azure.microsoft.com/pricing/details/virtual-machines/). Il est également disponible pour les machines virtuelles avec stockage premium. Il n’est pas disponible sur les machines virtuelles de niveau de base.

**Q :** quelles sont les distributions Linux prises en charge par Azure Disk Encryption ?

**R :** Azure Disk Encryption est pris en charge sur les versions et distributions de serveur Linux suivantes :

| Distribution Linux | Version | Type de volume pris en charge pour le chiffrement|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Disque de système d’exploitation et de données |
| Ubuntu | 14.04.5-DAILY-LTS | Disque de système d’exploitation et de données |
| RHEL | 7.4 | Disque de données* |
| RHEL | 7.3 | Disque de données* |
| RHEL | 7,2 | Disque de données* |
| RHEL | 6.8 | Disque de données* |
| RHEL | 6.7 | Disque de données* |
| CentOS | 7.3 | Disque de système d’exploitation et de données |
| CentOS | 7.2n | Disque de système d’exploitation et de données |
| CentOS | 6.8 | Disque de système d’exploitation et de données |
| CentOS | 7.1 | Disque de données |
| CentOS | 7.0 | Disque de données |
| CentOS | 6.7 | Disque de données |
| CentOS | 6.6 | Disque de données |
| CentOS | 6.5 | Disque de données |
| openSUSE | 13.2 | Disque de données |
| SLES | 12 SP1 | Disque de données |
| SLES | Priority:12-SP1 | Disque de données |
| SLES | HPC 12 | Disque de données |
| SLES | Priority:11-SP4 | Disque de données |
| SLES | 11 SP4 | Disque de données |

* __ADE est pris en charge pour les disques de données RHEL. L’implémentation actuelle d’ADE fonctionne avec les disques de système d’exploitation, mais elle n’est pas prise en charge conjointement. Microsoft et Red Hat fonctionnent tous les deux sur une solution conjointement prise en charge. Entre-temps, vous pouvez consulter le livre blanc ADE concernant le chiffrement des disques de système d’exploitation Linux, [disponible ici](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption).__

**Q :** comment puis-je commencer à utiliser Azure Disk Encryption ?

**A :** pour bien démarrer, lisez le livre blanc [Azure Disk Encryption pour machines virtuelles IaaS Windows](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

**Q :** Puis-je chiffrer des volumes de démarrage et de données avec Azure Disk Encryption ?

**R :** Oui, vous pouvez chiffrer des volumes de démarrage et de données pour les machines virtuelles IaaS Windows et Linux. Pour les machines virtuelles Windows, vous ne pouvez pas chiffrer les données sans d’abord chiffrer le volume du système d’exploitation. Pour les machines virtuelles Linux, vous pouvez chiffrer le volume de données sans chiffrer d’abord le volume du système d’exploitation. Une fois que vous avez chiffré le volume du système d’exploitation sous Linux, la désactivation du chiffrement sur un volume du système d’exploitation pour les machines virtuelles IaaS Linux n’est pas prise en charge.

**Q :** Azure Disk Encryption vous permet-il d’apporter vos propres fonctionnalités clés « BYOK » ?

**R :** Oui, vous pouvez fournir vos propres clés de chiffrement à clé. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

**Q :** puis-je utiliser une clé de chiffrement à clé créée par Azure ?

**R :** oui, vous pouvez utiliser Azure Key Vault pour générer la clé de chiffrement à clé pour une utilisation avec Azure Disk Encryption. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

**Q :** puis-je utiliser un service de gestion de clés local ou HSM pour sauvegarder les clés de chiffrement ?

**R :** vous ne pouvez pas utiliser le service de gestion de clés local ou HSM pour sauvegarder les clés de chiffrement avec Azure Disk Encryption. Vous pouvez uniquement utiliser le service Azure Key Vault pour sauvegarder les clés de chiffrement. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

**Q :** quelles sont les conditions requises pour configurer Azure Disk Encryption ?

**R :** il existe une configuration requise de script PowerShell. Ce script permet de créer une application Azure Disk Encryption, un nouveau coffre de clés ou en configurer un existant pour l’accès au chiffrement de disque et pour activer le chiffrement et sauvegarder les secrets et les clés. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les configurations requises ainsi que les scénarios et expériences de déploiement d’Azure Disk Encryption.

**Q :** Où puis-je obtenir plus d’informations sur l’utilisation de PowerShell pour la configuration d’Azure Disk Encryption ?

**R :** Vous pouvez consulter des articles très intéressants sur l’exécution de tâches Azure Disk Encryption de base, ainsi que des exemples de scénarios plus avancés. Pour en savoir plus sur les tâches de base, consultez [Explorer Azure Disk Encryption avec Azure PowerShell - Partie 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Pour obtenir des scénarios plus avancés, consultez [Explorer Azure Disk Encryption avec Azure PowerShell - Partie 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

**Q :** quelle version d’Azure PowerShell est prise en charge par Azure Disk Encryption ?

**R :** utilisez la dernière version du Kit SDK Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version d’[Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption n’est *pas* pris en charge par le Kit SDK Azure version 1.1.0.

> [!NOTE]
> Il est déconseillé d’utiliser l’extension de la version préliminaire d’Azure Disk Encryption pour Linux. Pour plus d’informations, consultez [Dépréciation de l’extension d’aperçu du chiffrement de disque Azure pour les machines virtuelles IaaS Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

**Q :** puis-je appliquer Azure Disk Encryption sur mon image Linux personnalisée ?

**R :** vous ne pouvez pas appliquer Azure Disk Encryption sur votre image Linux personnalisée. Nous prenons uniquement en charge les images Linux de la galerie pour les distributions prises en charge indiquées précédemment. Actuellement, nous ne prenons pas en charge les images Linux personnalisées.

**Q :** puis-je appliquer des mises à jour sur une machine virtuelle Red Hat Linux à partir d’une mise à jour Yum ?

**R :** oui, vous pouvez effectuer un correctif d’une machine virtuelle de Red Hat Linux. Pour plus d’informations, consultez [Application de mises à jour à une machine virtuelle IaaS Red Hat Azure chiffrée à l’aide de la mise à jour Yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

**Q :** Quel est le workflow de chiffrement de disque Azure recommandé pour Linux ?

**R :** Le workflow suivant est recommandé pour obtenir les meilleurs résultats sur Linux :
* Démarrer à partir de l’image de la galerie de stock non modifié correspondant à la distribution et à la version du système d’exploitation souhaitées.
* Sauvegarder tous les lecteurs montés qui seront chiffrés.  Cette sauvegarde permet la récupération en cas d’échec, par exemple, si la machine virtuelle redémarre avant la fin du chiffrement.
* Chiffrer (opération qui peut prendre plusieurs heures voire même plusieurs jours selon les caractéristiques de machine virtuelle et la taille de tous les disques de données attachés).
* Personnaliser et ajouter des logiciels à l’image selon les besoins.

Si ce workflow n’est pas possible, s’appuyer sur le [chiffrement du service de stockage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) au niveau de la couche du compte de stockage de la plateforme peut être une alternative au chiffrement de disque complet à l’aide dm-crypt.

**Q :** À quoi correspond le disque « volume Bek » ou « /mnt/azure_bek_disk » ?

**R :** Le disque « volume Bek » pour Windows ou « /mnt/azure_bek_disk» pour Linux est un volume de données local qui stocke en toute sécurité les clés de chiffrement pour les machines virtuelles Azure IaaS chiffrées.
> [!NOTE]
> Vous ne devez pas supprimer ni modifier le contenu de ce disque. Vous ne devez pas non plus démonter ce disque, car les clés de chiffrement qui y sont stockées sont nécessaires pour effectuer les opérations de chiffrement sur les machines virtuelles IaaS.

**Q :** où puis-je poser des questions ou envoyer des commentaires ?

**R :** vous pouvez poser vos questions ou envoyer vos commentaires sur le [forum d’Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a fourni les réponses aux questions les plus courantes concernant Azure Disk Encryption. Pour plus d’informations sur ce service et ses fonctionnalités, consultez les articles suivants :

- [Appliquer le chiffrement de disque dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Chiffrer une machine virtuelle Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Chiffrement des données au repos Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
