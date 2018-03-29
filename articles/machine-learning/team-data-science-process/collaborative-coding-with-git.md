---
title: Développement collaboratif avec Git - Azure Machine Learning | Microsoft Docs
description: Comment développer le code de projets de science des données en collaboration avec Git selon une planification agile.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: f3eabf0b754f777f25811d30c158b647b1d3954e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="collaborative-coding-with-git"></a>Développement collaboratif avec Git

Dans cet article, nous expliquons comment développer le code de projets de science des données en utilisant Git comme framework de développement de code partagé. Nous décrivons aussi comment lier ces activités de code au travail planifié dans un [développement Agile](agile-development.md) et comment effectuer des revues de code.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Lier un élément de travail à une branche Git 

VSTS fournit un moyen pratique de connecter un élément de travail (un récit ou une tâche) à une branche Git. En effet, il vous permet de lier votre récit ou votre tâche directement au code associé. 

Pour connecter un élément de travail à une nouvelle branche, double-cliquez sur un élément de travail, puis, dans la fenêtre contextuelle, cliquez sur **Créer une branche** sous **+ Ajouter un lien**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Fournissez les informations concernant cette nouvelle branche, telles que son nom ou son dépôt Git de base. Le dépôt Git choisi doit être celui situé sous le projet d’équipe auquel appartient l’élément de travail. La branche de base peut être la branche master ou une autre branche existante.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Il est préférable de créer une branche Git pour chaque élément de travail d’un récit. Ensuite, pour chaque élément de travail de tâche, vous devez créer une branche basée sur la branche du récit. Lorsque plusieurs personnes travaillent sur les différents récits d’un même projet ou les différentes tâches d’un même récit, il est utile d’organiser les branches sous la forme d’une hiérarchie correspondant aux relations entre le récit et les tâches. Le nombre de conflits peut être réduit lorsque chaque membre d’équipe travaille sur une branche différente et lorsque chaque membre travaille sur du code (ou autre artefact) différent en cas de partage d’une branche. 

L’illustration suivante montre la stratégie de création de branche recommandée pour TDSP. Vous n’aurez peut-être pas besoin d’autant de branches, en particulier si seulement une ou deux personnes travaillent à un projet, ou si une seule personne est chargée de l’ensemble des tâches d’un récit. Toutefois, le fait de séparer la branche de développement de la branche master est toujours conseillé. Cela peut éviter à la branche de publication d’être interrompue par les activités de développement. Pour obtenir une description plus complète du modèle de branche Git, consultez [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Pour basculer vers la branche sur laquelle vous souhaitez travailler, exécutez la commande suivante dans une invite de commandes (Windows ou Linux). 

    git checkout <branch name>

Si vous remplacez *<branch name\>* par **master**, vous allez revenir à la branche **maître**. Une fois que vous avez basculé vers la branche de travail, vous pouvez commencer à travailler sur l’élément de travail, en développant les artefacts du code ou de la documentation nécessaires à la réalisation de l’élément. 

Vous pouvez également lier un élément de travail à une branche existante. Dans la page **Détail** d’un élément de travail, au lieu de cliquer sur **Créer une branche**, cliquez sur **+ Ajouter un lien**. Ensuite, sélectionnez la branche à laquelle vous souhaitez lier l’élément de travail. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Vous pouvez également créer une branche à l’aide des commandes Bash Git. Si <base branch name\> est manquant, <new branch name\> est basé sur la branche _maître_. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Travailler sur une branche et valider les modifications 

Maintenant, supposons que vous apportiez des modifications à la branche *data\_ingestion* de l’élément de travail, comme l’ajout d’un fichier R à la branche de votre ordinateur local. Vous pouvez valider le fichier R ajouté à la branche de cet élément de travail, du moment que vous vous trouvez dans cette branche dans l’interpréteur de commandes Git et que vous utilisez les commandes Git suivantes :

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5.](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Créer une demande de tirage (pull request) dans VSTS 

Lorsque vous êtes prêt, après quelques validations et envois (push), à fusionner la branche actuelle avec la branche de base, vous pouvez envoyer une **demande de tirage (pull request)** sur le serveur VSTS. 

Dans la page principale de votre projet d’équipe, cliquez sur **CODE**. Sélectionnez la branche à fusionner et le nom du dépôt Git avec lequel vous souhaitez fusionner la branche. Ensuite, cliquez sur **Demandes de tirage (Pull requests)**, puis cliquez sur **Nouvelle demande de tirage (Pull request)** pour créer une revue de demande de tirage avant que le travail de la branche ne soit fusionné avec sa branche de base.

![6.](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Ajoutez une description pour la demande de tirage, ajoutez des réviseurs, puis envoyez la demande.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Réviser et fusionner 

Lorsque des demandes de tirage sont créées, vos réviseurs reçoivent une notification par e-mail leur demandant de réviser les demandes. Les réviseurs doivent vérifier si les modifications apportées fonctionnent, et les tester avec le demandeur, si possible. Selon le résultat de leur évaluation, les réviseurs peuvent approuver ou rejeter la demande de tirage. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9.](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Une fois la revue terminée, pour fusionner la branche de travail avec la branche de base, cliquez sur le bouton **Terminer**. Vous pouvez choisir de supprimer la branche de travail une fois celle-ci fusionnée. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Dans le coin supérieur gauche, vérifiez que la demande est marquée comme **Terminée**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Lorsque vous revenez au dépôt sous **CODE**, vous êtes informé que vous venez de basculer vers la branche master.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Vous pouvez également utiliser les commandes Git pour fusionner votre branche de travail avec la branche de base, et supprimer la branche de travail après la fusion :

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13.](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Étapes suivantes

[Exécuter des tâches de science des données](execute-data-science-tasks.md) montre comment utiliser des outils pour effectuer plusieurs tâches de science des données courantes telles que l’exploration des données, l’analyse des données, la création de rapports et la création de modèles interactives.

Des procédures pas à pas illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 

