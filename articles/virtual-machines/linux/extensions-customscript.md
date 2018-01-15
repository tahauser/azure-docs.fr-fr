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
ms.openlocfilehash: 53a241f12373acdb5d40575915d8d6c2f3c86b9a
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Utilisation de l’extension de script personnalisé Azure avec des machines virtuelles Linux
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de tout autre emplacement Internet accessible, ou fournis lors de l’exécution de l’extension. L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure.

Ce document explique en détail comment utiliser l’extension de script personnalisé de l’interface de ligne de commande Azure et un modèle Azure Resource Manager, et détaille également les étapes de résolution de problèmes sur les systèmes Linux.

## <a name="extension-configuration"></a>Configuration de l’extension
La configuration de l’extension de script personnalisé spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration peut être stockée dans des fichiers de configuration ou spécifiée sur la ligne de commande ou dans un modèle Azure Resource Manager. Les données sensibles peuvent être stockées dans une configuration protégée qui est chiffrée et déchiffrée uniquement à l’intérieur de la machine virtuelle. La configuration protégée est utile lorsque la commande d’exécution comprend des secrets tels qu’un mot de passe.

### <a name="public-configuration"></a>Configuration publique
Schéma :

**Remarque** : ces noms de propriété respectent la casse. Utilisez les noms tels qu’ils sont indiqués ci-dessous afin d’éviter les problèmes de déploiement.

* **commandToExecute**: (obligatoire, chaîne) script de point d’entrée à exécuter
* **fileUris**: (facultatif, tableau de chaînes) URL des fichiers à télécharger.
* **timestamp** : (facultatif, entier) utilisez ce champ uniquement pour déclencher la réexécution du script en modifiant la valeur de ce champ.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuration protégée
Schéma :

**Remarque** : ces noms de propriété respectent la casse. Utilisez les noms tels qu’ils sont indiqués ci-dessous afin d’éviter les problèmes de déploiement.

* **commandToExecute**: (facultatif, chaîne) script de point d’entrée à exécuter. Utilisez plutôt ce champ si votre commande contient des secrets tels que des mots de passe.
* **storageAccountName**: (facultatif, chaîne) nom du compte de stockage. Si vous spécifiez des informations d’identification de stockage, tous les fileUris doivent être des URL pour les objets blob Azure.
* **storageAccountKey**: (facultatif, chaîne) clé d’accès du compte de stockage.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Lorsque vous utilisez l’interface de ligne de commande Azure pour exécuter l’extension de script personnalisé, créez un ou plusieurs fichiers de configuration contenant au minimum l’URI du fichier et la commande d’exécution de script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Vous pouvez éventuellement spécifier les paramètres dans la commande sous forme de chaîne au format JSON. Cela permet de spécifier la configuration lors de l’exécution et sans fichier de configuration distinct.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemples d’interface de ligne de commande Azure

**Exemple 1** : configuration publique avec fichier de script.

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

**Exemple 2** : configuration publique sans fichier de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exemple 3** : un fichier de configuration publique est utilisé pour spécifier l’URI du fichier de script et un fichier de configuration protégée est utilisé pour spécifier la commande à exécuter.

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
**Exemple 1** : configuration publique.

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

**Exemple 2** : commande d’exécution dans une configuration protégée.

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

Pour obtenir un exemple complet, consultez la démonstration .NET Core du Store musique : [démonstration du Store musique](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Résolution de problèmes
Lors de l’exécution de l’extension de script personnalisé, le script est créé ou téléchargé dans un répertoire similaire à l’exemple suivant. La sortie de commande est également enregistrée dans ce répertoire, dans les fichiers `stdout` et `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

L’extension de script Azure génère un journal qui se trouve ici.

```bash
/var/log/azure/custom-script/handler.log
```

L’état d’exécution de l’extension de script personnalisé peut également être récupéré avec l’interface de ligne de commande Azure.

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
Pour plus d’informations sur les autres extensions de script de machine virtuelle, consultez [Vue d’ensemble de l’extension de script Azure pour Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

