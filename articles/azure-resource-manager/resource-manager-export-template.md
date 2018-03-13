---
title: "Exporter un modèle Azure Resource Manager | Microsoft Docs"
description: "Utilisez Azure Resource Manage pour exporter un modèle à partir d’un groupe de ressources existant."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 7ac553a3608df41548f845e27c545ff63886e37c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exporter un modèle Azure Resource Manager à partir de ressources existantes
Cet article vous montre comment exporter un modèle Resource Manager à partir de ressources actuelles de votre abonnement. Vous pouvez utiliser ce modèle généré pour obtenir une meilleure compréhension de la syntaxe de modèle.

Il existe deux façons d’exporter un modèle :

* Vous pouvez exporter le **modèle réel que vous avez utilisé pour un déploiement**. Le modèle exporté inclut l’ensemble des paramètres et des variables exactement comme ils apparaissent dans le modèle d’origine. Cette approche est utile lorsque vous avez déployé des ressources au moyen du portail et que vous souhaitez voir le modèle pour créer ces ressources. Ce modèle est facilement utilisable. 
* Vous pouvez exporter un **modèle généré qui représente l’état actuel du groupe de ressources**. Le modèle exporté n’est pas basé sur un modèle utilisé pour le déploiement. Au lieu de cela, il crée un modèle qui est un « instantané » ou une « sauvegarde » du groupe de ressources. Le modèle exporté a probablement de nombreuses valeurs codées en dur et pas autant de paramètres que vous pourriez généralement définir. Cette option permet de redéployer les ressources sur le même groupe de ressources. Pour utiliser ce modèle pour un autre groupe de ressources, vous devrez peut-être le modifier de façon significative.

Cet article présente les deux approches par le biais du portail.

## <a name="deploy-resources"></a>Déployer des ressources
Commençons par déployer des ressources dans Azure que vous pouvez utiliser pour l’exportation en tant que modèle. Si vous disposez déjà d’un groupe de ressources dans votre abonnement que vous voulez exporter, vous pouvez ignorer cette solution. Le reste de cet article suppose que vous avez déployé l’application web et la solution de base de données SQL présentées dans cette section. Si vous utilisez une autre solution, votre expérience peut être un peu différente, mais les étapes pour exporter un modèle sont identiques. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
   
      ![Sélectionner nouveau](./media/resource-manager-export-template/new.png)
2. Recherchez **application web + SQL** et sélectionnez l’option correspondante dans les options disponibles.
   
      ![Rechercher application web et SQL](./media/resource-manager-export-template/webapp-sql.png)

3. Sélectionnez **Créer**.

      ![Sélectionner Créer](./media/resource-manager-export-template/create.png)

4. Indiquez les valeurs requises pour configurer l’application web et la base de données SQL. Sélectionnez **Créer**.

      ![Fournir une valeur web et SQL](./media/resource-manager-export-template/provide-web-values.png)

Ce déploiement peut prendre une minute. Une fois le déploiement terminé, votre abonnement contient la solution.

## <a name="view-template-from-deployment-history"></a>Voir un modèle à partir de l’historique de déploiement
1. Accédez au groupe de ressources de votre nouveau groupe de ressources. Vous pouvez voir que le portail affiche le résultat du dernier déploiement. Sélectionnez ce lien.
   
      ![Groupe de ressources](./media/resource-manager-export-template/select-deployment.png)
2. Un historique des déploiements du groupe s’affiche. Dans votre cas, le portail affiche certainement un seul déploiement. Sélectionnez ce déploiement.
   
     ![Dernier déploiement](./media/resource-manager-export-template/select-history.png)
3. Le portail affiche un résumé du déploiement. Le résumé inclut l’état du déploiement et ses opérations, ainsi que les valeurs de paramètres que vous avez fournies. Pour voir le modèle utilisé pour le déploiement, sélectionnez **Afficher le modèle**.
   
     ![Afficher le résumé du déploiement](./media/resource-manager-export-template/view-template.png)
