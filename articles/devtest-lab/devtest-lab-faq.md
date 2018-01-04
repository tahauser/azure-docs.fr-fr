---
title: FAQ Azure DevTest Labs | Microsoft Docs
description: "Trouvez des réponses aux questions les plus fréquentes sur Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: v-craic
ms.openlocfilehash: 24a3220a21280684a34405ac4c3d9f9eab9e3683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="azure-devtest-labs-faq"></a>FAQ d’Azure DevTest Labs
Obtenez des réponses aux questions les plus fréquemment posées sur Azure DevTest Labs.

**Généralités**
## <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?
Si votre question n’est pas répertoriée ici, faites-le-nous savoir pour que nous puissions vous aider à trouver une réponse.

* Postez une question à la fin de ce Forum aux questions. Dialoguez avec l’équipe du Cache Azure et les autres membres de la communauté au sujet de cet article.
* Pour atteindre un public plus large, postez une question sur le [forum MSDN consacré à Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Dialoguez avec l’équipe d’Azure DevTest Labs et les autres membres de la communauté.
* Concernant les demandes et idées de fonctionnalité, envoyez-les à [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Pourquoi dois-je utiliser Azure DevTest Labs ?
Azure DevTest Labs peut faire gagner du temps et de l’argent à votre équipe. Les développeurs peuvent créer leurs propres environnements à l’aide de plusieurs bases différentes. Ils peuvent également utiliser des artefacts pour déployer et configurer des applications rapidement. À l’aide de formules et d’images personnalisées, vous pouvez enregistrer des machines virtuelles en tant que modèles et les reproduire facilement au sein de l’équipe. En outre, DevTest Labs offre plusieurs stratégies configurables que les administrateurs de laboratoire peuvent utiliser pour réduire le gaspillage et gérer les environnements d’une équipe. Ces stratégies comprennent l’arrêt automatique, le seuil de coût, le nombre maximal de machines virtuelles par utilisateur et la taille maximale de machine virtuelle. Pour plus de détails sur DevTest Labs, consultez la [présentation](devtest-lab-overview.md) ou la [vidéo d’introduction](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Que signifie « libre-service sans problème » ?
« Libre-service sans problème » signifie que les développeurs et les testeurs créent leurs propres environnements en fonction des besoins. Les administrateurs ont l’assurance que DevTest Labs peut aider à réduire le gaspillage et les coûts de contrôle. Les administrateurs peuvent spécifier quelles tailles de machine virtuelle sont autorisées, le nombre maximal de machines virtuelles et le moment où les machines virtuelles sont démarrées et arrêtées. DevTest Labs facilite également la surveillance des coûts et la définition des alertes afin que vous soyez informé sur l’utilisation des ressources du laboratoire.

## <a name="how-can-i-use-devtest-labs"></a>Comment puis-je Azure DevTest Labs ?
DevTest Labs vous est utile dès que vous voulez créer des environnements de test ou de développement, les reproduire ou les gérer rapidement à l’aide des stratégies de réduction des coûts.

Voici quelques scénarios pour lesquels nos clients utilisent DevTest Labs :

* Gérer les environnements de développement et de test en un seul emplacement. Utiliser des stratégies pour réduire les coûts et créer des images personnalisées pour partager des builds au sein de l’équipe.
* Développer une application à l’aide d’images personnalisées pour enregistrer l’état du disque tout au long des étapes de développement.
* Suivre les coûts par rapport à la progression.
* Créer des environnements de test en série pour des tests d’assurance qualité.
* Utiliser des artefacts et des formules pour configurer et reproduire facilement une application dans divers environnements.
* Distribuer des machines virtuelles pour des hackathons (travail de test ou de développement collaboratif), puis les déprovisionner facilement quand l’événement se termine.

## <a name="how-am-i-billed-for-devtest-labs"></a>Combien me coûte DevTest Labs ?
DevTest Labs est un service gratuit. La création de laboratoires et la configuration de stratégies, de modèles et d’artefacts dans DevTest Labs ne coûtent rien. Vous payez uniquement pour les ressources Azure utilisées dans vos laboratoires, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels. Pour plus de détails sur le coût des ressources de laboratoire, consultez la page sur la [tarification Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Sécurité**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quels sont les différents niveaux de sécurité dans DevTest Labs ?
L’accès à la sécurité est déterminé par le [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-built-in-roles.md). Pour savoir comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue, comme défini par RBAC.

* **Autorisation** : une autorisation est un accès défini pour une action spécifique. Par exemple, une autorisation peut être en lecture seule pour toutes les machines virtuelles.
* **Rôle** : un rôle est un jeu d’autorisations qui peuvent être regroupées et assignées à un utilisateur. Par exemple, un utilisateur détenteur du rôle propriétaire de l’abonnement a accès à toutes les ressources au sein d’un abonnement.
* **Étendue** : une étendue est un niveau dans la hiérarchie d’une ressource Azure. Par exemple, une étendue peut être un groupe de ressources, un même laboratoire ou l’ensemble de l’abonnement.

Dans l’étendue de DevTest Labs, il existe deux types de rôles qui définissent des autorisations utilisateur :

* **Propriétaire de laboratoire** : un propriétaire de laboratoire a accès à toutes les ressources du laboratoire. Le propriétaire d’un laboratoire peut modifier les stratégies, lire et écrire sur toutes les machines virtuelles, changer le réseau virtuel et ainsi de suite.
* **Utilisateur de laboratoire** : un utilisateur de laboratoire peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels. Toutefois, il ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs. 

Vous pouvez également créer des rôles personnalisés dans DevTest Labs. Pour découvrir comment créer des rôles personnalisés dans DevTest Labs, consultez [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Étant donné que les étendues sont hiérarchiques, quand un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit automatiquement ces autorisations pour chaque niveau d’étendue inférieur dans l’étendue. Par exemple, si un utilisateur se voit affecter le rôle de propriétaire d’abonnement, il a accès à toutes les ressources dans un abonnement. Ces ressources incluent toutes les machines virtuelles, tous les réseaux virtuels et tous les laboratoires. Un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Ainsi, un propriétaire de laboratoire ne peut pas voir les machines virtuelles, les réseaux virtuels ou toutes les autres ressources qui sont en dehors du laboratoire.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Comment puis-je créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Pour savoir comment créer des rôles personnalisés et assigner des autorisations à un rôle, consultez l’article complet [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Voici un exemple de script qui crée le rôle *Utilisateur avancé DevTest Labs*, qui a l’autorisation de démarrer et d’arrêter toutes les machines virtuelles dans le laboratoire :

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**Automatisation et intégration CI/CD**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ?
Si vous utilisez Visual Studio Team Services, vous pouvez utiliser une [extension des tâches Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) pour automatiser votre pipeline de versions dans DevTest Labs. Voici quelques-unes des tâches que vous pouvez effectuer avec cette extension :

* Créer et déployer automatiquement une machine virtuelle. Vous pouvez également configurer la machine virtuelle avec la build la plus récente en utilisant des tâches de copie de fichiers Azure ou de PowerShell Team Services.
* Capturer automatiquement l’état d’une machine virtuelle après le test, afin de reproduire un bogue sur la même machine virtuelle pour un examen approfondi.
* Supprimer la machine virtuelle à la fin du pipeline de versions quand elle n’est plus nécessaire.

Les billets de blog suivants offrent des conseils et des informations sur l’utilisation de l’extension Team Services :

* [DevTest Labs et l’extension Visual Studio Team Services](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Déployer une nouvelle machine virtuelle dans un laboratoire DevTest Labs existant à partir de Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Utiliser la gestion des mises en production Team Services pour des déploiements en continu vers DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pour d’autres chaînes d’outils d’intégration continue (CI)/livraison continue (CD), vous pouvez réaliser les mêmes scénarios en déployant des [modèles Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) à l’aide des [applets de commande Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) et des [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). De plus, vous pouvez utiliser les [API REST pour DevTest Labs](http://aka.ms/dtlrestapis) pour une intégration à votre chaîne d’outils.  


**Machines virtuelles**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Pourquoi ne puis-je pas voir certaines machines virtuelles dans le panneau Machines virtuelles alors que je peux les voir dans DevTest Labs ?
Quand vous créez une machine virtuelle dans DevTest Labs, l’autorisation d’accéder à cette machine virtuelle vous est accordée. Vous pouvez voir la machine virtuelle à la fois dans le panneau Laboratoires et dans le panneau **Machines virtuelles**. Les utilisateurs assignés au rôle utilisateur de laboratoire DevTest Labs peuvent voir toutes les machines virtuelles qui ont été créées dans le laboratoire dans le panneau **All Virtual Machines** (Toutes les machines virtuelles) du laboratoire. Toutefois, les utilisateurs détenteurs du rôle utilisateur de laboratoire DevTest Labs ne reçoivent pas automatiquement l’accès en lecture aux ressources des machines virtuelles créées par d’autres utilisateurs. Ainsi, ces machines virtuelles ne s’affichent pas dans le panneau **Machines virtuelles**.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Quelle est la différence entre une image personnalisée et une formule ?
Une image personnalisée est un disque dur virtuel (VHD). Une formule est une image que vous pouvez configurer avec des paramètres supplémentaires, puis enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez créer rapidement plusieurs environnements à l’aide de la même image de base, immuable. Une formule peut être préférable si vous souhaitez reproduire la configuration de votre machine virtuelle avec les tout derniers bits, dans le cadre d’un sous-réseau ou d’un réseau virtuel ou pour obtenir une machine virtuelle d’une taille spécifique. Pour plus de détails, consultez [Comparaison entre les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ?
Vous avez deux options pour créer simultanément plusieurs machines virtuelles à partir du même modèle :
* Vous pouvez utiliser [l’extension de tâches Visual Studio Team Services](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Vous pouvez [générer un modèle Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) quand vous créez une machine virtuelle et [déployer le modèle Resource Manager à partir de Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire DevTest Labs ?
Pour copier vos machines virtuelles existantes vers DevTest Labs :

1. Copiez le fichier de disque dur virtuel de votre machine virtuelle existante à l’aide d’un [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Créez l’image personnalisée](devtest-lab-create-template.md) dans votre laboratoire DevTest Labs.
3. Créez une machine virtuelle dans le laboratoire à partir de votre image personnalisée.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Puis-je attacher plusieurs disques à mes machines virtuelles ?
Oui, vous pouvez attacher plusieurs disques à vos machines virtuelles.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Si je souhaite utiliser une image de système d’exploitation Windows pour mon test, dois-je acheter un abonnement MSDN ?
Pour utiliser des images de système d’exploitation client Windows (Windows 7 ou une version ultérieure) à des fins de développement ou de test dans Azure, vous devez effectuer l’une des opérations suivantes :

- [Achetez un abonnement MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Si vous disposez d’un contrat Entreprise, créez un abonnement Azure avec [l’offre Entreprise Dev/Test](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Pour plus d’informations sur les crédits Azure pour chaque offre de MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?
Pour automatiser le chargement des fichiers de VHD afin de créer des images personnalisées, vous avez deux options :

* Utiliser [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) pour copier ou charger des fichiers de VHD sur le compte de stockage qui est associé au laboratoire.
* Utiliser [l’explorateur de stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). L’Explorateur de stockage est une application autonome qui s’exécute sur Windows, OSX et Linux.   

Pour trouver le compte de stockage de destination associé à votre laboratoire :

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
3. Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire.
4. Sous **Vue d’ensemble**, sélectionnez l’un des comptes de stockage.
5. Sélectionnez **Objets Blob**.
6. Recherchez les téléchargements dans la liste. S’il n’y en a aucun, revenez à l’étape 4 et essayez un autre compte de stockage.
7. Utilisez **l’URL** en tant que destination dans votre commande AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Comment puis-je automatiser le processus de suppression de toutes les machines virtuelles dans mon laboratoire ?
Vous pouvez supprimer des machines virtuelles de votre laboratoire dans le portail Azure. Vous pouvez également supprimer toutes les machines virtuelles de votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, sous le commentaire **Values to change** (Valeurs à changer), modifiez les valeurs des paramètres. Vous pouvez récupérer les valeurs `subscriptionId`, `labResourceGroup` et `labName` à partir du volet de laboratoire dans le portail Azure.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Login-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefacts**
## <a name="what-are-artifacts"></a>Que sont les artefacts ?
Les artefacts sont des éléments personnalisables que vous pouvez utiliser pour déployer vos tout derniers bits ou déployer vos outils de développement sur une machine virtuelle. Attachez les artefacts à votre machine virtuelle quand vous créez celle-ci. Une fois la machine virtuelle approvisionnée, les artefacts la déploient et la configurent. Divers artefacts préexistants sont disponibles dans notre [dépôt GitHub public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Vous pouvez également [créer vos propres artefacts](devtest-lab-artifact-author.md).


**Configuration du labo**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Comment puis-je créer un laboratoire à partir d’un modèle Resource Manager ?
Nous offrons un [dépôt GitHub de modèles Azure Resource Manager de laboratoire](https://aka.ms/dtlquickstarttemplate) que vous pouvez déployer tels quels ou modifier pour créer des modèles personnalisés pour vos laboratoires. À chaque modèle est associé un lien que vous pouvez utiliser pour déployer le laboratoire tel quel dans votre propre abonnement Azure. Ou bien, vous pouvez personnaliser le modèle et [effectuer le déploiement à l’aide de PowerShell ou de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Pourquoi mes machines virtuelles sont-elles créées dans différents groupes de ressources, avec des noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ?
Les groupes de ressources sont créés de cette façon afin que DevTest Labs puisse gérer les autorisations utilisateur et accéder aux machines virtuelles. Bien que vous puissiez déplacer une machine virtuelle vers un autre groupe de ressources et utiliser le nom que vous voulez, nous vous recommandons de ne pas apporter de modifications aux groupes de ressources. Nous travaillons sur l’amélioration de cette fonctionnalité pour une flexibilité accrue.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Combien de laboratoires puis-je créer sous le même abonnement ?
Il n’existe pas de limite spécifique concernant le nombre de laboratoires qui peuvent être créés par abonnement. Toutefois, la quantité de ressources utilisées par abonnement est limitée. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites associés aux abonnements Azure](../azure-subscription-service-limits.md) et sur [la façon d’augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Combien de machines virtuelles puis-je créer par laboratoire ?
Il n’existe aucune limite spécifique concernant le nombre de machines virtuelles qui peuvent être créées par laboratoire. Toutefois, les ressources (cœurs de machines virtuelles, adresses IP publiques, etc.) utilisées sont limitées par abonnement. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites associés aux abonnements Azure](../azure-subscription-service-limits.md) et sur [la façon d’augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Comment partager un lien direct vers mon laboratoire ?

1. Dans le portail Azure, accédez au laboratoire.
2. Copiez l’URL du laboratoire dans votre navigateur, puis partagez-la avec les utilisateurs de votre laboratoire.

> [!NOTE]
> Si un utilisateur du laboratoire est un utilisateur externe qui a un [compte Microsoft](#what-is-a-microsoft-account), mais qui n’est pas membre de l’instance Active Directory de votre organisation, il peut voir un message d’erreur quand il tente d’accéder au lien partagé. Si un utilisateur externe voit un message d’erreur, demandez-lui de sélectionner d’abord son nom dans le coin supérieur droit du portail Azure. Ensuite, dans la section **Annuaire** du menu, l’utilisateur peut sélectionner l’annuaire où se trouve le laboratoire.
>
>

## <a name="what-is-a-microsoft-account"></a>Qu’est-ce qu’un compte Microsoft ?
Un compte Microsoft est un compte que vous utilisez pour la plupart des opérations effectuées avec les services et les périphériques Microsoft. Il s’agit d’une adresse e-mail et d’un mot de passe que vous utilisez pour vous connecter à Skype, Outlook.com, OneDrive, Windows Phone et Xbox Live. Quand vous utilisez un compte unique, vos fichiers, photos, contacts et paramètres vous suivent sur n’importe quel appareil.

> [!NOTE]
> Auparavant, un compte Microsoft était appelé *Windows Live ID*.
>
>


**Résolution des problèmes**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ?
Pour savoir comment récupérer les journaux relatifs à l’échec d’un artefact, consultez [Guide pratique pour diagnostiquer les échecs d’artefacts dans DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?
Il se peut que votre nom de réseau virtuel contienne des points. Dans ce cas, essayez de supprimer les points ou de les remplacer par des traits d’union. Ensuite, réessayez d’enregistrer le réseau virtuel.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Pourquoi l’erreur signalant que la ressource parente est introuvable s’affiche-t-elle quand j’approvisionne une machine virtuelle à partir de PowerShell ?
Quand une ressource est parent d’une autre ressource, la ressource parente doit exister pour que vous puissiez créer la ressource enfant. Si la ressource parente n’existe pas, vous voyez un message **ParentResourceNotFound**. Si vous ne spécifiez pas de dépendance sur la ressource parente, la ressource enfant peut être déployée avant cette dernière.

Les machines virtuelles sont des ressources enfants se trouvant dans un laboratoire d’un groupe de ressources. Quand vous utilisez des modèles Resource Manager pour déployer des machines virtuelles à l’aide de PowerShell, le nom du groupe de ressources fourni dans le script PowerShell doit être le nom du groupe de ressources du laboratoire. Pour plus d’informations, consultez la rubrique [Résolution des erreurs courantes dans des déploiements Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Où puis-je trouver plus d’informations d’erreur si le déploiement d’une machine virtuelle échoue ?
Les erreurs de déploiement de machine virtuelle sont capturées dans des journaux d’activité. Vous pouvez trouver les journaux d’activité des machines virtuelles du laboratoire sous **Journaux d’audit** ou **Diagnostics de machine virtuelle** dans le menu de la ressource dans le panneau Machine virtuelle du laboratoire (le panneau s’affiche une fois que vous sélectionnez la machine virtuelle dans la liste **Mes machines virtuelles**).

Parfois, l’erreur de déploiement se produit avant que ne commence le déploiement de la machine virtuelle. C’est par exemple le cas quand la limite d’abonnement pour une ressource qui a été créée avec la machine virtuelle est dépassée. Dans ce cas, les détails de l’erreur sont capturés dans les journaux d’activité au niveau du laboratoire. Les journaux d’activité sont situés en bas des paramètres **Configuration et stratégies**. Pour plus d’informations sur l’utilisation des journaux d’activité dans Azure, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
