---
title: Créer un espace de travail Machine Learning Studio | Microsoft Docs
description: Création d’un espace de travail pour Microsoft Azure Machine Learning Studio.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 8da10e474c62868ca109a44e7c66dc11ee62b6de
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Créer et partager un espace de travail Azure Machine Learning
Ce menu pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le processus d’analyse Cortana (CAP).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Pour utiliser Azure Machine Learning Studio, vous devez disposer d’un espace de travail Machine Learning. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Pour créer un espace de travail
1. Connectez-vous au [portail Azure](https://portal.azure.com/)

    > [!NOTE]
    > Pour vous connecter et créer un espace de travail, vous devez être un administrateur d’abonnements Azure. 
    >
    > 

2. Cliquez sur **+Nouveau**.

3. Dans la zone de recherche, tapez **espace de travail Machine Learning Studio** et sélectionnez l’élément correspondant. Ensuite, cliquez sur **Créer** en bas de la page.

4. Entrez les informations sur votre espace de travail :

    - Le *Nom de l’espace de travail* peut contenir jusqu’à 260 caractères. Il ne doit pas se terminer par un espace. Le nom ne peut pas contenir ces caractères : `< > * % & : \ ? + /`
    - Le *plan de service web* que vous choisissez (ou créez), ainsi que le *niveau tarifaire* associé que vous sélectionnez, est utilisé si vous déployez des services web à partir de cet espace de travail.

    ![Créer un espace de travail](./media/create-workspace/create-new-workspace.png)

5. Cliquez sur **Créer**.

Une fois l’espace de travail déployé, vous pouvez l’ouvrir dans Machine Learning Studio.

1. Accédez à Machine Learning Studio à l’adresse [https://studio.azureml.net/](https://studio.azureml.net/).

2. Sélectionnez votre espace de travail dans le coin supérieur droit.

    ![Sélectionner un espace de travail](./media/create-workspace/open-workspace.png)

3. Cliquez sur **my experiments (mes expérimentations)**.

    ![Ouvrir des expérimentations](./media/create-workspace/my-experiments.png)

Pour plus d’informations sur la gestion de votre espace de travail, consultez [Gestion d’un espace de travail Azure Machine Learning](manage-workspace.md).
En cas de problème lors de la création de votre espace de travail, consultez l’article [Guide de résolution des problèmes : création d’un espace de travail Machine Learning et connexion à celui-ci](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Partage d’un espace de travail Azure Machine Learning
Une fois l’espace de travail Machine Learning créé, vous pouvez y inviter les utilisateurs pour partager l’accès à votre espace de travail et à l’ensemble de ses expérimentations, jeux de données, bloc-notes, etc. Vous pouvez ajouter des utilisateurs dans l’un des deux rôles :

* **Utilisateur** : un utilisateur de l’espace de travail peut créer, ouvrir, modifier et supprimer des expérimentations, jeux de données, etc. dans l’espace de travail.
* **Propriétaire** : un propriétaire peut inviter et supprimer des utilisateurs dans l’espace de travail, ainsi que les actions qu’ils sont autorisés à effectuer.

> [!NOTE]
> Le compte d’administrateur qui crée l’espace de travail y est automatiquement ajouté en tant que propriétaire de l’espace de travail. Toutefois, les autres administrateurs ou utilisateurs de cet abonnement ne bénéficient pas automatiquement d’un accès à l’espace de travail : vous devez les inviter explicitement.
> 
> 

### <a name="to-share-a-workspace"></a>Pour partager un espace de travail

1. Connectez-vous à Machine Learning Studio à l’adresse [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Dans le panneau gauche, cliquez sur **SETTINGS (PARAMÈTRES)**.

3. Cliquez sur l’onglet **USERS (UTILISATEURS)**.

4. En bas de la page, cliquez sur **INVITE MORE USERS (INVITER D’AUTRES UTILISATEURS)**.

    ![Paramètres Studio](./media/create-workspace/settings.png)

5. Entrez une ou plusieurs adresses e-mail. Les utilisateurs ont besoin d’un compte Microsoft valide ou d’un compte d’entreprise (issu d’Azure Active Directory).

6. Indiquez si vous voulez ajouter des utilisateurs en tant que propriétaire ou utilisateur.

7. Cliquez sur la coche **OK** .

Chaque utilisateur ajouté reçoit un e-mail contenant des instructions de connexion à l’espace de travail partagé.

> [!NOTE]
> Pour que les utilisateurs puissent déployer ou gérer des services web dans cet espace de travail, ils doivent être collaborateurs ou administrateurs dans l’abonnement Azure. 



