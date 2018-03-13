---
title: "Exécuter des scripts personnalisés sur des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Automatiser les tâches de configuration de machine virtuelle Linux à l’aide de l’extension de script personnalisé"
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 53adef0f512c54e036a981dbaa0d08453db6b194
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Utiliser l’extension de script personnalisé Azure avec des machines virtuelles Linux
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Elle est utile pour la configuration de post-déploiement, l’installation de logiciels ou toute autre tâche de configuration/gestion. Il est possible de télécharger des scripts à partir du Stockage Azure ou de tout autre emplacement Internet accessible, ou de les fournir au runtime de l’extension. 

L’extension de script personnalisé est compatible avec les modèles Azure Resource Manager. Elle est également exécutable avec Azure CLI, PowerShell, le Portail Azure et l’API REST Machines virtuelles Azure.

Cet article explique en détail comment utiliser l’extension de script personnalisé avec Azure CLI et l’exécuter à l’aide d’un modèle Azure Resource Manager. Il indique également la procédure de résolution des problèmes pour les systèmes Linux.

## <a name="extension-configuration"></a>Configuration de l’extension
La configuration de l’extension de script personnalisé spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration peut être stockée dans des fichiers de configuration, ou spécifiée en ligne de commande ou dans un modèle Azure Resource Manager. 

Les données sensibles peuvent être stockées dans une configuration protégée qui n’est chiffrée et déchiffrée qu’à l’intérieur de la machine virtuelle. La configuration protégée est utile lorsque la commande d’exécution comprend des secrets tels qu’un mot de passe.

### <a name="public-configuration"></a>Configuration publique
Le schéma de la configuration publique est le suivant :

>[!NOTE]
>Ces noms de propriétés respectent la casse. Pour éviter des problèmes de déploiement, utilisez les noms présentés ici.

* **commandToExecute** (obligatoire, chaîne) : script de point d’entrée à exécuter.
* **fileUris** (facultatif, tableau de chaînes) : URL des fichiers à télécharger.
* **timestamp** (facultatif, entier) : horodatage du script. Modifiez la valeur de ce champ uniquement si vous souhaitez déclencher la réexécution du script.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuration protégée
Le schéma de la configuration protégée est le suivant :

>[!NOTE]
>Ces noms de propriétés respectent la casse. Pour éviter des problèmes de déploiement, utilisez les noms présentés ici.

* **commandToExecute** (facultatif, chaîne) : script de point d’entrée à exécuter. Utilisez ce champ si votre commande contient des secrets tels que des mots de passe.
* **storageAccountName** (facultatif, chaîne) : nom du compte de stockage. Si vous spécifiez des informations d’identification de stockage, tous les URI de fichiers doivent être des URL d’objets blob Azure.
* **storageAccountKey** (facultatif, chaîne) : clé d’accès du compte de stockage.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Si vous utilisez Azure CLI pour exécuter l’extension de script personnalisé, créez un fichier de configuration ou des fichiers. Au minimum, les fichiers de configuration contiennent l’URI de fichier et la commande d’exécution du script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Vous pouvez éventuellement spécifier les paramètres dans la commande sous forme de chaînes au format JSON. Cela permet de spécifier la configuration lors de l’exécution et sans fichier de configuration distinct.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemples d’interface de ligne de commande Azure

#### <a name="public-configuration-with-script-file"></a>Configuration publique avec fichier de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Configuration publique sans fichier de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Fichiers de configuration publique et protégée

Un fichier de configuration publique permet de spécifier l’URI du fichier de script. Un fichier de configuration protégée permet de spécifier la commande à exécuter.

Fichier de configuration publique :

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Fichier de configuration protégée :  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Modèle Resource Manager
L’extension de script personnalisé Azure peut être exécutée au moment du déploiement de la machine virtuelle à l’aide d’un modèle Resource Manager. Pour ce faire, ajoutez un JSON correctement mis en forme au modèle de déploiement.

### <a name="resource-manager-examples"></a>Exemples Resource Manager

#### <a name="public-configuration"></a>Configuration publique

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Commande d’exécution dans une configuration protégée

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Vous trouverez un exemple complet sur la page [Démo du Store musique .NET](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Résolution de problèmes
Lors de l’exécution de l’extension de script personnalisé, le script est créé ou téléchargé dans un répertoire semblable à l’exemple suivant. La sortie de la commande y est également enregistrée, dans les fichiers `stdout` et `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

L’extension de script Azure génère un journal qui se trouve à cet emplacement :

```bash
/var/log/azure/custom-script/handler.log
```

Il est également possible de récupérer l’état d’exécution de l’extension de script personnalisé avec Azure CLI :

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

La sortie ressemble au texte suivant :

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les autres extensions de scripts de machine virtuelle, consultez la page [Vue d’ensemble des extensions de scripts Azure pour Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

