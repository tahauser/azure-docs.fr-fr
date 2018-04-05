---
title: Solution Start/Stop VMs during off-hours (préversion)
description: Cette solution de gestion de machines virtuelles assure le démarrage et l’arrêt de vos machines virtuelles Azure Resource Manager selon une planification, et permet une surveillance proactive à partir de Log Analytics.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: da2d95bc100a6160282c93682ad76f7ee881e105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Solution Start/Stop VMs during off-hours (préversion) dans Azure Automation

La solution Start/Stop VMs during off-hours démarre et arrête vos machines virtuelles Azure selon une planification définie par l’utilisateur, fournit des informations via Azure Log Analytics et peut envoyer des e-mails en utilisant [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Elle prend en charge Azure Resource Manager et les machines virtuelles classiques pour la plupart des scénarios.

Cette solution propose une option décentralisée d’Automation pour les utilisateurs souhaitant réduire les coûts en utilisant des ressources serverless et à faible coût. Cette option permet d’effectuer les tâches suivantes :

* Planification du démarrage et de l’arrêt de machines virtuelles
* Planification du démarrage et de l’arrêt de machines virtuelles dans un ordre croissant à l’aide de balises Azure (non prises en charge pour les machines virtuelles classiques)
* Arrêt automatique de machines virtuelles en fonction de la faible utilisation de l’UC

## <a name="prerequisites"></a>Prérequis


* Les runbooks fonctionnent avec un [compte d’identification Azure](automation-offering-get-started.md#authentication-methods). Le compte d’identification est la méthode d’authentification recommandée, car elle utilise l’authentification par certificat au lieu d’un mot de passe, susceptible d’expirer ou de changer fréquemment.
* Cette solution permet uniquement de gérer les machines virtuelles qui se trouvent dans le même abonnement que votre compte Azure Automation.
* Cette solution peut seulement être déployée dans les régions Azure suivantes : Sud-Est de l’Australie, Centre du Canada, Centre de l’Inde, Est des États-Unis, Est du Japon, Sud-Est Asiatique, Royaume-Uni Sud et Europe de l’Ouest.

  > [!NOTE]
  > Les runbooks gérant la planification des machines virtuelles peuvent cibler les machines virtuelles de n’importe quelle région.

* Pour envoyer des notifications par e-mail lorsque les runbooks de démarrage et d’arrêt de machine virtuelle se terminent, durant l’intégration à partir de Place de marché Azure, sélectionnez **Oui** pour déployer SendGrid.

  > [!IMPORTANT]
  > SendGrid est un service tiers. Pour toute assistance, contactez [SendGrid](https://sendgrid.com/contact/).

  Les limites de SendGrid sont les suivantes :

  * Un seul compte SendGrid maximum par utilisateur et par abonnement
  * Deux comptes SendGrid maximum par abonnement

## <a name="deploy-the-solution"></a>Déployer la solution

Procédez comme suit pour ajouter la solution Start/Stop VMs during off-hours (préversion) à votre compte Automation, puis configurer les variables pour personnaliser la solution.

1. Dans le portail Azure, cliquez sur **Créer une ressource**.
1. Dans la page Place de marché, saisissez un mot clé, tel que **Démarrer** ou **Arrêter/Démarrer**. Au fur et à mesure de la saisie, la liste est filtrée. Vous pouvez également saisir un ou plusieurs des mots clés à partir du nom complet de la solution, puis appuyer sur la touche Entrée. Sélectionnez **Démarrer/arrêter des machines virtuelles pendant les heures creuses [version préliminaire]** à partir des résultats de recherche.
1. Sur la page **Start/Stop VMs during off-hours [version préliminaire]** de la solution sélectionnée, vérifiez les informations du résumé, puis cliquez sur **Créer**.

   ![Portail Azure](media/automation-solution-vm-management/azure-portal-01.png)

1. La page **Ajouter une solution** s’affiche. Vous êtes invité à configurer la solution pour pouvoir l’importer dans votre abonnement Automation.
   ![Page Ajouter une solution de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/azure-portal-add-solution-01.png)
1. Sur la page **Ajouter une solution**, sélectionnez **Espace de travail**. Sélectionnez un espace de travail OMS lié au même abonnement Azure que celui dans lequel le compte Automation se trouve. Si vous ne disposez pas d’espace de travail, sélectionnez **Créer un espace de travail**. Sur la page **Espace de travail OMS**, procédez comme suit :
   * Spécifiez un nom pour le nouvel **espace de travail OMS**.
   * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   * Sous **Groupe de ressources**, vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà.
   * Sélectionnez un **emplacement**. Actuellement, les seuls emplacements disponibles sont **Sud-Est de l’Australie**, **Centre du Canada**, **Centre de l’Inde**, **Est des États-Unis**, **Est du Japon**, **Sud-Est Asiatique**, **Royaume-Uni Sud** et **Europe de l’Ouest**.
   * Sélectionner un **niveau de tarification**. Deux niveaux sont proposés pour la solution : **Gratuit** et **Par nœud (OMS)**. La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau Gratuit. Le niveau Par nœud permet de collecter une quantité illimitée de données quotidiennement.

        > [!NOTE]
        > Bien que le niveau payant par Go (autonome) s’affiche comme une option, il n’est pas applicable. Si vous la sélectionnez et poursuivez la création de cette solution dans votre abonnement, elle échouera. Ce problème sera résolu lors de la sortie officielle de cette solution. Cette solution utilise uniquement les minutes du travail d’automatisation et l’ingestion du journal. Elle n’ajoute pas de nœuds OMS à votre environnement.

1. Après avoir entré les informations requises sur la page **Espace de travail OMS**, cliquez sur **Créer**. Vous pouvez suivre sa progression sous **Notifications** dans le menu, qui vous renvoie à la page **Ajouter une solution** une fois terminé.
1. Sur la page **Ajouter une solution**, sélectionnez **Compte Automation**. Si vous créez un espace de travail OMS, vous devez également créer un compte Automation à lui associer. Sélectionnez **Créer un compte Automation**, puis sur la page **Ajouter un compte Automation**, fournissez les informations suivantes :
   * Dans le champ **Nom**, saisissez le nom du compte Automation.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné et ne peuvent pas être modifiées. Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus dans cette solution. Après avoir cliqué sur **OK**, les options de configuration sont validées et le compte Automation est créé. Vous pouvez suivre la progression sous **Notifications** dans le menu.

1. Enfin, sur la page **Ajouter une solution**, sélectionnez **Configuration**. La page **Paramètres** s’affiche.

   ![Page Paramètres pour la solution](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Ce volet vous permet de :
   * Spécifier les **noms des groupes de ressources cibles**. Il s’agit des noms des groupes de ressources qui contiennent des machines virtuelles devant être gérées par cette solution. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge. Cette valeur est stockée dans les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**.
   * Spécifier la **liste d’exclusion de machines virtuelles (chaîne)**. Il s’agit du nom d’une ou de plusieurs machines virtuelles appartenant au groupe de ressources cible. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Les caractères génériques sont pris en charge. Cette valeur est stockée dans la variable **External_ExcludeVMNames**.
   * Sélectionner une **planification**. Il s’agit d’une date et d’une heure récurrentes pour le démarrage et l’arrêt des machines virtuelles des groupes de ressources cibles. Par défaut, la planification est configurée pour le fuseau horaire UTC. La sélection d’une autre région n’est pas possible. Pour configurer la planification sur votre propre fuseau horaire après la configuration de la solution, consultez [Modification de la planification de démarrage et d’arrêt](#modify-the-startup-and-shutdown-schedule).
   * Pour recevoir des **notifications par e-mail** de SendGrid, acceptez la valeur par défaut **Oui** et fournissez une adresse e-mail valide. Si vous sélectionnez **Non**, mais souhaitez par la suite recevoir des notifications par e-mail, vous pouvez mettre à jour la variable **External_EmailToAddress** en indiquant des adresses e-mail valides séparées par une virgule, puis définir la variable **External_IsSendEmail** sur la valeur **Oui**.

1. Après avoir configuré les paramètres initiaux requis pour la solution, cliquez sur **OK** pour fermer la page **Paramètres** et sélectionnez **Créer**. Quand tous les paramètres sont validés, la solution est déployée dans votre abonnement. Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

## <a name="scenarios"></a>Scénarios

La solution inclut trois scénarios distincts. Ces scénarios sont les suivants :

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scénario 1 : Démarrer/arrêter les machines virtuelles selon une planification

Il s’agit de la configuration par défaut lorsque vous déployez la solution pour la première fois. Par exemple, vous pouvez le configurer pour arrêter toutes les machines virtuelles d’un abonnement quand vous quittez le travail le soir, et pour les démarrer le matin quand vous arrivez au bureau. Quand vous configurez les planifications **Scheduled-StartVM** et **Scheduled-StopVM** pendant le déploiement, celles-ci démarrent et arrêtent les machines virtuelles cibles. Il est possible de configurer cette solution simplement pour arrêter les machines virtuelles, consultez la section [Modifier les planifications de démarrage et d’arrêt](#modify-the-startup-and-shutdown-schedules) pour savoir comment configurer une planification personnalisée.

>[!NOTE]
>Le fuseau horaire est votre fuseau horaire actuel quand vous configurez le paramètre du temps de planification. Toutefois, il est conservé au format UTC dans Azure Automation. Il est inutile de convertir un fuseau horaire car cela est géré pendant le déploiement.

Vous choisissez l’étendue des machines virtuelles cibles en configurant les variables suivantes : **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames**.

Vous pouvez activer cette solution pour prendre un charge un abonnement et un groupe de ressources, ou bien une liste spécifique de machines virtuelles, mais pas les deux.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Configurer le démarrage et l’arrêt pour un abonnement et un groupe de ressources

1. Configurez les variables **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** pour spécifier les machines virtuelles cibles.
2. Activez et mettez à jour les planifications **Scheduled-StartVM** et **Scheduled-StopVM**.
3. Exécutez le runbook **ScheduledStartStop_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Exécutez le runbook **ScheduledStartStop_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
2. Configurez le paramètre **External_ExcludeVMNames** avec une liste de machines virtuelles séparées par une virgule (VM1, VM2, VM3).
3. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).

>[!NOTE]
> La valeur de **Target ResourceGroup Names** est stockée comme étant également la valeur de **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames**. Pour plus de granularité, vous pouvez modifier chacune de ces variables pour cibler des groupes de ressources différents. Utilisez **External_Start_ResourceGroupNames** pour une action de démarrage, et **External_Stop_ResourceGroupNames** pour une action d’arrêt. Les machines virtuelles sont automatiquement ajoutées aux planifications de démarrage et d’arrêt.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scénario 2 : Démarrer/arrêter les machines virtuelles dans un certain ordre en utilisant des balises

Dans un environnement comprenant deux composants ou plus sur plusieurs machines virtuelles prenant en charge une charge de travail distribuée, il est important de prendre en charge l’ordre de démarrage et d’arrêt des composants. Pour ce faire, vous pouvez procéder comme suit :

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Configurer le démarrage et l’arrêt pour un abonnement et un groupe de ressources

1. Ajoutez une balise **SequenceStart** et une balise **SequenceStop** avec une valeur entière positive aux machines virtuelles cibles dans les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames**. Les actions de démarrage et d’arrêt sont effectuées dans l’ordre croissant. Pour en savoir plus sur le balisage d’une machine virtuelle, consultez [Baliser une machine virtuelle Windows dans Azure](../virtual-machines/windows/tag.md) et [Baliser une machine virtuelle Linux dans Azure](../virtual-machines/linux/tag.md).
2. Modifiez les planifications **Sequenced-StartVM** et **StopVM-Sequenced** avec la date et l’heure répondant à vos besoins et activez la planification.
3. Exécutez le runbook **SequencedStartStop_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.
4. Affichez un aperçu de l’action et apportez les modifications nécessaires avant l’implémentation des machines virtuelles de production. Une fois prêt, vous pouvez exécuter manuellement le runbook avec le paramètre défini sur **False** ou laisser les planifications de Automation **Sequenced-StartVM** et **Sequenced-StopVM** s’exécuter automatiquement d’après votre planification établie.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Ajoutez des balises **SequenceStart** et **SequenceStop** balise contenant une valeur entière positive aux machines virtuelles que vous prévoyez d’ajouter à la variable **VMList**. 
2. Exécutez le runbook **SequencedStartStop_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
3. Configurez le paramètre **External_ExcludeVMNames** avec une liste de machines virtuelles séparées par une virgule (VM1, VM2, VM3).
4. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).
5. Affichez un aperçu de l’action et apportez les modifications nécessaires avant l’implémentation des machines virtuelles de production. Une fois prêt, vous pouvez exécuter manuellement le runbook avec le paramètre défini sur **False** ou laisser les planifications de Automation **Sequenced-StartVM** et **Sequenced-StopVM** s’exécuter automatiquement d’après votre planification établie.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scénario 3 : Démarrer/arrêter automatiquement les machines virtuelles en fonction de l’utilisation de l’UC

Cette solution peut aider à gérer le coût d’exécution des machines virtuelles de votre abonnement en évaluant les machines virtuelles Azure non utilisées pendant les heures creuses, par exemple après les heures de travail, et en les arrêtant automatiquement si l’utilisation de l’UC est inférieure à X %.

Par défaut, la solution est préconfigurée de manière à évaluer les mesures du pourcentage d’utilisation de l’UC pour vérifier si l’utilisation moyenne est de 5 % ou moins. Cela est contrôlé par les variables suivantes et peut être modifié si leurs valeurs par défaut ne répondent pas à vos besoins :

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Vous pouvez activer cette solution pour prendre un charge un abonnement et un groupe de ressources, ou bien une liste spécifique de machines virtuelles, mais pas les deux.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Configurer l’arrêt pour un abonnement et un groupe de ressources

1. Configurez les variables **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** pour spécifier les machines virtuelles cibles.
2. Activez et mettez à jour la planification **Schedule_AutoStop_CreateAlert_Parent**.
3. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
2. Configurez le paramètre **External_ExcludeVMNames** avec une liste de machines virtuelles séparées par une virgule (VM1, VM2, VM3).
3. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).

Maintenant que vous avez une planification pour l’arrêt des machines virtuelles en fonction de l’utilisation de l’UC, vous devez activer l’une des planifications suivantes pour les démarrer.

* Configurez le démarrage pour un abonnement et un groupe de ressources. Consultez les étapes du [Scénario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) pour tester et activer les planifications **Scheduled-StartVM**.
* Configurez le démarrage pour un abonnement, un groupe de ressources et des balises. Consultez les étapes du [Scénario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) pour tester et activer les planifications **Sequenced-StartVM**.

## <a name="solution-components"></a>Composants de la solution

Cette solution inclut des runbooks et des planifications préconfigurés et une intégration avec Log Analytics qui vous permet de personnaliser le démarrage et l’arrêt de vos machines virtuelles selon les besoins de votre entreprise.

### <a name="runbooks"></a>Runbooks

Le tableau suivant répertorie les runbooks déployés sur votre compte Automation par cette solution. Vous ne devez pas modifier le code de runbook. À la place, écrivez votre propre runbook pour une nouvelle fonctionnalité.

> [!IMPORTANT]
> N’exécutez pas directement de runbook dont le nom contient le suffixe « child » (enfant).

Tous les runbooks parents incluent le paramètre *WhatIf*. Une fois configuré sur **True**, *WhatIf* prend en charge la description du comportement exact du runbook lors de l’exécution sans le paramètre *WhatIf* et valide les machines virtuelles correctes ciblées. Un runbook effectue uniquement ses actions définies quand le paramètre *WhatIf* est défini sur **False**.

|**Runbook** | **Paramètres** | **Description**|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Appelé par le runbook parent. Ce runbook crée des alertes en fonction des ressources pour le scénario d’AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf : True ou False  | Crée ou met à jour des règles d’alerte Azure sur des machines virtuelles dans l’abonnement ou les groupes de ressource ciblées. <br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, *vm1,vm2,vm3*.<br> *WhatIf* valide la logique du runbook sans l’exécuter.|
|AutoStop_Disable | Aucun | Désactive les alertes et la planification par défaut d’AutoStop.|
|AutoStop_StopVM_Child | WebHookData | Appelé par le runbook parent. Les règles d’alerte appellent ce runbook pour arrêter la machine virtuelle.|
|Bootstrap_Main | Aucun | Utilisé une seule fois pour établir les configurations Bootstrap, telles que webhookURI, qui ne sont généralement pas accessibles à partir d’Azure Resource Manager. Ce runbook est automatiquement supprimé après un déploiement réussi.|
|ScheduledStartStop_Child | VMName <br> Action : Start ou Stop <br> ResourceGroupName | Appelé par le runbook parent. Exécute une action de démarrage ou d’arrêt pour l’arrêt planifié.|
|ScheduledStartStop_Parent | Action : Start ou Stop <br>VMList <br> WhatIf : True ou False | Cela concerne toutes les machines virtuelles de l’abonnement. Modifiez les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames** pour exécuter le runbook uniquement sur les groupes de ressources ciblés. Vous pouvez également exclure des machines virtuelles spécifiques en mettant à jour la variable **External_ExcludeVMNames**.<br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, *vm1,vm2,vm3*.<br> *WhatIf* valide la logique du runbook sans l’exécuter.|
|SequencedStartStop_Parent | Action : Start ou Stop <br> WhatIf : True ou False<br>VMList| Créez des balises nommées **SequenceStart** et **SequenceStop** sur chaque machine virtuelle pour laquelle vous souhaitez séquencer l’activité de démarrage et d’arrêt. La valeur de la balise doit être un entier positif (1, 2, 3) correspondant à l’ordre du démarrage ou d’arrêt. <br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, *vm1,vm2,vm3*. <br> *WhatIf* valide la logique du runbook sans l’exécuter. <br> **Remarque** : les machines virtuelles doivent se trouver dans des groupes de ressources définis External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames et External_ExcludeVMNames dans des variables d’Azure Automation. Elles doivent disposer des étiquettes appropriées pour que les actions puissent prendre effet.|

### <a name="variables"></a>variables

Le tableau suivant répertorie les variables créées dans votre compte Automation. Vous devez uniquement modifier les variables avec le préfixe **External**. La modification de variables avec le préfixe **Internal** risque d’avoir un impact indésirable.

|**Variable** | **Description**|
---------|------------|
|External_AutoStop_Condition | Opérateur conditionnel requis pour configurer la condition avant le déclenchement d’une alerte. Les valeurs acceptables sont **GreaterThan** (supérieur à), **GreaterThanOrEqual** (supérieur ou égal à), **LessThan** (Inférieur à) et **LessThanOrEqual** (Inférieur ou égal à).|
|External_AutoStop_Description | Alerte pour arrêter la machine virtuelle si le pourcentage d’utilisation de l’UC dépasse le seuil.|
|External_AutoStop_MetricName | Nom de la métrique de performances pour laquelle la règle d’alerte Azure doit être configurée.|
|External_AutoStop_Threshold | Seuil de la règle d’alerte Azure spécifiée dans la variable *External_AutoStop_MetricName*. Les valeurs de pourcentage vont de 1 à 100.|
|External_AutoStop_TimeAggregationOperator | Opérateur d’agrégation de temps appliqué à la taille de la fenêtre sélectionnée pour évaluer la condition. Les valeurs admises sont **Average** (moyen), **Minimum**, **Maximum**, **Total** et **Last** (dernier).|
|External_AutoStop_TimeWindow | Taille de la fenêtre durant laquelle Azure analyse la métrique sélectionnée pour déclencher une alerte. Ce paramètre accepte une entrée au format d’un intervalle de temps. Les valeurs possibles sont comprises entre 5 minutes et 6 heures.|
|External_EmailFromAddress | Spécifie l’expéditeur de l’e-mail.|
|External_EmailSubject | Spécifie le texte de la ligne d’objet du message électronique.|
|External_EmailToAddress | Spécifie le ou les destinataires du message électronique. Séparez les noms par une virgule.|
|External_ExcludeVMNames | Saisissez les noms des machines virtuelles à exclure, en séparant les noms par une virgule et sans espace.|
|External_IsSendEmail | Spécifie l’option d’envoi d’une notification par e-mail à la fin. Spécifiez **Oui** ou **Non** pour ne pas envoyer d’e-mail. Cette option doit être définie sur **Non** si vous n’avez pas créé de notifications par e-mail au cours du déploiement initial.|
|External_Start_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources (avec les valeurs séparées par une virgule) ciblés pour les actions de démarrage.|
|External_Stop_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources (avec les valeurs séparées par une virgule) ciblés pour les actions d’arrêt.|
|Internal_AutomationAccountName | Spécifie le nom du compte Automation.|
|Internal_AutoSnooze_WebhookUri | Spécifie l’URI du Webhook appelée pour le scénario AutoStop.|
|Internal_AzureSubscriptionId | Spécifie l’ID d’abonnement Azure.|
|Internal_ResourceGroupName | Spécifie le nom du groupe de ressources du compte Automation.|
|Internal_SendGridAccountName | Spécifie le nom du compte SendGrid.|
|Internal_SendGridPassword | Spécifie le mot de passe du compte SendGrid.|

Pour tous les scénarios, les variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** sont nécessaires pour le ciblage de machines virtuelles, à l’exception de la fourniture d’une liste de machines virtuelles séparées par des virgules pour les runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** et **ScheduledStartStop_Parent**. Autrement dit, vos machines virtuelles doivent résider dans des groupes de ressources cibles pour que les actions de démarrage et d’arrêt se produisent. La logique fonctionne de manière similaire à la stratégie d’Azure, dans la mesure où vous pouvez cibler l’abonnement ou un groupe de ressources et faire en sorte que les machines virtuelles nouvellement créées héritent des actions. Cette approche évite de devoir mettre à jour une planification distincte pour chaque machine virtuelle et de gérer le démarrage et l’arrêt à l’échelle.

### <a name="schedules"></a>Planifications

Le tableau suivant répertorie chacune des planifications par défaut créées dans votre compte Automation.  Vous pouvez les modifier ou créer vos propres planifications personnalisées. Par défaut, chacune de ces planifications est désactivée, à l’exception de **Scheduled_StartVM** et **Scheduled_StopVM**.

Vous ne devez pas activer toutes les planifications, car vous risqueriez de créer des actions de planification qui se chevauchent. Il est préférable de déterminer les optimisations que vous souhaitez réaliser et d’effectuer les modifications en conséquence. Consultez les exemples de scénarios dans la section Vue d’ensemble pour obtenir davantage d’explications.

|**Nom de la planification** | **Fréquence** | **Description**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Toutes les 8 heures | Exécute le runbook AutoStop_CreateAlert_Parent toutes les 8 heures, qui arrête les valeurs basées sur les machines virtuelles dans External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames et External_ExcludeVMNames dans les variables d’Azure Automation. Vous pouvez également spécifier une liste de machines virtuelles séparées par des virgules en utilisant le paramètre VMList.|
|Scheduled_StopVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre *Stop* tous les jours à l’heure spécifiée. Arrête automatiquement toutes les machines virtuelles répondant aux règles définies par les variables de ressource. Vous devez activer la planification associée, **Scheduled-StartVM**.|
|Scheduled_StartVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre *Start* tous les jours à l’heure spécifiée.  Démarre automatiquement toutes les machines virtuelles répondant aux règles définies par les variables appropriées. Vous devez activer la planification associée, **Scheduled-StopVM**.|
|Sequenced-StopVM | 01:00 (UTC), tous les vendredis | Exécute le runbook Sequenced_Parent avec un paramètre *Stop* tous les vendredis à l’heure spécifiée. Arrête séquentiellement (dans l’ordre croissant) toutes les machines virtuelles avec la balise **SequenceStop** définie par les variables appropriées. Reportez-vous à la section Runbooks pour plus d’informations sur les valeurs de balise et les variables des ressources. Vous devez activer la planification associée, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), tous les lundis | Exécute le runbook Sequenced_Parent avec un paramètre *Start* tous les lundis à un instant donné. Démarre séquentiellement (dans l’ordre décroissant) toutes les machines virtuelles avec la balise **SequenceStart** définie par les variables appropriées. Reportez-vous à la section Runbooks pour plus d’informations sur les valeurs de balise et les variables des ressources. Vous devez activer la planification associée, **Sequenced-StopVM**.|

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

Automation crée deux types d’enregistrements dans le référentiel OMS : les journaux de tâches et les flux de tâches.

### <a name="job-logs"></a>Journaux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données. Pour Automation, la valeur est JobLogs.|
CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job.|
ResourceId | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer. Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Succeeded|
resultDescription | Décrit l’état résultant du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed|
RunbookName | Spécifie le nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises. Pour Automation, la valeur est OpsManager.|
StreamType | Spécifie le type d’événement. Les valeurs possibles sont les suivantes :<br>- Verbose<br>Sortie<br>Error<br>Avertissement|
SubscriptionId | Spécifie l’ID d’abonnement de la tâche.
Temps | Date et heure d’exécution du travail du runbook.|

### <a name="job-streams"></a>Flux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données. Pour Automation, la valeur est JobStreams.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
ResourceId | Spécifie l’ID de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer. Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | Résultat de la tâche du runbook au moment où l’événement a été généré. Une valeur possible est :<br>- InProgress|
resultDescription | Inclut le flux de sortie du runbook.|
RunbookName | Nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises. Pour Automation, la valeur est OpsManager.|
StreamType | Type de flux de travail. Les valeurs possibles sont les suivantes :<br>- Progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose|
Temps | Date et heure d’exécution du travail du runbook.|

Quand vous effectuez une recherche de journal qui retourne des enregistrements de catégorie de **JobLogs** ou **JobStreams**, vous pouvez sélectionner la vue **JobLogs** ou **JobStreams** pour afficher un ensemble de vignettes résumant les informations retournées par la recherche.

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de tâches collectés par cette solution.

Requête | Description|
----------|----------|
Rechercher les tâches du runbook ScheduledStartStop_Parent terminées avec succès | Catégorie de recherche == "JobLogs" &#124; où ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; où ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; trié par TimeGenerated desc|
Rechercher les tâches du runbook SequencedStartStop_Parent terminées avec succès | Catégorie de recherche == "JobLogs" &#124; où ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; où ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; trié par TimeGenerated desc

## <a name="viewing-the-solution"></a>Affichage de la solution

Pour accéder à la solution, rendez-vous dans votre compte Automation et sous **RESSOURCES CONNEXES**, sélectionnez **Espace de travail**. Sur la page Log Analytics, sélectionnez **Solutions** dans la section **GÉNÉRAL**. Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM[espace de travail]** dans la liste.

La sélection de la solution affiche la page Solution de **Start-Stop-VM[espace de travail]**. Vous pouvez y consulter des informations importantes, telles que la vignette **StartStopVM**. Tout comme dans votre espace de travail Log Analytics, cette vignette affiche un compteur et une représentation graphique des tâches de runbooks démarrées et terminées avec succès.

![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

À ce stade, vous pouvez analyser plus en détail les enregistrements de tâche en cliquant sur la vignette en forme d’anneau. Le tableau de bord des solutions affiche l’historique des travaux et les requêtes de recherche dans les journaux prédéfinies. Passez au portail avancé Log Analytics pour effectuer des recherches en fonction de vos requêtes de recherche.

## <a name="configure-email-notifications"></a>Configurer les notifications par e-mail

Pour configurer les notifications par e-mail une fois la solution déployée, modifiez les trois variables suivantes :

* External_EmailFromAddress : spécifiez l’adresse e-mail de l’expéditeur.
* External_EmailToAddress : spécifiez une liste d’adresses e-mail séparées par une virgule (user@hotmail.com, user@outlook.com) pour recevoir des e-mails de notification.
* External_IsSendEmail : définissez la valeur sur **Oui** pour recevoir des e-mails. Pour désactiver les notifications par e-mail, définissez la valeur sur **Non**.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modifier les planifications de démarrage et d’arrêt

La gestion de la planification du démarrage et de l’arrêt dans cette solution suit la procédure indiquée dans [Planification d’un Runbook dans Azure Automation](automation-schedules.md).

Il est possible de configurer la solution pour simplement arrêter les machines virtuelles à une certaine heure. Pour cela, vous devez procéder comme suit :

1. Assurez-vous d’avoir ajouté les groupes de ressources des machines virtuelles à arrêter dans la variable **External_Start_ResourceGroupNames**.
2. Créez votre propre planification pour l’heure à laquelle vous souhaitez arrêter les machines virtuelles.
3. Accédez au runbook **ScheduledStartStop_Parent** et cliquez sur **Planification**. Cela vous permet de sélectionner la planification créée à l’étape précédente.
4. Sélectionnez **Paramètres et valeurs pour l’exécution** et définissez le paramètre ACTION sur « Stop ».
5. Cliquez sur **OK** pour enregistrer vos modifications.

## <a name="update-the-solution"></a>Mettre à jour la solution

Si vous avez déployé une version précédente de cette solution, vous devez tout d’abord la supprimer de votre compte avant de déployer une version mise à jour. Suivez les étapes de [suppression de la solution](#remove-the-solution), puis effectuez les étapes ci-dessus pour [déployer la solution](#deploy-the-solution).

## <a name="remove-the-solution"></a>Supprimer la solution

Si vous estimez que vous n’avez plus besoin d’utiliser la solution, vous pouvez la supprimer à partir du compte Automation. La suppression de la solution supprime uniquement les runbooks. Elle ne supprime pas les planifications ni les variables créées quand la solution a été ajoutée. Vous devez supprimer manuellement ces ressources si vous ne les utilisez pas avec d’autres runbooks.

Pour supprimer la solution, procédez comme suit :

1. À partir de votre compte Automation, sélectionnez **Espace de travail** sur la page de gauche.
1. Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM [Espace de travail]**. Sur la page **VMManagementSolution[Espace de travail]**, sélectionnez l’option **Supprimer** dans le menu.<br><br> ![Supprimer la solution de gestion de machine virtuelle](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Dans la fenêtre **Supprimer la solution**, confirmez que vous souhaitez supprimer la solution.
1. Pendant que les informations sont vérifiées et la solution supprimée, vous pouvez suivre la progression sous **Notifications** dans le menu. Vous serez redirigé vers la page **Solutions** après le démarrage du processus de suppression de la solution.

Le compte Automation et l’espace de travail Log Analytics ne sont pas supprimés au cours de ce processus. Si vous ne souhaitez pas conserver l’espace de travail Log Analytics, vous devez le supprimer manuellement. Cette opération peut se faire à partir du portail Azure :

1. Dans l’écran d’accueil du portail Azure, sélectionnez **Log Analytics**.
1. Sur la page **Log Analytics**, sélectionnez l’espace de travail.
1. Sur la page des paramètres de l’espace de travail, sélectionnez **Supprimer** dans le menu.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux de travaux Automation avec Log Analytics, consultez [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md).
* Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
* Pour en savoir plus sur Log Analytics et sur les sources de collecte de données, consultez [Vue d’ensemble de la collecte des données de stockage Azure dans Log Analytics](../log-analytics/log-analytics-azure-storage.md).
