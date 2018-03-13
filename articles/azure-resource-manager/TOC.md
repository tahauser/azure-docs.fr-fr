# [Documentation Azure Resource Manager](index.md)

# Vue d'ensemble
## [Présentation de Resource Manager](resource-group-overview.md)
## [Fournisseurs et types de ressources](resource-manager-supported-services.md)
## [Déploiements Resource Manager et classiques](resource-manager-deployment-model.md)
## [Gouvernance de l’abonnement](resource-manager-subscription-governance.md)
## [Organiser les ressources avec des groupes d’administration](management-groups-overview.md)

# Prise en main
## [Création et déploiement du modèle](resource-manager-create-first-template.md)
## [Extension de VS Code pour les modèles](resource-manager-vscode-extension.md)
## [Visual Studio avec Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Procédures
## Créer des modèles
### [Sections du modèle](resource-group-authoring-templates.md)
#### [Paramètres](resource-manager-templates-parameters.md)
#### [Variables](resource-manager-templates-variables.md)
#### [Ressources](resource-manager-templates-resources.md)
#### [Sorties](resource-manager-templates-outputs.md)
### [Modèles liés et imbriqués](resource-group-linked-templates.md)
### [Définir la dépendance entre les ressources](resource-group-define-dependencies.md)
### [Créer plusieurs instances](resource-group-create-multiple.md)
### [Mettre à jour une ressource](/azure/architecture/building-blocks/extending-templates/update-resource)

## Déployer
### Azure PowerShell
#### [Déployer un modèle](resource-group-template-deploy.md)
#### [Déployer un modèle privé avec un jeton SAS](resource-manager-powershell-sas-token.md)
#### [Exporter un modèle et le redéployer](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Déployer un modèle](resource-group-template-deploy-cli.md)
#### [Déployer un modèle privé avec un jeton SAS](resource-manager-cli-sas-token.md)
#### [Exporter un modèle et le redéployer](resource-manager-export-template-cli.md)
### Portail Azure
#### [Déploiement des ressources](resource-group-template-deploy-portal.md)
#### [Exportation du modèle](resource-manager-export-template.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Plusieurs groupes de ressources ou abonnements](resource-manager-cross-resource-group-deployment.md)
### [Intégration continue avec Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md)

## gérer
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
### [Portail Azure](resource-group-portal.md)
### [Utiliser des balises pour organiser les ressources](resource-group-using-tags.md)
### [Déplacer les ressources vers un nouveau groupe ou un nouvel abonnement](resource-group-move-resources.md)
### [Créer des groupes d’administration](management-groups-create.md)
### [Gérer vos groupes d’administration](management-groups-manage.md)
### [Exemples de gouvernance](resource-manager-subscription-examples.md)

## Contrôler l’accès
### Créer un principal du service
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [interface de ligne de commande Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Portail Azure](resource-group-create-service-principal-portal.md)
### [API d’authentification pour l’accès aux abonnements](resource-manager-api-authentication.md)
### [Verrouiller des ressources](resource-group-lock-resources.md)

## Audit
### [Afficher les journaux d’activité](resource-group-audit.md)
### [Voir les opérations de déploiement](resource-manager-deployment-operations.md)

## Résolution des problèmes
### [Résoudre les erreurs de déploiement](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Problèmes de déploiement Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Problèmes d’approvisionnement et de répartition pour Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Problèmes d’approvisionnement et de répartition pour Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Problèmes de déploiement Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Informations de référence
## [Format de modèle](/azure/templates/)
## [Fonctions des modèles de gestionnaire des ressources Azure](resource-group-template-functions.md)
### [Fonctions de tableau et d’objet](resource-group-template-functions-array.md)
### [Fonctions de comparaison](resource-group-template-functions-comparison.md)
### [Fonctions de déploiement](resource-group-template-functions-deployment.md)
### [Fonctions logiques](resource-group-template-functions-logical.md)
### [Fonctions numériques](resource-group-template-functions-numeric.md)
### [Fonctions de ressource](resource-group-template-functions-resource.md)
### [Fonctions de chaîne](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [interface de ligne de commande Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Demandes de limitation](resource-manager-request-limits.md)
## [Suivre les opérations asynchrones](resource-manager-async-operations.md)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
