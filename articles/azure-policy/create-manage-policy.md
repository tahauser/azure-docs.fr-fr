---
title: "Utiliser une stratégie Azure pour créer et gérer des stratégies afin d’appliquer la conformité de l’organisation | Microsoft Docs"
description: "Utilisez une stratégie Azure pour appliquer des normes, se conformer aux réglementations de conformité et d’audit, maîtriser les coûts, garantir la sécurité et maintenir les performances, et imposer les principes de conception à l’échelle de l’entreprise."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/18/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: a3d47abcbf41133b9bc7194fd97f9b66a70003ff
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Créer et gérer des stratégies pour appliquer la conformité

Il est important de comprendre comment créer et gérer des stratégies dans Azure pour rester conforme aux normes de votre entreprise et aux contrats de niveau de service. Dans ce didacticiel, vous allez découvrir comment utiliser Azure Policy pour effectuer certaines des tâches les plus courantes liées à la création, à l’affectation et à la gestion des stratégies dans votre organisation, notamment :

> [!div class="checklist"]
> * Affecter une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> * Créer et affecter une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> * Résoudre une ressource non conforme ou refusée
> * Implémenter une nouvelle stratégie dans l’ensemble de l’entreprise

Si vous souhaitez affecter une stratégie pour identifier l’état actuel de la conformité de vos ressources existantes, consultez les articles de démarrage rapide qui passent en revue la marche à suivre. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="assign-a-policy"></a>Attribution d’une stratégie

La première étape de l’application de la conformité avec une stratégie Azure consiste à affecter une définition de stratégie. Une définition de stratégie définit dans quelle condition une stratégie est appliquée et l’action à effectuer. Dans cet exemple, affectez une définition de stratégie intégrée appelée *Nécessiter SQL Server version 12.0* afin d’appliquer la condition selon laquelle toutes les bases de données SQL Server doivent être de version 12.0 pour être compatibles.

1. Lancez le service de la stratégie Azure dans le portail Azure en recherchant puis en sélectionnant **Stratégie** dans le volet gauche.

   ![Rechercher une stratégie](media/assign-policy-definition/search-policy.png)

2. Sélectionnez **Attributions** dans le volet gauche de la page Azure Policy. Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.
3. Sélectionnez **Assigner une stratégie** en haut du volet **Affectations**.

   ![Affecter une définition de stratégie](media/create-manage-policy/select-assign-policy.png)

4. Dans la page **Assigner une stratégie**, cliquez sur le ![bouton de définition de stratégie](media/assign-policy-definition/definitions-button.png) situé en regard du champ **Stratégie** pour afficher la liste des définitions disponibles. Vous pouvez filtrer le **Type** de définition de stratégie sur *BuiltIn* pour les afficher dans leur ensemble et lire leurs descriptions.

   ![Afficher les définitions de stratégie disponibles](media/create-manage-policy/open-policy-definitions.png)

5. Sélectionnez **Nécessitent SQL Server version 12.0**. Si vous ne la trouvez pas immédiatement, tapez **Nécessiter SQL Server version 12.0** dans la zone de recherche, puis appuyez sur ENTRÉE.

   ![Rechercher une stratégie](media/create-manage-policy/select-available-definition.png)

6. Le **nom** affiché est automatiquement renseigné, mais vous pouvez le modifier. Pour cet exemple, utilisez *Nécessiter SQL Server version 12.0*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur la façon dont cette affectation de stratégie s’assure que tous les serveurs SQL créés dans cet environnement utilisent la version 12.0.

