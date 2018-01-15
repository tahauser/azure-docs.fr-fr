---
title: "Leçon supplémentaire du didacticiel Azure Analysis Services : Sécurité dynamique | Microsoft Docs"
description: "Explique comment utiliser la sécurité dynamique à l’aide de filtres de lignes dans le didacticiel Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 7512af39041090db22d4ef45ec46925c14c4adb9
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="supplemental-lesson---dynamic-security"></a>Leçon supplémentaire – Sécurité dynamique

Dans cette leçon supplémentaire, vous allez créer un rôle supplémentaire qui implémente la sécurité dynamique. La sécurité dynamique offre une sécurité au niveau des lignes basée sur le nom ou sur l’ID de connexion de l’utilisateur actuellement connecté. 
  
Pour implémenter la sécurité dynamique, vous ajoutez à votre modèle une table contenant les noms des utilisateurs qui peuvent se connecter au modèle et parcourir les données et objets du modèle. Le modèle que vous créez à l’aide de ce didacticiel est dans le contexte d’Adventure Works. Toutefois, pour suivre cette leçon, vous devez ajouter une table contenant les utilisateurs de votre propre domaine. Vous n’avez pas besoin des mots de passe correspondant aux noms d’utilisateur qui sont ajoutés. Pour créer une table EmployeeSecurity, avec un petit échantillon d’utilisateurs de votre propre domaine, vous utilisez la fonctionnalité Coller pour coller les données des employés d’une feuille de calcul Excel. Dans la vraie vie, la table contenant les noms d’utilisateur serait généralement une table d’une vraie base de données comme source de données ; par exemple, une vraie table DimEmployee.  
  
