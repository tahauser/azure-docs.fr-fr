---
title: "Ajouter des comptes de stockage Azure supplémentaires à HDInsight | Microsoft Docs"
description: "Découvrez comment ajouter des comptes de stockage Azure supplémentaires à un cluster HDInsight existant."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 72045d363516a2f16d45e3f8ee157ddd9d9242bd
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Ajouter des comptes de stockage supplémentaires à HDInsight

Découvrez comment utiliser des actions de script pour ajouter des comptes de stockage Azure à HDInsight. Les étapes décrites dans ce document permettent d’ajouter un compte de stockage à un cluster HDInsight existant basé sur Linux.

> [!IMPORTANT]
> Ce document porte sur l’ajout de stockage supplémentaire à un cluster après que celui-ci a été créé. Pour plus d’informations sur l’ajout de comptes de stockage lors de la création du cluster, voir [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka, etc](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Fonctionnement

Le script utilise les paramètres suivants :

* __Nom du compte de stockage Azure__ : nom du compte de stockage à ajouter au cluster HDInsight. Une fois le script exécuté, HDInsight est en mesure de lire et d’écrire des données stockées dans ce compte de stockage.

* __Clé du compte de stockage Azure__ : clé qui donne accès au compte de stockage.

* __p -__ (facultatif) : si ce paramètre est spécifié, la clé n’est pas chiffrée et est stockée en texte brut dans le fichier core-site.xml.

Pendant le traitement, le script effectue les opérations suivantes :

* Si le compte de stockage existe déjà dans la configuration core-site.xml du cluster, le script s’arrête et aucune action supplémentaire n’est effectuée.

* Il vérifie que le compte de stockage existe et est accessible à l’aide de la clé.

* Il chiffre la clé à l’aide des informations d’identification du cluster.

* Il ajoute le compte de stockage dans le fichier core-site.xml.

* Il arrête et redémarre les services Oozie, YARN, MapReduce2 et HDFS. L’arrêt et le redémarrage de ces services leur permettent d’utiliser le nouveau compte de stockage.

> [!WARNING]
> L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

## <a name="the-script"></a>Le script

__Emplacement du script__ : [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Conditions requises__ :

* Le script doit être appliqué sur les __nœuds principaux__.

## <a name="to-use-the-script"></a>Pour utiliser le script

Le script peut être utilisé avec le portail Azure, Azure PowerShell ou Azure CLI 1.0. Pour plus d’informations, consultez le document [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

> [!IMPORTANT]
> Lorsque vous suivez les étapes décrites dans le document de personnalisation, utilisez les informations suivantes pour appliquer ce script :
>
> * Remplacez l’URI des exemples d’actions de script par l’URI de ce script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Remplacez les paramètres des exemples par le nom du compte de stockage Azure et la clé du compte de stockage à ajouter au cluster. Si vous utilisez le portail Azure, ces paramètres doivent être séparés par une espace.
> * Vous n’avez pas besoin de marquer ce script comme __Persistant__, car il met directement à jour la configuration Ambari.

## <a name="known-issues"></a>Problèmes connus

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Comptes de stockage non affichés dans le portail ou les outils Azure

Lorsque vous affichez le cluster HDInsight dans le portail Azure, les comptes de stockage ajoutés à l’aide de cette action de script ne s’affichent pas lorsque vous sélectionnez l’entrée __Comptes de stockage__ sous __Propriétés__. Azure PowerShell et l’interface de ligne de commande Azure n’affichent pas non plus les comptes de stockage supplémentaires.

Les informations de stockage n’apparaissent pas car le script modifie uniquement la configuration core-site.xml du cluster. Ces informations ne sont pas utilisées lors de la récupération des informations du cluster à l’aide des API de gestion Azure.

Pour afficher les informations des comptes de stockage ajoutés au cluster à l’aide de ce script, utilisez l’API REST Ambari. Pour récupérer ces informations pour votre cluster, utilisez les commandes suivantes :

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Définissez `$clusterName` sur le nom du cluster HDInsight. Définissez `$storageAccountName` sur le nom du compte de stockage. Lorsque vous y êtes invité, entrez l’ID de connexion (admin) et le mot de passe du cluster.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Définissez `$PASSWORD` sur l’ID de connexion (admin) et le mot de passe du cluster. Définissez `$CLUSTERNAME` sur le nom du cluster HDInsight. Définissez `$STORAGEACCOUNTNAME` sur le nom du compte de stockage.
>
> Cet exemple utilise [curl (http://curl.haxx.se/)](http://curl.haxx.se/) et [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) pour récupérer et analyser les données JSON.

Lorsque vous utilisez cette commande, remplacez __CLUSTERNAME__ par le nom du cluster HDInsight. Remplacez __PASSWORD__ par le mot de passe de connexion HTTP du cluster. Remplacez __STORAGEACCOUNT__ par le nom du compte de stockage ajouté à l’aide de l’action de script. Les informations renvoyées par cette commande sont semblables au texte suivant :

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ce texte est un exemple de clé chiffrée, utilisée pour accéder au compte de stockage.

### <a name="unable-to-access-storage-after-changing-key"></a>Impossible d’accéder au stockage après avoir modifié la clé

Si vous modifiez la clé d’un compte de stockage, HDInsight ne peut plus accéder au compte de stockage. HDInsight utilise une copie de la clé mise en cache dans le fichier core-site.xml pour le cluster. Cette copie mise en cache doit être mise à jour pour correspondre à la nouvelle clé.

Si vous exécutez de nouveau l’action de script, la clé n’est __pas__ mise à jour, car le script vérifie s’il existe déjà une entrée pour le compte de stockage. Si une entrée existe déjà, aucune modification n’est apportée.

Pour contourner ce problème, vous devez supprimer l’entrée existante pour le compte de stockage. Procédez comme suit pour supprimer l’entrée existante :

1. Dans un navigateur web, ouvrez l’interface utilisateur web Ambari pour votre cluster HDInsight. L’URI est https://CLUSTERNAME.azurehdinsight.net. Remplacez __CLUSTERNAME__ par le nom de votre cluster.

    Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe de connexion HTTP de votre cluster.

2. Dans la liste des services située à gauche de la page, sélectionnez __HDFS__. Ensuite, sélectionnez l’onglet __Configurations__ au centre de la page.

3. Dans la __Filtrer...__, entrez une valeur de __fs.azure.account__. Des entrées sont renvoyées pour tous les comptes de stockage supplémentaires qui ont été ajoutés au cluster. Il existe deux types d’entrées : __keyprovider__ et __key__. Les deux contiennent le nom du compte de stockage dans le nom de la clé.

    Voici des exemples d’entrées pour un compte de stockage nommé __mystorage__ :

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Après avoir identifié les clés du compte de stockage à supprimer, utilisez l’icône « - » rouge à droite de l’entrée pour procéder à la suppression. Ensuite, cliquez sur le bouton __Enregistrer__ pour enregistrer vos modifications.

5. Une fois les modifications enregistrées, utilisez l’action de script pour ajouter le compte de stockage et la nouvelle valeur de clé du cluster.

### <a name="poor-performance"></a>Problèmes de performances

Si le compte de stockage se trouve dans une région différente de celle du cluster HDInsight, il se peut que vous rencontriez des problèmes de performances. Lorsque vous accédez à des données stockées dans une autre région, le trafic réseau sort du centre de données Azure régional, puis est acheminé via l’Internet public, ce qui peut entraîner de la latence.

> [!WARNING]
> L’utilisation d’un compte de stockage dans une région différente de celle du cluster HDInsight n’est pas prise en charge.

### <a name="additional-charges"></a>Frais supplémentaires

Si le compte de stockage se trouve dans une région différente de celle du cluster HDInsight, vous remarquerez peut-être des frais de sortie supplémentaires sur votre facture Azure. Des frais de sortie sont appliqués lorsque des données quittent un centre de données régional. Ces frais sont appliqués même si le trafic est destiné à un autre centre de données Azure situé dans une autre région.

> [!WARNING]
> L’utilisation d’un compte de stockage dans une région différente de celle du cluster HDInsight n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

Vous avez découvert comment ajouter des comptes de stockage Azure supplémentaires à un cluster HDInsight existant. Pour plus d’informations sur les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).
