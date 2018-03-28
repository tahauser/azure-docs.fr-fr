---
title: Création de développement de modèles pour Azure Logic Apps | Microsoft Docs
description: Créer des modèles Azure Resource Manager pour déployer des applications logiques
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: SyntaxC4
editor: ''
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 91d93a02bb9bf48c5bda0304c9d3d52c22e30209
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Créer des modèles Azure Resource Manager pour déployer des applications logiques

Après avoir créé une application logique, vous pouvez, si vous le souhaitez, la définir en tant que modèle Azure Resource Manager.
De cette façon, vous pouvez facilement déployer l’application logique dans n’importe quel environnement ou groupe de ressources où elle peut être utile.
Pour en savoir plus sur les modèles Resource Manager, consultez les articles sur la [création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) et le [déploiement de ressources à l’aide de modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modèle de déploiement de l’application logique

Une application logique possède trois composants de base :

* **Ressource d’application logique** : regroupe des informations sur certains éléments tels que le plan de tarification, l’emplacement et la définition de workflow.
* **Définition de workflow** : décrit les étapes de workflow de votre application logique et comment le moteur Logic Apps doit exécuter workflow.
Vous pouvez afficher cette définition dans la fenêtre **Mode Code** de votre application logique.
Dans la ressource d’application logique, vous pouvez trouver cette définition dans la propriété `definition`.
* **Connexions** : il s’agit des ressources distinctes qui permettent de stocker les métadonnées en toute sécurité autour de n’importe quelle connexion de connecteur (par exemple, une chaîne de connexion et un jeton d’accès).
Dans la ressource d’application logique, votre application logique fait référence à ces ressources dans la section `parameters`.

Vous pouvez afficher tous ces éléments pour les applications logiques existantes en utilisant un outil tel que [l’Explorateur de ressources Azure](http://resources.azure.com).

Pour créer un modèle pour une application logique pouvant être utilisée avec les déploiements de groupes de ressources, vous devez définir les ressources et les paramétrer selon vos besoins.
Par exemple, si vous effectuez un déploiement dans un environnement de développement, de test et de production, vous préférez sans doute utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement.
Ou bien, vous pourrez procéder au déploiement dans différents abonnements ou groupes de ressources.  

## <a name="create-a-logic-app-deployment-template"></a>Création d’un modèle de déploiement d’applications logiques

Le moyen le plus simple pour disposer d’un modèle de déploiement d’application logique valide consiste à utiliser les [outils Visual Studio pour Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Les outils Visual Studio génèrent un modèle de déploiement valide qui peut être utilisé sur n’importe quel abonnement ou emplacement.

Certains autres outils peuvent vous être utiles lorsque vous créez un modèle de déploiement d’application logique.
Vous pouvez le créer manuellement, c’est-à-dire, en utilisant les ressources déjà présentées ici pour créer des paramètres en fonction des besoins.
Vous avez également la possibilité d’utiliser un module PowerShell [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) . Le module open source évalue dans un premier temps l’application logique et les connexions qu’elle utilise, puis génère les ressources de modèle avec les paramètres nécessaires au déploiement.
Par exemple, si votre application logique reçoit un message à partir d’une file d’attente Service Bus Azure et ajoute des données à une base de données SQL Azure, l’outil conserve toute la logique d’orchestration et paramètre les chaînes de connexion SQL et Service Bus afin qu’elles puissent être définies à la phase de déploiement.

> [!NOTE]
> Les connexions doivent se trouver dans le même groupe de ressources que l’application logique
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installer le module PowerShell Logic App Template
Le moyen le plus simple d’installer le module consiste à utiliser [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) à l’aide de la commande `Install-Module -Name LogicAppTemplate`.  

Vous pouvez également installer le module PowerShell manuellement :

1. Téléchargez la dernière version du module [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Décompressez le dossier dans le dossier de votre module PowerShell (généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pour que le module puisse fonctionner avec n’importe quel jeton d’accès client et abonnement, il est recommandé d’utiliser conjointement l’outil de ligne de commande [ARMClient](https://github.com/projectkudu/ARMClient) .  Ce [billet de blog ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) présente ARMClient de façon plus détaillée.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Générer un modèle d’application logique à l’aide de PowerShell
Une fois PowerShell installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` est l’ID d’abonnement Azure. Cette ligne obtient d’abord un jeton d’accès via ARMClient, puis le dirige vers le script PowerShell et enfin crée le modèle dans un fichier JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Ajouter des paramètres à un modèle d’application logique
Après avoir créé votre modèle d’application logique, vous pouvez continuer à ajouter ou modifier les paramètres dont vous avez besoin. Par exemple, si votre définition inclut un ID de ressource à une fonction Azure ou à un workflow imbriqué que vous envisagez de déployer dans un déploiement unique, vous pouvez ajouter des ressources supplémentaires à votre modèle et  paramétrer les ID en fonction de vos besoins. Cela s’applique également à toutes les références aux API personnalisées ou aux points de terminaison Swagger que vous pensez déployer avec chaque groupe de ressources.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Ajouter les références des ressources dépendantes aux modèles de déploiement Visual Studio

Si vous souhaitez que votre application logique référence des ressources dépendantes, vous pouvez utiliser les [fonctions de gabarit Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) dans votre modèle de déploiement d’application logique. Par exemple, vous souhaiterez peut-être que votre application logique référence une fonction Azure ou un compte d’intégration que vous souhaitez déployer avec votre application logique. Suivez ces instructions pour utiliser les paramètres dans votre modèle de déploiement afin que le Concepteur d’applications logiques offre un rendu correct. 

Vous pouvez utiliser les paramètres d’application logique dans ces types de déclencheurs et d’actions :

*   Flux de travail enfant
*   Conteneur de fonctions
*   Appel APIM
*   URL d’exécution de connexion d’API
*   Chemin de connexion d’API

Vous pouvez utiliser les fonctions de modèle, telles que les paramètres, les variables, resourceId, concat, etc. Par exemple, voici par quoi vous pouvez remplacer l’ID de ressource de fonction Azure :

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

Et où vous pouvez utiliser les paramètres :

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Vous pouvez, par exemple, paramétrer l’opération d’envoi de message Service Bus :

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl est facultatif et peut être supprimé de votre modèle.
> 


> [!NOTE] 
> Pour que le Concepteur d’applications logiques fonctionne lorsque vous utilisez des paramètres, vous devez fournir des valeurs par défaut, par exemple :
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Ajouter votre application logique à un projet de groupe de ressources existant

Si vous disposez d’un projet de groupe de ressources existant, vous pouvez ajouter votre application logique à ce projet dans la fenêtre Structure JSON. Vous pouvez également ajouter une autre application logique en même temps que l’application que vous avez créée précédemment.

1. Ouvrez le fichier `<template>.json` .

2. Pour ouvrir la fenêtre Structure JSON, rendez-vous à **Affichage** > **Autres fenêtres** > **Structure JSON**.

3. Pour ajouter une ressource au fichier de modèle, cliquez sur **Ajouter une ressource** en haut de la fenêtre Structure JSON. Ou, dans la fenêtre Structure JSON, cliquez avec le bouton droit sur **Ressources**, puis sélectionnez **Ajouter une nouvelle ressource**.

    ![Fenêtre Structure JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. Dans la boîte de dialogue **Ajouter une ressource**, recherchez et sélectionnez **Application logique**. Donnez un nom à votre application logique, puis choisissez **Ajouter**.

    ![Ajouter une ressource](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Déployer un modèle d’application logique

Vous pouvez procéder au déploiement à l’aide d’outils tels que PowerShell, API REST, [Visual Studio Team Services Release Management](#team-services) et le déploiement de modèles par le biais du powershell Azure.
Nous vous recommandons également de créer un [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) pour stocker les valeurs du paramètre.
Découvrez comment [déployer des ressources à l’aide de modèles Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) ou [déployer des ressources à l’aide de modèles Resource Manager et du portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autoriser des connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides.
Toutefois, vous devez autoriser des connexions OAuth pour générer un jeton d’accès valide.
Pour autoriser les connexions OAuth, ouvrez l’application logique dans le Concepteur d’application logique et autoriser ces connexions. Ou, pour un déploiement automatisé, utilisez un script pour autoriser chacune des connexions OAuth.
Vous trouverez un exemple de script sur GitHub sous le projet [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

Le déploiement et la gestion d’un environnement passent souvent par l’utilisation d’un outil tel que Release Management dans Visual Studio Team Services avec un modèle de déploiement d’application logique. Visual Studio Team Services inclut une tâche de [déploiement d’un groupe de ressources Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que vous pouvez ajouter dans n’importe quel pipeline de build ou de version. Vous devez disposer d’un [principal de service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pour obtenir l’autorisation de déployer, après quoi vous pouvez générer la définition de version.

1. Dans Release Management, sélectionnez **Vide** pour créer une définition vide.

    ![Créer une définition vide][1]

2. Choisissez toute ressource dont vous avez besoin pour ce faire, notamment probablement le modèle d’application logique généré manuellement ou dans le cadre du processus de création.
3. Ajoutez une tâche de **déploiement d’un groupe de ressources Azure** .
4. Configurez-la avec un [principal de service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), puis référencez le Modèle et les fichiers Paramètres de modèle.
5. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