Pour implémenter la sécurité dynamique, vous utilisez deux fonctions DAX :la [fonction USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) et la [fonction LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Ces fonctions, appliquées dans une formule de filtre de lignes, sont définies dans un nouveau rôle. En utilisant la fonction LOOKUPVALUE, la formule spécifie une valeur à partir de la table EmployeeSecurity. Puis la formule transmet cette valeur à la fonction USERNAME, qui spécifie que le nom de l’utilisateur connecté appartient à ce rôle. L’utilisateur peut alors parcourir uniquement les données spécifiées par les filtres de lignes du rôle. Dans ce scénario, vous spécifiez que les représentants commerciaux peuvent parcourir uniquement les données des ventes sur Internet pour les secteurs de ventes dont ils sont membres.  
  
Les tâches qui sont propres à ce scénario de modèle tabulaire Adventure Works, mais qui ne s’appliqueraient pas nécessairement dans la vraie vie, sont identifiées comme telles. Chaque tâche inclut des informations supplémentaires décrivant son objectif.  
  
Durée estimée pour suivre cette leçon : **30 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique de leçon supplémentaire fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon supplémentaire, vous devez avoir suivi toutes les leçons précédentes.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Ajouter la table DimSalesTerritory au projet de modèle tabulaire AW Internet Sales  
Pour implémenter la sécurité dynamique dans ce scénario Adventure Works, vous devez ajouter deux tables supplémentaires à votre modèle. La première table que vous ajoutez est DimSalesTerritory (comme secteur de vente) à partir de la même base de données AdventureWorksDW. Par la suite, vous appliquez un filtre de lignes dans la table SalesTerritory qui définit les données spécifiques que l’utilisateur connecté peut parcourir.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Pour ajouter la table DimSalesTerritory  
  
1.  Dans l’Explorateur de modèles tabulaires > **Sources de données**, cliquez avec le bouton droit sur votre connexion, puis cliquez sur **Importer de nouvelles tables**.  

    Si la boîte de dialogue Informations d’identification s’affiche, tapez les informations d’emprunt d’identification que vous avez utilisé dans la leçon 2 : Ajouter des données.
  
2.  Dans le navigateur, sélectionnez la table **DimSalesTerritory**, puis cliquez sur **OK**.    
  
3.  Dans l’éditeur de requête, cliquez sur la requête **DimSalesTerritory**, puis supprimez la colonne **SalesTerritoryAlternateKey**.  
  
7.  Cliquez sur **Importer**.  
  
    La nouvelle table est ajoutée à l’espace de travail du modèle. Les objets et les données de la table DimSalesTerritory source sont ensuite importés dans votre modèle tabulaire AW Internet Sales.  
  
9. Une fois la table importée, cliquez sur **Fermer**.  

## <a name="add-a-table-with-user-name-data"></a>Ajouter une table avec des données de noms d’utilisateur  
La table DimEmployee de l’exemple de base de données AdventureWorksDW contient des utilisateurs du domaine AdventureWorks. Ces noms d’utilisateur n’existent pas dans votre propre environnement. Vous devez donc créer dans votre modèle une table qui contient un petit échantillon (au moins trois) d’utilisateurs réels de votre organisation. Vous ajoutez ensuite ces utilisateurs comme membres au nouveau rôle. Vous n’avez pas besoin des mots de passe correspondant aux exemples de noms d’utilisateur, mais vous avez besoin de noms d’utilisateurs Windows réels de votre propre domaine.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Pour ajouter une table EmployeeSecurity  
  
1.  Ouvrez Microsoft Excel, ce qui crée une feuille de calcul.  
  
2.  Copiez la table suivante, notamment la ligne d’en-tête, puis collez-la dans la feuille de calcul.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Remplacez le prénom, le nom et le domaine\nom d’utilisateur par les noms et ID de connexion des trois utilisateurs de votre organisation. Pour EmployeeId 1, placez le même utilisateur sur les deux premières lignes. Cela indique que cet utilisateur appartient à plusieurs secteurs de vente. Laissez les champs EmployeeId et SalesTerritoryId inchangés.  
  
4.  Enregistrez la feuille de calcul sous **SampleEmployee**.  
  
5.  Dans la feuille de calcul, sélectionnez toutes les cellules contenant des données d’employés, notamment les en-têtes, cliquez avec le bouton droit sur les données sélectionnées, puis cliquez sur **Copier**.  
  
6.  Dans SSDT, cliquez sur le menu **Edition**, puis sur **Coller**.  
  
    Si l’option Coller apparaît en grisé, cliquez sur une colonne de n’importe quelle table dans la fenêtre du Concepteur de modèles, puis réessayez.  
  
7.  Dans la boîte de dialogue **Aperçu de collage** dans **Nom de la table**, tapez **EmployeeSecurity**.  
  
8.  Dans **Données à coller**, vérifiez que les données incluent l’ensemble des données et des en-têtes utilisateur de la feuille de calcul SampleEmployee.  
  
9. Vérifiez que la case **Utiliser la première ligne comme en-têtes de colonne** est cochée, puis cliquez sur **OK**.  
  
    Une table nommée EmployeeSecurity et contenant les données des employés copiées à partir de la feuille de calcul SampleEmployee est créée.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Créer des relations entre les tables FactInternetSales, DimGeography et DimSalesTerritory  
Les tables FactInternetSales, DimGeography et DimSalesTerritory contiennent toutes une colonne commune, SalesTerritoryId. La colonne SalesTerritoryId de la table DimSalesTerritory contient des valeurs avec un ID différent pour chaque secteur de vente.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Pour créer des relations entre les tables FactInternetSales, DimGeography et DimSalesTerritory  
  
1.  Dans la vue de diagramme, dans la table **DimGeography**, cliquez sur la colonne **SalesTerritoryId** en maintenant le bouton de la souris enfoncé, faites glisser le curseur jusqu’à la colonne **SalesTerritoryId** de la table **DimSalesTerritory**, puis relâchez le bouton de la souris.  
  
2.  Dans la table **FactInternetSales**, cliquez sur la colonne **SalesTerritoryId** en maintenant le bouton enfoncé, puis faites glisser le curseur jusqu’à la colonne **SalesTerritoryId** de la table **DimSalesTerritory**, puis relâchez.  
  
    Notez que la propriété Active de cette relation présente la valeur False, ce qui signifie qu’elle est inactive. En effet, la table FactInternetSales a déjà une autre relation active.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Masquer la table EmployeeSecurity pour les applications clientes  
Dans cette tâche, vous allez masquer la table EmployeeSecurity en l’empêchant de s’afficher dans la liste des champs d’une application cliente. N’oubliez pas que le fait de masquer une table ne garantit pas sa sécurité. Les utilisateurs peuvent toujours interroger les données de la table EmployeeSecurity s’ils savent comment procéder. Pour sécuriser les données de la table EmployeeSecurity en empêchant les utilisateurs d’en interroger les données, vous appliquez un filtre lors d’une tâche ultérieure.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Pour masquer la table EmployeeSecurity pour les applications clientes  
  
-   Dans le Concepteur de modèles, dans la vue de diagramme, cliquez sur l’en-tête de la table **Employee**, puis cliquez sur **Masquer dans les outils clients**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Créer un rôle d’utilisateur Sales Employees by Territory (Représentants commerciaux par secteur)  
Dans cette tâche, vous allez créer un rôle d’utilisateur. Ce rôle comprend un filtre de lignes définissant les lignes de la table DimSalesTerritory qui sont visibles aux utilisateurs. Le filtre est ensuite appliqué dans la direction de relation un-à-plusieurs à toutes les autres tables associées à DimSalesTerritory. Vous appliquez également un filtre qui empêche tout utilisateur membre du rôle d’interroger l’ensemble de la table EmployeeSecurity.  
  
> [!NOTE]  
> Le rôle Sales Employees by Territory (Représentants commerciaux par secteur) que vous créez dans cette leçon limite les membres à parcourir (ou à interroger) uniquement les données des ventes pour le secteur de vente auquel ils appartiennent. Si vous ajoutez au rôle Sales Employees by Territory un utilisateur comme membre qui existe également comme membre d’un rôle créé dans la [leçon 11 : Créer des rôles](../tutorials/aas-lesson-11-create-roles.md), vous obtenez une combinaison d’autorisations. Quand un utilisateur est membre de plusieurs rôles, les autorisations et les filtres de lignes définis pour chaque rôle sont cumulatifs. Autrement dit, l’utilisateur dispose des autorisations supérieures déterminées par la combinaison des rôles.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Pour créer un rôle d’utilisateur Sales Employees by Territory (Représentants commerciaux par secteur)  
  
1.  Dans SSDT, cliquez sur le menu **Modèle**, puis sur **Rôles**.  
  
2.  Dans le **Gestionnaire de rôles**, cliquez sur **Nouveau**.  
  
    Un nouveau rôle avec l’autorisation Aucune est ajouté à la liste.  
  
3.  Cliquez sur le nouveau rôle, puis dans la colonne **Nom**, renommez le rôle **Sales Employees by Territory**.  
  
4.  Dans la colonne **Autorisations**, cliquez sur la liste déroulante et sélectionnez l’autorisation **Lecture**.  
  
5.  Cliquez sur l’onglet **Membres**, puis sur **Ajouter**.  
  
6.  Dans la boîte de dialogue **Sélectionner un utilisateur ou un groupe**, dans **Entrer le nom de l’objet à sélectionner**, tapez le premier exemple de nom d’utilisateur que vous avez utilisé lors de la création de la table EmployeeSecurity. Cliquez sur **Vérifier les noms** pour vérifier si le nom d’utilisateur est valide, puis cliquez sur **OK**.  
  
    Répétez cette étape en ajoutant les autres exemples de noms d’utilisateur que vous avez utilisés lors de la création de la table EmployeeSecurity.  
  
7.  Cliquez sur l’onglet **Filtres de lignes**.  
  
8.  Pour la table **EmployeeSecurity**, dans la colonne **Filtre DAX**, tapez la formule suivante :  
  
    ```
      =FALSE()  
    ```
  
    Cette formule indique que toutes les colonnes se résolvent en condition booléenne false. Par conséquent, aucune colonne de la table EmployeeSecurity ne peut être interrogée par un membre du rôle d’utilisateur Sales Employees by Territory.  
  
9. Pour la table **DimSalesTerritory**, tapez la formule suivante :  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    Dans cette formule, la fonction LOOKUPVALUE retourne toutes les valeurs de la colonne DimEmployeeSecurity[SalesTerritoryId], où la valeur EmployeeSecurity[LoginId] est identique au nom de l’utilisateur Windows actuellement connecté et la valeur EmployeeSecurity[SalesTerritoryId] est identique à la valeur DimSalesTerritory[SalesTerritoryId].  
  
    L’ensemble des ID de secteurs de vente retournés par LOOKUPVALUE sont ensuite utilisés pour limiter les lignes affichées dans la table DimSalesTerritory. Seules les lignes où la valeur SalesTerritoryID de la ligne figure dans l’ensemble d’ID retournés par la fonction LOOKUPVALUE sont affichées.  
  
10. Dans le Gestionnaire de rôles, cliquez sur **OK**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Tester le rôle d’utilisateur Sales Employees by Territory (Représentants commerciaux par secteur)  
Dans cette tâche, vous allez utiliser la fonctionnalité Analyser dans Excel dans SSDT pour tester l’efficacité du rôle d’utilisateur Sales Employees by Territory. Vous allez spécifier l’un des noms d’utilisateur que vous avez ajoutés à la table EmployeeSecurity et comme membre du rôle. Ce nom d’utilisateur est ensuite utilisé comme le nom d’utilisateur effectif dans la connexion créée entre Excel et le modèle.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Pour tester le rôle d’utilisateur Sales Employees by Territory (Représentants commerciaux par secteur)  
  
1.  Dans SSDT, cliquez sur le menu **Modèle**, puis sur **Analyser dans Excel**.  
  
2.  Dans la boîte de dialogue **Analyser dans Excel**, dans **Spécifier le nom d’utilisateur ou le rôle à utiliser pour se connecter au modèle**, sélectionnez **Autre utilisateur Windows**, puis cliquez sur **Parcourir**.  
  
3.  Dans la boîte de dialogue **Sélectionner un utilisateur ou un groupe**, dans la zone **Entrer le nom de l’objet à sélectionner**, tapez un nom d’utilisateur que vous avez inclus dans la table EmployeeSecurity, puis cliquez sur **Vérifier les noms**.  
  
4.  Cliquez sur **OK** pour fermer la boîte de dialogue **Sélectionner un utilisateur ou un groupe**, puis cliquez sur **OK** pour fermer la boîte de dialogue **Analyser dans Excel**.  
  
    Excel s’ouvre avec un nouveau classeur. Un tableau croisé dynamique est automatiquement créé. La liste des champs du tableau croisé dynamique inclut la plupart des champs de données disponibles dans votre nouveau modèle.  
  
    Notez que la table EmployeeSecurity n’est pas visible dans la liste Champs de tableau croisé dynamique. En effet, vous avez masqué cette table dans les outils clients lors d’une tâche précédente.  
  
5.  Dans la liste **Champs** dans **∑ Internet Sales** (mesures), sélectionnez la mesure **InternetTotalSales**. La mesure est entrée dans les champs **Valeurs**.  
  
6.  Sélectionnez la colonne **SalesTerritoryId** dans la table **DimSalesTerritory**. La colonne est entrée dans les champs **Étiquettes de lignes**.  
  
    Notez que les chiffres des ventes sur Internet s’affichent uniquement pour la région à laquelle le nom d’utilisateur effectif que vous avez utilisé appartient. Si vous sélectionnez une autre colonne, telle que City, dans la table DimGeography en tant que champ Étiquette de ligne, seules les villes du secteur de vente auquel appartient l’utilisateur effectif sont affichées.  
  
    Cet utilisateur ne peut parcourir ou interroger aucune donnée des ventes sur Internet pour les secteurs autres que celui auquel il appartient. Cette restriction est due au fait que le filtre de lignes défini pour la table DimSalesTerritory, dans le rôle d’utilisateur Sales Employees by Territory, protège efficacement toutes les données liées à d’autres secteurs de vente.  
  
## <a name="see-also"></a>Voir aussi  
[USERNAME, fonction (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE, fonction (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA, fonction (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  