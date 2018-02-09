---
title: "Mises à jour de schéma du 1er juin 2016 - Azure Logic Apps | Microsoft Docs"
description: "Créer des définitions JSON pour Azure Logic Apps avec la version de schéma 2016-06-01"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 525df7ddb8cd569bfd361da10d14ae08c1a721e0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Mises à jour de schéma pour Azure Logic Apps - 1er juin 2016

La [version mise à jour](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) du schéma et de la version de l’API pour Azure Logic Apps intègrent différentes améliorations clés destinées à accroître la fiabilité et la simplicité d’utilisation des applications logiques :

* Les [étendues](#scopes) vous permettent de regrouper ou d’imbriquer des actions sous la forme d’un ensemble d’actions.
* Les [conditions et les boucles](#conditions-loops) sont désormais des actions de première classe.
* La précision de l’ordre d’exécution des actions a été améliorée grâce au remplacement de la propriété `dependsOn` par la propriété `runAfter`.

Pour mettre à niveau vos applications logiques à partir du schéma en version préliminaire du 1er août 2015 vers le schéma du 1er juin 2016, [consultez la section relative à la mise à niveau](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Étendues

Ce schéma comporte des étendues, qui vous permettent de regrouper ou d’imbriquer des actions. Par exemple, une condition peut en contenir une autre. Apprenez-en davantage sur la [syntaxe des étendues](../logic-apps/logic-apps-loops-and-scopes.md), ou examinez l’exemple d’étendue de base ci-dessous :

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Modifications des conditions et boucles

Dans les versions de schéma précédentes, les conditions et les boucles étaient des paramètres associés avec une seule action. Cette limitation a été levée dans ce schéma, de sorte que les conditions et les boucles apparaissent à présent sous forme de types d’actions. Apprenez-en davantage sur les [boucles et étendues](../logic-apps/logic-apps-loops-and-scopes.md), ou examinez l’exemple d’action de condition de base ci-dessous :

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Propriété « runAfter »

La propriété `runAfter` remplace `dependsOn`, ce qui vous permet de spécifier plus précisément l’ordre d’exécution des actions en fonction de l’état des actions précédentes.

La propriété `dependsOn` signifiait « l’action a été exécutée et a réussi » et ne tenait pas compte du nombre de fois où vous vouliez exécuter une action en fonction de la réussite, de l’échec ou de l’omission de l’action précédente. La propriété `runAfter` vous offre cette souplesse sous la forme d’un objet spécifiant le nom de toutes les actions après lesquelles l’objet doit s’exécuter. Cette propriété définit également un tableau d’états acceptables en tant que déclencheurs. Par exemple, si vous souhaitez exécuter une action après la réussite de l’étape A, ainsi qu’après la réussite ou l’échec de l’étape B, vous construisez la propriété `runAfter` suivante :

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Mettre à niveau votre schéma

Quelques étapes suffisent pour mettre à niveau vers le [schéma le plus récent](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json). Le processus de mise à niveau implique l’exécution du script de mise à niveau, l’enregistrement en tant que nouvelle application logique et, si vous le souhaitez, le remplacement éventuel de l’application logique précédente.

1. Dans le Portail Azure, ouvrez votre application logique.

2. Accédez à **Vue d’ensemble**. Dans la barre d’outils de l’application logique, choisissez **Mettre à jour le schéma**.
   
    ![Choisir l’option Mettre à jour le schéma][1]
   
    Le portail vous renvoie la définition mise à niveau, que vous pouvez copier et coller dans une définition de ressource si nécessaire. 
    Toutefois, nous vous **recommandons vivement** de choisir l’option **Enregistrer sous** pour vérifier que toutes les références de connexion sont valides dans l’application logique mise à niveau.

3. Dans la barre d’outils du panneau de mise à niveau, choisissez **Enregistrer sous**.

4. Entrez le nom et l’état de l’application logique. Pour déployer votre application logique mise à niveau, choisissez **Créer**.

5. Vérifiez que votre application logique mise à niveau fonctionne comme prévu.
   
   > [!NOTE]
   > Si vous utilisez un déclencheur manuel ou sur demande, l’URL de rappel change dans votre nouvelle application logique. Testez la nouvelle URL pour vérifier son fonctionnement de bout en bout. Pour conserver les URL précédentes, vous pouvez cloner votre application logique existante.

6. *Facultatif* Pour remplacer votre application logique précédente par la nouvelle version du schéma, dans la barre d’outils, choisissez **Cloner** en regard de l’option **Mettre à jour le schéma**. Cette étape n’est requise que si vous souhaitez conserver les mêmes ID de ressource ou URL du déclencheur sur demande de votre application logique.

### <a name="upgrade-tool-notes"></a>Notes de l’outil de mise à niveau

#### <a name="mapping-conditions"></a>Mappage de conditions

Dans la définition mise à niveau, l’outil s’efforce de regrouper les actions des branches true et false sous la forme d’une étendue. Le modèle de concepteur de `@equals(actions('a').status, 'Skipped')` doit notamment apparaître en tant qu’action `else`. Toutefois, si l’outil détecte des modèles non reconnaissables, il peut éventuellement créer des conditions distinctes pour les branches true et false. Vous pouvez remapper les actions après la mise à niveau si nécessaire.

#### <a name="foreach-loop-with-condition"></a>Boucle « foreach » avec condition

Dans le nouveau schéma, vous pouvez utiliser l’action de filtrage pour répliquer le modèle d’une boucle `foreach` avec une condition par élément, mais cette modification doit se produire automatiquement lorsque vous procédez à la mise à niveau. La condition devient une action de filtrage avant la boucle foreach de façon à renvoyer uniquement un tableau d’éléments correspondant à cette condition, puis ce tableau est transmis dans l’action foreach. Pour découvrir un exemple, consultez l’article relatif aux [boucles et étendues](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Balises de ressource

Après la mise à niveau, les balises de ressource sont supprimées. Vous devez donc les redéfinir pour le workflow mis à niveau.

## <a name="other-changes"></a>Autres modifications

### <a name="renamed-manual-trigger-to-request-trigger"></a>Déclencheur « manual » (manuel) renommé déclencheur « request » (sur demande)

Le type de déclencheur `manual` est déconseillé et a été renommé sous la forme `request` avec le type `http`. Cette modification est plus cohérente avec le type de modèle pour la génération duquel le déclencheur est utilisé.

### <a name="new-filter-action"></a>Nouvelle action de « filtre »

Pour filtrer un tableau d’éléments volumineux de façon à obtenir un sous-ensemble d’éléments plus restreint, le nouveau type `filter` accepte un tableau et une condition, évalue la condition pour chaque élément, puis renvoie un tableau d’éléments correspondant à la condition.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrictions relatives aux actions « foreach » et « until »

La boucle `foreach` et `until` est limitée à une seule action.

### <a name="new-trackedproperties-for-actions"></a>Nouvelle propriété « trackedProperties » relative aux actions

Les actions peuvent désormais comporter une propriété supplémentaire appelée `trackedProperties`, sœur des propriétés `runAfter` et `type`. Cet objet spécifie certaines entrées ou sorties d’action que vous souhaitez inclure dans la télémétrie de diagnostic Azure, dont l’émission intervient dans le cadre d’un workflow. Par exemple : 

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Créer des définitions d’application logique](../logic-apps/logic-apps-author-definitions.md)
* [Création d’un modèle de déploiement d’applications logiques](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
