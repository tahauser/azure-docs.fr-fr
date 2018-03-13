---
title: "Sorties de modèles Azure Resource Manager | Microsoft Docs"
description: "Explique comment définir des sorties dans des modèles Azure Resource Manager suivant une syntaxe JSON déclarative."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 64d7a0ea72b2f629160f31e4bc1fb4a90f10653d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Section outputs des modèles Azure Resource Manager
Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

## <a name="define-and-use-output-values"></a>Définir et utiliser des valeurs de sortie

L’exemple suivant montre comment retourner l’ID de ressource d’une adresse IP publique :

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Après le déploiement, vous pouvez récupérer la valeur à l’aide d’un script. Pour PowerShell, utilisez la commande suivante :

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Vous pouvez récupérer la valeur de sortie à partir d’un modèle lié à l’aide de la fonction [reference](resource-group-template-functions-resource.md#reference). Pour obtenir une valeur de sortie d’un modèle lié, récupérez la valeur de propriété à l’aide d’une syntaxe comme : `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Par exemple, vous pouvez définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un modèle lié.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section outputs d’un [modèle imbriqué](resource-group-linked-templates.md#link-or-nest-a-template). Pour retourner les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

## <a name="available-properties"></a>Propriétés disponibles

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| outputName |OUI |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| Type |OUI |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. |
| value |OUI |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

## <a name="recommendations"></a>Recommandations

Si vous utilisez un modèle pour créer des adresses IP publiques, il doit comporter une section outputs qui renvoie les détails de l’adresse IP et le nom de domaine complet. Vous pouvez utiliser des valeurs de sortie pour récupérer facilement plus d’informations sur les adresses IP publiques et sur les noms de domaine complets après le déploiement.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Exemples de modèles


|Modèle  |Description  |
|---------|---------|
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crée des variables complexes et génère ces valeurs. Ne déploie aucune ressource. |
|[Adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crée une adresse IP publique et génère l’ID de ressource. |
|[Équilibreur de charge](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Est lié au modèle précédent. Utilise l’ID de ressource dans la sortie durant la création de l’équilibreur de charge. |


## <a name="next-steps"></a>étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Ce scénario est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid).