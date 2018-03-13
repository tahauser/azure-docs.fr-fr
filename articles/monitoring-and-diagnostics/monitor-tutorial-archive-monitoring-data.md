---
title: "Archiver les données de surveillance d’Azure | Microsoft Docs"
description: "Archivez les données de journal et de métrique produites dans Azure sur un compte de stockage."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: a3ab4713861d4d9681ad2ac5f084255fc29462ce
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="archive-azure-monitoring-data"></a>Archiver les données de surveillance d’Azure

Plusieurs couches de votre environnement Azure génèrent des données de journal et de métrique qui peuvent être archivées sur un compte de stockage Azure. Vous pouvez effectuer cette opération pour conserver un historique des données de surveillance au fil du temps dans un magasin non interrogeable peu coûteux après que les données ont dépassé leur période de rétention dans Log Analytics ou Azure Monitor. Ce didacticiel décrit le processus de configuration de votre environnement Azure pour archiver des données sur un compte de stockage.

> [!div class="checklist"]
> * Créer un compte de stockage pour contenir les données de surveillance
> * Y acheminer les journaux d’abonnement 
> * Y acheminer les données de ressource 
> * Y acheminer les données de machine virtuelle (système d’exploitation invité) 
> * Y afficher les données de surveillance 
> * Nettoyage des ressources 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Vous devez d’abord configurer un compte de stockage sur lequel archiver les données de surveillance. Pour ce faire, [procédez de la manière décrite dans cet article](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Router les journaux d’abonnement vers le compte de stockage

Vous êtes désormais prêt à commencer la configuration de votre environnement Azure pour router les données de surveillance vers un compte de stockage. Tout d’abord, nous configurons les données au niveau de l’abonnement (contenues dans le journal d’activité Azure) de façon à ce qu’elles soient acheminées vers le compte de stockage. Le [**Journal d’activité Azure**](monitoring-overview-activity-logs.md) fournit un historique des événements au niveau de l’abonnement dans Azure. Vous pouvez le parcourir dans le portail Azure pour déterminer *qui* a créé, mis à jour ou supprimé *quelles* ressources et *quand* il l’a fait.

1. Cliquez sur le bouton **Moniteur** dans la liste de navigation de gauche, puis sur **Journal d’activité**.

   ![Section Journal d’activité](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. Dans la section Journal d’activité qui s’affiche, cliquez sur le bouton **Exporter**.

3. Dans la section **Exporter le journal d’activité** qui s’affiche, activez la case à cocher **Exporter vers un compte de stockage**, puis cliquez sur **Sélectionner un compte de stockage.**

   ![Exportation du Journal d’activité](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. Dans la section qui s’affiche, dans la liste déroulante **Compte de stockage**, sélectionnez le nom du compte de stockage que vous avez créé à l’étape précédente **Créer un compte de stockage**, puis cliquez sur **OK**.

   ![Sélectionner un compte de stockage](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Positionnez le curseur **Rétention (jours)** sur 30. Ce curseur définit le nombre de jours de rétention des données de surveillance dans le compte de stockage. Azure Monitor supprime automatiquement les données antérieures au nombre de jours spécifié. Si la durée de la période de rétention est zéro, les données sont conservées pendant une durée indéfinie.

6. Cliquez sur **Enregistrer**, puis fermez cette section.

Les données de surveillance de votre abonnement sont maintenant transférées dans le compte de stockage.

## <a name="route-resource-data-to-the-storage-account"></a>Router les données de ressource vers le compte de stockage

Nous configurons à présent les données au niveau des ressources (journaux de diagnostic et de métriques des ressources) pour qu’elles soient routées vers le compte de stockage en configurant les **paramètres de diagnostic des ressources**.

1. Cliquez sur le bouton **Moniteur** dans la liste de navigation de gauche, puis sur **Paramètres de diagnostic**. Vous trouverez la liste de toutes les ressources dans votre abonnement, qui produisent des données de surveillance dans Azure Monitor. Si vous n’avez pas de ressources dans cette liste, vous pouvez [créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) avant de poursuivre afin que de disposer d’une ressource sur laquelle configurer un paramètre de diagnostic.

2. Cliquez sur une ressource dans la liste, puis sur **Activer les diagnostics**.
   
   ![Activer les diagnostics](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Si un paramètre est déjà configuré, vous voyez à la place les paramètres existants et un bouton pour **Ajouter une configuration de diagnostic**. Cliquez sur ce bouton.

   Un paramètre de diagnostic de ressource définit *quelles* données de surveillance doivent être routées à partir d’une ressource particulière, et vers *où* ces données doivent être routées.

3. Dans la section qui s’affiche, attribuez un **nom** à votre paramètre, puis activez la case à cocher **Archiver dans un compte de stockage**.

   ![Section Paramètres de diagnostic](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Cliquez sur le bouton **Configurer** sous **Archiver dans un compte de stockage**, puis sélectionnez le compte de stockage que vous avez créé dans la section précédente. Cliquez sur **OK**.

   ![Compte de stockage des paramètres de diagnostic](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Activez toutes les cases à cocher sous **Journal** et **Métrique**. Selon le type de ressource, il se peut qu’une seule de ces options soit disponible. Ces cases à cocher déterminent les catégories de données de journal et de métrique disponibles pour ce type de ressource, qui sont envoyées à la destination que vous avez sélectionnée, en l’occurrence, un compte de stockage.

   ![Catégories de paramètres de diagnostic](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Positionnez le curseur **Rétention (jours)** sur 30. Ce curseur définit le nombre de jours de rétention des données de surveillance dans le compte de stockage. Azure Monitor supprime automatiquement les données antérieures au nombre de jours spécifié. Si la durée de la période de rétention est zéro, les données sont conservées pendant une durée indéfinie.

7. Cliquez sur **Enregistrer**.

Les données de surveillance de votre ressource sont maintenant transférées dans le compte de stockage.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Router les données de machine virtuelle (système d’exploitation invité) vers le compte de stockage

1. Si vous n’avez pas encore de machine virtuelle dans votre abonnement, [créez une machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

2. Dans la liste de navigation de gauche sur le portail, cliquez sur **Machines virtuelles**.

3. Dans la liste des machines virtuelles qui s’affiche, cliquez sur la machine virtuelle que vous avez créée.

4. Dans la section qui s’affiche, dans le volet de navigation de gauche, cliquez sur **Paramètres de diagnostic**. Cette section vous permet de configurer l’extension de surveillance prédéfinie d’Azure Monitor sur votre machine virtuelle et de router les données produites par Windows ou Linux vers un compte de stockage.

   ![Accédez aux paramètres de diagnostic](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Dans la section qui s’affiche, cliquez sur **Activer la surveillance d’invités**.

   ![Activer les paramètres de diagnostic](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Une fois le paramètre de diagnostic correctement enregistré, l’onglet **Vue d’ensemble** affiche la liste des données collectées et l’emplacement où elles sont stockées. Cliquez sur la section **Compteurs de performances** pour passer en revue l’ensemble des compteurs de performances de Windows collectés.

   ![Paramètres des compteurs de performances](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Cliquez sur l’onglet **Journaux**, puis activez les cases à cocher des journaux de niveau **Informations** sur les journaux Application et Système.

   ![Paramètres des journaux](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Cliquez sur l’onglet **Agent**, puis, sous **Compte de stockage**, cliquez sur le nom du compte de stockage affiché.

   ![Mettre à jour le compte de stockage](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. Dans la section qui s’affiche, sélectionnez le compte de stockage que vous avez créé à l’étape précédente **Créer un compte de stockage**.

10. Cliquez sur **Enregistrer**.

Les données de surveillance de vos machines virtuelles sont maintenant transférées dans le compte de stockage.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Afficher les données de surveillance figurant dans le compte de stockage

Si vous avez suivi les étapes précédentes, les données ont commencé à circuler vers votre compte de stockage.

1. Pour certains types de données, par exemple le Journal d’activité, il doit y avoir une activité qui génère un événement dans le compte de stockage. Pour générer une activité dans le Journal d’activité, suivez [ces instructions](./monitor-quick-audit-notify-action-in-subscription.md). Il se peut que vous deviez attendre cinq minutes avant que l’événement apparaisse dans le compte de stockage.

2. Dans le portail, accédez à la section **Comptes de stockage** en la recherchant dans la barre de navigation de gauche.

3. Identifiez le compte de stockage que vous avez créé dans la section précédente, puis cliquez dessus.

4. Cliquez sur **Objets blob**, sur le conteneur nommé **insights-operational-logs**, puis sur le conteneur nommé **nom = valeur par défaut**. Il s’agit du conteneur dans lequel figure votre Journal d’activité. Les données de surveillance sont réparties dans les conteneurs par ID de ressource (simplement l’ID d’abonnement pour le Journal d’activité), puis par date et heure. Le format complet de ces objets blob est le suivant :

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID de l’abonnement}/y={année, quatre chiffes}/m={mois, deux chiffres}/d={jour, deux chiffres}/h={heure, deux chiffres, format 24 heures}/m=00/PT1H.json

5. Accédez au fichier PT1H.json en cliquant dans les conteneurs pour l’ID de ressource, la date et l’heure. Cliquez sur le fichier PT1H.json, puis sur **Télécharger**. Chaque objet blob PT1H.json contient un objet blob d’événements JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h=12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m=00) est toujours 00, car les événements du journal sont répartis en objets blob par heure.

   Vous pouvez à présent afficher l’événement JSON stocké dans le compte de stockage. Pour les journaux de diagnostic de ressource, le format de l’objet blob est le suivant :

   insights-logs-{nom de la catégorie de journal}/resourceId=/{ID de la ressource}/y={année, quatre chiffres}/m={mois, deux chiffres}/d={jour, deux chiffres}/h={heure, deux chiffres, format 24 h}/m=00/PT1H.json

6. Les données de surveillance du système d’exploitation invité sont stockées dans des tables. Revenez à la page d’accueil du compte de stockage, puis cliquez sur **Tables**. Il existe des tables pour les métriques, les compteurs de performances et les journaux des événements.

Vous avez à présent correctement configuré les données de surveillance à archiver sur un compte de stockage.

## <a name="clean-up-resources"></a>Supprimer des ressources

1. Revenez à la section **Exporter le journal d’activité** de l’étape précédente **Router les journaux d’abonnement vers le compte de stockage**, puis cliquez sur **Réinitialiser**.

2. Accédez à la section **Paramètres de Diagnostic**, cliquez sur la ressource sur laquelle vous avez créé un paramètre de diagnostic à l’étape précédente **Router les données de ressource vers le compte de stockage**, recherchez le paramètre que vous avez créé, cliquez sur le bouton **Modifier le paramètre**, puis cliquez sur **Supprimer**.

3. Accédez à la section **Paramètres de Diagnostic** sur la machine virtuelle que vous avez configurée à l’étape précédente **Router les données de machine virtuelle (système d’exploitation invité) vers le compte de stockage**, puis, sous l’onglet **Agent**, cliquez sur **Supprimer** (sous la section **Supprimer l’agent de diagnostics Azure**).

4. Accédez au compte de stockage que vous avez créé à l’étape précédente **Créer un compte de stockage**, puis cliquez sur **Supprimer le compte de stockage**. Entrez le nom du compte de stockage, puis cliquez sur **Supprimer**.

5. Si vous avez créé une machine virtuelle ou une application logique pour les étapes précédentes, supprimez-les également.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer les données de surveillance de votre environnement Azure (abonnement, ressources et système d’exploitation invité) de façon à ce qu’elles soient archivées sur un compte de stockage. 


> [!div class="checklist"]
> * Créer un compte de stockage pour contenir les données de surveillance
> * Y acheminer les journaux d’abonnement 
> * Y acheminer les données de ressource 
> * Y acheminer les données de machine virtuelle (système d’exploitation invité) 
> * Y afficher les données de surveillance 
> * Nettoyage des ressources 

Pour tirer le meilleur parti de vos données et en dégager des informations supplémentaires, envoyez également vos données dans Log Analytics.

> [!div class="nextstepaction"]
> [Prise en main d’un espace de travail Log Analytics](../log-analytics/log-analytics-get-started.md)
