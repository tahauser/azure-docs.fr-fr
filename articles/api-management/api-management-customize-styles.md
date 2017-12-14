---
title: "Personnaliser le style des pages du portail des développeurs Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce démarrage rapide pour personnaliser le style des éléments du portail des développeurs Gestion des API Azure."
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f427663ba1c437785c8c521925d9f733c45cb40d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personnaliser le style des pages du portail des développeurs

Il existe essentiellement trois manières courantes de personnaliser le portail des développeurs dans la Gestion des API Azure :
 
* [Modifier le contenu des pages statiques et des éléments de mise en page](api-management-modify-content-layout.md)
* Mettre à jour les styles utilisés pour les éléments de page dans le portail des développeurs (procédure expliquée dans ce guide)
* [Modifier les modèles utilisés pour les pages générées par le portail](api-management-developer-portal-templates.md) (par exemple, documents API, produits, authentification des utilisateurs.)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Personnaliser le style des éléments des pages du portail des **développeurs**
> * Afficher votre modification

![Personnaliser le style](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Composants requis

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="customize-the-developer-portal"></a>Personnaliser le portail des développeurs

1. Sélectionnez **Vue d’ensemble**.
2. Cliquez sur le bouton **Portail des développeurs** en haut de la fenêtre **Vue d’ensemble**. Vous pouvez également cliquer sur le lien **URL du portail des développeurs**.
3. Sur le côté supérieur gauche de l’écran, une icône composée de deux pinceaux apparaît. Placez le curseur de la souris au-dessus de cette icône pour ouvrir le menu de personnalisation du portail.

    ![Personnaliser le style](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Sélectionnez **Styles** dans le menu pour ouvrir le volet de personnalisation du style.

    Tous les éléments que vous pouvez personnaliser à l’aide de **Styles** apparaissent dans la page.
5. Entrez « headings-color » dans le champ **Changez les valeurs des variables pour personnaliser l’apparence du portail des développeurs**.

    L’élément **@headings-color** apparaît dans la page. Cette variable contrôle la couleur du texte.

    ![Personnaliser le style](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Cliquez sur le champ associé à la variable **@headings-color**. 
    
    Une liste déroulante de sélecteur de couleurs s’ouvre.
7. Dans cette liste déroulante, sélectionnez une nouvelle couleur.

    > [!TIP]
    > Un aperçu en temps réel est disponible pour toutes les modifications. Un indicateur de progression s’affiche en haut du volet de personnalisation. Après quelques secondes, la couleur nouvellement sélectionnée est appliquée au texte d’en-tête.

8. Sélectionnez **Publier** dans la partie inférieure gauche du menu du volet de personnalisation.
9. Sélectionnez **Publier les personnalisations** pour publier les modifications.

## <a name="view-your-change"></a>Afficher votre modification

1. Accédez au portail des développeurs.
2. Vous pouvez voir la modification que vous avez apportée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Personnaliser le style des éléments des pages du portail des **développeurs**
> * Afficher votre modification

> [!div class="nextstepaction"]
> [Personnaliser le portail des développeurs Gestion des API Azure à l’aide de modèles](api-management-developer-portal-templates.md)