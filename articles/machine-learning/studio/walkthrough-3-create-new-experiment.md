---
title: 'Étape 3 : Création d’une expérience Machine Learning | Microsoft Docs'
description: "Étape 3 du guide pas à pas du développement d'une solution prédictive : Création d'une expérience d'apprentissage dans Azure Machine Learning Studio."
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 72d6c3acfdf095ad8652561244139304b690344a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Étape 3 de la procédure pas à pas : création d’une expérience Azure Machine Learning
Voici la troisième étape de la procédure pas à pas [Développement d’une solution d’analyse prédictive avec Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](walkthrough-2-upload-data.md)
3. **Créer une expérience**
4. [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](walkthrough-5-publish-web-service.md)
6. [Accéder au service web](walkthrough-6-access-web-service.md)

- - -
L’étape suivante de cette procédure pas à pas consiste à créer une expérience dans Machine Learning Studio, qui utilise le jeu de données que nous avons chargé.  

1. Dans Studio, cliquez sur **+NOUVEAU** en bas de la fenêtre.
2. Sélectionnez **EXPÉRIENCE**, puis sélectionnez « Expérience vide ». 

    ![Création d'une expérience][0]

2. Sélectionnez le nom d’expérience par défaut, situé en haut du canevas, et remplacez-le par un nom significatif.

    ![Renommer l’expérience][5]
   
   > [!TIP]
   > Il est conseillé de compléter les champs **Résumé** et **Description** relatifs à l’expérience dans le volet **Propriétés**. Ces propriétés vous donnent la possibilité de documenter l'expérience afin que toute personne la consultant ultérieurement comprenne vos objectifs et votre méthodologie.
   > 
   > ![Propriétés de l’expérience][6]
   > 
3. Dans la palette des modules à gauche du canevas d'expérience, développez **Jeux de données enregistrés**.
4. Recherchez le jeu de données que vous avez créé sous **My Datasets** (Mes jeux de données) et faites-le glisser sur la zone de dessin. Vous pouvez également le rechercher en entrant son nom dans la zone **Rechercher** au-dessus de la palette.  

    ![Ajouter le jeu de données à l’expérience][7]

## <a name="prepare-the-data"></a>Préparation des données
Vous pouvez voir les 100 premières lignes de données et quelques informations statistiques concernant tout le jeu de données : pour ce faire, cliquez sur le port de sortie du jeu de données (le petit cercle en bas) et en sélectionnez **Visualiser**.  

Le fichier de données étant dépourvu d’en-têtes de colonne, Studio a fourni des en-têtes génériques (Col1, Col2, *etc.*). Des en-têtes explicites ne sont pas essentiels pour créer un modèle, mais ils facilitent l’utilisation des données dans l’expérience. En outre, lors de la publication de ce modèle dans un service web, les en-têtes permettent à l’utilisateur du service d’identifier les colonnes.  

Nous pouvons ajouter des en-têtes de colonne en utilisant le module [Modifier les métadonnées][edit-metadata].
Vous utilisez le module [Modifier les métadonnées][edit-metadata] pour modifier des métadonnées associées à un jeu de données. Dans ce cas, nous l’utilisons pour fournir des noms plus conviviaux pour les en-têtes de colonne. 

Pour utiliser [Modifier les métadonnées][edit-metadata], vous devez commencer par spécifier les colonnes à modifier (en l’occurrence, toutes). Ensuite, vous spécifiez l’action à effectuer sur ces colonnes (en l’occurrence, la modification de leurs en-têtes).

1. Dans la palette des modules, tapez « métadonnées » dans la zone **Rechercher** . Le module [Modifier les métadonnées][edit-metadata] apparaît dans la liste des modules.

2. Cliquez sur le module [Modifier les métadonnées][edit-metadata] et faites-le glisser sur le canevas avant de le déposer sous le jeu de données que nous avons ajouté précédemment.

3. Connectez le jeu de données au module [Modifier les métadonnées][edit-metadata] : cliquez sur le port de sortie du jeu de données (le petit cercle en bas du jeu de données), faites glisser vers le port d’entrée de [Modifier les métadonnées][edit-metadata] (le petit cercle en haut du module), puis relâchez le bouton de la souris. Le jeu de données et le module restent connectés même si vous opérez des déplacements sur le canevas.
   
   L'expérience doit ressembler à ceci :  
   
   ![Ajout de Modifier les métadonnées][1]
   
   Le point d’exclamation rouge indique que nous n’avons pas encore défini les propriétés de ce module. Ce sera notre prochaine tâche.
   
   > [!TIP]
   > Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module [Modifier les métadonnées][edit-metadata] et tapez le commentaire « Ajouter des en-têtes de colonne ». Cliquez n’importe où sur le canevas pour fermer la zone de texte. Pour afficher le commentaire, cliquez sur la flèche vers le bas sur le module.
   > 
   > ![Module Modifier les métadonnées avec un commentaire ajouté][8]
   > 
4. Sélectionnez [Modifier les métadonnées][edit-metadata], puis, dans le panneau **Propriétés** à droite du canevas, cliquez sur **Lancer le sélecteur de colonne**.

5. Dans la boîte de dialogue **Sélectionner les colonnes**, sélectionnez toutes les lignes des **Colonnes disponibles** puis cliquez sur > pour les déplacer vers les **Colonnes sélectionnées**.
   La boîte de dialogue doit ressembler à ceci :

   ![Sélecteur de colonnes avec toutes les colonnes sélectionnées][2]

6. Cliquez sur la coche **OK**.

7. Dans le panneau **Propriétés**, recherchez le paramètre **Nouveaux noms de colonne**. Dans ce champ, entrez une liste de noms pour les 21 colonnes du jeu de données, séparés par des virgules et dans l’ordre de la colonne. Vous pouvez obtenir le nom des colonnes dans la documentation du jeu de données sur le site web UCI ou, par commodité, vous pouvez copier et coller la liste suivante :  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   Le volet Propriétés ressemble à ceci :
   
   ![Propriétés de Modifier les métadonnées][3]

> [!TIP]
> Si vous souhaitez vérifier les en-têtes de colonne, exécutez l’expérience (cliquez sur **EXÉCUTER** sous le canevas d’expérience). À la fin de l’exécution (une coche verte s’affiche sur [Modifier les métadonnées][edit-metadata]), cliquez sur le port de sortie du module [Modifier les métadonnées][edit-metadata] et sélectionnez **Visualiser**. Vous pouvez voir la sortie de tous les modules en procédant de même pour afficher la progression des données dans l'expérience.
> 
> 

## <a name="create-training-and-test-datasets"></a>Création de jeux de données d'apprentissage et de test
Nous avons besoin de certaines données pour former le modèle et d’autres pour le tester.
Ainsi, lors de l’étape suivante de l’expérience, nous divisons le jeu de données en deux jeux de données distincts : un pour la formation de notre modèle et l’autre pour le tester.

Pour ce faire, nous utilisons le module [Fractionner les données][split].  

1. Recherchez le module [Fractionner les données][split], faites-le glisser sur le canevas et connectez-le au module [Modifier les métadonnées][edit-metadata].

2. Par défaut, le rapport de division est 0,5 et le paramètre **Fractionnement aléatoire** est défini. Cela signifie qu’une moitié aléatoire des données sort par un port du module [Fractionner les données][split] et l’autre moitié par l’autre port. Vous pouvez ajuster ces paramètres, de même que le paramètre **Valeur de départ aléatoire**, pour changer la répartition entre les données d’apprentissage et de test. Pour cet exemple, nous ne changeons rien.
   
   > [!TIP]
   > La propriété **Fraction de lignes dans le premier jeu de données de sortie** détermine la quantité de données qui est sortie par le port de sortie *gauche*. Par exemple, si vous définissez le rapport sur 0,7, 70 % des données sont sorties par le port gauche et 30 % par le port droit.  
   > 
   > 

3. Double-cliquez sur le module [Fractionner les données][split] et entrez le commentaire « Fractionnement des données de formation/test de 50 % ». 

Nous pouvons utiliser les sorties du module [Fractionner les données][split] à notre gré, mais choisissons la sortie gauche pour les données d’apprentissage et la sortie droite pour les données de test.  

Comme indiqué lors de l’[étape précédente](walkthrough-2-upload-data.md), le coût d’une erreur consistant à classer un crédit à risque élevé comme étant à faible risque est cinq fois plus élevé que celui de l’erreur consistant à classer un crédit à faible risque comme étant à risque élevé. Pour tenir compte de cela, nous générons un nouveau jeu de données qui reflète cette fonction de coût. Dans le nouveau jeu de données, chaque exemple à haut risque est répliqué cinq fois, alors que les exemples à faible risque ne sont pas répliqués.   

Nous pouvons procéder à la réplication en utilisant le code R :  

1. Recherchez le module [Exécuter un script R][execute-r-script] et faites-le glisser sur le canevas de l’expérience. 

2. Connectez le port de sortie gauche du module [Fractionner les données][split] au premier port d’entrée (« Dataset 1 ») du module [Exécuter un script R][execute-r-script].

3. Double-cliquez sur le module [Exécuter un script R][execute-r-script] et entrez le commentaire « Définir l’ajustement des coûts ».

4. Dans le volet **Propriétés**, supprimez le texte par défaut du paramètre **Script R**, puis entrez le script suivant :
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script R dans le module Exécuter un script R][9]

