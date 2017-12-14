---
title: "Estimer la capacité de réplication dans Azure | Microsoft Docs"
description: "Utiliser cet article pour estimer la capacité en cas de réplication avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: f504888aac9e8d97e974fb5bec0a12a8ede39c76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
Une nouvelle version améliorée du [Planificateur de déploiement Azure Site Recovery de Hyper-V vers Azure](site-recovery-hyper-v-deployment-planner.md) est désormais disponible. Celle-ci remplace l’ancien outil. Utilisez le nouvel outil pour la planification de votre déploiement. L’outil vous guide sur les points suivants :
* Évaluation de l’éligibilité de la machine virtuelle en fonction du nombre de disques, de la taille du disque, des E/S par seconde, de l’activité et de quelques caractéristiques de machine virtuelle.
* Besoin de bande passante réseau et évaluation de RPO.
* Exigences de l’infrastructure Azure.
* Exigences de l’infrastructure locale.
* Conseils de traitement par lot de réplication initiale.
* Estimation du coût total de récupération d’urgence vers Azure.

# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planifier la capacité de la protection des machines virtuelles Hyper-V avec Site Recovery

L’outil Azure Site Recovery Capacity Planner vous aide à prévoir vos besoins en capacité pour la réplication de machines virtuelles Hyper-V avec Azure Site Recovery.

Utilisez l’outil Site Recovery Capacity Planner pour analyser votre environnement source et vos charges de travail, ainsi que pour déterminer vos besoins en bande passante et en ressources serveur à l’emplacement source, ainsi que les ressources (machines virtuelles et stockage, etc.) dont vous avez besoin à l’emplacement cible.

Vous pouvez exécuter l’outil de deux manières :

* **Planification rapide**: exécutez l’outil dans ce mode pour obtenir des projections réseau et serveur sur la base de la quantité moyenne de machines virtuelles, de disques et de stockage, et sur le taux de changement moyen.
* **Planification détaillée** : exécutez l’outil dans ce mode et fournissez les détails de chaque charge de travail au niveau de la machine virtuelle. Analysez la compatibilité de machine virtuelle et obtenez des projections réseau et serveur.

## <a name="before-you-start"></a>Avant de commencer


