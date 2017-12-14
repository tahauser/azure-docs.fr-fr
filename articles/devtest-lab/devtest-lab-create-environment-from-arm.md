---
title: "Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager | Microsoft Docs"
description: "Découvrez comment créer des environnements de plusieurs machines virtuelles et des ressources PaaS dans Azure DevTest Labs à partir d’un modèle Azure Resource Manager"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 381b3e5cc82e71dde4fe3a4266678dbd5919290d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager

Le [portail Azure ](http://go.microsoft.com/fwlink/p/?LinkID=525040) vous permet de [créer et d’ajouter facilement une machine virtuelle à un laboratoire](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Cela fonctionne bien pour la création d’une machine virtuelle à la fois. Toutefois, si l’environnement contient plusieurs machines virtuelles, chaque machine virtuelle doit être créée individuellement. Pour des scénarios comme une application web à plusieurs niveaux ou une batterie de serveurs SharePoint, un mécanisme est nécessaire pour permettre la création de plusieurs machines virtuelles en une seule étape. Les modèles Azure Resource Manager, vous permettent désormais de définir l’infrastructure et la configuration de votre solution Azure et de déployer de manière répétée plusieurs machines virtuelles dans un état cohérent. Cette fonctionnalité permet de bénéficier des avantages suivants :

- Les modèles Azure Resource Manager sont chargés directement à partir de votre dépôt de contrôle de code source (GitHub ou Team Services Git).
- Suite à la configuration, vos utilisateurs peuvent créer un environnement en choisissant un modèle Azure Resource Manager à partir du portail Azure, ce qu’ils peuvent faire avec les autres types de [bases de machines virtuelles](./devtest-lab-comparing-vm-base-image-types.md).
- Les ressources PaaS Azure peuvent être approvisionnées dans un environnement à partir d’un modèle Azure Resource Manager, en plus des machines virtuelles IaaS.
- Le coût des environnements peut être suivi dans le laboratoire en plus des machines virtuelles créées par d’autres types de bases.
- Une fois créées, les ressources PaaS s’affichent dans le suivi des coûts. Notez que l’arrêt automatique des machines virtuelles ne s’applique pas aux ressources PaaS.
- Les utilisateurs disposent du même contrôle de stratégie sur les machines virtuelles des environnements que sur celles d’un laboratoire.

Découvrez les nombreux [avantages de l’utilisation des modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) pour déployer, mettre à jour et supprimer toutes vos ressources lab en une seule opération.

> [!NOTE]
> Si vous utilisez un modèle Resource Manager comme base pour créer des machines virtuelles de laboratoire, sachez qu’il existe des différences entre la création d’un groupe de machines virtuelles et celle de plusieurs machines virtuelles individuelles. La rubrique « Utiliser un modèle Resource Manager de machine virtuelle » explique ces différences en détail.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Configurer les dépôts du modèle Azure Resource Manager

Dans le cadre des bonnes pratiques liées à l’Infrastructure as Code et à la Configuration as Code, des modèles d’environnement doivent être gérés dans le contrôle de code source. Azure DevTest Labs suit cette pratique et charge tous les modèles Azure Resource Manager directement à partir de vos dépôts GitHub ou VSTS Git. Par conséquent, les modèles Resource Manager peuvent être utilisés tout le long du cycle de publication, de l’environnement de test jusqu’à l’environnement de production.

Quelques règles sont à prendre en compte pour organiser vos modèles Azure Resource Manager dans un dépôt :

- Le fichier de modèle principal doit être nommé `azuredeploy.json`. 

    ![Fichiers de modèle Azure Resource Manager clés](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Si vous souhaitez utiliser des valeurs de paramètres définies dans un fichier de paramètres, le fichier de paramètres doit être nommé `azuredeploy.parameters.json`.
- Vous pouvez utiliser les paramètres `_artifactsLocation` et `_artifactsLocationSasToken` pour créer la valeur de l’URI parametersLink et permettre à DevTest Labs de gérer automatiquement les modèles imbriqués. Pour plus d’informations, consultez [How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Les métadonnées peuvent être définies pour spécifier le nom d’affichage et la description du modèle. Ces métadonnées doivent être dans un fichier nommé `metadata.json`. Le fichier de métadonnées d’exemple suivant montre comment spécifier le nom d’affichage et la description : 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

Les étapes suivantes vous guident pour ajouter un dépôt dans votre laboratoire à l’aide du portail Azure. 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
1. Dans le panneau du laboratoire, sélectionnez **Configuration et stratégies**.

    ![Configuration et stratégies](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. À partir de la liste de paramètres **Configuration et stratégies**, sélectionnez **Dépôts**. Le panneau **Dépôts** liste les dépôts qui ont été ajoutés au laboratoire. Un dépôt nommé `Public Repo` est généré automatiquement pour tous les laboratoires et se connecte au [dépôt GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab) qui contient plusieurs artefacts de machine virtuelle que vous pouvez utiliser.

    ![Dépôt public](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Sélectionnez **Ajouter +** pour ajouter votre dépôt de modèle Azure Resource Manager.
1. Lorsque le second panneau **Dépôts** s’ouvre, entrez les informations nécessaires comme suit :
    - **Nom** : entrez le nom du dépôt utilisé dans le laboratoire.
    - **URL de clonage Git** : entrez l’URL de clonage Git HTTPS provenant de GitHub ou Visual Studio Team Services.  
    - **Branche** : entrez le nom de la branche pour accéder à vos définitions de modèle Azure Resource Manager. 
    - **Jeton d’accès personnel** : le jeton d’accès personnel est utilisé pour accéder en toute sécurité à votre dépôt. Pour obtenir votre jeton à partir de Visual Studio Team Services, sélectionnez **&lt;YourName > > Mon profil > Sécurité > Public access token (Jeton d’accès public)**. Pour obtenir votre jeton à partir de GitHub, sélectionnez votre avatar, puis sélectionnez **Paramètres > Public access token (Jeton d’accès public)**. 
    - **Chemins d’accès du dossier**  : en utilisant l’un des deux champs d’entrée, entrez le chemin d’accès du dossier qui commence par une barre oblique - / - et est relatif à votre Url de clonage Git de vos définitions d’artefacts (premier champ d’entrée) ou de vos définitions de modèle Azure.   
    
        ![Dépôt public](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. Une fois tous les champs nécessaires renseignés et validés, cliquez sur **Enregistrer**.

La section suivante vous guidera dans la création d’environnements à partir d’un modèle Azure Resource Manager.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Créer un environnement à partir d’un modèle Azure Resource Manager

Une fois qu’un dépôt de modèles Azure Resource Manager a été configuré dans le laboratoire, vos utilisateurs de laboratoire peuvent créer un environnement à l’aide du portail Azure en procédant comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
1. Dans le panneau du laboratoire, sélectionnez **Ajouter +**.
1. Le panneau **Choisir une base** affiche les images de base que vous pouvez utiliser avec les modèles Azure Resource Manager répertoriés en premier. Sélectionnez le modèle Azure Resource Manager souhaité.

    ![Choisir une base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Dans le panneau **Ajouter**, entrez la valeur **Nom de l’environnement**. Le nom de l’environnement est ce qui est affiché aux utilisateurs dans le laboratoire. Les autres champs d’entrée sont définis dans le modèle Azure Resource Manager. Si les valeurs par défaut sont définies dans le modèle ou si le fichier `azuredeploy.parameter.json` est présent, les valeurs par défaut sont affichées dans ces champs d’entrée. Pour les paramètres de type *chaîne sécurisée*, vous pouvez utiliser les secrets stockés dans le [magasin personnel des secrets](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store) du laboratoire.

    ![Ajouter un panneau](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Il existe plusieurs valeurs de paramètres, même quand elles sont spécifiées, qui sont affichées sous forme de valeurs vides. Par conséquent, si les utilisateurs affectent ces valeurs aux paramètres dans un modèle Azure Resource Manager, DevTest Labs n’affiche pas les valeurs ; au lieu de cela des champs d’entrée vides sont affichés et les utilisateurs du laboratoire doivent les renseigner lors de la création de l’environnement.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Sélectionnez **Ajouter** pour créer l’environnement. L’environnement lance immédiatement l’approvisionnement avec l’affichage de l’état dans la liste **Mes machines virtuelles**. Un groupe de ressources est automatiquement créé par le laboratoire pour approvisionner toutes les ressources définies dans le modèle Azure Resource Manager.
1. Une fois que l’environnement est créé, sélectionnez-le dans la liste **Mes machines virtuelles** pour ouvrir le panneau du groupe de ressources et parcourir les ressources approvisionnées dans l’environnement.
    
    ![Liste Mes machines virtuelles](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Vous pouvez également développer l’environnement pour afficher uniquement la liste des machines virtuelles qui sont approvisionnées dans l’environnement.
    
    ![Liste Mes machines virtuelles](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Cliquez sur l’un des environnements pour afficher les actions disponibles comme appliquer des artefacts, attacher des disques de données, modifier le temps d’arrêt automatique, etc.

    ![Actions de l’environnement](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Étapes suivantes
* Après la création d’une machine virtuelle, vous pouvez vous connecter à celle-ci en sélectionnant **Connexion** dans le panneau de la machine virtuelle.
* Vous pouvez afficher et gérer les ressources d’un environnement en sélectionnant celui-ci dans la liste **Mes machines virtuelles** de votre laboratoire. 
* Explorez les [modèles Azure Resource Manager de la galerie de modèles Azure QuickStart](https://github.com/Azure/azure-quickstart-templates)