Nous devons répéter cette opération de réplication pour chaque sortie du module [Fractionner les données][split] pour que les données d’apprentissage et de test aient le même ajustement des coûts. Pour ce faire, la manière la plus simple consiste à dupliquer le module [Exécuter un script R][execute-r-script] que nous venons de créer et à le connecter à l’autre port de sortie du module [Fractionner les données][split].

1. Cliquez avec le bouton droit sur le module [Exécuter un script R][execute-r-script] et sélectionnez **Copier**.

2. Cliquez avec le bouton droit sur le canevas d'expérience et sélectionnez **Coller**.

3. Faites glisser le nouveau module pour le mettre en place, puis connectez le port de sortie droit du module [Fractionner les données][split] au premier port d’entrée du module [Exécuter un script R][execute-r-script]. 

4. En bas du canevas, cliquez sur **Exécuter**. 

> [!TIP]
> La copie du Module d’exécution de script R contient le même script que le module d’origine. Lorsque vous copiez-collez un module sur le canevas, la copie conserve toutes les propriétés de l'original.  
> 
> 

À ce stade, notre expérience ressemble à cela :

![Adding Split module and R scripts][4]

Pour plus d’informations sur l'utilisation de scripts R dans vos expériences, consultez la page [Prolonger votre expérience avec R](extend-your-experiment-with-r.md).

**Étape suivante : [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