1. Collecter des informations relatives à votre environnement, et notamment les machines virtuelles, le nombre de disques par machine virtuelle, le stockage par disque.
2. Déterminer le taux de modification (l’évolution) quotidienne des données répliquées. Pour cela, téléchargez [l’outil de planification de la capacité Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour obtenir le taux de modifications. [En savoir plus](site-recovery-capacity-planning-for-hyper-v-replication.md) sur cet outil. Nous vous recommandons d’exécuter cet outil sur une semaine pour enregistrer les moyennes.
   

## <a name="run-the-quick-planner"></a>Exécutez Quick Planner
1. Téléchargez et ouvrez l’outil [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) . Vous devez exécuter des macros, et donc sélectionner cette option pour activer la modification et le contenu lorsque vous y êtes invité.
2. Dans **Sélectionner un type de planificateur**, sélectionnez **Quick Planner** dans la zone de liste.

   ![Prise en main](./media/site-recovery-capacity-planner/getting-started.png)
3. Dans la feuille de calcul **Capacity Planner**, saisissez les informations requises. Vous devez renseigner tous les champs cerclés de rouge de la capture d’écran ci-dessous.

   * Dans **Sélectionner votre scénario**, choisissez **Hyper-V to Azure** (Hyper-V vers Azure) ou **VMware/Physical to Azure** (VMware/Physique vers Azure).
   * Dans **Taux de modification de données moyen par jour (%)**, entrez les informations que vous recueillez à l’aide de l’[outil de planification de la capacité Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou d’[Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).  
   * Le paramètre **Compression** n’est pas utilisé lors de la réplication de machines virtuelles Hyper-V vers Azure. Pour la compression, utilisez une appliance tierce telle que Riverbed.
   * Dans **Retention Inputs** (Entrées de rétention), spécifiez la durée de conservation des réplicas, en heures.
   * Dans **Number of hours in which initial replication for the batch of virtual machines should complete** (Nombre d’heures prévu pour la réplication initiale du lot de machines virtuelles) et **Number of virtual machines per initial replication batch** (Nombre de machines virtuelles par lot de réplication initiale), vous devez saisir les paramètres de saisie utilisés pour calculer les exigences de réplication initiales.  Lorsque vous déployez Site Recovery, vous devez charger l’intégralité du jeu de données initial.

   ![Entrées](./media/site-recovery-capacity-planner/inputs.png)
4. Une fois que vous avez placé les valeurs de l’environnement source, la sortie affichée inclut :

   * **Bande passante requise pour la réplication delta** (Mo/s). La bande passante réseau pour la réplication delta est calculée sur le taux de modification de données moyen par jour.
   * **Bande passante requise pour la réplication initiale** (Mo/s). La bande passante réseau pour la réplication initiale est calculée sur les valeurs de réplication initiales que vous entrez.
   * **Stockage requis (en Go)** correspond au stockage Azure total requis.
   * **Nombre d’E/S par seconde sur les comptes de stockage standard** est calculé sur la base d’une taille d’unité d’E/S par seconde de 8K sur l’ensemble des comptes de stockage standard.  Pour Quick Planner, le nombre est calculé en fonction de l’ensemble des disques de machines virtuelles source et du taux de changement de données par jour. Pour Detailed Planner, le nombre est calculé en fonction du nombre total de machines virtuelles mappées sur des machines virtuelles Azure standard et du taux de modification de données sur ces dernières.
   * **Nombre de comptes de stockage standard** fournit le nombre total de comptes de stockage standard nécessaire pour protéger les machines virtuelles. Un compte de stockage standard peut contenir jusqu’à 20 000 E/S par secondes sur toutes les machines virtuelles appartenant à un stockage standard et qu’un maximum de 500 E/S par seconde est pris en charge pour chaque disque.
   * **Nombre de disques blob requis** donne le nombre de disques qui seront créés sur le stockage Azure.
   * **Nombre de comptes de stockage premium requis** fournit le nombre total de comptes de stockage premium nécessaire pour protéger les machines virtuelles. Une machine virtuelle source avec une valeur d’E/S par seconde élevée (supérieure à 20 000) a besoin d’un compte de stockage Premium. Un compte de stockage premium peut contenir jusqu’à 80 000 E/S par seconde.
   * **Nombre d’E/S par seconde sur les comptes de stockage premium** est calculé sur la base d’une taille d’unité d’E/S par seconde de 256K sur l’ensemble des comptes de stockage premium.  Pour Quick Planner, le nombre est calculé en fonction de l’ensemble des disques de machines virtuelles source et du taux de changement de données par jour. Concernant le Detailed Planner, le nombre est calculé en fonction du nombre total de machines virtuelles mappées sur des machines virtuelles Azure premium (séries DS et GS) et du taux de modification des données sur ces dernières.
   * Le **nombre de serveurs de configuration requis** indique le nombre de serveurs de configuration requis pour le déploiement.
   * Le **nombre de serveurs de traitement supplémentaires requis** indique si des serveurs de traitement supplémentaires sont nécessaires en plus du serveur de traitement qui est exécuté sur le serveur de configuration par défaut.
   * **100 % de stockage supplémentaire sur la source** indique si un stockage supplémentaire est nécessaire dans l’emplacement source.

   ![Sortie](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Exécuter Detailed Planner

1. Téléchargez et ouvrez l’outil [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) . Vous devez exécuter des macros, et donc sélectionner cette option pour activer la modification et le contenu lorsque vous y êtes invité.
2. Dans **Sélectionner un type de planificateur**, sélectionnez **Detailed Planner** dans la liste.

   ![Mise en route](./media/site-recovery-capacity-planner/getting-started-2.png)
3. Dans la feuille de calcul **Workload Qualification**, saisissez les informations obligatoires. Vous devez renseigner tous les champs marqués.

   * Dans **Noyaux d’un processeur**, spécifiez le nombre total de cœurs sur un serveur source.
   * Dans **Allocation de mémoire en Mo**, spécifiez la taille de la mémoire RAM d’un serveur source.
   * Le **nombre de cartes réseau** spécifie le nombre de cartes réseau sur un serveur source.
   * Pour le **stockage total (en Go)**, spécifiez la taille totale du stockage de machine virtuelle. Par exemple, si le serveur source comprend 3 disques de 500 Go chacun, la taille de stockage totale est de 1 500 Go.
   * Pour le **nombre de disques rattaché**, spécifiez le nombre total de disques d’un serveur source.
   * Pour le **taux d’utilisation de capacité de disque**, spécifiez son utilisation moyenne.
   * Pour le **taux de modification par jour (%)**, spécifiez le taux de modification de données quotidien d’un serveur source.
   * Pour la **taille du mappage Azure**, entrez la taille de machine virtuelle Azure que vous souhaitez mapper. Si vous ne souhaitez pas effectuer cette opération manuellement, cliquez sur **Compute IaaS VMs** (Calcul des machines virtuelles IaaS). Si vous entrez un paramètre manuel puis cliquez sur Calcul des machines virtuelles IaaS, votre paramètre manuel peut être remplacé, car le processus de calcul identifie automatiquement la meilleure correspondance sur la taille de la machine virtuelle Azure.

   ![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Si vous cliquez sur **Calcul des machines virtuelles IaaS** , les opérations suivantes seront effectuées :

   * Validation des entrées obligatoires.
   * Calcul du nombre d’E/S par seconde et indication de la taille de machine virtuelle Azure la plus appropriée pour chaque machine virtuelle éligible à la réplication vers Azure. Si la détection d’une machine virtuelle de taille inappropriée ne peut être détectée, une erreur est émise. Par exemple, si le nombre de disques rattachés est 65, une erreur est émise, car la taille de machine virtuelle Azure la plus élevée est 64.
   * Suggère un compte de stockage pouvant être utilisé pour une machine virtuelle Azure.
   * Calcule le nombre total de comptes de stockage standard et premium nécessaires pour la charge de travail. Faites défiler vers le bas pour afficher le type de stockage Azure et le compte de stockage qui peuvent être utilisés pour un serveur source.
   * Se termine et trie le reste de la table en fonction du type de stockage (standard ou premium) affecté à une machine virtuelle et du nombre de disques attachés. Pour toutes les machines virtuelles qui répondent aux exigences de sauvegarde d’Azure, la colonne indiquant si **la machine virtuelle est qualifiée** affiche **Oui**. Si une machine virtuelle ne peut pas être sauvegardée sur Azure, une erreur s’affiche.

Les colonnes AA à AE générées fournissent des informations pour chaque machine virtuelle.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemple
Exemple : pour les six machines virtuelles avec les valeurs indiquées dans le tableau, l’outil calcule et affecte la meilleure correspondance de machine virtuelle Azure, ainsi que les exigences de stockage Azure.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Dans l’exemple de résultat, notez les points suivants :

  * La première colonne est une colonne de validation pour les machines virtuelles, les disques et les taux de changement.
  * Pour cinq machines virtuelles, deux comptes de stockage standard et un compte de stockage premium sont nécessaires.
  * VM 3 ne se qualifie pas pour la protection, car un ou plusieurs disques ont un volume supérieur à 1 To.
  * VM1 et VM2 peuvent utiliser le premier compte de stockage standard
  * VM4 peut utiliser le deuxième compte de stockage standard
  * VM5 et VM6 ont besoin d’un compte de stockage Premium et peuvent utiliser un compte simple.

    > [!NOTE]
    > Le nombre d’E/S par seconde sur les comptes de stockage standard et Premium est calculé au niveau de la machine virtuelle et non à celui du disque. Une machine virtuelle standard peut gérer jusqu’à 500 E/S par seconde par disque. Si le nombre d’E/S par seconde pour un disque est supérieur à 500, le stockage Premium est nécessaire. Toutefois, si le nombre d’E/S par seconde pour un disque est supérieur à 500, mais que le nombre d’E/S par seconde pour l’ensemble des disques de machine virtuelle ne dépasse pas les limites établies pour les machines virtuelles Azure standard (taille de machine virtuelle, nombre de disques, nombre d’adaptateurs, processeur, mémoire), le planificateur choisit une machine virtuelle standard et non une machine virtuelle des séries DS ou GS. Vous devez mettre à jour manuellement la cellule Mapping Azure size avec la machine virtuelle de série DS ou GS appropriée.


Une fois tous les détails fournis, cliquez sur **Submit data to the planner tool** (Envoyer des données à l’outil Planificateur) pour ouvrir **Capacity Planner**. Les charges de travail sont mises en surbrillance, afin d’indiquer si elles sont éligibles ou non à la protection.

### <a name="submit-data-in-the-capacity-planner"></a>Envoyer des données dans Capacity Planner.
1. Lorsque vous ouvrez la feuille de calcul **Capacity Planner** , elle est remplie en fonction des paramètres que vous avez spécifiés. Le mot « Workload » apparaît dans la cellule **Infra inputs source** (Source des entrées Infra) pour indiquer les entrées de la feuille de calcul **Workload Qualification** (Qualification de la charge de travail).
2. Si vous souhaitez apporter des modifications, vous devez modifier la feuille de calcul **Workload Qualification** (Qualification de la charge de travail) et cliquer de nouveau sur **Submit data to the planner tool** (Envoyer les données à l’outil de planification).  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Étapes suivantes

[Apprenez à exécuter](site-recovery-capacity-planning-for-hyper-v-replication.md) l’outil de planification de la capacité.
