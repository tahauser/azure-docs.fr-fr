---
title: Informations de référence sur l’interface CLI de gestion des modèles Azure Machine Learning | Microsoft Docs
description: Informations de référence sur l’interface CLI de gestion des modèles Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 27361c5b92a8748a026d457875fadfc1f3529076
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="model-management-command-line-interface-reference"></a>Informations de référence sur l’interface CLI de gestion des modèles

## <a name="base-cli-concepts"></a>Concepts de base de l’interface CLI :

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Commandes du compte
Vous avez besoin d’un compte de gestion des modèles pour utiliser les services qui vous permettent de déployer et gérer des modèles. Utilisez `az ml account modelmanagement -h` pour afficher la liste suivante :

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Créer un compte de gestion des modèles**

Créez un compte de gestion des modèles pour la facturation à l’aide de la commande suivante :

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Arguments locaux :

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Commandes de l’environnement

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Configurer l’environnement de développement**

La commande de configuration implique que vous disposiez d’un accès Contributeur à l’abonnement. Si vous ne disposez pas de cet accès, il vous faut au moins un accès Contributeur au groupe de ressources vers lequel vous effectuez le déploiement. Pour l’obtenir, vous devez spécifier le nom du groupe de ressources dans la commande de configuration à l’aide de l’indicateur `-g`. 

Il existe deux options de déploiement : *local* et *cluster*. Définissez l’indicateur `--cluster` (ou `-c`) pour configurer le déploiement cluster, qui provisionne un cluster ACS. La syntaxe de configuration de base est la suivante :

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Cette commande initialise votre environnement Azure Machine Learning avec un compte de stockage, le Registre de l’ACR et le service App Insights créé dans votre abonnement. Par défaut, l’environnement est initialisé pour des déploiements locaux uniquement (sans ACS) si aucun indicateur n’est spécifié. Si vous avez besoin de mettre à l’échelle le service, spécifiez l’indicateur `--cluster` (ou `-c`) pour créer un cluster ACS.

Détails de la commande :

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Arguments globaux
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Commandes du modèle

    list
    register
    show

**Inscrire un modèle**

Commande pour enregistrer le modèle.

`az ml model register --model [path to model file] --name [model name]`

Détails de la commande :

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Arguments globaux

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Commandes du manifeste

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Créer un manifeste**

La commande suivante crée un fichier manifeste pour le modèle. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Détails de la commande :

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Arguments d’un modèle inscrit

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Arguments d’un modèle non inscrit

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Arguments globaux

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Commandes de l’image

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Créer une image**

Vous pouvez créer une image avec la possibilité de créer préalablement son manifeste. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Vous pouvez aussi créer le manifeste et l’image avec une seule commande. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Détails de la commande :

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Arguments d’un manifeste inscrit

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Arguments d’un manifeste non inscrit

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Commandes du service
Les commandes suivantes sont prises en charge pour le service. Pour voir les paramètres de chaque commande, utilisez l’option -h. Par exemple, utilisez `az ml service create realtime -h` pour voir les détails de la commande create.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Création d’un service**

Pour créer un service avec une image préalablement créée, utilisez la commande suivante :

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Pour créer un service, un manifeste et une image avec une seule commande, utilisez la commande suivante :

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Détails de la commande :

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Arguments d’une image inscrite

    --image-id                        : [Required] Image to deploy to the service.

Arguments d’une image non inscrite

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Arguments globaux

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Remarque sur l’indicateur `-d` pour joindre des dépendances : si vous passez le nom d’un répertoire qui n’est pas encore compressé (zip, tar, etc.), ce répertoire est automatiquement compressé au format tar avant d’être passé, puis il est automatiquement décompressé à l’autre extrémité. 

Si vous passez dans un répertoire déjà dans un bundle, il est traité comme un fichier et transmis en l’état. Il est automatiquement décompressé. Vous devez gérer cela dans votre code.

**Obtenir les détails du service**

Obtenez les détails du service, comme son URL et ses données d’utilisation (avec les exemples de données si un schéma a été créé).

`az ml service show realtime --name [service name]`

Détails de la commande :

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Arguments globaux

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Exécuter le service**

`az ml service run realtime -n [service name] -d [input_data]`

Détails de la commande :

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Arguments globaux

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Commande

    az ml service run realtime

Arguments --id -i    : [Obligatoire] ID du service à évaluer.
-d         : données à utiliser pour appeler le service web.
-v         : indicateur de commentaires.

Arguments globaux

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
