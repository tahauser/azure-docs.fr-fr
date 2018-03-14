---
title: "Utiliser la CLI Databricks à partir d’Azure Cloud Shell | Microsoft Docs"
description: "Découvrez comment utiliser la CLI Databricks à partir d’Azure Cloud Shell."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 212789e8189abf362bb8eaf12e4c551b113b7adb
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Utiliser la CLI Databricks à partir d’Azure Cloud Shell

Découvrez comment utiliser la CLI Databricks à partir d’Azure Cloud Shell pour effectuer des opérations sur Databricks.

## <a name="prerequisites"></a>configuration requise

* Un cluster et un espace de travail Azure Databricks. Pour obtenir des instructions, consultez [Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure](quickstart-create-databricks-workspace-portal.md). 

* Configurez un jeton d’accès personnel dans Databricks. Pour obtenir des instructions, consultez [Generate token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management) (Générer un jeton).

## <a name="use-the-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

1. Connectez-vous au [portail Azure](https://portal.azure.com).
 
2. Dans l’angle supérieur droit, cliquez sur l’icône **Cloud Shell**.

   ![Lancer Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Lancer ODBC depuis Excel")

3. Veillez à sélectionner **Bash** comme environnement Cloud Shell. Vous pouvez effectuer la sélection à partir de l’option de liste déroulante, comme indiqué dans la capture d’écran suivante.

   ![Lancer Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Lancer ODBC depuis Excel") 

4. Créez un environnement virtuel dans lequel vous pouvez installer la CLI Databricks. Dans l’extrait de code ci-dessous, vous créez un environnement virtuel appelé `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Basculez vers l’environnement virtuel que vous avez créé.

       source databrickscli/bin/activate

6. Installez la CLI Databricks.

       pip install databricks-cli

7. Configurez l’authentification avec Databricks en utilisant le jeton d’accès que vous devez avoir créé, répertorié comme faisant partie des composants requis. Utilisez la commande suivante :

       databricks configure --token

    Les invites suivantes s’affichent :

    * Vous êtes invité à entrer l’hôte Databricks. Entrez la valeur au format `https://eastus2.azuredatabricks.net`. Ici, **Est des États-Unis 2** correspond à la région Azure où vous avez créé votre espace de travail Azure Databricks.

    * Vous êtes invité à saisir un nom d’utilisateur. Entrez un **jeton**.

    * Pour finir, vous êtes invité à entrer le mot de passe. Entrez le jeton que vous avez créé précédemment.

Après avoir terminé ces étapes, vous pouvez commencer à utiliser la CLI Databricks à partir d’Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Utiliser la CLI Databricks

Vous pouvez maintenant démarrer à l’aide de la CLI Databricks. Par exemple, exécutez la commande suivante pour répertorier tous les clusters Databricks que vous avez dans votre espace de travail.

    databricks clusters list

Vous pouvez également utiliser la commande suivante pour accéder au système de fichiers Databricks.

    databricks fs ls


Pour obtenir une référence complète sur les commandes, consultez [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (CLI Databricks).


## <a name="next-steps"></a>étapes suivantes

* Pour en savoir plus sur Azure CLI, consultez [Azure CLI overview](../cloud-shell/overview.md) (Vue d’ensemble d’Azure CLI)
* Pour obtenir une liste de commandes pour Azure CLI, consultez la [référence sur Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
* Pour obtenir une liste de commandes pour Databricks CLI, consultez [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (CLI Databricks)


