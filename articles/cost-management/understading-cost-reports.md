---
title: "Compréhension des rapports sur les coûts dans Azure Cost Management | Microsoft Docs"
description: Cet article vous permet de comprendre la structure et les fonctions de base des rapports Cloudyn.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 38c1313f42a58403e158cad9c2930b6541da5adc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-cost-reports"></a>Compréhension des rapports sur les coûts

Cet article vous permet de comprendre la structure et les fonctions de base des rapports Cloudyn. La plupart des rapports Cloudyn sont intuitifs et ont une apparence uniforme. Après lecture de cet article, vous êtes prêt à utiliser tous les rapports. La plupart des fonctionnalités standard sont disponibles dans les différents rapports, vous permettant de naviguer facilement dans les rapports. Les rapports sont personnalisables et vous pouvez sélectionner plusieurs options pour calculer et afficher des résultats.

## <a name="report-fields-and-options"></a>Champs et options des rapports

Vous trouverez ci-dessous un exemple de rapport des coûts dans le temps. La plupart des rapports Cloudyn ont une mise en page similaire.

![exemple de rapport](./media/understanding-cost-reports/sample-report.png)

Chaque zone numérotée dans l’image précédente est décrite en détail dans les informations suivantes :

1. **Plage de dates**

    Utilisez la liste des plages de dates pour définir un intervalle de temps de rapport à l’aide d’une balise prédéfinie ou personnalisée.
2. **Filtre enregistré**

    Utilisez la liste des filtres enregistrés pour enregistrer les groupes et les filtres actuellement appliqués au rapport. Les filtres enregistrés sont disponibles dans les rapports sur les coûts et les performances, notamment :

      - Analyse des coûts
      - Allocation
      - Gestion des ressources
      - Optimisation

  Saisissez un nom de filtre et cliquez sur **Enregistrer**.

3. **Balises**

    Utilisez la zone Balises pour grouper par catégorie de balises. Les balises répertoriées dans le menu sont des balises du département Azure ou du centre de coûts ou encore des balises d’entité de coûts ou d’abonnement Cloudyn. Sélectionnez les balises pour filtrer les résultats. Vous pouvez également saisir un nom de balise (mot clé) pour filtrer les résultats.

    ![options de sélection](./media/understanding-cost-reports/select-options.png)

    Cliquez sur **Ajouter** pour ajouter un nouveau filtre.

    ![ajouter un filtre](./media/understanding-cost-reports/add-filter.png)

    Le regroupement ou le filtrage des balises ne s’applique pas aux ressources Azure ni aux balises de groupes de ressources.

    Le regroupement et le filtrage des balises d’allocation des coûts sont disponibles dans l’option du menu **Groupes**.

4. **Groupes dans les rapports**

    Utilisez les Groupes dans les rapports d’analyse des coûts pour afficher les catégories standard et détaillées à partir des données de facturation dans votre rapport.  Toutefois, les groupes dans les rapports d’allocation des coûts affichent des catégories de vue avec indicateur. Les catégories avec indicateur sont définies dans le modèle d’allocation des coûts et les catégories détaillées standard dans les données de facturation.

    ![Balises des groupes](./media/understanding-cost-reports/groups-tags01.png)

    ![Balises des groupes](./media/understanding-cost-reports/groups-tags02.png)

    Dans les rapports d’allocation des coûts, les groupes des catégories de groupes avec indicateur doivent inclure :
      - Balises
      - Balises des groupes de ressources
      - Balises des entités de coûts Cloudyn
      - Catégories de balises d’abonnement à des fins d’allocation des coûts

  Les exemples doivent inclure :
     - Centre de coûts
     - department
     - Application
     - Environnement
     - Code de coût

5. **Filtres**

    Utilisez des filtres uniques ou à sélection multiple pour définir des plages sur des valeurs sélectionnées. Pour définir un filtre, cliquez sur **Ajouter** puis sélectionnez des catégories et des valeurs de filtres.

6. **Modèle de coût**

    Utilisez le Modèle de coût pour sélectionner un modèle de coût précédemment créé avec l’allocation des coûts 360. Vous pouvez avoir plusieurs modèles de coût Cloudyn, en fonction de vos exigences d’allocation de coûts. Certaines équipes de votre organisation peuvent avoir des exigences d’allocation de coûts différentes des autres. Chaque équipe peut disposer de son propre modèle de coût dédié.

    Pour plus d’informations sur la création d’une définition d’un modèle d’allocation de coût, consultez la rubrique [Utilisez des balises personnalisées pour allouer des coûts](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortissement**

    Utilisez les rapports Amortissement dans l’allocation des coûts pour afficher les frais de service non liés à l’utilisation ou les coûts payables une seule fois. Les coûts sont étalés dans le temps de manière régulière tout au long de leur durée de vie. Par exemple, les frais ponctuels peuvent inclure :
    - les frais d’assistance annuels ;
    - les frais annuels liés aux composants de sécurité ;
    - les frais d’achat d’instances réservées ;
    - certains éléments de Place de Marché Azure.

  Sous Amortissement, sélectionnez **Coût amorti** ou **Coût réel**.

8. **Résolution :**

    Utilisez Résolution pour sélectionner la résolution de temps dans la plage de dates sélectionnée. Votre résolution de temps détermine le nombre d’unités affichées dans le rapport et peut être :
    - Quotidien
    - Hebdomadaire
    - Mensuelle
    - Trimestrielle
    - Annuelle

9. **Règles d'allocation**

    Utilisez Règles d’allocation pour appliquer ou désactiver le nouveau calcul d’allocation des coûts. Vous pouvez activer ou désactiver le nouveau calcul d’allocation des coûts pour les données de facturation. Le nouveau calcul s’applique aux catégories sélectionnées dans le rapport. Ceci vous permet d’évaluer l’impact du nouveau calcul d’allocation des coûts par rapport aux données de facturation brutes.

10. **Sans catégorie**

    Utilisez Sans catégorie pour inclure ou exclure des coûts sans catégorie dans le rapport.

11. **Afficher/masquer des champs**

    L’option Afficher/masquer n’a aucun impact sur les rapports.

12.   **Formats d’affichage**

    Utilisez Formats d’affichage pour sélectionner différentes vues de tableau ou de graphique.

    ![formats d’affichage](./media/understanding-cost-reports/display-formats.png)

13. **Multicolore**

    Utilisez Multicolore pour définir la couleur des graphiques de votre rapport.

14. **Actions**

    Utilisez Action pour enregistrer, exporter ou planifier le rapport.

## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas encore suivi le premier didacticiel de Cost Management, consultez-le dans [Réviser l’utilisation et les coûts](tutorial-review-usage.md).