4. Resource Manager récupère pour vous les sept fichiers ci-dessous :
   
   1. **Modèle** - Modèle définissant l’infrastructure de votre solution. Lorsque vous avez créé le compte de stockage via le portail, Resource Manager a utilisé un modèle pour le déployer et a enregistré ce modèle pour référence ultérieure.
   2. **Paramètres** - Fichier de paramètres que vous pouvez utiliser pour transmettre des valeurs au cours du déploiement. Il contient les valeurs que vous avez fournies pendant le premier déploiement. Vous pouvez modifier ces valeurs lorsque vous redéployez le modèle.
   3. **CLI** - Fichier de script d’interface de ligne de commande Azure que vous pouvez utiliser pour déployer le modèle.
   3. **CLI 2.0** - Fichier script d’interface de ligne de commande Azure que vous pouvez utiliser pour déployer le modèle.
   4. **PowerShell** - Fichier de script Azure PowerShell que vous pouvez utiliser pour déployer le modèle.
   5. **.NET** - Classe .NET que vous pouvez utiliser pour déployer le modèle.
   6. **Ruby** - Classe Ruby que vous pouvez utiliser pour déployer le modèle.
      
      Par défaut, le portail affiche le modèle.
      
       ![Afficher le modèle](./media/resource-manager-export-template/see-template.png)
      
Il s’agit du modèle réel utilisé pour créer votre application web et votre base de données SQL. Notez qu’il contient des paramètres qui vous permettent de fournir différentes valeurs au cours du déploiement. Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

## <a name="export-the-template-from-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources
Si vous avez manuellement modifié vos ressources ou ajouté des ressources dans plusieurs déploiements, la récupération d’un modèle à partir de l’historique de déploiement ne reflète pas l’état actuel du groupe de ressources. Cette section vous montre comment exporter un modèle qui représente l’état actuel du groupe de ressources. Il s’agit d’un instantané du groupe de ressources, qui vous permet de refaire un déploiement sur le même groupe de ressources. Pour utiliser le modèle exporté pour d’autres solutions, vous devez le modifier considérablement.

> [!NOTE]
> Vous ne pouvez pas exporter un modèle pour un groupe de ressources qui contient plus de 200 ressources.
> 
> 

