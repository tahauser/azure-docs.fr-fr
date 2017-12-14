---
title: "Grouper des machines à l’aide des dépendances de machine avec Azure Migrate | Microsoft Docs"
description: "Explique comment créer une évaluation à l’aide des dépendances de machine avec le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 769c05916de4e7ad5b14812c2c8dbcf69e91320c
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grouper des machines à l’aide du mappage de dépendances de machine

Cet article explique comment créer un groupe de machines pour l’évaluation [Azure Migrate](migrate-overview.md) grâce au mappage de dépendances de machine. On utilise généralement cette méthode pour évaluer des groupes de machines virtuelles avec un niveau supérieur de confiance en vérifiant par recoupement les dépendances de machine avant d’exécuter une évaluation.



## <a name="prepare-machines-for-dependency-mapping"></a>Préparer des machines au mappage de dépendances
Pour inclure des machines au mappage de dépendances, vous devez télécharger et installer des agents sur chacune des machines locales à évaluer. Si, par ailleurs, certaines de vos machines n’ont pas de connexion Internet, il vous faudra télécharger et y installer la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles
1. Dans **Vue d’ensemble**, cliquez sur **Gérer** > **Machines** et sélectionnez la machine souhaitée.
2. Dans la colonne **Dépendances**, cliquez sur **Installer des agents**. 
3. Sur la page **Dépendances**, téléchargez et installez Microsoft Monitoring Agent (MMA) et l’agent de dépendances sur chacune des machines virtuelles à évaluer.
4. Copiez l’ID et la clé de l’espace de travail. Vous en aurez besoin lorsque vous installerez MMA sur la machine locale.

### <a name="install-the-mma"></a>Installer MMA

Pour installer l’agent sur une machine Windows :

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**. 
4. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics (OMS)** > **Suivant**. 
5. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail OMS. Collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.


Pour installer l’agent sur une machine Linux :

1. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
2. Installez le bundle avec l’argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances
1. Pour installer l’agent de dépendances sur une machine Windows, double-cliquez sur le fichier d’installation et suivez l’Assistant.
2. Pour installer l’agent de dépendances sur une machine Linux en tant que racine, exécutez la commande suivante :

    ```sh InstallDependencyAgent-Linux64.bin```

[En savoir plus](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) sur les systèmes d’exploitation pris en charge par l’agent de dépendances. 

## <a name="create-a-group"></a>Créer un groupe

1. Après avoir installé les agents, accédez au portail et cliquez sur **Gérer** > **Machines**.
2. La colonne **Dépendances** doit maintenant apparaître comme **Afficher les dépendances**. Cliquez sur la colonne pour afficher les dépendances.
3. Pour chaque machine, vous pouvez vérifier :
    - que MMA et l’agent de dépendances sont installés et que la machine a été détectée ;
    - le système d’exploitation invité en cours d’exécution sur la machine ;
    - les connexions IP entrantes et sortantes et les ports ;
    - les processus en cours d’exécution sur les machines ;
    - les dépendances entre les machines.

4. Pour obtenir des dépendances plus précises, cliquez sur l’intervalle de temps et modifiez-le. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.
5. Lorsque vous avez identifié des machines dépendantes à grouper, sélectionnez-les sur le mappage, puis cliquez sur **Grouper les machines**.
6. Spécifiez un nom de groupe. Vérifiez que la machine est détectée par Azure Migrate. Si ce n’est pas le cas, exécutez à nouveau le processus de détection en local. Vous pouvez exécuter une évaluation immédiatement si vous le souhaitez.
7. Cliquez sur **OK** pour enregistrer le groupe.

    ![Créer un groupe avec des dépendances de machine](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment](how-to-create-group-dependencies.md) affiner le groupe en vérifiant les dépendances de groupe.
- [Découvrez plus en détails](concepts-assessment-calculation.md) le mode de calcul des évaluations.
