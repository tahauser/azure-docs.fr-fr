---
title: Calcul Azure - Extension de diagnostic Linux | Microsoft Docs
description: "Comment configurer l’extension de diagnostic Linux Azure pour collecter des métriques et consigner les événements provenant de machines virtuelles Linux qui s’exécutent dans Azure."
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.openlocfilehash: 1eae6d302827c977b9258174dec68fd8f3009a11
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2018
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Utilisez l’extension de diagnostic Linux pour surveiller les métriques et les journaux

Ce document décrit la version 3.0 et les versions ultérieures de l’extension de diagnostic Linux.

> [!IMPORTANT]
> Pour plus d’informations sur la version 2.3 et les versions antérieures, consultez [ce document](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduction

L’extension de diagnostic Linux aide l’utilisateur à surveiller l’intégrité d’une machine virtuelle Linux s’exécutant sur Microsoft Azure. Elle présente les fonctionnalités suivantes :

* Elle collecte des métriques de performances du système auprès de la machine virtuelle et les stocke dans une table spécifique d’un compte de stockage désigné.
* Elle récupère les journaux d’événements Syslog et les stocke dans une table spécifique du compte de stockage désigné.
* Elle permet aux utilisateurs de personnaliser les métriques des données qui sont collectées et chargées.
* Elle permet aux utilisateurs de personnaliser les fonctions Syslog et les niveaux de gravité des événements qui sont collectés et chargés.
* Elle permet aux utilisateurs de télécharger les fichiers journaux spécifiés dans la table de stockage désignée.
* Elle prend en charge l’envoi des métriques et des événements des journaux à des points de terminaison EventHub arbitraires et à des objets blob au format JSON dans le compte de stockage désigné.

Cette extension fonctionne avec les deux modèles de déploiement d’Azure.

## <a name="installing-the-extension-in-your-vm"></a>Installation de l’extension dans votre machine virtuelle

Vous pouvez activer cette extension via des applets de commande Azure PowerShell, des scripts Azure CLI ou des modèles de déploiement Azure. Pour plus d’informations, consultez [Fonctionnalités des extensions](./extensions-features.md).

Le portail Azure ne peut pas être utilisé pour activer ou configurer l’extension de diagnostic Linux 3.0. Au lieu de cela, il installe et configure la version 2.3. Les graphes et les alertes du portail Azure fonctionnent avec les données provenant des deux versions de l’extension.

Ces instructions d’installation et un [exemple de configuration téléchargeable](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configurent l’extension de diagnostic Linux 3.0 pour :

* Capturer et stocker les mêmes mesures que celles fournies par l’extension de diagnostic Linux 2.3
* Capturer un ensemble utile de métriques du système de fichiers, nouveau dans l’extension de diagnostic Linux 3.0
* Capturer la collection Syslog par défaut activée par l’extension de diagnostic Linux 2.3
* Permettre l’utilisation du portail Azure pour les graphes et les alertes sur des métriques de machine virtuelle.

La configuration téléchargeable est seulement un exemple. Modifiez-la selon vos besoins.

### <a name="prerequisites"></a>Prérequis

* **Agent Azure Linux version 2.2.0 ou ultérieure**. La plupart des images de la galerie Linux de machines virtuelles Azure incluent la version 2.2.7 ou ultérieure. Exécutez `/usr/sbin/waagent -version` pour vérifier la version installée sur la machine virtuelle. Si la machine virtuelle exécute une version antérieure de l’agent invité, suivez [ces instructions](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) pour le mettre à jour.
* **Azure CLI**. [Configurez l’environnement Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) sur votre machine.
* Si vous n’avez pas déjà la commande wget : exécutez `sudo apt-get install wget`.
* Un abonnement Azure et un compte de stockage existants sont utilisés pour stocker les données.

### <a name="sample-installation"></a>Exemple d’installation

Renseignez les paramètres corrects sur les trois premières lignes puis exécutez ce script en tant que root :

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

L’URL de l’exemple de configuration et son contenu sont susceptibles d’être modifiés. Téléchargez une copie du fichier JSON des paramètres du portail et personnalisez-la en fonction de vos besoins. Les modèles ou l’automation que vous construisez doit utiliser votre propre copie au lieu de télécharger cette URL à chaque fois.

### <a name="updating-the-extension-settings"></a>Mise à jour les paramètres de l’extension

Une fois que vous avez modifié vos paramètres protégés ou publics, déployez-les sur la machine virtuelle en exécutant la même commande. Si quelque chose a changé dans les paramètres, les paramètres mis à jour sont envoyés à l’extension. L’extension de diagnostic Linux recharge la configuration et redémarre automatiquement.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migration depuis des versions antérieures de l’extension

La dernière version de l’extension est **3.0**. **Les anciennes versions (2.x) sont dépréciées, et leur publication peut cesser le 31 juillet 2018 ou après**.

> [!IMPORTANT]
> Cette extension introduit des changements majeurs à la configuration de l’extension. Une telle modification a été apportée pour améliorer la sécurité de l’extension. Par conséquent, la compatibilité descendante avec les versions 2.x n’a pas pu être conservée. En outre, l’éditeur d’extension pour cette extension est différent de l’éditeur pour les versions 2.x.
>
> Pour migrer de 2.x vers cette nouvelle version de l’extension, vous devez désinstaller l’ancienne extension (sous l’ancien nom de l’éditeur) puis installer la version 3 de l’extension.

Recommandations :

* Installez l’extension avec la mise à niveau automatique de version mineure activée.
  * Sur les machines virtuelles du modèle de déploiement classique, spécifiez « 3.* » comme version si vous installez l’extension via Azure XPLAT CLI ou Powershell.
  * Sur les machines virtuelles du modèle de déploiement Azure Resource Manager, incluez '"autoUpgradeMinorVersion": true' dans le modèle de déploiement des machines virtuelles.
* Utilisez un compte de stockage nouveau ou différent pour l’extension de diagnostic Linux 3.0. Il existe plusieurs petites incompatibilités entre l’extension de diagnostic Linux 2.3 et l’extension de diagnostic Linux 3.0, qui rendent problématique le partage d’un compte :
  * L’extension de diagnostic Linux 3.0 stocke les événements Syslog dans une table avec un nom différent.
  * Les chaînes counterSpecifier pour les métriques `builtin` diffèrent dans l’extension de diagnostic Linux 3.0.

## <a name="protected-settings"></a>Paramètres protégés

Cet ensemble d’informations de configuration contient des informations sensibles qui doivent être protégées d’une exposition publique, par exemple les informations d’identification de stockage. Ces paramètres sont transmis et stockés par l’extension sous une forme chiffrée.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

NOM | Valeur
---- | -----
storageAccountName | Nom du compte de stockage dans lequel les données sont écrites par l’extension.
storageAccountEndPoint | (facultatif) Le point de terminaison identifiant le cloud dans lequel se trouve le compte de stockage. Si ce paramètre est absent, l’extension de diagnostic Linux utilise par défaut le cloud public Azure, `https://core.windows.net`. Pour utiliser un compte de stockage Azure Allemagne, Azure Government ou Azure Chine, définissez cette valeur en conséquence.
storageAccountSasToken | [Jeton SAS de compte](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pour les services Blob et Table (`ss='bt'`), applicables aux conteneurs et aux objets (`srt='co'`), qui accorde les autorisations de créer, répertorier, mettre à jour et écrire (`sp='acluw'`). *N’incluez pas* le point d’interrogation ( ?) du début.
mdsdHttpProxy | (facultatif) Informations du proxy HTTP nécessaires pour permettre à l’extension de se connecter au compte de stockage et au point de terminaison spécifiés.
sinksConfig | (facultatif) Détails des destinations alternatives auxquelles les métriques et les événements peuvent délivrés. Les détails spécifiques de chaque récepteur de données pris en charge par l’extension sont traités dans les sections qui suivent.


> [!NOTE]
> Lors du déploiement de l’extension avec un modèle de déploiement Azure, le compte de stockage et le jeton SAS doivent être créés au préalable et ensuite transmis au modèle. Vous ne pouvez pas déployer une machine virtuelle, un compte de stockage, ni configurer l’extension d’un modèle unique. La création d’un jeton SAS dans un modèle n’est pas prise en charge actuellement.

Vous pouvez facilement construire le jeton SAS nécessaire via le portail Azure.

1. Sélectionnez le compte de stockage à usage général dans lequel vous voulez que l’extension écrive
1. Sélectionnez « Signature d’accès partagé » dans la partie Paramètres du menu de gauche
1. Rendez les sections appropriées comme décrit précédemment
1. Cliquez sur le bouton « Générer une signature d’accès partagé ».

![image](./media/diagnostic-extension/make_sas.png)

Copiez la signature SAS générée dans le champ storageAccountSasToken et supprimez le premier d’interrogation (« ? ») du début.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Cette section facultative définit les autres destinations auxquelles l’extension envoie les informations collectées. Le tableau « récepteur » contient un objet pour chaque récepteur de données supplémentaire. L’attribut « type » détermine les autres attributs de l’objet.

Élément | Valeur
------- | -----
Nom | Chaîne utilisée pour référencer ce récepteur ailleurs dans la configuration de l’extension.
Type | Type du récepteur défini. Détermine les autres valeurs (le cas échéant) dans les instances de ce type.

La version 3.0 de l’extension de diagnostic Linux prend en charge deux types de récepteur : EventHub et JsonBlob.

#### <a name="the-eventhub-sink"></a>Récepteur EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

L’entrée « sasURL » contient l’URL complète, incluant un jeton SAS, pour l’Event Hub sur lequel les données doivent être publiées. L’extension de diagnostic Linux nécessite une signature SAS nommant une stratégie qui permet l’envoi de revendication. Exemple :

* Créer un espace de noms Event Hub appelé `contosohub`
* Créer un Event Hub dans l’espace de noms appelé `syslogmsgs`
* Créer une stratégie d’accès partagé sur l’Event Hub nommé `writer` qui permet l’envoi de revendication

Si vous avez créé une signature SAS valable jusqu’au 1er janvier 2018 à minuit (UTC), la valeur de sasURL doit être :

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Pour plus d’informations sur la génération de jetons SAS pour les Event Hubs, consultez [cette page web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>Récepteur JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Les données dirigées vers un récepteur JsonBlob sont stockées dans des objets blob dans Stockage Azure. Chaque instance de l’extension de diagnostic Linux crée un objet blob toutes les heures pour chaque nom de récepteur. Chaque objet blob contient toujours un tableau JSON syntaxiquement valide de l’objet. Les nouvelles entrées sont ajoutées au tableau de manière atomique. Les objets blob sont stockés dans un conteneur du même nom que le récepteur. Les règles du Stockage Azure pour les noms de conteneur d’objets blob s’appliquent aux noms des récepteurs JsonBlob : entre 3 et 63 caractères ASCII alphanumériques minuscules ou des tirets.

## <a name="public-settings"></a>Paramètres publics

Cette structure contient différents blocs de paramètres qui contrôlent les informations collectées par l’extension. Chaque paramètre est facultatif. Si vous spécifiez `ladCfg`, vous devez aussi spécifier `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Élément | Valeur
------- | -----
StorageAccount | Nom du compte de stockage dans lequel les données sont écrites par l’extension. Doit être le même nom que celui spécifié dans les [paramètres protégés](#protected-settings).
mdsdHttpProxy | (facultatif) Comme dans les [paramètres protégés](#protected-settings). La valeur publique est remplacée par la valeur privée si celle-ci est définie. Placez les paramètres de proxy qui contiennent un secret, comme un mot de passe, dans le [paramètres protégés](#protected-settings).

Les éléments restants sont décrits en détail dans les sections suivantes.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Cette structure facultative contrôle la collecte des métriques et des journaux pour les délivrer au service des métriques Azure et à d’autres récepteurs. Vous devez spécifier `performanceCounters` ou `syslogEvents`, ou les deux. Vous devez spécifier la structure `metrics`.

Élément | Valeur
------- | -----
eventVolume | (facultatif) Contrôle le nombre de partitions créées dans la table de stockage. Doit être `"Large"`, `"Medium"` ou `"Small"`. Si elle n’est pas spécifiée, la valeur par défaut est `"Medium"`.
sampleRateInSeconds | (facultatif) Intervalle par défaut entre les collectes des métriques brutes (non agrégées). L’échantillonnage le plus petit pris en charge de 15 secondes. Si elle n’est pas spécifiée, la valeur par défaut est `15`.

#### <a name="metrics"></a>Mesures

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Élément | Valeur
------- | -----
ResourceId | L’ID de ressource Azure Resource Manager de la machine virtuelle ou du groupe de machines virtuelles identiques auquel la machine virtuelle appartient. Ce paramètre doit également être spécifié si un récepteur JsonBlob est utilisé dans la configuration.
scheduledTransferPeriod | La fréquence à laquelle les métriques agrégées doivent être calculées et transférées vers les métriques Azure, exprimée sous la forme d’un intervalle de temps IS 8601. La périodicité de transfert la plus petite est de 60 secondes, c’est-à-dire PT1M. Vous devez spécifier au moins une scheduledTransferPeriod.

Des échantillons des métriques spécifiées dans la section performanceCounters sont collectés toutes les 15 secondes ou selon le taux d’échantillonnage défini explicitement pour le compteur. Si plusieurs fréquences scheduledTransferPeriod apparaissent (comme dans l’exemple), chaque agrégation est calculée indépendamment.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Cette section facultative contrôle la collecte des métriques. Les échantillons bruts sont agrégés pour chaque [scheduledTransferPeriod](#metrics) pour produire ces valeurs :

* mean
* minimum
* maximum
* dernière valeur collectée
* nombre d’échantillons bruts utilisés pour calculer l’agrégation

Élément | Valeur
------- | -----
sinks | (facultatif) Liste séparée par des virgules des noms des récepteurs auquel l’extension de diagnostic Linux envoie les résultats des métriques agrégées. Toutes les métriques agrégées sont publiées sur chaque récepteur répertorié. Consultez [sinksConfig](#sinksconfig). Exemple : `"EHsink1, myjsonsink"`.
Type | Identifie le fournisseur réel de la mesure.
class | Avec « counter », identifie la métrique spécifique au sein de l’espace de noms du fournisseur.
counter | Avec « class », identifie la métrique spécifique au sein de l’espace de noms du fournisseur.
counterSpecifier | Identifie la métrique spécifique au sein de l’espace de noms des métriques Azure.
condition | (facultatif) Sélectionne une instance spécifique de l’objet auquel s’applique la métrique ou sélectionne l’agrégation sur toutes les instances de cet objet. Pour plus d’informations, consultez les [ `builtin` définitions des métriques](#metrics-supported-by-builtin).
sampleRate | Intervalle IS 8601 qui définit la fréquence à laquelle des échantillons bruts sont collectés pour cette métrique. S’il n’est pas défini, l’intervalle de collecte est défini par la valeur de [sampleRateInSeconds](#ladcfg). L’échantillonnage le plus court pris en charge est de 15 secondes (PT15S).
unité | Doit être une des chaînes suivantes : « Count », « Bytes », « Seconds », « Percent », « CountPerSecond », « BytesPerSecond », « Millisecond ». Définit l’unité pour la métrique. Les consommateurs des données collectées attendent des valeurs de données collectées correspondant à cette unité. L’extension de diagnostic Linux ignore ce champ.
displayName | Étiquette (dans la langue spécifiée par les paramètres régionaux associés) à attacher à ces données dans les métriques Azure. L’extension de diagnostic Linux ignore ce champ.

counterSpecifier est un identificateur arbitraire. Les consommateurs de métriques, comme les fonctionnalités de graphe et d’alerte du portail Azure, utilisent counterSpecifier comme « clé » qui identifie une métrique ou une instance de métrique. Pour les métriques `builtin`, nous vous recommandons d’utiliser des valeurs pour counterSpecifier qui commencent par `/builtin/`. Si vous collectez une instance spécifique d’une métrique, nous vous recommandons d’attacher l’identificateur de l’instance à la valeur de counterSpecifier. Voici quelques exemples :

* `/builtin/Processor/PercentIdleTime` : temps d’inactivité moyen pour tous les processeurs virtuels
* `/builtin/Disk/FreeSpace(/mnt)` : Espace libre pour le système de fichiers /mnt
* `/builtin/Disk/FreeSpace` : Espace libre moyen pour tous les systèmes de fichiers montés

Ni l’extension de diagnostic Linux ni le portail Azure n’attendent un modèle particulier pour la valeur de counterSpecifier. Soyez cohérent dans la façon dont vous construisez les valeurs de counterSpecifier.

Quand vous spécifiez `performanceCounters`, l’extension de diagnostic Linux écrit toujours les données dans une table de Stockage Azure. Les mêmes données peuvent être écrites dans des objets blob JSON et/ou des Event Hubs, mais vous ne pouvez pas désactiver le stockage des données dans une table. Toutes les instances de l’extension de diagnostic configuré pour utiliser le même nom et le même point de terminaison de compte de stockage ajoutent leurs mesures et leurs journaux à la même table. Si un trop grand nombre de machines virtuelles écrivent dans la même partition de table, Azure peut limiter les écritures sur cette partition. Le paramètre eventVolume permet de répartir les entrées entre 1 (Small), 10 (Medium) ou 100 (Large) partitions différentes. En règle générale, « Medium » est suffisant pour que le trafic ne soit pas limité. La fonctionnalité des métriques Azure du portail Azure utilise les données de cette table pour produire des graphes ou déclencher des alertes. Le nom de la table est la concaténation des chaînes suivantes :

* `WADMetrics`
* « ScheduledTransferPeriod » pour les valeurs agrégées stockées dans la table
* `P10DV2S`
* Une date, sous la forme « AAAAMMJJ », qui change tous les 10 jours

Exemples : `WADMetricsPT1HP10DV2S20170410` et `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Cette section facultative contrôle la collecte des événements des journaux de Syslog. Si la section est omise, les événements Syslog ne sont pas capturés du tout.

La collection syslogEventConfiguration a une entrée pour chaque fonction Syslog intéressante. Si minSeverity est défini sur « NONE » pour une fonction donnée ou si cette fonction n’apparaît pas du tout dans l’élément, aucun événement de cette fonction n’est capturé.

Élément | Valeur
------- | -----
sinks | Une liste séparée par des virgules de noms de récepteurs sur lesquels des événements de journaux sont publiés. Tous les événements de journaux correspondant aux restrictions de syslogEventConfiguration sont publiés sur chaque récepteur répertorié. Exemple : « EHforsyslog »
facilityName | Nom de fonction Syslog (comme « LOG\_USER » ou « LOG\_LOCAL0 »). Consultez la section « facility » de la [page du manuel Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) pour obtenir la liste complète.
minSeverity | Niveau de gravité Syslog (comme « LOG\_ERR » ou « LOG\_INFO »). Consultez la section « level » de la [page du manuel Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) pour obtenir la liste complète. L’extension capture les événements envoyés à la fonction à un niveau supérieur ou égal au niveau spécifié.

Quand vous spécifiez `syslogEvents`, l’extension de diagnostic Linux écrit toujours les données dans une table de Stockage Azure. Les mêmes données peuvent être écrites dans des objets blob JSON et/ou des Event Hubs, mais vous ne pouvez pas désactiver le stockage des données dans une table. Le comportement de partitionnement pour cette table est identique à celui décrit pour `performanceCounters`. Le nom de la table est la concaténation des chaînes suivantes :

* `LinuxSyslog`
* Une date, sous la forme « AAAAMMJJ », qui change tous les 10 jours

Exemples : `LinuxSyslog20170410` et `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Cette section facultative contrôle l’exécution des requêtes [OMI](https://github.com/Microsoft/omi) arbitraires.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Élément | Valeur
------- | -----
namespace | (facultatif) L’espace de noms OMI dans lequel la requête doit être exécutée. Si elle n’est pas spécifiée, la valeur par défaut est « root/scx », implémentée par les [fournisseurs multiplateformes System Center](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | Requête OMI à exécuter.
table | (facultatif) La table de stockage Azure, dans le compte de stockage désigné (consultez [Paramètres protégés](#protected-settings)).
frequency | (facultatif) Le nombre de secondes entre chaque exécution de la requête. La valeur par défaut est 300 (5 minutes). La valeur minimale est de 15 secondes.
sinks | (facultatif) Une liste séparée par des virgules de noms de récepteurs supplémentaires sur lesquels les résultats des échantillons bruts des métriques doivent être publiés. Aucune agrégation de ces échantillons bruts n’est calculée par l’extension ou par les métriques Azure.

Vous devez spécifier « table » ou « sinks », ou les deux.

### <a name="filelogs"></a>fileLogs

Contrôle la capture des fichiers journaux. L’extension de diagnostic Linux capture les nouvelles lignes de texte au fil de leur écriture dans le fichier et les écrit dans des lignes de la table et/ou dans les récepteurs spécifiés (JsonBlob ou EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Élément | Valeur
------- | -----
file | Nom du chemin complet du fichier journal à surveiller et à capturer. Le nom du chemin doit désigner un seul fichier. Il ne peut pas nommer un répertoire ni contenir des caractères génériques.
table | (facultatif) La table Stockage Azure, dans le compte de stockage désigné (tel que spécifié dans la configuration protégée), dans laquelle les nouvelles lignes de la « fin » du fichier sont écrites.
sinks | (facultatif) Une liste séparée par des virgules des noms des récepteurs supplémentaires auxquels les lignes des journaux sont envoyées.

Vous devez spécifier « table » ou « sinks », ou les deux.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métriques prises en charge par le fournisseur intégré

Le fournisseur de métriques intégré est une source de métriques parmi les plus intéressantes pour un large éventail d’utilisateurs. Ces métriques se répartissent en cinq classes principales :

* Processeur
* Mémoire
* Réseau
* Filesystem
* Disque

### <a name="builtin-metrics-for-the-processor-class"></a>métriques intégrées pour la classe Processeur

La classe de métriques Processeur fournit des informations sur l’utilisation du processeur dans la machine virtuelle. Lors de l’agrégation de pourcentages, le résultat est la moyenne pour toutes les UC. Dans une machine virtuelle à deux processeurs virtuels, si un processeur virtuel a été occupé à 100 % et que l’autre a été inactif à 100 %, la valeur de PercentIdleTime s’élèverait à 50. Si chaque processeur virtuel a été occupé à 50 % pendant la même période, le résultat serait également 50. Dans une machine virtuelle à quatre processeurs virtuels, si un processeur virtuel a été occupé à 100 % et que les autres ont été inactifs, la valeur de PercentIdleTime s’élèverait à 75.

counter | Signification
------- | -------
PercentIdleTime | Pourcentage de temps de la fenêtre d’agrégation pendant lequel les processeurs ont exécuté la boucle d’inactivité du noyau
percentProcessorTime | Pourcentage de temps passé à exécuter un thread actif
PercentIOWaitTime | Pourcentage de temps passé à attendre la fin d’opérations d’E/S
PercentInterruptTime | Pourcentage de temps passé à exécuter des interruptions matérielles/logicielles et des appels DPC (appels de procédure différés)
PercentUserTime | Pourcentage de temps passé pour l’utilisateur à une priorité supérieure à la normale, relativement au temps d’activité de la fenêtre d’agrégation
PercentNiceTime | Pourcentage de temps passé à une priorité abaissée (commande nice), relativement au temps d’activité
PercentPrivilegedTime | Pourcentage de temps passé en mode privilégié (noyau), relativement au temps d’activité

La somme des quatre premiers compteurs doit être de 100 %. La somme des trois derniers compteurs est également de 100 %. Ils subdivisent la somme de PercentProcessorTime, PercentIOWaitTime et PercentInterruptTime.

Pour obtenir une seule métrique agrégée pour tous les processeurs, définissez `"condition": "IsAggregate=TRUE"`. Pour obtenir une métrique pour un processeur spécifique, par exemple le deuxième processeur logique d’une machine virtuelle à quatre processeurs virtuels, définissez `"condition": "Name=\\"1\\""`. Les numéros des processeurs logiques sont dans la plage `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métriques intégrées pour la classe Mémoire

La classe de métriques Mémoire fournit des informations sur l’utilisation, la pagination et les échanges de la mémoire.

counter | Signification
------- | -------
AvailableMemory | Mémoire physique disponible en Mio
PercentAvailableMemory | Mémoire physique disponible sous forme de pourcentage de la mémoire totale
UsedMemory | Mémoire physique utilisée (Mio)
PercentUsedMemory | Mémoire physique utilisée sous forme de pourcentage de la mémoire totale
PagesPerSec | Pagination totale (lecture/écriture)
PagesReadPerSec | Pages lues dans le magasin de stockage (fichier d’échange, fichier programme, fichier mappé, etc.)
PagesWrittenPerSec | Pages écrites dans le magasin de stockage (fichier d’échange, fichier mappé, etc.)
AvailableSwap | Espace d’échange non utilisé (Mio)
PercentAvailableSwap | Espace d’échange non utilisé sous forme de pourcentage de l’espace d’échange total
UsedSwap | Espace d’échange utilisé (Mio)
PercentUsedSwap | Espace d’échange utilisé sous forme de pourcentage de l’espace d’échange total

Cette classe de métriques n’a qu’une seule instance. L’attribut « condition » n’a pas de paramètre utile et doit être omis.

### <a name="builtin-metrics-for-the-network-class"></a>métriques intégrées pour la classe Réseau

La classe de métriques Réseau fournit des informations sur l’activité réseau sur une interface réseau individuelle depuis le démarrage. L’extension de diagnostic Linux n’expose pas de métriques de la bande passante, qui peuvent être récupérées à partir des métriques de l’hôte.

counter | Signification
------- | -------
BytesTransmitted | Nombre total d’octets envoyés depuis le démarrage
BytesReceived | Nombre total d’octets reçus depuis le démarrage
BytesTotal | Nombre total d’octets envoyés ou reçus depuis le démarrage
PacketsTransmitted | Nombre total de paquets envoyés depuis le démarrage
PacketsReceived | Nombre total de paquets reçus depuis le démarrage
TotalRxErrors | Nombre d’erreurs de réception depuis le démarrage
TotalTxErrors | Nombre d’erreurs de transmission depuis le démarrage
TotalCollisions | Nombre de collisions signalées par les ports réseau depuis le démarrage

 Bien que cette classe soit instanciée, l’extension de diagnostic Linux ne prend pas en charge la capture de métriques réseau agrégées pour tous les périphériques réseau. Pour obtenir les métriques pour une interface spécifique, comme eth0, définissez `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>métriques intégrées pour la classe Filesystem

La classe de métriques Filesystem fournit des informations sur l’utilisation du système de fichiers. Les valeurs absolues et en pourcentage sont indiquées comme elles sont affichées pour un utilisateur ordinaire (non root).

compteur | Signification
------- | -------
FreeSpace | Espace disque disponible en octets
UsedSpace | Espace disque utilisé en octets
PercentFreeSpace | Pourcentage d’espace libre
PercentUsedSpace | Pourcentage d’espace utilisé
PercentFreeInodes | Pourcentage d’inodes non utilisés
PercentUsedInodes | Pourcentage total d’inodes alloués (utilisés) pour tous les systèmes de fichiers
BytesReadPerSecond | Octets lus par seconde
BytesWrittenPerSecond | Octets écrits par seconde
BytesPerSecond | Octets lus ou écrits par seconde
ReadsPerSecond | Opérations de lecture par seconde
WritesPerSecond | Opérations d’écriture par seconde
TransfersPerSecond | Opérations de lecture ou d’écriture par seconde

Les valeurs agrégées pour tous les systèmes de fichiers peuvent être obtenues en définissant `"condition": "IsAggregate=True"`. Les valeurs pour un système de fichiers monté spécifique, comme « / mnt », peuvent être obtenues en définissant `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>métriques intégrées pour la classe Disque

La classe de métriques Disque fournit des informations sur l’utilisation du disque. Ces statistiques s’appliquent à la totalité du lecteur. S’il existe plusieurs systèmes de fichiers sur un périphérique, les compteurs pour ce périphérique sont agrégés pour tous les systèmes.

counter | Signification
------- | -------
ReadsPerSecond | Opérations de lecture par seconde
WritesPerSecond | Opérations d’écriture par seconde
TransfersPerSecond | Nombre total d’opérations par seconde
AverageReadTime | Nombre moyen de secondes par opération de lecture
AverageWriteTime | Nombre moyen de secondes par opération d’écriture
AverageTransferTime | Nombre moyen de secondes par opération
AverageDiskQueueLength | Nombre moyen d’opérations disque en file d’attente
ReadBytesPerSecond | Nombre d’octets lus par seconde
WriteBytesPerSecond | Nombre d’octets écrits par seconde
BytesPerSecond | Nombre d’octets lus ou écrits par seconde

Les valeurs agrégées pour tous les disques peuvent être obtenues en définissant `"condition": "IsAggregate=True"`. Pour obtenir des informations pour un périphérique spécifique (par exemple /dev/sdf1), définissez `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installation et configuration de l’extension de diagnostic Linux 3.0 via CLI

En supposant que vos paramètres protégés sont dans le fichier PrivateConfig.json et que vos informations de configuration publique sont PublicConfig.json, exécutez la commande suivante :

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

La commande suppose que vous utilisez le mode Azure Resource Manager (arm) d’Azure CLI. Pour configurer l’extension de diagnostic Linux pour des machines virtuelles du modèle de déploiement classique (ASM), passez au mode « asm » (`azure config mode asm`) et omettez le nom du groupe de ressources dans la commande. Pour plus d’informations, consultez la [documentation relative à l’interface de ligne de commande multiplateforme](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Exemple de configuration de l’extension de diagnostic Linux 3.0

Voici un exemple de configuration de l’extension de diagnostic Linux 3.0 basé sur les définitions précédentes, avec quelques explications. Pour appliquer cet exemple à votre cas, vous devez utiliser le nom de votre compte de stockage, votre jeton SAS de compte SAP et vos jetons SAS EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Ces paramètres privés configurent :

* un compte de stockage
* un jeton SAS de compte correspondant
* plusieurs récepteurs (JsonBlob ou EventHubs avec des jetons SAS)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Ces paramètres publics font que l’extension de diagnostic Linux :

* Charge les métriques de pourcentage de temps processeur et d’espace disque utilisé dans la table `WADMetrics*`
* Charge les messages depuis la fonction Syslog « user » et le niveau de gravité « info » dans la table `LinuxSyslog*`
* Charge les résultats bruts des requêtes OMI (PercentProcessorTime et PercentIdleTime) dans la table nommée `LinuxCPU`
* Charge les lignes ajoutées au fichier `/var/log/myladtestlog` dans la table `MyLadTestLog`

Dans chaque cas, les données sont également chargées dans :

* Stockage Blob Azure (le nom du conteneur est celui qui est défini dans le récepteur JsonBlob)
* Point de terminaison EventHubs (comme spécifié dans le récepteur EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Le `resourceId` dans la configuration doit correspondre à celui de la machine virtuelle ou du groupe de machines virtuelles identiques.

* Les graphes et les alertes des métriques de la plateforme Azure connaissent le resourceId de la machine virtuelle sur laquelle vous travaillez. Ils s’attendent à trouver les données pour votre machine virtuelle en utilisant le resourceId comme clé de recherche.
* Si vous utilisez la mise à l’échelle automatique d’Azure, le resourceId dans la configuration de la mise à l’échelle automatique doit correspondre au resourceId utilisé par l’extension de diagnostic Linux.
* Le resourceId est intégré dans les noms des récepteurs JsonBlob écrit par l’extension de diagnostic Linux.

## <a name="view-your-data"></a>Affichage de vos données

Utilisez le portail Azure pour afficher les données de performances ou pour définir des alertes :

![image](./media/diagnostic-extension/graph_metrics.png)

Les données `performanceCounters` sont toujours stockées dans une table de Stockage Azure. Les API Stockage Azure sont disponibles pour de nombreux langages et de nombreuses plateformes.

Les données envoyées aux récepteurs JsonBlob sont stockées dans des objets blob dans le compte de stockage nommé dans les [paramètres protégés](#protected-settings). Vous pouvez consommer les données d’objet blob à l’aide des API de Stockage Blob Azure.

Vous pouvez aussi utiliser ces outils d’interface utilisateur pour accéder aux données de Stockage Azure :

* Explorateur de serveurs Visual Studio.
* [Explorateur de stockage Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Explorateur de stockage Azure").

Cette capture instantanée d’une session de l’Explorateur de stockage Microsoft Azure montre les tables et les conteneurs Stockage Azure générés à partir d’une extension de diagnostic Linux 3.0 correctement configurée sur une machine virtuelle de test. L’image ne correspond pas exactement à [l’exemple de configuration de l’extension de diagnostic Linux 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostic-extension/stg_explorer.png)

Consultez la [documentation EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) appropriée pour savoir comment consommer des messages publiés sur un point de terminaison EventHubs.

## <a name="next-steps"></a>étapes suivantes

* Créez des alertes de métrique dans [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) pour les métriques que vous collectez.
* Créez [des graphiques de surveillance](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pour vos métriques.
* Découvrez comment [créer un groupe de machines virtuelles identiques](/azure/virtual-machines/linux/tutorial-create-vmss) en utilisant vos métriques pour contrôler la mise à l’échelle automatique.
