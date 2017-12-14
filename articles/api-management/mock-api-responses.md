---
title: "Simuler des réponses de l’API avec le portail Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment utiliser le service Gestion des API (APIM) pour définir une stratégie sur une API afin qu’elle retourne une réponse simulée. Cette méthode permet aux développeurs de procéder à l’implémentation et au test de l’instance Gestion des API au cas où le backend ne serait pas en mesure d’envoyer des réponses réelles."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: e485071b026c52eb23532639546ad475fc92cde3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="mock-api-responses"></a>Simuler des réponses de l’API

Les API backend peuvent être importées dans une API APIM ou créées et gérées manuellement. Les étapes décrites dans ce didacticiel vous montrent comment utiliser APIM pour créer une API vide et la gérer manuellement. Le didacticiel montre comment définir une stratégie sur une API afin qu’elle retourne une réponse simulée. Cette méthode permet aux développeurs de procéder à l’implémentation et au test de l’instance APIM même si le backend n’est pas en mesure d’envoyer des réponses réelles. La possibilité de simuler les réponses peut être utile dans de nombreux scénarios :

+ Quand la façade de l’API est conçue avant que le backend ne soit implémenté. Ou bien, quand le backend est développé en parallèle.
+ Quand le backend est temporairement non opérationnel ou qu’il ne peut pas être mis à l’échelle.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une API de test 
> * Ajouter une opération à l’API de test
> * Activer la simulation des réponses
> * Tester l’API simulée

![Réponse simulée à l’opération](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Composants requis

Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-test-api"></a>Créer une API de test 

Cette section montre comment créer une API vide sans backend. Elle montre également comment ajouter une opération à l’API. Appeler l’opération une fois effectuées les étapes décrites dans cette section génère une erreur. Vous n’obtiendrez aucune erreur une fois que vous aurez effectué les étapes décrites dans la section « Activer la simulation des réponses ».

1. Sélectionnez **API** sous **Gestion des API**.
2. Dans le menu de gauche, cliquez sur **Ajouter l’API**.
3. Sélectionnez **API vide** dans la liste.
4. Entrez « *API de test* » pour **Nom complet**.
5. Entrez « *Illimité* » pour **Produits**.
6. Sélectionnez **Créer**.

## <a name="add-an-operation-to-the-test-api"></a>Ajouter une opération à l’API de test

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Cliquez sur **+ Ajouter une opération**.

    ![Réponse simulée à l’opération](./media/mock-api-responses/mock-api-responses02.png)

    |Paramètre|Valeur|Description|
    |---|---|---|
    |**URL** (verbe HTTP)|GET|Vous pouvez choisir parmi les verbes HTTP prédéfinis.|
    |**URL** |*/test*|Chemin d’URL de l’API. |
    |**Nom complet**|*Appel de test*|Le nom s’affiche dans le **portail des développeurs**.|
    |**Description**||Fournissez une description de l’opération qui est utilisée pour offrir des informations aux développeurs utilisant cette API dans le **portail des développeurs**.|
    |Onglet **Requête**||Vous pouvez ajouter des paramètres de requête. Outre un nom et une description, vous pouvez fournir des valeurs qui peuvent être attribuées à ce paramètre. Une des valeurs peut être marquée comme valeur par défaut (facultatif).|
    |Onglet **Demande**||Vous pouvez définir les types de contenu de demande, les exemples et les schémas. |
    |Onglet **Réponse**|Consultez les étapes qui suivent ce tableau.|Définissez les codes d’état de réponse, les types de contenu, les exemples et les schémas.|

3. Sélectionnez l’onglet **Réponse**, situé sous les champs URL, Nom complet et Description.
4. Cliquez sur **+ Ajouter une réponse**.
5. Sélectionnez **200 OK** dans la liste.
6. Sous le titre **Représentations** à droite, sélectionnez **+ Ajouter la représentation**.
7. Entrez « *application/json* » dans la zone de recherche et sélectionnez le type de contenu **application/json**.
8. Dans la zone de texte **Exemple**, entrez « *{ 'sampleField' : 'test' }* ».
9. Sélectionnez **Enregistrer**.

## <a name="enable-response-mocking"></a>Activer la simulation des réponses

1. Sélectionnez l’API que vous avez créée à l’étape « Créer une API de test ».
2. Sélectionnez l’opération de test que vous avez ajoutée.
2. Dans la fenêtre de droite, cliquez sur l’onglet **Conception**.
3. Dans la fenêtre **Traitement entrant**, cliquez sur l’icône en forme de crayon.
4. Sous l’onglet **Simulation**, sélectionnez **Réponses statiques** pour **Comportement de simulation**.
5. Dans la zone de texte **La Gestion des API retourne la réponse suivante :**, tapez **200 OK, application/json**. Cette sélection indique que votre API doit retourner l’exemple de réponse que vous avez défini dans la section précédente.
6. Sélectionnez **Enregistrer**.

## <a name="test-the-mocked-api"></a>Tester l’API simulée

1. Sélectionnez l’API que vous avez créée à l’étape « Créer une API de test ».
2. Ouvrez l’onglet **Test**.
3. Vérifiez que l’API **Appel de test** est sélectionnée.

    > [!TIP]
    > Une barre jaune accompagnée du texte **La simulation de réponse est activée** indique que les réponses retournées par le service Gestion des API envoient une stratégie de simulation au lieu d’une réponse de backend réelle.

3. Sélectionnez **Envoyer** pour effectuer un appel de test.
4. La **réponse HTTP** affiche le JSON fourni comme exemple dans la première section du didacticiel.

## <a name="video"></a>Vidéo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une API de test
> * Ajouter une opération à l’API de test
> * Activer la simulation des réponses
> * Tester l’API simulée

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Transformer et protéger une API publiée](transform-api.md)
