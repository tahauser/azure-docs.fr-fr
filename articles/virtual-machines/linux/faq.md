---
title: Forum aux questions sur les machines virtuelles Linux dans Azure | Microsoft Docs
description: Fournit des réponses à certaines questions courantes sur les machines virtuelles Linux créées avec le modèle de déploiement Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: 8a4d93ff12affac56c12c0eab85168c609400ee2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Forum aux questions sur les machines virtuelles Linux
Cet article traite certaines questions courantes concernant les machines virtuelles Linux créées dans Azure avec le modèle de déploiement Resource Manager. Pour la version Windows de cette rubrique, consultez les [Questions fréquences sur les machines virtuelles Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Qu’est-il possible d’exécuter sur une machine virtuelle Azure ?
Tous les abonnés peuvent exécuter des logiciels serveur sur une machine virtuelle Azure. Pour plus d’informations, consultez [Linux dans des distributions prises en charge par Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?
Chaque disque de données peut avoir une capacité allant jusqu’à 4 To (4,095 Go). Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les disques gérés Azure sont les offres de stockage sur disque recommandées à utiliser avec les machines virtuelles Azure pour le stockage persistant de données. Vous pouvez utiliser plusieurs disques managés avec chaque machine virtuelle. Ces disques offrent deux types d’options de stockage durable : les disques managés Premium et les disques managés Standard. Pour obtenir des informations sur la tarification, consultez [Tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks).

Les comptes de stockage Azure peuvent aussi fournir le stockage pour le disque du système d’exploitation et tout disque de données. Chaque disque est un fichier .vhd stocké sous la forme d’un objet blob de pages. Pour plus d’informations sur la tarification, voir [Tarification – Stockage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Comment puis-je accéder à ma machine virtuelle ?
Établissez une connexion à distance pour vous connecter à la machine virtuelle à l’aide de SSH (Secure Shell). Consultez les instructions sur la connexion [à partir de Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [à partir de Linux et Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Par défaut, SSH autorise un maximum de 10 connexions simultanées. Vous pouvez augmenter ce nombre en modifiant le fichier de configuration.

Si vous rencontrez des problèmes, consultez [Dépanner les connexions Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Est-ce que je peux utiliser le disque temporaire (/dev/sdb1) pour stocker les données ?
N’utilisez pas le disque temporaire (/dev/sdb1) pour stocker des données. Il s’agit uniquement d’un stockage temporaire. Vous risquez de perdre des données qui ne pourront pas être récupérées.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Puis-je copier ou cloner une machine virtuelle Azure ?
Oui. Pour obtenir des instructions, consultez [Création d’une copie d’une machine virtuelle Linux dans le modèle de déploiement Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Pourquoi ne vois-je pas les régions Centre et Est du Canada dans Azure Resource Manager ?
Les deux régions Centre et Est du Canada ne sont pas enregistrées automatiquement lors de la création de machines virtuelles pour des abonnements Azure existants. Cet enregistrement s’effectue automatiquement lorsqu’une machine virtuelle est déployée par le biais du portail Azure dans n’importe quelle autre région à l’aide d’Azure Resource Manager. Une fois une machine virtuelle déployée dans toute autre région Azure, les nouvelles régions doivent être disponibles pour les machines virtuelles suivantes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Puis-je ajouter une carte réseau à ma machine virtuelle après sa création ?
Oui, c’est maintenant possible. La machine virtuelle doit d’abord être arrêtée et libérée. Ensuite, vous pouvez ajouter ou supprimer une carte réseau (sauf si elle est la dernière carte réseau sur la machine virtuelle). 

## <a name="are-there-any-computer-name-requirements"></a>Existe-t-il des exigences en matière de nom d’ordinateur ?
Oui. Le nom d’ordinateur peut avoir une longueur maximale de 64 caractères. Consultez l’article [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Restrictions et règles de conventions d’affectation de noms) pour en savoir plus sur la dénomination de vos ressources.

## <a name="are-there-any-resource-group-name-requirements"></a>Existe-t-il des exigences pour le nom du groupe de ressources ?
Oui. Le nom du groupe de ressources peut avoir une longueur maximale de 90 caractères. Consultez l’article [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Restrictions et règles de conventions d’affectation de noms) pour en savoir plus sur les groupes de ressources.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quelles sont les exigences en matière de nom d’utilisateur lors de la création d’une machine virtuelle ?

Les noms d’utilisateur doivent avoir une longueur de 1 à 32 caractères.

Les noms d’utilisateur suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> propriétaire </td><td style="text-align:center"> root </td><td style="text-align:center"> serveur</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quelles sont les exigences en matière de mot de passe lors de la création d’une machine virtuelle ?
Les mots de passe doivent comporter de 6 à 72 caractères et répondre à 3 des 4 exigences de complexité suivantes :

* Avoir des minuscules
* Avoir des majuscules
* Avoir un chiffre
* Avoir un caractère spécial (correspondances Regex [\W_])

Les noms mots de passe suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