1. Pour afficher le modèle pour un groupe de ressources, sélectionnez **Script d’automatisation**.
   
      ![Exporter un groupe de ressources](./media/resource-manager-export-template/select-automation.png)
   
     Resource Manager évalue les ressources du groupe de ressources et génère un modèle pour ces ressources. Tous les types de ressources prennent en charge la fonction de modèle d’exportation. Vous pouvez rencontrer une erreur indiquant qu’il existe un problème avec l’exportation. Vous allez apprendre à gérer ces problèmes dans la section [Résoudre les problèmes d’exportation](#fix-export-issues) .
2. Vous voyez à nouveau les six fichiers que vous pouvez utiliser pour redéployer la solution. Mais cette fois le modèle est un peu différent. Notez que le modèle généré contient moins de paramètres que le modèle présenté dans la section précédente. De plus, la plupart des valeurs (par exemple celles relatives aux emplacements et références SKU) sont codées en dur dans ce modèle, plutôt que d’accepter une valeur de paramètre. Avant de réutiliser ce modèle, vous pouvez le modifier pour mieux utiliser les paramètres. 
   
3. Vous avez deux possibilités pour continuer à travailler avec ce modèle. Vous pouvez d’une part télécharger le modèle et travailler dessus en local avec un éditeur JSON. Vous pouvez également enregistrer le modèle dans votre bibliothèque et travailler dessus au moyen du portail.
   
     Si vous êtes habitué à utiliser un éditeur JSON comme [VS Code](https://code.visualstudio.com/) ou [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), il peut être intéressant de télécharger le modèle en local et d’utiliser cet éditeur. Pour travailler en local, sélectionnez **Télécharger**.
   
      ![Télécharger un modèle](./media/resource-manager-export-template/download-template.png)
   
     Si vous ne disposez pas d’un éditeur JSON, vous préférerez peut-être modifier le modèle sur le portail. Le reste de cet article suppose que vous avez enregistré le modèle dans votre bibliothèque dans le portail. Toutefois, les modifications que vous apportez à la syntaxe du modèle sont les mêmes en local avec un éditeur JSON et sur le portail. Pour travailler sur le portail, sélectionnez **Ajouter à la bibliothèque**.
   
      ![Ajouter à la bibliothèque](./media/resource-manager-export-template/add-to-library.png)
   
     Lorsque vous ajoutez un modèle à la bibliothèque, vous lui donnez un nom et une description. Sélectionnez ensuite **Enregistrer**.
   
     ![Définir les valeurs du modèle](./media/resource-manager-export-template/save-library-template.png)
4. Pour afficher un modèle enregistré dans votre bibliothèque, sélectionnez **Plus de services**, tapez **Modèles** pour filtrer les résultats et sélectionnez **Modèles**.
   
      ![Trouver des modèles](./media/resource-manager-export-template/find-templates.png)
5. Sélectionnez le modèle possédant le nom que vous avez enregistré.
   
      ![Sélectionner un modèle](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>Personnaliser le modèle
Le modèle exporté fonctionne bien si vous souhaitez créer la même application web et la même base de données SQL pour tous les déploiements. Toutefois, Resource Manager propose des options permettant de déployer des modèles avec une plus grande flexibilité. Cet article explique comment ajouter des paramètres pour le nom et le mot de passe de l’administrateur de base de données. Vous pouvez utiliser cette même approche pour ajouter davantage de flexibilité pour les autres valeurs dans le modèle.

1. Sélectionnez **Modifier** pour personnaliser le modèle.
   
     ![Afficher le modèle](./media/resource-manager-export-template/select-edit.png)
2. Sélectionnez le modèle.
   
     ![Modifier un modèle](./media/resource-manager-export-template/select-added-template.png)
3. Pour permettre la transmission des valeurs à spécifier pendant le déploiement, ajoutez les deux paramètres suivants à la section **parameters** du modèle :

   ```json
   "administratorLogin": {
       "type": "String"
   },
   "administratorLoginPassword": {
       "type": "SecureString"
   },
   ```

4. Pour utiliser les nouveaux paramètres, remplacez la définition SQL Server dans la section **resources**. Notez que **administratorLogin** et **administratorLoginPassword** utilisent maintenant des valeurs de paramètre.

   ```json
   {
       "comments": "Generalized from resource: '/subscriptions/{subscription-id}/resourceGroups/exportsite/providers/Microsoft.Sql/servers/tfserverexport'.",
       "type": "Microsoft.Sql/servers",
       "kind": "v12.0",
       "name": "[parameters('servers_tfserverexport_name')]",
       "apiVersion": "2014-04-01-preview",
       "location": "South Central US",
       "scale": null,
       "properties": {
           "administratorLogin": "[parameters('administratorLogin')]",
           "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
           "version": "12.0"
       },
       "dependsOn": []
   },
   ```

6. Sélectionnez **OK** lorsque vous avez terminé la modification du modèle.
7. Sélectionnez **Enregistrer** pour enregistrer les modifications apportées au modèle.
   
     ![Enregistrer un modèle](./media/resource-manager-export-template/save-template.png)
8. Pour redéployer le modèle mis à jour, sélectionnez **Déployer**.
   
     ![Déployer un modèle](./media/resource-manager-export-template/redeploy-template.png)
9. Fournissez des valeurs de paramètres, puis sélectionnez le groupe de ressources dans lequel vous souhaitez déployer les ressources.


## <a name="fix-export-issues"></a>Résoudre les problèmes d’exportation
Tous les types de ressources prennent en charge la fonction de modèle d’exportation. Pour contourner ce problème, ajoutez manuellement les ressources manquantes à votre modèle. Le message d’erreur inclut les types de ressources qui ne peuvent pas être exportés. Recherchez le type de ressource dans [Référence de modèle](/azure/templates/). Par exemple, pour ajouter manuellement une passerelle de réseau virtuel, consultez [Référence de modèle Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways).

> [!NOTE]
> Vous rencontrerez des problèmes d’exportation uniquement lors de l’exportation à partir d’un groupe de ressources et non à partir de votre historique de déploiement. Si votre dernier déploiement représente précisément l’état actuel du groupe de ressources, vous devez exporter le modèle à partir de l’historique de déploiement, plutôt que depuis le groupe de ressources. Procédez à une exportation à partir d’un groupe de ressources lorsque vous avez apporté des modifications au groupe de ressources qui ne sont pas définies dans un modèle unique.
> 
> 

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez déployer un modèle avec [PowerShell](resource-group-template-deploy.md), [l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md) ou [l’API REST](resource-group-template-deploy-rest.md).
* Pour savoir comment exporter un modèle par le biais de PowerShell, consultez [Exporter des modèles Azure Resource Manager avec PowerShell](resource-manager-export-template-powershell.md).
* Pour savoir comment exporter un modèle par le biais d’Azure CLI, consultez [Exporter des modèles Azure Resource Manager avec Azure CLI](resource-manager-export-template-cli.md).

