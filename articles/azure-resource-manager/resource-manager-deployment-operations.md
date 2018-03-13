---
title: "Opérations de déploiement avec Azure Resource Manager | Microsoft Docs"
description: "Décrit comment afficher les opérations de déploiement d’Azure Resource Manager avec le portail, PowerShell, la CLI Azure et l’API REST."
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
ms.openlocfilehash: 197f890690ff68236cba221988ead9b9abd8c04e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Afficher les opérations de déploiement avec Azure Resource Manager


Vous pouvez afficher les opérations d’un déploiement via le portail Azure. Il est plus intéressant d’afficher les opérations lorsque vous recevez une erreur lors du déploiement ; cet article porte donc sur l’affichage des opérations ayant échoué. Le portail fournit une interface qui vous permet de rechercher facilement les erreurs et de déterminer des corrections potentielles.

Vous pouvez dépanner votre déploiement en examinant les journaux d'audit ou les opérations de déploiement. Cette rubrique illustre les deux méthodes. Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portail
Pour afficher les opérations de déploiement, procédez comme suit :

1. Pour le groupe de ressources impliqué dans le déploiement, notez l'état du dernier déploiement. Vous pouvez sélectionner cet état pour obtenir plus de détails.
   
    ![état du déploiement](./media/resource-manager-deployment-operations/deployment-status.png)
2. Vous voyez l’historique des déploiements récents. Sélectionnez le déploiement ayant échoué.
   
    ![état du déploiement](./media/resource-manager-deployment-operations/select-deployment.png)
3. Cliquez sur le lien pour voir la description de la raison de l’échec du déploiement. Dans l’image ci-dessous, l’enregistrement DNS n’est pas unique.  
   
    ![afficher le déploiement ayant échoué](./media/resource-manager-deployment-operations/view-error.png)
   
    Ce message d’erreur devrait vous suffire pour lancer le dépannage. Toutefois, si vous avez besoin de plus d’informations sur les tâches ayant été effectuées, vous pouvez afficher les opérations, comme indiqué dans les étapes suivantes.
4. Vous pouvez afficher toutes les opérations de déploiement. Sélectionnez n’importe quelle opération pour afficher plus de détails.
   
    ![afficher des opérations](./media/resource-manager-deployment-operations/view-operations.png)
   
    Dans ce cas, vous verrez que le compte de stockage, le réseau virtuel et le groupe à haute disponibilité ont été créés avec succès. L’adresse IP publique a cependant échoué, et d’autres ressources n’ont pas été tentées.
5. Pour afficher les événements pour le déploiement, sélectionnez **Événements**.
   
    ![visualiser les événements](./media/resource-manager-deployment-operations/view-events.png)
6. Vous voyez tous les événements du déploiement. Pour voir plus de détails, cliquez sur un des événements. Notez les ID de corrélation. Cette valeur peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement.
   
    ![voir les événements](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Pour obtenir l’état global d’un déploiement, utilisez la commande **Get-AzureRmResourceGroupDeployment** . 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Sinon, vous pouvez filtrer les résultats en les restreignant aux déploiements qui ont échoué.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Chaque déploiement comprend plusieurs opérations. Chaque opération représente une étape dans le processus de déploiement. Pour découvrir la cause du problème rencontré lors d’un déploiement, vous devez généralement afficher les détails concernant les opérations de déploiement. Vous pouvez afficher l'état des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Cette requête retourne plusieurs opérations, chacune au format suivant :

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Pour obtenir plus d’informations sur les opérations ayant échoué, récupérez les propriétés des opérations dont l’état est **Failed** .

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Cette requête retourne toutes les opérations ayant échoué, chacune au format suivant :

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Notez le serviceRequestId et le trackingId de l’opération. L’élément serviceRequestId peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement. Vous utiliserez le trackingId à l’étape suivante afin de vous concentrer sur une opération en particulier.
4. Pour obtenir le message d’état d’une opération ayant échoué, utilisez la commande suivante :

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Résultat :

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Chaque opération de déploiement dans Azure inclut le contenu de la demande et de la réponse. Le contenu de la demande représente ce que vous avez envoyé à Azure pendant le déploiement (par exemple, la création d’une machine virtuelle, le disque de système d’exploitation et d’autres ressources). Le contenu de la réponse représente ce qu’Azure a renvoyé depuis votre demande de déploiement. Au cours du déploiement, vous pouvez utiliser le paramètre **DeploymentDebugLogLevel** pour spécifier que la demande et/ou la réponse doivent être conservées dans le journal. 

  Vous obtenez ces informations à partir du journal et les enregistrez localement en utilisant les commandes PowerShell suivantes :

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Récupérez l’état global d’un déploiement avec la commande **azure group deployment show** .

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  Une des valeurs retournées est **correlationId**. Cette valeur est utilisée pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Pour voir les opérations d’un déploiement, utilisez :

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Récupérez des informations sur un déploiement avec l’opération [Obtenir des informations sur le déploiement d’un modèle](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) .

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    Dans la réponse, repérez en particulier les éléments **provisioningState**, **correlationId** et **error**. L’élément **correlationId** est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Pour obtenir des informations sur les déploiements, voir [Répertorier toutes les opérations de déploiement de modèle](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    La réponse inclut des informations sur la demande et/ou la réponse selon ce que vous avez spécifié dans la propriété **debugSetting** pendant le déploiement.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour en savoir plus sur l’utilisation des journaux d’activité pour surveiller d’autres types d’actions, consultez [Afficher les journaux d’activité pour gérer les ressources Azure](resource-group-audit.md).
* Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

