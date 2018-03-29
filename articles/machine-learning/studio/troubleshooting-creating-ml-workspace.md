---
title: "Résolution de problèmes : création d'un espace de travail Machine Learning et connexion à celui-ci | Microsoft Docs"
description: Solutions aux problèmes courants liés à la création d'un espace de travail Azure Machine Learning et à la connexion à un tel espace
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5c265b14a88e993512811de365f1ba51f7ba6028
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guide de résolution des problèmes : création d'un espace de travail Machine Learning et connexion à celui-ci
Ce guide propose des solutions pour quelques-uns des défis qui se posent souvent quand vous configurez des espaces de travail Azure Machine Learning.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Propriétaire de l'espace de travail
Pour ouvrir un espace de travail de Machine Learning Studio, vous devez être connecté au compte Microsoft utilisé pour créer l’espace de travail ou recevoir une invitation du propriétaire à rejoindre l’espace de travail. À partir du portail Azure, vous pouvez gérer l’espace de travail, notamment configurer l’accès.

Pour plus d'informations sur la gestion d'un espace de travail, consultez [Gestion d'un espace de travail Azure Machine Learning].

[Gestion d'un espace de travail Azure Machine Learning]: manage-workspace.md

## <a name="allowed-regions"></a>Régions autorisées
Machine Learning est actuellement disponible dans un nombre limité de régions. Si votre abonnement n’inclut une de ces régions, le message d’erreur suivant risque de s’afficher : « Vous ne disposez d’aucun abonnement dans les régions autorisées ».

Pour demander qu’une région soit ajoutée à votre abonnement, créez une nouvelle demande d’assistance depuis le portail Azure, choisissez **Facturation** comme type de problème, puis suivez les invites pour envoyer votre demande.

## <a name="storage-account"></a>Compte de stockage
Le service Machine Learning a besoin d'un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant ou vous pouvez en créer un quand vous créez l’espace de travail Machine Learning (si vous disposez d’un quota pour la création d’un compte de stockage).

Une fois l’espace de travail Machine Learning créé, vous pouvez vous connecter à Machine Learning Studio au moyen du compte Microsoft utilisé pour créer l’espace de travail. Si vous rencontrez le message d'erreur « Espace de travail introuvable » (comme dans la capture d'écran suivante), effectuez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Workspace not found][screen3]

**Pour supprimer les cookies du navigateur**

1. Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** dans le coin supérieur droit et sélectionnez **Options Internet**.  

![Options Internet][screen4]

2. Sous l'onglet **Général**, cliquez sur **Supprimer…**

![General tab][screen5]

3. Dans la boîte de dialogue **Supprimer l’historique de navigation**, vérifiez que **Cookies et données de sites web** est sélectionné, puis cliquez sur **Supprimer**.

![Delete cookies][screen6]

Une fois les cookies supprimés, redémarrez le navigateur, puis accédez à la page [Microsoft Azure Machine Learning](https://studio.azureml.net) . Lorsque vous êtes invité à fournir un nom d’utilisateur et un mot de passe, entrez le même compte Microsoft que celui utilisé pour créer l’espace de travail.

## <a name="comments"></a>Commentaires

Notre objectif est de vous offrir une expérience de Machine Learning qui soit aussi transparente que possible. Publiez tous vos commentaires et problèmes sur le [forum Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous permettre de vous aider au mieux.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
