---
title: "Solution de démarrage/arrêt des machines virtuelles durant les heures creuses | Microsoft Docs"
description: "Cette solution de gestion de machines virtuelles assure le démarrage et l’arrêt de vos machines virtuelles Azure Resource Manager selon une planification, et permet une surveillance proactive depuis Log Analytics."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solution de démarrage/arrêt des machines virtuelles durant les heures creuses dans Azure Automation

La solution de démarrage/arrêt des machines virtuelles durant les heures creuses démarre et arrête vos machines virtuelles Azure selon une planification définie par l’utilisateur, fournit des informations via Log Analytics et peut envoyer des e-mails en utilisant [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Elle prend en charge Azure Resource Manager et les machines virtuelles classiques pour la plupart des scénarios. 

Cette solution propose une fonctionnalité décentralisée d’Automation pour les clients souhaitant réduire les coûts en utilisant des ressources sans serveur et à faible coût. Voici quelques fonctionnalités :

* Planification du démarrage/arrêt de machines virtuelles
* Planification du démarrage/arrêt de machines virtuelles dans un ordre croissant à l’aide de balises Azure (non pris en charge pour les machines virtuelles classiques)
* Arrêt automatique de machines virtuelles en fonction de la faible utilisation de l’UC

## <a name="prerequisites"></a>Composants requis

- Les runbooks fonctionnent avec un [compte d’identification Azure](automation-offering-get-started.md#authentication-methods).  Le compte d’identification est la méthode d’authentification recommandée, car elle utilise l’authentification par certificat au lieu d’un mot de passe, susceptible d’expirer ou de changer fréquemment.  

- Cette solution permet uniquement de gérer les machines virtuelles qui se trouvent dans le même abonnement que le compte Automation.  

- Cette solution peut seulement être déployée dans les régions Azure suivantes : Sud-Est de l’Australie, Centre du Canada, Centre de l’Inde, Est des États-Unis, Est du Japon, Sud-Est Asiatique, Royaume-Uni Sud et Europe de l’Ouest.  
    
    > [!NOTE]
    > Les runbooks gérant la planification des machines virtuelles peuvent cibler les machines virtuelles de n’importe quelle région.  

- Pour envoyer des notifications par e-mail lorsque les runbooks de démarrage et d’arrêt de machine virtuelle se terminent, lors de l’intégration à partir de Azure Marketplace, vous devez sélectionner **Oui** pour déployer SendGrid. 

    > [!IMPORTANT]
    > SendGrid est un service tiers, contactez [SendGrid](https://sendgrid.com/contact/) pour des informations sur la prise en charge.  
    >
   
    Les limites de SendGrid sont les suivantes :

    * Un seul compte SendGrid maximum par utilisateur et par abonnement
    * Deux comptes SendGrid maximum par abonnement

Si vous avez déployé une version précédente de cette solution, vous devez d’abord la supprimer à partir de votre compte avant de déployer cette version.  

## <a name="solution-components"></a>Composants de la solution

Cette solution inclut des runbooks et des planifications préconfigurés et une intégration avec Log Analytics qui vous permet de personnaliser le démarrage et l’arrêt de vos machines virtuelles selon les besoins de votre entreprise. 

### <a name="runbooks"></a>Runbooks

Le tableau suivant répertorie les runbook déployés sur votre compte Automation.  Il n’est pas recommandé de modifier le code du runbook, écrivez plutôt votre propre runbook pour disposer de nouvelles fonctionnalités.

> [!NOTE]
> N’exécutez pas directement n’importe quel runbook contenant le mot « Child » ajouté à la fin de son nom.
>

Tous les runbooks parents incluent le paramètre *WhatIf* qui, une fois configuré sur **True**, prend en charge la description du comportement exact du runbook lors de l’exécution sans le paramètre *WhatIf* et valide les machines virtuelles correctes ciblées.  Les runbooks effectuent uniquement leurs actions définies lorsque le paramètre *WhatIf* est défini sur **False**. 

|**Runbook** | **Paramètres** | **Description**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Appelé par le runbook parent uniquement. Crée des alertes en fonction des ressources pour le scénario de AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf : True ou False <br> VMList | Crée ou met à jour des règles d’alerte Azure sur des machines virtuelles dans l’abonnement ou les groupes de ressource ciblées. <br> VMList : liste des machines virtuelles séparée par des virgules.  Par exemple, *vm1,vm2,vm3*| 
|AutoStop_Disable | Aucun | Désactiver les alertes et la planification par défaut de AutoStop.| 
|AutoStop_StopVM_Child | WebHookData | Appelé par le runbook parent uniquement. Les règles d’alerte appellent ce runbook, et il arrête la machine virtuelle.|  
|Bootstrap_Main | Aucun | Utilisé une seule fois pour établir les configurations Bootstrap, telles que webhookURI qui n’est généralement pas accessible à partir de Azure Resource Manager. Ce runbook est automatiquement supprimé si le déploiement s’est terminé avec succès.|  
|ScheduledStartStop_Child | VMName <br> Action : Stop ou Start <br> ResourceGroupName | Appelé par le runbook parent uniquement. Exécute l’arrêt ou le démarrage pour l’arrêt planifié.|  
|ScheduledStartStop_Parent | Action : Stop ou Start <br> WhatIf : True ou False | Cette opération prendra effet sur toutes les machines virtuelles dans l’abonnement à moins que vous ne modifiiez **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames** qui limiteront l’exécution aux groupes de ressources cible. Vous pouvez également exclure des machines virtuelles spécifiques en mettant à jour la variable **External_ExcludeVMNames**. WhatIf se comporte de la même façon que dans les autres runbooks.|  
|SequencedStartStop_Parent | Action : Stop ou Start <br> WhatIf : True ou False | Créez une balise nommée **SequenceStart** et une autre nommée **SequenceStop** sur chaque machine virtuelle dont vous souhaitez configurer le démarrage\\arrêt d’activité. La valeur de la balise doit être un entier positif (1,2,3) correspondant à l’ordre du démarrage\\arrêt dans l’ordre croissant. WhatIf se comporte de la même façon que dans les autres runbooks. <br> **Remarque : les machines virtuelles doivent se trouver dans des groupes de ressources définis External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames et External_ExcludeVMNames dans des variables de Azure Automation et avoir les balises appropriées pour que les actions prennent effet.**|

### <a name="variables"></a>variables

Le tableau suivant répertorie les variables créées dans votre compte Automation.  Il est recommandé de modifier uniquement les variables avec le préfixe **External**, modifier des variables avec le préfixe **Internal** entraîne des effets indésirables.  

|**Variable** | **Description**|
---------|------------|
|External_AutoStop_Condition | Il s’agit de l’opérateur conditionnel requis pour configurer la condition avant le déclenchement d’une alerte. Les valeurs acceptables sont **GreaterThan** (supérieur à), **GreaterThanOrEqual** (égal ou supérieur à), **LessThan** (Inférieur à), **LessThanOrEqual** (Inférieur ou égal à).|  
|External_AutoStop_Description | Alerte pour arrêter la machine virtuelle si le pourcentage d’utilisation de l’UC dépasse le seuil.|  
|External_AutoStop_MetricName | Nom de la métrique de performances pour laquelle la règle d’alerte Azure doit être configurée.| 
|External_AutoStop_Threshold | Seuil de la règle d’alerte Azure spécifiée dans la variable *External_AutoStop_MetricName*. Les valeurs de pourcentage vont de 1 à 100.|  
|External_AutoStop_TimeAggregationOperator | L’opérateur d’agrégation de temps appliqué à la taille de la fenêtre sélectionnée à évaluer la condition. Les valeurs acceptables sont **Average** (moyenne), **Minimum**, **Maximum**, **Total**, **Last** (dernier).|  
|External_AutoStop_TimeWindow | La taille de la fenêtre sur laquelle Azure analysera la métrique sélectionnée pour déclencher une alerte. Ce paramètre accepte une entrée au format d’un intervalle de temps. Les valeurs possibles vont de cinq minutes à six heures.|  
|External_EmailFromAddress | Spécifie l’expéditeur de l’e-mail.|  
|External_EmailSubject | Spécifie le texte de la ligne d’objet du message électronique.|  
|External_EmailToAddress | Spécifie le ou les destinataires du message électronique. Séparer les noms à l’aide d’une virgule.|  
|External_ExcludeVMNames | Saisissez les noms des machines virtuelles à exclure, en séparant les noms à l’aide d’une virgule sans espace.|  
|External_IsSendEmail | Spécifie l’option d’envoi d’une notification par e-mail à l’achèvement.  Spécifiez **Oui** ou **Non** pour ne pas envoyer d’e-mail.  Cette option doit être **Non** si vous n’avez pas créé SendGrid pendant le déploiement initial.|  
|External_Start_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources, séparant les valeurs à l’aide d’une virgule, ciblés pour les actions de démarrage.|  
|External_Stop_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources, séparant les valeurs à l’aide d’une virgule, ciblés pour les actions d’arrêt.|  
|Internal_AutomationAccountName | Spécifie le nom du compte Automation.|  
|Internal_AutoSnooze_WebhookUri | Spécifie l’URI du Webhook appelée pour le scénario AutoStop.|  
|Internal_AzureSubscriptionId | Spécifie l’ID d’abonnement Azure.|  
|Internal_ResourceGroupName | Spécifie le nom du groupe de ressources du compte Azure Automation.|  
|Internal_SendGridAccountName | Spécifie le nom du compte SendGrid.|  
|Internal_SendGridPassword | Spécifie le mot de passe du compte SendGrid.|  

<br>

Pour tous les scénarios, les variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, et **External_ExcludeVMNames** sont nécessaires pour le ciblage de machines virtuelles à l’exception du fait de fournir une liste de machines virtuelles séparées par des virgules pour le runbook **AutoStop_CreateAlert_Parent**. Autrement dit, vos machines virtuelles doivent résider dans des groupes de ressources cibles pour que les actions de démarrage/arrêt se produisent. La logique fonctionne un peu comme la stratégie de Azure, dans la mesure où vous pouvez cibler l’abonnement ou un groupe de ressources et faire que les machines virtuelles nouvellement créées héritent des actions. Cette approche évite de devoir mettre à jour une planification distincte pour chaque machine virtuelle et de gérer le démarrage/arrêt à l’échelle.

### <a name="schedules"></a>Planifications

Le tableau suivant répertorie chacune des planifications par défaut créées dans votre compte Automation.  Vous pouvez les modifier ou créer vos propres planifications personnalisées.  Par défaut, chacune de ces planifications est désactivée à l’exception de **Scheduled_StartVM** et **Scheduled-StopVM**.

Il n’est pas recommandé d’activer toutes les planifications, car cela pourrait créer un chevauchement avec plusieurs planifications devant effectuer une même action.  Au lieu de cela, il est préférable de déterminer quelles optimisations vous souhaitez exécuter et d’effectuer les modifications en conséquence.  Consultez les exemples de scénarios dans la section Vue d’ensemble pour obtenir davantage d’explications.   

|**Nom de la planification** | **Fréquence** | **Description**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | S’exécute toutes les huit heures | Exécute le runbook AutoStop_CreateAlert_Parent toutes les 8 heures, qui arrête les valeurs basées sur les machines virtuelles dans « External_Start_ResourceGroupNames », « External_Stop_ResourceGroupNames » et « External_ExcludeVMNames » dans les variables de Azure Automation.  Vous pouvez également spécifier une liste de machines virtuelles séparées par des virgules en utilisant le paramètre « VMList ».|  
|Scheduled_StopVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre « Stop » tous les jours à un instant donné.  Arrête automatiquement toutes les machines virtuelles répondant aux règles définies par le biais des Variables de ressource.  Nous recommandons l’activation de la planification sœur, Scheduled-StartVM.|  
|Scheduled_StartVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre *Start* tous les jours à un instant donné.  Démarre automatiquement toutes les machines virtuelles répondant aux règles définies et une partie des variables appropriées.  Nous recommandons l’activation de la planification sœur, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), chaque vendredi | Exécute le runbook Sequenced_Parent avec un paramètre *Stop* tous les vendredis à un instant donné.  Arrête séquentiellement (de façon croissante) toutes les machines virtuelles avec la balise **SequenceStop** définie et une partie des variables appropriées.  Reportez-vous à la section Runbooks pour plus d’informations sur les valeurs de balise et les variables des ressources.  Nous recommandons l’activation de la planification sœur, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), tous les lundis | Exécute le runbook Sequenced_Parent avec un paramètre *Start* tous les lundis à un instant donné.  Démarre séquentiellement (de façon décroissante) toutes les machines virtuelles avec la balise **SequenceStart** définie et une partie des variables appropriées.  Reportez-vous à la section Runbooks pour plus d’informations sur les valeurs de balise et les variables des ressources.  Nous recommandons l’activation de la planification sœur, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configuration

Procédez comme suit pour ajouter la solution Start/Stop VMs during off-hours (Preview) (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) à votre compte Automation, puis configurer les variables pour personnaliser la solution.

1. Dans le portail Azure, cliquez sur **Nouveau**.<br> ![Portail Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Dans le volet de Marketplace, saisissez **Démarrer une machine virtuelle**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Démarrer/arrêter des machines virtuelles pendant les heures creuses [version préliminaire]** à partir des résultats de recherche.  
3. Dans le panneau **Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]** de la solution sélectionnée, vérifiez les informations du résumé, puis cliquez sur **Créer**.  
4. Le panneau **Ajouter une solution** s’affiche, vous invitant à configurer la solution pour pouvoir l’importer dans votre abonnement Automation.<br><br> ![Panneau Ajouter une solution de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Dans le panneau **Ajouter une solution**, sélectionnez **Espace de travail**, puis sélectionnez un espace de travail OMS lié à l’abonnement Azure où le compte Automation se trouve ou créez un espace de travail.  Si vous ne disposez pas d’espace de travail, vous pouvez sélectionner **Créer un espace de travail**, puis dans le panneau **Espace de travail OMS**, procédez comme suit : 
   - Spécifiez un nom pour le nouvel **espace de travail OMS**.
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Pour **Groupe de ressources**, vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant.  
   - Sélectionnez un **emplacement**.  Actuellement, les seuls emplacement sélectionnables sont **Sud-Est de l’Australie**, **Centre du Canada**, **Centre de l’Inde**, **Est des États-Unis**, **Est du Japon**, **Sud-Est Asiatique**, **Royaume-Uni Sud** et **Europe de l’Ouest**.
   - Sélectionner un **niveau de tarification**.  Deux niveaux sont proposés pour la solution : gratuit et payant OMS.  La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau gratuit.  Le niveau payant OMS permet de collecter une quantité illimitée de données quotidiennement.  

        > [!NOTE]
        > Tandis que le niveau payant par Go (autonome) s’affiche comme une option, il n’est pas applicable.  Si vous la sélectionnez et poursuivez la création de cette solution dans votre abonnement, elle échouera.  Ce problème sera résolu lors de la sortie officielle de cette solution.<br>Si vous utilisez cette solution, elle utilise uniquement les minutes du travail d’automatisation et l’ingestion du journal.  La solution n’ajoute pas de nœuds OMS à votre environnement.  

6. Après avoir entré les informations requises dans le panneau **Espace de travail OMS**, cliquez sur **Créer**.  Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous serez redirigé vers le panneau **Ajouter une solution**.  
7. Dans le panneau **Ajouter une solution**, sélectionnez **Compte Automation**.  Si vous créez un espace de travail OMS, vous devez également créer un compte Automation qui sera associé à l’espace de travail OMS spécifié précédemment, y compris votre abonnement, votre groupe de ressources et votre région Azure.  Vous pouvez sélectionner **Créer un compte Automation** et dans le panneau **Ajouter un compte Automation**, fournir les informations suivantes : 
  - Dans le champ **Nom**, saisissez le nom du compte Automation.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné et ne peuvent pas être modifiées.  Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus dans cette solution.  Lorsque vous cliquez sur **OK**, les options de configuration sont validées et le compte Automation est créé.  Vous pouvez suivre la progression sous **Notifications** dans le menu. 

    Sinon, vous pouvez sélectionner un compte d’identification Automation existant.  Notez que le compte que vous sélectionnez ne peut pas déjà être lié à un autre espace de travail OMS, ou un message s’affichera dans le panneau pour vous en informer.  Si le compte est déjà lié, vous devez sélectionner un autre compte d’identification Automation ou en créer un nouveau.<br><br> ![Compte Automation déjà lié à l’espace de travail OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Enfin, dans le panneau **Ajouter une solution**, sélectionnez **Configuration** pour afficher le panneau **Paramètres**.<br><br> ![Volet Paramètres pour la solution](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Dans le panneau **Paramètres**, vous êtes invité à :  
   - Spécifier le **nom du groupe de ressources cible**, c’est-à-dire le nom du groupe de ressources qui contient les machines virtuelles à gérer par cette solution.  Vous pouvez entrer plusieurs noms en les séparant à l’aide de virgules (les valeurs ne respectent pas la casse).  Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge.
   - Spécifiez la **liste d’exclusion de machines virtuelles (chaîne)**, qui est le nom d’une ou plusieurs machines virtuelles à partir du groupe de ressources cible.  Vous pouvez entrer plusieurs noms en les séparant à l’aide de virgules (les valeurs ne respectent pas la casse).  Les caractères génériques sont pris en charge.
   - Sélectionnez une **planification**, c’est-à-dire une date et une heure récurrentes pour le démarrage et l’arrêt des machines virtuelles du ou des groupes de ressources.  Par défaut, la planification est configurée sur le fuseau horaire UTC et il est impossible de sélectionner une autre région.  Si vous souhaitez configurer la planification sur votre propre fuseau horaire après la configuration de la solution, consultez [Modification de la planification de démarrage et d’arrêt](#modifying-the-startup-and-shutdown-schedule) ci-dessous.
   - Pour recevoir des **notifications par e-mail** de SendGrid, acceptez la valeur par défaut **Oui** et fournissez une adresse e-mail valide.  Si vous sélectionnez **Non** et décidez ultérieurement de recevoir des notifications par e-mail, vous devez redéployer la solution à partir de Azure Marketplace.  

10. Une fois que vous avez configuré les paramètres initiaux requis pour la solution, sélectionnez **Créer**.  Tous les paramètres sont validés et une tentative de déploiement de la solution dans votre abonnement est effectuée.  Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="collection-frequency"></a>Fréquence de collecte

Le journal des tâches Automation et les données des flux de tâches sont ingérés dans le référentiel Log Analytics toutes les cinq minutes.  

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez cette solution de gestion de machines virtuelles à votre espace de travail Log Analytics à partir du portail Azure, la vignette **StartStopVMView** est ajoutée à votre tableau de bord.  Cette vignette affiche un compteur et une représentation graphique des tâches de runbooks qui ont démarré et qui ont abouti.<br><br> ![Vignette StartStopVMView de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Dans votre compte Automation, vous pouvez accéder et gérer la solution en sélectionnant l’option **Espace de travail** ainsi dans la page Log Analytics, sélectionnez **Solutions** dans le volet gauche.  Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM [Espace de travail]** dans la liste.<br><br> ![Liste de solutions dans Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

La sélection de la solution entraîne l’ouverture du panneau de la solution **Start-Stop-VM[Espace de travail]**, où vous pouvez consulter des informations importantes telles que la vignette **StartStopVM**, qui comme dans votre espace de travail Log Analytics affiche un compteur et une représentation graphique des tâches de runbooks qui ont démarré et qui ont abouti.<br><br> ![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

À ce stade, vous pouvez également analyser plus en détails les enregistrements de tâche en cliquant sur la vignette de l’anneau et à partir du tableau de bord de la solution, il affiche l’historique des travaux, des requêtes de recherche de journal prédéfinies, et basculer vers le portail avancé Log Analytics pour faire des recherches en fonction de vos requêtes de recherche.  

Tous les runbooks parents incluent le paramètre *WhatIf* qui, une fois configuré sur **True**, prend en charge la description du comportement exact du runbook lors de l’exécution sans le paramètre *WhatIf* et valide les machines virtuelles correctes ciblées.  Les runbooks effectuent uniquement leurs actions définies lorsque le paramètre *WhatIf* est défini sur **False**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Scénario 1 : arrêt/démarrage journalier des machines virtuelles d’un abonnement ou de groupes de ressources cibles 

Il s’agit de la configuration par défaut lorsque vous déployez la solution pour la première fois.  Par exemple, vous pouvez le configurer pour arrêter toutes les machines virtuelles d’un abonnement dans la soirée lorsque vous quittez le travail, et pour les démarrer le matin lorsque vous arrivez au bureau. Lorsque vous configurez les planifications **Scheduled-StartVM » et **Scheduled-StopVM** pendant le déploiement, ils démarrent et arrêtent les machines virtuelles cibles.
>[!NOTE]
>Le fuseau horaire est votre fuseau horaire actuel lorsque vous configurez le paramètre du temps de planification ; Toutefois, il est conservé au format UTC dans Azure Automation.  Il est inutile de convertir un fuseau horaire car cela est géré pendant le déploiement.

Vous choisissez l’étendue des machines virtuelles cibles en configurant les deux variables - **External_Start_ResourceGroupNames**, ** External_Stop_ResourceGroupNames, et **External_ExcludeVMNames**.  

>[!NOTE]
> La valeur de la variable **Target ResourceGroup Names**» est stockée comme étant également la valeur des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames**. Pour plus de granularité, vous pouvez modifier chacune de ces variables pour cibler des groupes de ressources différents.  Pour une action de démarrage, utilisez **External_Start_ResourceGroupNames** et utilisez **External_Stop_ResourceGroupNames** pour un arrêt. Les nouvelles machines virtuelles sont automatiquement ajoutées aux planifications de démarrage et d’arrêt.

Pour tester et valider votre configuration, démarrez manuellement le runbook **ScheduledStartStop_Parent** et définissez le paramètre *ACTION* sur **Start** ou **Stop** et le paramètre *WhatIf* sur **True**.<br><br> ![Configurer les paramètres d’un runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Cela vous permet d’avoir un aperçu de l’action qui aurait lieu et d’apporter les modifications nécessaires avant une implémentation sur les machines virtuelles de production.  Une fois que vous en avez la maitrise, vous pouvez exécuter manuellement le runbook avec le paramètre défini sur **False** ou laisser les planifications de Automation **Schedule-StartVM** et **Schedule-StopVM** s’exécuter automatiquement d’après votre planification établie.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Scénario 2 : Organiser la séquence de démarrage/arrêt des machines virtuelles d’un abonnement à l’aide de balises
Dans un environnement comprenant deux composants ou plus sur plusieurs machines virtuelles prenant en charge une charge de travail distribuée, il est important de prendre en charge l’ordre de démarrage/arrêt des composants.  Pour ce faire, vous pouvez procéder comme suit.

1. L’ajout de balises **SequenceStart** et **SequenceStop** avec une valeur entière positive aux machines virtuelles cibles de votre abonnement dans les variables **External_Start_ResourceGroupNames**et **External_Stop*ResourceGroupNames**.  Les actions de démarrage et d’arrêt sont effectuées dans l’ordre croissant.  Pour en savoir plus sur le balisage d’une machine virtuelle, consultez [Baliser une machine virtuelle Windows dans Azure](../virtual-machines/windows/tag.md) et [Baliser une machine virtuelle Linux dans Azure](../virtual-machines/linux/tag.md)
2. Modifiez les planifications **Sequenced-StartVM** et **StopVM-Sequenced** avec la date et l’heure répondant à vos besoins et activez la planification.  

Pour tester et valider votre configuration, démarrez manuellement le runbook **SequencedStartStop_Parent** et définissez le paramètre *ACTION* sur **Start** ou **Stop** et le paramètre *WhatIf* sur **True**.<br><br> ![Configurer les paramètres d’un runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Cela vous permet d’avoir un aperçu de l’action qui aurait lieu et d’apporter les modifications nécessaires avant une implémentation sur les machines virtuelles de production.  Une fois que vous en avez la maitrise, vous pouvez exécuter manuellement le runbook avec le paramètre défini sur **False** ou laisser les planifications de Automation **Sequenced-StartVM** et **Sequenced-StopVM** s’exécuter automatiquement d’après votre planification établie.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Scénario 3 : Démarrage/arrêt automatique des machines virtuelles d’un abonnement en fonction de l’utilisation de l’UC
Pour aider à gérer le coût d’exécution des machines virtuelles de votre abonnement, cette solution peut aider en évaluant les machines virtuelles Azure non utilisées pendant les heures creuses, par exemple après les heures de travail, et les arrête automatiquement si l’utilisation de l’UC est inférieure à X %.  

Par défaut, la solution est préconfigurée de manière à évaluer les mesures du pourcentage d’utilisation de l’UC, et si l’utilisation moyenne est de 5 % ou moins.  Cela est contrôlé par les variables suivantes et peut être modifié si leurs valeurs par défaut ne répondent pas à vos besoins :

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Vous pouvez activer cette solution pour prendre un charge un abonnement et un groupe de ressources, ou bien une liste spécifique de machines virtuelles, mais pas les deux.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Configurer l’arrêt pour un abonnement et un groupe de ressources

1. Configurez les variables **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** pour spécifier les machines virtuelles cibles.  
2. Activez et mettez à jour la planification **Schedule_AutoStop_CreateAlert_Parent**.
3. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre *ACTION* configuré sur **Start** et le paramètre *WhatIf* configuré sur  **True** pour prévisualiser vos modifications.

#### <a name="target-stop-action-by-vm-list"></a>Configurer l’arrêt par une liste des ordinateurs virtuels

1. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre *ACTION* configuré sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et configurez le paramètre *WhatIf* sur **True** pour prévisualiser vos modifications.  
2. Configurez le paramètre **External_ExcludeVMNames** avec une liste de machines virtuelles séparées par une virgule (VM1, VM2, VM3).
3. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**.  Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus de détails, voir [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).

Maintenant que vous avez une planification pour l’arrêt des machines virtuelles en fonction de l’utilisation du processeur, vous devez activer l’une des planifications ci-dessous pour les démarrer.

* Configurer le démarrage pour un abonnement et un groupe de ressources.  Consultez les étapes du [Scénario n°1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) pour tester et activer la planification **Scheduled-StartVM**.
* Configurer le démarrage pour un abonnement un groupe de ressources et des balises.  Consultez les étapes du [Scénario n°2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) pour tester et activer la planification « Sequenced-StartVM ».


### <a name="configuring-e-mail-notifications"></a>Configuration des notifications par courrier électronique

Pour configurer les notifications par e-mail une fois la solution déployée, vous pouvez modifier les trois variables suivantes :

* External_EmailFromAddress - spécifiez l’adresse e-mail de l’expéditeur
* External_EmailToAddress - liste d’adresses e-mail séparées par une virgule (user@hotmail.com, user@outlook.com) pour recevoir des e-mails de notification
* External_IsSendEmail - si elle est définie sur **Oui**, vous allez recevoir des e-mails, pour désactiver les notifications par e-mails, définissez la valeur sur **Non**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modification des planifications de démarrage et d’arrêt

La gestion de la planification du démarrage et de l’arrêt dans cette solution suit la procédure indiquée dans [Planification d’un Runbook dans Azure Automation](automation-schedules.md).     

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

Automation crée deux types d’enregistrements dans le référentiel OMS.

### <a name="job-logs"></a>Journaux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données.  Pour Automation, la valeur est JobLogs.|
CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur sera Job.|
resourceId | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer.  Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | L’état du travail du runbook.  Les valeurs possibles sont les suivantes :<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Succeeded|
resultDescription | Décrit l’état résultant du travail du runbook.  Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed|
RunbookName | Spécifie le nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises.  Pour Automation, la valeur sera OpsManager.|
StreamType | Spécifie le type d’événement. Les valeurs possibles sont les suivantes :<br>- Verbose<br>Sortie<br>Error<br>Avertissement|
SubscriptionId | Spécifie l’ID d’abonnement de la tâche.
Time | Date et heure d’exécution du travail du runbook.|


### <a name="job-streams"></a>Flux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données.  Pour Automation, la valeur est JobStreams.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur sera Job.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
resourceId | Spécifie l’ID de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer.  Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | Résultat de la tâche du runbook au moment où l’événement a été généré.  Les valeurs possibles sont les suivantes :<br>- InProgress|
resultDescription | Inclut le flux de sortie du runbook.|
RunbookName | Nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises.  Pour Automation, la valeur sera OpsManager.|
StreamType | Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose|
Time | Date et heure d’exécution du travail du runbook.|

Lorsque vous effectuez une recherche de journal qui retourne des enregistrements de la catégorie **JobLogs** ou **JobStreams**, vous pouvez sélectionner la vue **JobLogs** ou **JobStreams** pour afficher un ensemble de vignettes résumant les informations renvoyées par la recherche.

## <a name="sample-log-searches"></a>Exemples de recherches de journaux

Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de tâches collectés par cette solution. 

Requête | Description|
----------|----------|
Rechercher les tâches du runbook ScheduledStartStop_Parent exécutées avec succès | Catégorie de recherche == "JobLogs" &#124; où ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; où ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; trié par TimeGenerated desc|
Rechercher les tâches du runbook SequencedStartStop_Parent exécutées avec succès | Catégorie de recherche == "JobLogs" &#124; où ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; où ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; trié par TimeGenerated desc

## <a name="removing-the-solution"></a>Suppression de la solution

Si vous estimez que vous n’avez plus besoin d’utiliser la solution, vous pouvez la supprimer à partir du compte Automation.  La suppression de la solution supprime uniquement les Runbooks, pas les planifications ou variables créées lors de l’ajout de la solution.  Vous devrez supprimer manuellement ces ressources si vous ne les utilisez pas avec d’autres Runbooks.  

Pour supprimer la solution, procédez comme suit :

1.  À partir de votre compte Automation, sélectionnez **Espace de travail** dans le volet gauche.  
2.  Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM [Espace de travail]**.  Sur la page **VMManagementSolution [Espace de travail]**, cliquez sur **Supprimer** à partir du menu.<br><br> ![Supprimer la solution de gestion de machine virtuelle](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Dans la fenêtre **Supprimer la solution**, confirmez que vous souhaitez supprimer la solution.
4.  Pendant que les informations sont vérifiées et la solution supprimée, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous serez redirigé vers la page **Solution** après le démarrage du processus de suppression de la solution.  

Le compte Automation et l’espace de travail Log Analytics ne sont pas supprimés au cours de ce processus.  Si vous ne souhaitez pas conserver l’espace de travail Log Analytics, vous devrez le supprimer manuellement.  Cette opération peut se faire également à partir du portail Azure.   Dans l’écran d’accueil du Portail Azure, sélectionnez **Log Analytics** puis, dans le panneau **Log Analytics**, sélectionnez l’espace de travail et cliquez sur **Supprimer** dans le menu du panneau des paramètres de l’espace de travail.  
      
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux de travaux Automation avec Log Analytics, consultez [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md)
- Pour en savoir plus sur Log Analytics et sur les sources de collecte de données, consultez [Vue d’ensemble de la collecte des données de stockage Azure dans Log Analytics](../log-analytics/log-analytics-azure-storage.md)






   

