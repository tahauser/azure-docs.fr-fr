---
title: Activer SSL sur un cluster Azure Machine Learning Compute (MLC) | Microsoft Docs
description: Obtenir des instructions pour configurer SSL pour les appels de notation sur un cluster Azure Machine Learning Compute (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: b76fe7c0caa4a9aca76a9a3f50d1fced0ab67cba
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Activer SSL sur un cluster Azure Machine Learning Compute (MLC) 

Ces instructions vous permettent de configurer SSL pour les appels de notation sur un cluster Azure Machine Learning Compute (MLC) 

## <a name="prerequisites"></a>Prérequis 

1. Choisissez un CNAME (nom DNS) à utiliser lorsque vous passez des appels de notation en temps réel.

2. Créez un certificat *sans mot de passe* portant ce CNAME.

3. Si le certificat n’est pas déjà au format PEM, convertissez-le en PEM à l’aide d’outils tels que *openssl*.

Une fois les conditions préalables remplies, vous disposerez de deux fichiers :

* Un fichier pour le certificat (par exemple `cert.pem`)
* Un fichier pour la clé (par exemple `key.pem`)



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Configurer un certificat SSL sur un nouveau cluster ACS

À l’aide de l’interface de ligne de commande Azure Machine Learning, exécutez la commande suivante avec le commutateur `-c` pour créer un cluster ACS auquel un certificat SSL est associé :

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Configurer un certificat SSL sur un cluster ACS existant

Si vous ciblez un cluster qui a été créé sans SSL, vous pouvez ajouter un certificat à l’aide des cmdlets Azure PowerShell : 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapper le CNAME et l’adresse IP

Créez un mappage entre le CNAME que vous avez sélectionné au départ et l’adresse IP du cluster frontal (FE) en temps réel. Pour découvrir l’adresse IP du FE, exécutez la commande ci-dessous. La sortie affiche un champ nommé « publicIpAddress » qui contient l’adresse IP du cluster frontal en temps réel. Reportez-vous aux instructions de votre fournisseur DNS pour configurer un enregistrement CNAME.



## <a name="auto-detection-of-a-certificate"></a>Détection automatique d’un certificat 

Lorsque vous créez un service web en temps réel à l’aide de la commande « `az ml service create realtime` », Azure Machine Learning détecte automatiquement le protocole SSL sur le cluster et configure automatiquement l’URL de notation selon le certificat désigné. 

Pour afficher l’état du certificat, exécutez la commande suivante. Notez le préfixe « https » de l’URI de notation, ainsi que le CNAME dans le nom d’hôte. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
