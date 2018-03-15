---
title: "Sélectionner des images de machine virtuelle Linux avec Azure CLI | Microsoft Docs"
description: "Découvrez comment utiliser Azure CLI pour déterminer l’éditeur, l’offre, la référence (SKU) et la version d’images de machine virtuelle de la Place de marché."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c65ebbc8a61c13b96364dadde45bd4bca828e337
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Comment rechercher des images de machine virtuelle Linux sur la Place de marché Microsoft Azure avec Azure CLI
Cette rubrique décrit comment utiliser Azure CLI 2.0 pour rechercher des images de machine virtuelle sur la Place de marché Microsoft Azure. Ces informations permettent de spécifier une image de la Place de marché lorsque vous créez une machine virtuelle par programmation avec l’interface CLI, des modèles de Resource Manager ou d’autres outils.

Veillez à installer la dernière version d’[Azure CLI 2.0](/cli/azure/install-az-cli2) et à être connecté à un compte Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Liste des images populaires

Exécutez la commande [az vm image list](/cli/azure/vm/image#az_vm_image_list), sans l’option `--all` pour afficher la liste des images de machine virtuelle populaires sur la Place de marché Microsoft Azure. Par exemple, exécutez la commande suivante pour afficher une liste mise en cache d’images populaires dans un format de tableau :

```azurecli
az vm image list --output table
```

La sortie inclut l’URN de l’image (la valeur dans la colonne *Urn*). Lorsque vous créez une machine virtuelle avec l’une des images populaires de la Place de marché, vous pouvez également spécifier *UrnAlias* (l’alias de l’URN), tel que *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Recherche d’images spécifiques

Pour rechercher une image de machine virtuelle dans la Place de marché, utilisez la commande `az vm image list` avec l’option `--all`. L’exécution de cette version de la commande peut prendre un certain temps et retourner une sortie très longue. En général, la sortie peut être filtrée à l’aide de `--publisher` ou d’un autre paramètre. 

Par exemple, la commande suivante affiche toutes les offres Debian (n’oubliez pas que, sans le commutateur `--all`, elle effectue une recherche uniquement dans le cache local d’images courantes) :

```azurecli
az vm image list --offer Debian --all --output table 

```

Résultat partiel : 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Appliquez des filtres similaires avec les options `--location`, `--publisher` et `--sku`. Vous pouvez même établir des correspondances partielles sur un filtre, par exemple en cherchant `--offer Deb` pour trouver toutes les images Debian.

Si vous ne spécifiez pas d’emplacement particulier avec l’option `--location`, les valeurs pour l’emplacement par défaut sont retournées. (définissez un autre emplacement par défaut en exécutant la commande `az configure --defaults location=<location>`).

Par exemple, la commande suivante répertorie toutes les références SKU Debian 8 dans l’emplacement Ouest de l’Europe :

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Résultat partiel :

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Parcourir les images 
Une autre façon de trouver une image dans un emplacement consiste à exécuter successivement les commandes [az vm image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers), [az vm image list-offers](/cli/azure/vm/image#az_vm_image_list_offers) et [az vm image list-skus](/cli/azure/vm/image#az_vm_image_list_skus). Ces commandes vous permettent de déterminer les valeurs suivantes :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Ensuite, pour une référence SKU sélectionnée, vous pouvez choisir une version à déployer.

Par exemple, la commande suivante répertorie les éditeurs d’image dans l’emplacement Ouest des États-Unis :

```azurecli
az vm image list-publishers --location westus --output table
```

Résultat partiel :

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Ces informations vous permettent de trouver des offres d’un éditeur spécifique. Par exemple, si *Canonical* est un éditeur d’image dans l’emplacement Ouest des États-Unis, vous pouvez trouver ses offres en exécutant la commande `azure vm image list-offers`. Passez l’emplacement et l’éditeur comme dans l’exemple suivant :

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Output:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Nous voyons maintenant que, dans la région « West US », « Canonical » publie l’offre *UbuntuServer* sur Azure. Mais quelles sont les références SKU ? Pour obtenir ces valeurs, exécutez la commande `azure vm image list-skus` et définissez l’emplacement, l’éditeur et l’offre que vous avez découverts :

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Output:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Enfin, utilisez la commande `az vm image list` pour trouver une version spécifique de la référence SKU, par exemple *16.04-LTS* :

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Output:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```

Vous pouvez maintenant choisir précisément l’image à utiliser en prenant note de la valeur URN. Passez cette valeur avec le paramètre `--image` lorsque vous créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). N’oubliez pas que vous pouvez remplacer le numéro de version de l’URN par « latest » (dernière version). Cette version est toujours la dernière version de l’image. 

Si vous déployez une machine virtuelle avec un modèle Resource Manager, vous définissez les paramètres d’image individuellement dans les propriétés `imageReference`. Consultez la [Référence de modèle](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Afficher les propriétés du plan
Pour afficher les informations du plan d’achat d’une image, exécutez la commande [az vm image show](/cli/azure/image#az_image_show). Si la propriété `plan` dans la sortie n’est pas `null`, l’image a des conditions que vous devez accepter avant le déploiement par programmation.

Par exemple, l’image Canonical Ubuntu Server 16.04 LTS ne possède pas de conditions supplémentaires, car l’information `plan` est `null` :

```azurecli
az vm image show --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --version 16.04.201801260
```

Output:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201801260",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

L’exécution d’une commande similaire pour le RabbitMQ certifié par Bitnami image affiche les propriétés `plan` suivantes : `name`, `product`, et `publisher`. (Certaines images ont également une propriété `promotion code`.) Pour déployer cette image, consultez les sections suivantes pour accepter les conditions et activer le déploiement par programmation.

```azurecli
az vm image show --location westus --publisher bitnami --offer rabbitmq --sku rabbitmq --version 3.7.1801130730
```
Output:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1801130730",
  "location": "westus",
  "name": "3.7.1801130730",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Accepter les conditions
Pour afficher et accepter les termes du contrat de licence, utilisez la commande [az vm image accept-terms](/cli/azure/vm/image?#az_vm_image_accept_terms). Lorsque vous acceptez les termes, vous activez un déploiement par programmation dans votre abonnement. Vous devez seulement accepter les termes une fois par abonnement pour l’image. Par exemple : 

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

La sortie inclut un `licenseTextLink` aux termes du contrat de licence et indique que la valeur de `accepted` est `true` :

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2018-02-22T04:06:28.7641907Z",
  "signature": "WVIEA3LAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNKLNLWI",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Déployer à l’aide des paramètres de plan d’achat
Après avoir accepté les termes du contrat de l’image, vous pouvez déployer une machine virtuelle dans l’abonnement. Pour déployer l’image à l’aide de la commande `az vm create`, fournissez des paramètres pour le plan d’achat en plus d’un URN pour l’image. Par exemple, pour déployer une machine virtuelle avec le RabbitMQ certifié par Bitnami image :

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```



## <a name="next-steps"></a>étapes suivantes
Pour créer rapidement une machine virtuelle en utilisant les informations d’une image, consultez [Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](tutorial-manage-vm.md).