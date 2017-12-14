---
title: Analyse continue de votre pipeline de mise en production DevOps avec VSTS et Azure Application Insights | Microsoft Docs
description: Fournit des instructions permettant de configurer rapidement une analyse continue avec Application Insights.
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: article
manager: carmonm
ms.openlocfilehash: 5bfbdd0033f966422a84071a694845627827f016
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Ajouter l’analyse continue à votre pipeline de mise en production

Visual Studio Team Services (VSTS) s’intègre à Azure Application Insights pour permettre l’analyse continue de votre pipeline de mise en production DevOps tout au long du cycle de vie du développement logiciel. 

VSTS prend désormais en charge l’analyse continue, grâce à laquelle les pipelines de mise en production peuvent intégrer les données d’analyse d’Application Insights et d’autres ressources Azure. Lorsqu’une alerte Application Insights est détectée, le déploiement peut rester contrôlé ou être restauré jusqu’à ce que l’alerte soit résolue. Si toutes les vérifications réussissent, les déploiements passent automatiquement de la phase de test à la mise en production sans nécessiter d’intervention manuelle. 

## <a name="configure-continuous-monitoring"></a>Configurer l’analyse continue

1. Sélectionnez un projet VSTS existant.

2. Passez le curseur sur **Build et mise en production** > sélectionnez **Versions** > cliquez sur le **signe plus** > **Création d’une définition de version** > recherchez **Analyse** > **Déploiement d’Azure App Service avec l’analyse continue.**

   ![Nouvelle définition de version VSTS](.\media\app-insights-continuous-monitoring\001.png)

3. Cliquez sur **Appliquer**.

4. À côté du point d’exclamation rouge, sélectionnez le texte en bleu pour **afficher les tâches d’environnement.**

   ![Afficher les tâches d’environnement](.\media\app-insights-continuous-monitoring\002.png)

   Une boîte de configuration s’affiche : utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètre        | Valeur |
   | ------------- |:-----|
   | **Nom de l’environnement**      | Nom qui décrit l’environnement de définition de mise en production |
   | **Abonnement Azure** | Liste déroulante contenant les abonnements Azure liés au compte VSTS|
   | **Nom de l’App Service** | En fonction des autres sélections, il peut être nécessaire de saisir manuellement une nouvelle valeur pour ce champ. |
   | **Groupe de ressources**    | La liste déroulante affiche les groupes de ressources disponibles |
   | **Nom de la ressource Application Insights** | La liste déroulante affiche toutes les ressources Application Insights correspondant au groupe de ressources sélectionné précédemment.

5. Sélectionnez **Configurer les alertes Application Insights**.

6. Pour les règles d’alerte par défaut, sélectionnez **Enregistrer** > entrez un commentaire descriptif > cliquez sur **OK**.

## <a name="modify-alert-rules"></a>Modifier les règles d’alerte

1. Pour modifier les paramètres d’alerte prédéfinis, cliquez sur les **points de suspension...**  à droite des **règles d’alerte.**

   (Par défaut, les quatre règles d’alerte sont : la disponibilité, les requêtes ayant échoué, le temps de réponse du serveur et les exceptions du serveur.)

2. Cliquez sur le symbole de la liste déroulante en regard de **Disponibilité.**

3. Modifiez le **seuil** de disponibilité pour répondre à vos exigences de niveau de service.

   ![Modifier l’alerte](.\media\app-insights-continuous-monitoring\003.png)

4. Sélectionnez **OK** > **Enregistrer** > entrez un commentaire descriptif > cliquez sur **OK.**

## <a name="add-deployment-conditions"></a>Ajouter des conditions de déploiement

1. Cliquez sur **Pipeline** > sélectionnez le symbole des conditions **pré** ou **post-déploiement** en fonction de l’étape nécessitant un portail d’analyse continue.

   ![Conditions préalables au déploiement](.\media\app-insights-continuous-monitoring\004.png)

2. Définissez **Portails** sur **Activé** > **Portails d’approbation**> cliquez sur **Ajouter.**

3. Sélectionnez **Azure Monitor** (cette option vous permet d’accéder aux alertes à partir d’Azure Monitor et d’Application Insights).

    ![Azure Monitor](.\media\app-insights-continuous-monitoring\005.png)

4. Saisissez une valeur pour le **délai d’attente des portails**.

5. Saisissez un **intervalle d’échantillonnage.**

## <a name="deployment-gate-status-logs"></a>Journaux d’état du portail de déploiement

Une fois que vous ajoutez des portails de déploiement, une alerte dans Application Insights, d’une valeur supérieure au seuil défini précédemment, protège votre déploiement de toute promotion non souhaitée de la mise en production. Une fois l’alerte résolue, le déploiement peut s’effectuer automatiquement.

Pour observer ce comportement, sélectionnez **Mises en production** > clic droit sur le nom de la mise en production **ouvrir** > **Journaux.**

![Journaux](.\media\app-insights-continuous-monitoring\006.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la build et la mise en production de VSTS, essayez ces [démarrages rapides.](https://docs.microsoft.com/vsts/build-release/)
