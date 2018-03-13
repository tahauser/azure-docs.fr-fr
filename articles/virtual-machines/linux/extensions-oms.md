---
title: Extension de machine virtuelle OMS Azure pour Linux | Microsoft Docs
description: "Déployez l’Agent OMS sur une machine virtuelle Linux avec une extension de machine virtuelle."
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: danis
ms.openlocfilehash: fea3e992c70d286695691d837c746522f6a5ebb3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle OMS pour Linux

## <a name="overview"></a>Vue d'ensemble

Log Analytics fournit des fonctionnalités de surveillance, d’alerte et de correction des alertes sur les ressources cloud et locales. L’extension de machine virtuelle de l’agent OMS pour Linux est publiée et prise en charge par Microsoft. L’extension installe l’agent OMS sur les machines virtuelles Azure et inscrit les machines virtuelles dans un espace de travail Log Analytics. Ce document présente les plateformes, configurations et options de déploiement prises en charge pour l’extension de machine virtuelle OMS pour Linux.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent OMS peut être exécutée sur ces distributions de Linux.

| Distribution | Version |
|---|---|
| CentOS Linux | 5, 6 et 7 |
| Oracle Linux | 5, 6 et 7 |
| Red Hat Enterprise Linux Server | 5, 6 et 7 |
| Debian GNU/Linux | 6, 7 et 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 et 12 |

Le tableau suivant fournit un mappage de la version de l’extension de machine virtuelle OMS et le bundle de l’Agent OMS pour chaque version. Un lien vers les notes de publication pour la version du bundle de l’Agent OMS est inclus.  

| Version d’extension de machine virtuelle Linux OMS | Version du bundle de l’Agent OMS | 
|--------------------------------|--------------------------|
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center configure automatiquement l’agent OMS et le connecte à l’espace de travail Log Analytics par défaut créé par ASC dans votre abonnement Azure. Si vous utilisez Azure Security Center, ne suivez pas la procédure de ce document. Si vous le faites, vous écrasez l’espace de travail configuré et interrompez la connexion à Azure Security Center.

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension de l’agent OMS pour Linux nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension d’agent OMS. L’extension nécessite l’ID et la clé de l’espace de travail Log Analytics cible ; Ces valeurs peuvent être [trouvées dans votre espace de travail Log Analytics](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) dans le portail Azure. La clé de l’espace de travail devant être traitée comme une donnée sensible, elle est stockée dans une configuration protégée. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible. Notez que **workspaceId** et **workspaceKey** respectent la casse.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| Type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (par exemple) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (par exemple) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement, comme l’intégration à Log Analytics. Vous trouverez un exemple de modèle Resource Manager qui inclut l’extension de machine virtuelle d’agent OMS dans la [galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

La configuration JSON pour une extension de machine virtuelle peut être imbriquée à l’intérieur de la ressource de machine virtuelle ou placée à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement de la configuration JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

L’exemple suivant suppose que l’extension de machine virtuelle est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente, et le type reflète la configuration imbriquée.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle d’agent OMS sur une machine virtuelle existante. Remplacez *workspaceId* et *workspaceKey* avec les valeurs de votre espace de travail Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Codes d’erreur et signification

| Code d'erreur | Signification | Action possible |
| :---: | --- | --- |
| 10 | La machine virtuelle est déjà connectée à un espace de travail Log Analytics | Pour connecter la machine virtuelle à l’espace de travail spécifié dans le schéma d’extension, définissez stopOnMultipleConnections sur la valeur false dans les paramètres publics ou supprimez cette propriété. Cette machine virtuelle est facturée une fois pour chaque espace de travail auquel elle est connectée. |
| 11 | Configuration non valide fournie à l’extension | Suivez les exemples précédents pour définir toutes les valeurs de propriété du déploiement. |
| 12 | Le gestionnaire de package dpkg est verrouillé | Vérifiez que toutes les opérations de mise à jour dpkg sur l’ordinateur sont terminées, puis réessayez. |
| 20 | Activation appelée prématurément | [Mettez à jour l’agent Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) à la dernière version disponible. |
| 51 | Cette extension n’est pas prise en charge sur le système d’exploitation de la machine virtuelle | |
| 55 | Connexion impossible au service Microsoft Operations Management Suite | Vérifiez que le système dispose d’un accès Internet ou qu’un proxy HTTP valide a été fourni. De plus, vérifiez que l’ID de l’espace de travail est correct. |

Pour plus d’informations sur la résolution des problèmes, consultez le [guide de résolution des problèmes de l’Agent OMS pour Linux](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
