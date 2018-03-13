---
title: "Copier des données de HDFS à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’une source HDFS dans le cloud ou locale vers des banques de données réceptrices prises en charge."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 400c58abf04d28dd0e5f1d7aac204f09c43b942e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copier des données depuis HDFS à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-hdfs-connector.md)
> * [Version 2 - Préversion](connector-hdfs.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis HDFS. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur HDFS dans V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis HDFS vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur HDFS prend en charge ce qui suit :

- Copie de fichiers en utilisant une authentification **Windows** (Kerberos) ou **Anonyme**.
- Copie de fichiers en utilisant le protocole **webhdfs** ou la prise en charge de **DistCp intégré**.
- Copie de fichiers en l'état ou analyse/génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>configuration requise

Pour copier des données depuis un HDFS qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations, voir l’article [Runtime d’intégration auto-hébergé](concepts-integration-runtime.md).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de HDFS :

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HDFS sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Hdfs**. | OUI |
| url |URL vers le système HDFS |OUI |
| authenticationType | Valeurs autorisées : **Anonyme** ou **Windows**. <br><br> Pour utiliser l’**authentification Kerberos** pour le connecteur HDFS, reportez-vous à [cette section](#use-kerberos-authentication-for-hdfs-connector) pour configurer votre environnement local en conséquence. |OUI |
| userName |Nom d’utilisateur de l’authentification Windows Pour l’authentification Kerberos, spécifiez `<username>@<domain>.com`. |Oui (pour l’authentification Windows) |
| password |Mot de passe de l’authentification Windows Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui (pour l’authentification Windows) |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple : utilisation d’une authentification anonyme**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : utilisation d’une authentification Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données HDFS.

Pour copier des données de HDFS, affectez la valeur **FileShare** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **FileShare** |OUI |
| folderPath | Chemin d'accès au dossier. Par exemple : dossier/sous-dossier / |OUI |
| fileName | Si vous souhaitez copier à partir d’un fichier spécifique, spécifiez le nom de celui-ci dans **folderPath**. Si vous ne spécifiez aucune valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier en tant que source. |Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non  |

**Exemple :**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source HDFS.

### <a name="hdfs-as-source"></a>HDFS en tant que source

Pour copier des données de HDFS, définissez le type de source dans l’activité de copie sur **HdfsSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source de l’activité de copie doit être définie sur **HdfsSource** |OUI |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |
| distcpSettings | Groupe de propriétés lors de l’utilisation de HDFS DistCp. | Non  |
| resourceManagerEndpoint | Point de terminaison de Yarn ResourceManager | Oui, en cas d’utilisation de DistCp |
| tempScriptPath | Chemin d’accès du dossier utilisé pour stocker le script de commande DistCp temporaire. Le fichier de script est généré par Data Factory et supprimé une fois le travail de copie terminé. | Oui, en cas d’utilisation de DistCp |
| distcpOptions | Options supplémentaires fournies à la commande DistCp. | Non  |

**Exemple : source HDFS dans une activité de copie utilisant UNLOAD**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Apprenez-en davantage sur l’utilisation de DistCp pour copier efficacement des données de HDFS en lisant la section suivante.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utiliser DistCp pour copier des données de HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) est un outil en ligne de commande Hadoop natif permettant d’effectuer une copie distribuée dans un cluster Hadoop. Lors de l’exécution d’une commande Distcp, celle-ci affiche tous les fichiers copiés et crée plusieurs travaux de mappage dans le cluster Hadoop, puis chaque travail de mappage effectue une copie binaire de la source au récepteur.

L’activité de copie prend en charge l’utilisation de DistCp pour copier des fichiers en l'état vers un objet blob Azure (y compris une [copie intermédiaire](copy-activity-performance.md)) ou Azure Data Lake Store, auquel cas elle peut tirer pleinement parti de la puissance de votre cluster au lieu de s’exécuter sur le runtime d’intégration auto-hébergé. Le débit de la copie est ainsi amélioré, en particulier si votre cluster est très puissant. Selon votre configuration dans Azure Data Factory, l’activité de copie construit automatiquement une commande distcp, l’envoie à votre cluster Hadoop et surveille l’état de la copie.

### <a name="prerequsites"></a>Conditions préalables

Pour utiliser DistCp pour copier des fichiers en l’état de HDFS vers un objet blob Azure (y compris une copie intermédiaire) ou Azure Data Lake Store, assurez-vous que votre cluster Hadoop présente la configuration requise suivante :

1. Les services MapReduce et Yarn sont activés.
2. La version minimale de Yarn est 2.5.
3. Le serveur HDFS est intégré avec votre banque de données cible (objet blob Azure ou Azure Data Lake Store) :

    - Le système de fichiers d’objet blob Azure est pris en charge en mode natif depuis Hadoop 2.7. Vous devez uniquement spécifier le chemin d’accès du fichier jar dans Hadoop env config.
    - Le système de fichiers d’Azure Data Lake Store est empaqueté depuis Hadoop 3.0.0-alpha1. Si votre cluster Hadoop est d’une version antérieure, vous devez importer manuellement les packages jar liés à Azure Data Lake Store (azure-datalake-store.jar) dans un cluster à partir d’[ici](https://hadoop.apache.org/releases.html), et spécifier le chemin d’accès du fichier jar dans Hadoop env config.

4. Préparez un dossier temporaire dans HDFS. Ce dossier temporaire est utilisé pour stocker le script d'interpréteur de commandes DistCp afin qu’il occupe un espace exprimable en Ko.
5. Assurez-vous que le compte d’utilisateur fourni dans le service lié HDFS est autorisé à : (a) soumettre une application dans Yarn ; (b) créer un sous-dossier, et lire/écrire des fichiers dans le dossier temporaire situé au-dessus.

### <a name="configurations"></a>Configurations

Voici un exemple de configuration d’activité de copie pour copier des données de HDFS vers un objet blob Azure à l’aide de DistCp :

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilisation de l’authentification Kerberos pour le connecteur HDFS

Il existe deux options de configuration de l’environnement local afin d’utiliser l’authentification Kerberos dans le connecteur HDFS. Vous pouvez choisir celle qui correspond mieux à votre situation.
* Option 1 : [joindre un ordinateur exécutant le runtime d’intégration auto-hébergé dans le domaine Kerberos](#kerberos-join-realm)
* Option 2 : [activer l’approbation mutuelle entre le domaine Windows et le domaine Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Option 1 : joindre un ordinateur exécutant le runtime d’intégration auto-hébergé dans le domaine Kerberos

#### <a name="requirements"></a>Configuration requise

* L’ordinateur exécutant le runtime d’intégration auto-hébergé doit joindre le domaine Kerberos et ne peut pas joindre un domaine Windows.

#### <a name="how-to-configure"></a>Comment configurer

**Sur un ordinateur exécutant le runtime d’intégration auto-hébergé :**

1.  Exécutez l’utilitaire **Ksetup** pour configurer le serveur Kerberos KDC et le domaine.

    L’ordinateur doit être configuré en tant que membre d’un groupe de travail, car un domaine Kerberos est différent d’un domaine Windows. Pour ce faire, définissez le domaine Kerberos et ajoutez un serveur KDC comme suit. Remplacez *REALM.COM* par votre propre domaine respectif en fonction des besoins.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Redémarrez** l’ordinateur après avoir exécuté ces 2 commandes.

2.  Vérifiez la configuration avec la commande **Ksetup**. La sortie doit être semblable à :

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Dans Azure Data Factory :**

* Configurez le connecteur HDFS à l’aide de l’**authentification Windows** avec votre nom principal Kerberos et le mot de passe pour vous connecter à la source de données HDFS. Vérifiez les détails de configuration dans la section sur les [propriétés du service lié HDFS](#linked-service-properties).

### <a name="kerberos-mutual-trust"></a>Option 2 : activer l’approbation mutuelle entre le domaine Windows et le domaine Kerberos

#### <a name="requirements"></a>Configuration requise

*   L’ordinateur exécutant le runtime d’intégration auto-hébergé doit joindre un domaine Windows.
*   Vous avez besoin d’autorisations pour mettre à jour les paramètres du contrôleur de domaine.

#### <a name="how-to-configure"></a>Comment configurer

> [!NOTE]
> Remplacez REALM.COM et AD.COM dans le didacticiel suivant par votre propre domaine et contrôleur de domaine respectifs en fonction des besoins.

**Sur le serveur KDC :**

1.  Modifiez la configuration KDC dans le fichier **krb5.conf** afin de permettre au KDC d’approuver le domaine Windows faisant référence au modèle de configuration suivant. Par défaut, la configuration se trouve dans **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Redémarrez** le service KDC après la configuration.

2.  Préparez un fichier principal nommé **krbtgt/REALM.COM@AD.COM** dans le serveur KDC avec la commande suivante :

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  Dans le fichier de configuration du service HDFS **hadoop.security.auth_to_local**, ajoutez `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Sur le contrôleur de domaine :**

1.  Exécutez les commandes **Ksetup** suivantes pour ajouter une entrée de domaine :

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Établir l’approbation entre le domaine Windows et le domaine Kerberos. [password] correspond au mot de passe pour le principal  **krbtgt/REALM.COM@AD.COM** .

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Sélectionnez l’algorithme de chiffrement utilisé dans Kerberos.

    1. Accédez à Gestionnaire de serveur > Gestion des stratégies de groupe > Domaine > Objets de stratégie de groupe > Stratégie de domaine par défaut ou actif, puis Modifier.

    2. Dans la fenêtre contextuelle **Éditeur de gestion des stratégies de groupe**, accédez à Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies locales > Options de sécurité, puis configurez **Sécurité réseau : Configurer les types de chiffrement autorisés pour Kerberos**.

    3. Sélectionnez l’algorithme de chiffrement à utiliser pour vous connecter à KDC. En règle générale, vous pouvez simplement sélectionner toutes les options.

        ![Configuration des types de chiffrement pour Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilisez la commande **Ksetup** pour spécifier l’algorithme de chiffrement à utiliser dans le domaine spécifique.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Créez le mappage entre le compte de domaine et le principal Kerberos afin de pouvoir utiliser le principal Kerberos dans un domaine Windows.

    1. Démarrez Outils d’administration > **Utilisateurs et ordinateurs Active Directory**.

    2. Pour configurer les fonctionnalités avancées, cliquez sur **Affichage** > **Fonctionnalités avancées**.

    3. Recherchez le compte pour lequel vous souhaitez créer des mappages, cliquez avec le bouton droit pour afficher **Mappages des noms** > cliquez sur l’onglet **Noms Kerberos**.

    4. Ajoutez un principal provenant du domaine.

        ![Mappage des identités de sécurité](media/connector-hdfs/map-security-identity.png)

**Sur un ordinateur exécutant le runtime d’intégration auto-hébergé :**

* Exécutez les commandes **Ksetup** suivantes pour ajouter une entrée de domaine.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Dans Azure Data Factory :**

* Configurez le connecteur HDFS à l’aide de l’**authentification Windows** avec votre compte de domaine ou le principal Kerberos pour vous connecter à la source de données HDFS. Vérifiez les détails de configuration dans la section sur les [propriétés du service lié HDFS](#linked-service-properties).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).