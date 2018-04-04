---
title: 'Didacticiel : créer et gérer un groupe de machines virtuelles identiques Azure | Microsoft Docs'
description: Découvrez comment utiliser Azure CLI 2.0 pour créer un groupe de machines virtuelles identiques, et effectuer certaines tâches de gestion courantes comme le démarrage et l’arrêt d’une instance, ou la modification de la capacité du groupe identique.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dc8c58efcaeb5491eb23257e470f42a8d7cfd5c1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Didacticiel : créer et gérer un groupe de machines virtuelles identiques avec Azure CLI 2.0
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Tout au long du cycle de vie du groupe de machines virtuelles identiques, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe de machines virtuelles identiques et se connecter
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles d’instance de VM spécifiques
> * Mettre un groupe identique à l’échelle manuellement
> * Effectuer des tâches courantes de gestion de groupe identique

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser la CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant le groupe de machines virtuelles identiques. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Le nom du groupe de ressources est spécifié lorsque vous créez ou modifiez un groupe identique dans ce didacticiel.


## <a name="create-a-scale-set"></a>Créer un groupe identique
Vous créez un groupe de machines virtuelles identiques avec la commande [az vmss create](/cli/azure/vmss#az_vmss_create). L’exemple suivant crée un groupe identique nommé *myScaleSet*, et génère des clés SSH si elles n’existent pas :

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création et la configuration de l’ensemble des ressources et des instances de machine virtuelle du groupe identique prennent quelques minutes. Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Afficher les instances de machine virtuelle dans un groupe identique
Pour afficher la liste des instances de machine virtuelle dans un groupe identique, utilisez la commande [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) comme suit :

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

L’exemple suivant montre deux instances de machine virtuelle dans le groupe identique :

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


La première colonne de sortie présente un *InstanceId*. Pour afficher des informations supplémentaires sur une instance de machine virtuelle spécifique, ajoutez le paramètre `--instance-id` à la commande [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view). L’exemple suivant présente des informations sur l’instance de machine virtuelle *1* :

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Obtenir les informations de connexion
Une adresse IP publique est assignée à l’équilibreur de charge qui achemine le trafic vers les instances de machine virtuelle individuelles. Par défaut, des règles de traduction d’adresses réseau (NAT) sont ajoutées à l’équilibreur de charge Azure qui transfère le trafic de connexion distant vers chaque machine virtuelle sur un port donné. Pour vous connecter aux instances de machine virtuelle d’un groupe identique, vous devez créer une connexion distante vers une adresse IP publique et un numéro de port assignés.

Pour obtenir l’adresse et les ports à connecter aux instances de machine virtuelle dans un groupe identique, utilisez la commande [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) :

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

L’exemple de sortie suivant montre le nom de l’instance, l’adresse IP publique de l’équilibreur de charge et le numéro du port où les règles NAT transfèrent le trafic :

```bash
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH vers votre première instance de machine virtuelle. Spécifiez votre adresse IP publique et le numéro de port avec le paramètre `-p`, comme indiqué dans la commande précédente :

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Une fois connecté à l’instance de machine virtuelle, vous pouvez modifier la configuration manuelle, le cas échéant. Pour l’instant, fermez la session SSH normalement :

```bash
exit
```


## <a name="understand-vm-instance-images"></a>Comprendre les images d’instance de machine virtuelle
Lorsque vous avez créé un groupe identique au début du didacticiel, un `--image` *UbuntuLTS* a été spécifié pour les instances de machine virtuelle. La Place de marché Azure comprend de nombreuses images qui permettent de créer des instances de machine virtuelle. Pour afficher une liste des images les plus couramment utilisées, utilisez la commande [az vm image list](/cli/azure/vm/image#az_vm_image_list).

```azurecli-interactive
az vm image list --output table
```

L’exemple de sortie suivant montre les images de machine virtuelle les plus courantes dans Azure. *UrnAlias* peut être utilisé pour spécifier l’une de ces images courantes lorsque vous créez un groupe identique.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Pour afficher la liste complète, ajoutez l’argument `--all`. Vous pouvez également utiliser `--publisher` ou `–-offer` pour filtrer la liste d’images. Dans l’exemple suivant, la liste est filtrée en fonction de toutes les images dont l’offre correspond à *CentOS* :

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

La sortie condensée suivante montre plusieurs images CentOS 7.3 disponibles :

```azurecli-interactive 
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Pour déployer un groupe identique qui utilise une image spécifique, servez-vous de la valeur de la colonne *Urn*. Lorsque vous spécifiez l’image, le numéro de version de l’image peut être remplacé par la valeur *latest* qui sélectionne la dernière version de la distribution. Dans l’exemple suivant, l’argument `--image` permet de spécifier la version la plus récente d’une image CentOS 7.3.

Comme la création et la configuration de toutes les ressources et les instances de machine virtuelle du groupe identique prennent quelques minutes, il est inutile de déployer le groupe identique suivant :

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Comprendre les tailles d’instance de machine virtuelle
Une taille d’instance de machine virtuelle, ou *référence SKU*, détermine la quantité de ressources de calcul, comme l’UC, le GPU et la mémoire, qui sont mises à la disposition de l’instance de machine virtuelle. Les instances de machine virtuelle doivent être correctement dimensionnées en fonction de la charge de travail attendue.

### <a name="vm-instance-sizes"></a>Taille des instances de machine virtuelle
Le tableau suivant classe les tailles courantes de machine virtuelle en fonction des cas d’usage.

| type                     | Tailles courantes           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Usage général](../virtual-machines/linux/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Ratio processeur/mémoire équilibré. Idéale pour le développement/test et pour les petites et moyennes applications et solutions de données.  |
| [Optimisé pour le calcul](../virtual-machines/linux/sizes-compute.md)   | Fs, F             | Ratio processeur/mémoire élevé. Convient pour les applications au trafic moyen, les appliances réseau et les processus de traitement par lots.        |
| [Mémoire optimisée](../virtual-machines/linux/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Ratio mémoire/cœur élevé. Idéale pour les bases de données relationnelles, les caches moyens à grands et l’analytique en mémoire.                 |
| [Optimisé pour le stockage](../virtual-machines/linux/sizes-storage.md)      | Ls                | Débit de disque et E/S élevés. Idéale pour les bases de données NoSQL, SQL et Big Data.                                                         |
| [GPU](../virtual-machines/linux/sizes-gpu.md)          | NV, NC            | Machines virtuelles spécialisées conçues pour les opérations graphiques lourdes et la retouche vidéo.       |
| [Hautes performances](../virtual-machines/linux/sizes-hpc.md) | H, A8-11          | Nos machines virtuelles dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Rechercher des tailles d’instance de machine virtuelle disponibles
Pour afficher la liste des tailles d’instance de machine virtuelle disponibles dans une région particulière, utilisez la commande [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

La sortie est semblable à l’exemple condensé suivant qui présente les ressources assignées à chaque taille de machine virtuelle :

```azurecli-interactive
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Créer un groupe identique avec une taille d’instance de machine virtuelle spécifique
Lorsque vous avez créé un groupe identique au début de ce didacticiel, une valeur de référence SKU de la machine virtuelle par défaut *Standard_D1_v2* a été fournie pour les instances de machine virtuelle. Vous pouvez spécifier une taille d’instance de machine virtuelle différente en fonction de la sortie de la commande [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes). L’exemple suivant crée un groupe identique avec le paramètre `--vm-sku` afin de spécifier une taille d’instance de machine virtuelle *Standard_F1*. Comme la création et la configuration de toutes les ressources et les instances de machine virtuelle du groupe identique prennent quelques minutes, il est inutile de déployer le groupe identique suivant :

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un groupe identique
Lorsque vous avez créé un groupe identique au début de ce didacticiel, deux instances de machine virtuelle ont été déployées par défaut. Vous pouvez spécifier le paramètre `--instance-count` avec la commande [az vmss create](/cli/azure/vmss#az_vmss_create) pour modifier le nombre d’instances créées avec un groupe identique. Pour augmenter ou diminuer le nombre d’instances de machine virtuelle dans votre groupe identique existant, vous pouvez modifier manuellement la capacité. Le groupe identique crée ou supprime le nombre requis d’instances de machine virtuelle, puis configure l’équilibreur de charge pour distribuer le trafic.

Pour augmenter ou diminuer manuellement le nombre d’instances de machine virtuelle dans le groupe identique, utilisez la commande [az vmss scale](/cli/azure/vmss#az_vmss_scale). L’exemple suivant fixe le nombre d’instances de machine virtuelle présentes dans votre groupe identique à *3* :

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Quelques minutes sont nécessaires pour mettre à jour la capacité de votre groupe identique. Pour afficher le nombre d’instances désormais présentes dans le groupe identique, utilisez [az vmss show](/cli/azure/vmss#az_vmss_show) et interrogez *sku.capacity* :

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Tâches de gestion courantes
Vous pouvez à présent créer un groupe identique, obtenir les informations de connexion et vous connecter aux instances de machine virtuelle. Vous avez appris à utiliser une autre image de système d’exploitation pour vos instances de machine virtuelle, sélectionner une autre taille de machine virtuelle et modifier manuellement le nombre d’instances. Dans le cadre de la gestion quotidienne, vous devrez peut-être arrêter, démarrer ou redémarrer les instances de machine virtuelle dans votre groupe identique.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Arrêter et libérer des instances de machine virtuelle dans un groupe identique
Pour arrêter une ou plusieurs instances de machine virtuelle dans un groupe identique, utilisez la commande [az vmss stop](/cli/azure/vmss#az_vmss_stop). Le paramètre `--instance-ids` vous permet de spécifier une ou plusieurs instances de machine virtuelle à arrêter. Si vous ne spécifiez pas d’ID d’instance, toutes les instances de machine virtuelle dans le groupe identique sont arrêtées. L’exemple suivant présente l’arrêt de l’instance *1* :

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

Les instances de machine virtuelle arrêtées restent allouées et continuent d’occasionner des frais de calcul. Si vous préférez que les instances de machine virtuelle soient libérées et n’occasionnent que des frais de stockage, utilisez la commande [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). L’exemple suivant présente l’arrêt et la libération de l’instance *1* :

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Démarrer les instances de machine virtuelle dans un groupe identique
Pour démarrer une ou plusieurs instances de machine virtuelle dans un groupe identique, utilisez la commande [az vmss start](/cli/azure/vmss#az_vmss_start). Le paramètre `--instance-ids` vous permet de spécifier une ou plusieurs instances de machine virtuelle à démarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les instances de machine virtuelle dans le groupe identique sont démarrées. L’exemple suivant montre le démarrage de l’instance *1* :

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Redémarrer les instances de machine virtuelle dans un groupe identique
Pour redémarrer une ou plusieurs instances de machine virtuelle dans un groupe identique, utilisez la commande [az vmss restart](/cli/azure/vmss#az_vm_restart). Le paramètre `--instance-ids` vous permet de spécifier une ou plusieurs instances de machine virtuelle à redémarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les instances de machine virtuelle dans le groupe identique sont redémarrées. L’exemple suivant présente le redémarrage de l’instance *1* :

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous supprimez un groupe de ressources, toutes les ressources qu’il contient, comme les instances de machine virtuelle, le réseau virtuel et les disques, sont également supprimées. Le paramètre `--no-wait` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine. Le paramètre `--yes` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à effectuer plusieurs tâches de création et de gestion de base concernant le groupe identique avec Azure CLI 2.0 :

> [!div class="checklist"]
> * Créer un groupe de machines virtuelles identiques et se connecter
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles de machine virtuelle spécifiques
> * Mettre un groupe identique à l’échelle manuellement
> * Effectuer des tâches courantes de gestion de groupe identique

Passez au didacticiel suivant pour en savoir plus sur les disques de groupe identique.

> [!div class="nextstepaction"]
> [Utiliser des disques de données avec des groupes identiques](tutorial-use-disks-cli.md)