7. Définissez le niveau tarifaire sur **Standard** pour vous assurer que la stratégie soit appliquée aux ressources existantes.

   Il existe deux niveaux tarifaires dans Azure Policy : *Gratuit* et *Standard*. Avec le niveau Gratuit, vous pouvez uniquement appliquer des stratégies à des ressources futures, alors qu’avec le niveau Standard, vous pouvez également les appliquer à des ressources existantes pour mieux comprendre votre état de conformité. Comme Azure Policy est en préversion, il n’existe pas encore de modèle de tarification publié. Vous ne recevrez donc pas de facture pour la sélection du niveau *Standard*. Pour en savoir plus sur les prix, consultez : [Prix Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

8. Sélectionnez **l’étendue** : l’abonnement (ou le groupe de ressources) précédemment enregistré. Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources.

   L’abonnement **Azure Analytics Capacity Dev** est utilisé dans cet exemple. Votre abonnement sera différent.

10. Sélectionnez **Attribuer**.

## <a name="implement-a-new-custom-policy"></a>Implémenter une nouvelle stratégie personnalisée

Maintenant que vous avez affecté une définition de stratégie intégrée, vous pouvez continuer avec Azure Policy. Créez une stratégie personnalisée pour réduire les coûts en veillant à ce que les machines virtuelles créées dans votre environnement ne puissent pas être dans la série G. De cette manière, à chaque fois qu’un utilisateur de votre organisation tente de créer une machine virtuelle dans la série G, la requête est refusée.

1. Sélectionnez **Définition** sous **Création** dans le volet gauche.

   ![Définition sous Création](media/create-manage-policy/definition-under-authoring.png)

2. Sélectionnez **+ Définition de stratégie**.
3. Entrez les informations suivantes :

   - Le nom de la définition de stratégie : *nécessite des références (SKU) de machines virtuelles inférieures à la série G*
   - La description de l’objectif de la définition de stratégie : cette définition de stratégie impose que toutes les machines virtuelles créées dans cette étendue possèdent des références (SKU) inférieures à la série G afin de réduire le coût.
   - Abonnement dans lequel réside la définition de stratégie. Dans ce cas, la définition de stratégie se trouve dans **Advisor Analytics Capacity Dev**. La liste de vos abonnements sera différente.
   - Faites votre choix parmi les options existantes ou créez une catégorie pour cette définition de stratégie.
   - Copiez le code json suivant et mettez-le à jour selon vos besoins avec :
      - Les paramètres de la stratégie.
      - Les règles/conditions de la stratégie, dans ce cas : la taille de la référence (SKU) de la machine virtuelle est égale à la série G
      - L’effet de la stratégie, dans ce cas – **Refuser**.

    Le fichier json devrait ressembler à ce qui suit. Collez le code révisé dans le portail Azure.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
    ```

    La valeur de la *propriété de champ* de la règle de stratégie doit être l’une des suivantes : Nom, Type, Emplacement, Balises ou un alias. Par exemple : `"Microsoft.Compute/VirtualMachines/Size"`.

    Pour afficher d’autres exemples de code json, consultez l’article [Modèles pour Azure Policy](json-samples.md).

4. Sélectionnez **Enregistrer**.

## <a name="create-a-policy-definition-with-rest-api"></a>Créer une définition de stratégie avec l’API REST

Vous pouvez créer une stratégie avec l’API REST pour les définitions des stratégies. L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes.
Pour créer une définition de stratégie, utilisez l’exemple suivant :

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Incluez un texte de demande semblable à l’exemple suivant :

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Créer une définition de stratégie avec PowerShell

Avant de passer à l’exemple PowerShell, assurez-vous d’avoir installé la dernière version d’Azure PowerShell. Les paramètres de stratégie ont été ajoutés dans la version 3.6.0. Si vous utilisez une version antérieure, les exemples renvoient une erreur indiquant que le paramètre est introuvable.

Vous pouvez créer une définition de stratégie en utilisant l’applet de commande `New-AzureRmPolicyDefinition`.

Pour créer une définition de stratégie à partir d’un fichier, transmettez le chemin d’accès au fichier. Pour un fichier externe, utilisez l’exemple suivant :

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pour un fichier local, utilisez l’exemple suivant :

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

Le résultat est stocké dans un objet `$definition` utilisé lors de l’attribution de la stratégie.
L’exemple suivant crée une définition de stratégie qui inclut des paramètres :

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Afficher les définitions de stratégie

Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```
Get-AzureRmPolicyDefinition
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Créer une définition de stratégie avec Azure CLI

Vous pouvez créer une définition de stratégie à l’aide d’Azure CLI avec la commande de définition de stratégie.
Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Afficher les définitions de stratégie

Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```
az policy definition list
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Créer et attribuer une définition d’initiative

Avec une définition d’initiative, vous pouvez regrouper plusieurs définitions de stratégie pour atteindre un objectif global. Vous créez une définition initiative pour vous assurer que les ressources dans l’étendue de la définition restent confirmes aux définitions de stratégie qui composent la définition d’initiative.  Consultez [Vue d’ensemble de la stratégie Azure](./azure-policy-introduction.md) pour plus d’informations sur les définitions d’initiative.

### <a name="create-an-initiative-definition"></a>Créer une définition d’initiative

1. Sélectionnez **Définitions** sous **Création** dans le volet gauche.

   ![Sélectionner les définitions](media/create-manage-policy/select-definitions.png)

2. Sélectionnez **Définition d’initiative** en haut de la page pour accéder au formulaire **Définition d’initiative**.
3. Entrez le nom et la description de l’initiative.

   Dans cet exemple, assurez-vous que les ressources sont conformes aux définitions de stratégie relatives à la sécurisation. Le nom de l’initiative serait donc **Garantir la sécurité**, et la description serait **Cette initiative a été créée pour gérer toutes les définitions de stratégie associées à la sécurisation des ressources**.

   ![Définition d’initiative](media/create-manage-policy/initiative-definition.png)

4. Parcourez la liste des **définitions disponibles** et sélectionnez la ou les définitions de stratégie à ajouter à cette initiative. Pour notre initiative **Garantir la sécurité**, **ajoutez** les définitions de stratégie intégrées suivantes :
   - Nécessitent SQL Server version 12.0
   - Analyser les applications web non protégées dans Security Center.
   - Surveiller le réseau permissif dans l’ensemble du centre de sécurité.
   - Surveiller la création de listes autorisées dans le centre de sécurité possibles pour les applications.
   - Analyser les disques de machine virtuelle non chiffrés dans le centre de sécurité.

   ![Définitions d’initiative](media/create-manage-policy/initiative-definition-2.png)

   Après avoir sélectionné les définitions de stratégie dans la liste, vous les voyez sous **Stratégies et paramètres**, comme illustré dans l’image précédente.

5. Utilisez **Emplacement de définition** pour sélectionner un abonnement afin de stocker la définition. Sélectionnez **Enregistrer**.

### <a name="assign-an-initiative-definition"></a>Attribuer une définition d’initiative

1. Accédez à l’onglet **Définitions** sous **Création**.
2. Recherchez la définition d’initiative **Garantir la sécurité** que vous avez créée.
3. Sélectionnez la définition d’initiative, puis choisissez **Attribuer**.

   ![Attribuer une définition](media/create-manage-policy/assign-definition.png)

4. Remplissez le formulaire **Affectation** en entrant les informations de l’exemple suivant. Vous pouvez utiliser vos propres informations.
   - Nom : affectation Garantir la sécurité
   - Description : cette affectation d’initiative est personnalisée pour appliquer ce groupe de définitions de stratégie dans l’abonnement **Azure Advisor Capacity Dev**.
   - Niveau tarifaire : Standard
   - Étendue souhaitée pour cette affectation : **Azure Advisor Capacity Dev**. Vous pouvez choisir votre propre abonnement et groupe de ressources.

5. Sélectionnez **Attribuer**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Exempter une ressource non conforme ou refusée en utilisant l’exclusion

En suivant l’exemple ci-dessus, après avoir attribué la définition de stratégie pour nécessiter SQL Server version 12.0, tout serveur SQL créé avec une autre version sera refusé. Dans cette section, vous allez résoudre pas à pas une situation dans laquelle la création d’un serveur SQL a été refusée en raison d’une version différente en demandant une exclusion. L’exclusion empêche principalement l’application des stratégies. Une exclusion peut s’appliquer à un groupe de ressources. Vous pouvez également la restreindre à des ressources particulières.

1. Sélectionnez **Attributions** dans le volet gauche.
2. Parcourez toutes les affectations de stratégie, puis ouvrez l’affectation *Nécessiter SQL Server version 12.0*.
3. **Sélectionnez** une exclusion pour les ressources dans les groupes de ressources dans lesquels vous essayez de créer le serveur SQL. Dans cet exemple, excluez Microsoft.Sql/servers/databases : *azuremetrictest/testdb* et *azuremetrictest/testdb2*.

   ![Exclusion d’une demande](media/create-manage-policy/request-exclusion.png)

   Voici d’autres méthodes pour résoudre une ressource refusée : contacter la personne associée à la stratégie si vous avez une raison valable pour demander la création du serveur SQL, et modifier directement la stratégie si vous y avez accès.

4. Cliquez sur **Affecter**.

Dans cette section, vous avez résolu le refus de votre tentative de création d’un serveur SQL avec une version 12.0, en demandant une exclusion des ressources.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous prévoyez de continuer avec les didacticiels suivants, ne nettoyez pas les ressources créées dans ce guide. Sinon, procédez aux étapes suivantes pour supprimer toutes les affectations ou définitions créées ci-dessus :

1. Sélectionnez **Définitions** (ou **Affectations** si vous tentez de supprimer une attribution) dans le volet gauche.
2. Recherchez la nouvelle définition d’initiative ou de stratégie (ou affectation) que vous venez de créer.
3. Cliquez sur les points de suspension à la fin de la définition ou de l’affectation, puis sélectionnez **Supprimer la définition** (ou **Supprimer l’affectation**).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué avec succès les opérations suivantes :

> [!div class="checklist"]
> * Attribué une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> * Créé et attribué une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> * Résolu une ressource non conforme ou refusée
> * Implémenté une nouvelle stratégie dans l’ensemble de l’entreprise

Pour plus d’informations sur les structures des définitions de stratégie, consultez cet article :

> [!div class="nextstepaction"]
> [Structure de définition Azure Policy](policy-definition.md)
