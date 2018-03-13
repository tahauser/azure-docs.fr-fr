---
title: "Service de métadonnées d’instance Azure | Microsoft Docs"
description: "Une interface RESTful permettant d’obtenir des informations sur le calcul, le réseau et les événements de maintenance à venir d’une machine virtuelle Windows."
services: virtual-machines-windows
documentationcenter: 
author: harijayms
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: f0a706a5a7724788d62479d1570fffac07ce6d54
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-instance-metadata-service"></a>Service de métadonnées d’instance Azure


Le service de métadonnées d’instance Azure fournit des informations sur les instances de machine virtuelle en cours d’exécution qui peuvent être utilisées pour gérer et configurer vos machines virtuelles.
Cela inclut des informations telles que la référence (SKU), la configuration réseau et les événements de maintenance à venir. Pour plus de détails sur le type des informations disponibles, voir [Catégories de métadonnées](#instance-metadata-data-categories).

Le service de métadonnées d’instance d’Azure est un point de terminaison REST accessible à toutes les machines virtuelles IaaS créées via [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Le point de terminaison est disponible à une adresse IP non routable bien connue (`169.254.169.254`) accessible uniquement à partir de la machine virtuelle.

> [!IMPORTANT]
> Ce service est **mis à la disposition générale** dans toutes les régions Azure.  Il reçoit régulièrement des mises à jour pour exposer de nouvelles informations sur les instances de machine virtuelle. Cette page ne reflète les [catégories de données](#instance-metadata-data-categories) à jour disponibles.

## <a name="service-availability"></a>Disponibilité du service
Le service est disponible dans toutes les régions Azure mises à la disposition générale. Toutes les versions d’API ne sont peut-être pas disponibles dans toutes les régions Azure.

Régions                                        | Disponibilité ?                                 | Versions prises en charge
-----------------------------------------------|-----------------------------------------------|-----------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/)     | Mise à la disposition générale   | 2017-04-02, 2017-08-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mise à la disposition générale | 2017-04-02
[Azure Chine](https://www.azure.cn/)                                                           | Mise à la disposition générale | 2017-04-02
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                    | Mise à la disposition générale | 2017-04-02

Ce tableau est mis à jour lors des mises à jour du service et lorsque de nouvelles versions prises en charge sont disponibles

Pour tester le service de métadonnées d’instance, créez une machine virtuelle à partir d’[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou du [portail Azure](http://portal.azure.com) dans les régions ci-dessus, puis suivez les exemples ci-dessous.

## <a name="usage"></a>Usage

### <a name="versioning"></a>Contrôle de version
Le service de métadonnées Instance fait l’objet d’une gestion de version. Les versions sont obligatoires et la version actuelle sur Azure mondial est `2017-08-01`. Les versions actuellement prises en charge sont (2017-04-02, 2017-08-01).

> [!NOTE] 
> Les préversions précédentes des événements planifiés prenaient en charge {dernière version} en tant que version de l’api. Ce format n’est plus pris en charge et sera déconseillé à l’avenir.

Au fur et à mesure que des versions plus récentes sont ajoutées, les versions antérieures sont toujours accessibles pour des questions de compatibilité si vos scripts ont des dépendances sur des formats de données spécifiques. Notez cependant que la préversion précédente (2017-03-01) ne sera peut-être pas disponible lors de la mise à la disposition générale du service.

### <a name="using-headers"></a>Utilisation d’en-têtes
Quand vous interrogez le service de métadonnées d’instance, vous devez fournir l’en-tête `Metadata: true` pour garantir que la demande n’a pas été redirigée involontairement.

### <a name="retrieving-metadata"></a>Récupération des métadonnées

Les métadonnées Instance sont disponibles pour l’exécution de machines virtuelles créées/gérées à l’aide [d’Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Accédez à toutes les catégories de données pour une instance de machine virtuelle à l’aide de la demande suivante :

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Toutes les requêtes de métadonnées d’instance respectent la casse.

### <a name="data-output"></a>Sortie des données
Par défaut, le service de métadonnées d’instance retourne des données au format JSON (`Content-Type: application/json`). Toutefois, différentes API retournent des données dans des formats différents si nécessaire.
Le tableau suivant répertorie les autres formats de données que les API peuvent prendre en charge.

API | Format de données par défaut | Autres formats
--------|---------------------|--------------
/instance | json | texte
/scheduledevents | json | Aucun

Pour accéder à un format de réponse autre que le format par défaut, spécifiez le format demandé en tant que paramètre de chaîne de requête dans la demande. Par exemple :

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Sécurité
Le point de terminaison du service de métadonnées d’instance est accessible uniquement à partir de l’instance de machine virtuelle active sur une adresse IP non routable. En outre, toute demande contenant un en-tête `X-Forwarded-For` est rejetée par le service.
Toute demande doit contenir un en-tête `Metadata: true` garantissant que la demande est réellement intentionnelle et ne fait pas partie d’une redirection involontaire. 

### <a name="error"></a>Error
S’il existe un élément de données introuvable ou une requête mal formée, le service de métadonnées d’instance retourne des erreurs HTTP standard. Par exemple :

Code d’état HTTP | Motif
----------------|-------
200 OK |
400 Demande incorrecte | En-tête `Metadata: true` manquant
404 Introuvable | L’élément demandé n’existe pas 
405 Méthode non autorisée | Seules les demandes `GET` et `POST` sont prises en charge
429 Trop de demandes | L’API prend actuellement en charge un maximum de 5 requêtes par seconde
500 Erreur de service     | Recommencez l’opération plus tard

### <a name="examples"></a>Exemples

> [!NOTE] 
> Toutes les réponses de l’API sont des chaînes JSON. Tous les exemples de réponses suivants sont imprimés avec soin par souci de lisibilité.

#### <a name="retrieving-network-information"></a>Récupération des informations réseau

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Réponse**

> [!NOTE] 
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Récupération de l’adresse IP publique

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Récupération de toutes les métadonnées d’une instance

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

**Réponse**

> [!NOTE] 
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmSize": "Standard_D1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Récupération de métadonnées dans une machine virtuelle Windows

**Requête**

Les métadonnées d’instance peuvent être récupérées dans Windows via l’utilitaire Powershell `curl` : 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Ou via l’applet de commande `Invoke-RestMethod` :
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Réponse**

> [!NOTE] 
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Catégories de données de métadonnées Instance
Les catégories de données suivantes sont disponibles via le service de métadonnées d’instance :

Données | Description | Version introduite 
-----|-------------|-----------------------
location | Région Azure dans laquelle la machine virtuelle est en cours d’exécution | 2017-04-02 
Nom | Nom de la machine virtuelle | 2017-04-02
offer | Offrent des informations pour l’image de machine virtuelle. Cette valeur est uniquement présente pour les images déployées à partir de la galerie d’images Azure. | 2017-04-02
publisher | Éditeur de l’image de machine virtuelle | 2017-04-02
sku | Référence (SKU) spécifique pour l’image de machine virtuelle | 2017-04-02
version | Version de l’image de machine virtuelle | 2017-04-02
osType | Linux ou Windows | 2017-04-02
platformUpdateDomain |  [Domaine de mise à jour](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
platformFaultDomain | [Domaine par défaut](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
vmId | [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle | 2017-04-02
vmSize | [Taille de la machine virtuelle](sizes.md) | 2017-04-02
subscriptionId | Abonnement Azure pour la machine virtuelle | 2017-08-01
tags | [Étiquettes](../../azure-resource-manager/resource-group-using-tags.md) de votre machine virtuelle  | 2017-08-01
nom_groupe_ressources | [Groupe de ressources](../../azure-resource-manager/resource-group-overview.md) de votre machine virtuelle | 2017-08-01
placementGroupId | [Groupe de placement](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de votre groupe de machines virtuelles identiques | 2017-08-01
ipv4/privateIpAddress | Adresse IPv4 locale de la machine virtuelle | 2017-04-02
ipv4/publicIpAddress | Adresse IPv4 publique de la machine virtuelle | 2017-04-02
subnet/address | Adresse de sous-réseau de la machine virtuelle | 2017-04-02 
subnet/prefix | Préfixe de sous-réseau, par exemple 24 | 2017-04-02 
ipv6/ipAddress | Adresse IPv6 locale de la machine virtuelle | 2017-04-02 
macAddress | Adresse MAC de la machine virtuelle | 2017-04-02 
scheduledevents | Consultez [Événements planifiés](scheduled-events.md). | 2017-03-01

## <a name="example-scenarios-for-usage"></a>Exemples de scénarios d’utilisation  

### <a name="tracking-vm-running-on-azure"></a>Suivi de la machine virtuelle s’exécutant sur Azure

En tant que fournisseur de service, vous aurez peut-être besoin de suivre le nombre de machines virtuelles s’exécutant sur votre logiciel ou de disposer d’agents effectuant le suivi de l’unicité de la machine virtuelle. Pour pouvoir obtenir un ID unique pour une machine virtuelle, utilisez le champ `vmId` du service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Réponse**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Positionnement du domaine d’erreur/de mise à jour basé sur des conteneurs et des partitions de données 

Pour certains scénarios, le positionnement des différents réplicas de données est de première importance. Par exemple, pour le [positionnement du réplica HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou pour le positionnement des conteneurs via un [orchestrateur](https://kubernetes.io/docs/user-guide/node-selection/), vous devez connaître les domaines `platformFaultDomain` et `platformUpdateDomain` sur lesquels la machine virtuelle s’exécute.
Vous pouvez interroger ces données directement via le service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Réponse**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtenir plus d’informations sur la machine virtuelle pour le dossier de support 

En tant que fournisseur de services, vous recevrez peut-être un appel du support pour lequel vous devrez connaître les informations de la machine virtuelle. Demandez au client de partager les métadonnées de calcul afin que l’équipe du support dispose des informations de base concernant le type de machine virtuelle sur Azure. 

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Réponse**

> [!NOTE] 
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemples d’appels du service de métadonnées utilisant différents langages à l’intérieur de la machine virtuelle 

Langage | Exemple 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
    

## <a name="faq"></a>Forum Aux Questions
1. J’obtiens l’erreur `400 Bad Request, Required metadata header not specified`. Qu’est-ce que cela signifie ?
   * Le service de métadonnées d’instance requiert que l’en-tête `Metadata: true` soit transmis dans cette demande. La transmission de cet en-tête dans l’appel REST permet d’accéder au service de métadonnées d’instance. 
2. Pourquoi je n’obtiens pas les informations de calcul pour ma machine virtuelle ?
   * Actuellement, le service de métadonnées d’instance prend uniquement en charge les instances créées avec Azure Resource Manager. À l’avenir, il se peut que la prise en charge des machines virtuelles du service cloud soit ajoutée.
3. J’ai créé une machine virtuelle via Azure Resource Manager il y quelque temps déjà. Pourquoi ne puis-je pas voir les informations de métadonnées de calcul ?
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/resource-group-using-tags.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données à la machine virtuelle pour actualiser les métadonnées.
4. Je ne vois pas toutes les données renseignées pour la nouvelle version 2017-08-01
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/resource-group-using-tags.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données à la machine virtuelle pour actualiser les métadonnées.
5. Pourquoi l’erreur `500 Internal Server Error` ?
   * Renouvelez votre demande en fonction du système d’interruption exponentiel. Si le problème persiste, contactez le support Azure.
6. Où partager des commentaires/questions supplémentaires ?
   * Envoyez vos commentaires au site http://feedback.azure.com.
7. Cela fonctionne-t-il pour l’instance de groupe de machines virtuelles identiques ?
   * Oui, le service de métadonnées est disponible pour les instances de groupe identique. 
8. Comment obtenir un support technique pour le service ?
   * Pour obtenir un support technique pour le service, créez un problème de support dans le portail Azure pour la machine virtuelle sur laquelle vous ne pouvez pas obtenir de réponse de métadonnées après plusieurs tentatives longues. 

   ![Support des métadonnées d’instance](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>étapes suivantes

- En savoir plus sur les [événements planifiés](scheduled-events.md)
