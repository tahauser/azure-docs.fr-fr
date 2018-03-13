---
title: "Affiner un groupe d’évaluation avec mappage de dépendances de groupe dans Azure Migrate | Microsoft Docs"
description: "Explique comment affiner une évaluation à l’aide du mappage de dépendances de groupe dans le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Affiner un groupe à l’aide du mappage de dépendances de groupe

Cet article décrit comment affiner un groupe en visualisant les dépendances de tous les ordinateurs dans le groupe. On utilise généralement cette méthode pour affiner l’appartenance à un groupe existant, en vérifiant par recoupement les dépendances du groupe avant d’exécuter une évaluation. Affiner un groupe à l’aide de la visualisation des dépendances peut vous aider à planifier efficacement votre migration vers Azure. Vous pouvez découvrir tous les systèmes interdépendants devant migrer en même temps. Elle vous permet de ne rien oublier et vous épargne les pannes inopinées pendant la migration vers Azure. 


> [!NOTE]
> Les groupes pour lesquels vous souhaitez visualiser les dépendances ne doivent pas contenir plus de 10 machines. Si vous avez plus de 10 machines dans le groupe, nous vous recommandons de le diviser en groupes plus petits pour tirer parti des fonctionnalités de visualisation de dépendance.


# <a name="prepare-the-group-for-dependency-visualization"></a>Préparer le groupe à la visualisation de dépendance
Pour afficher les dépendances d’un groupe, vous devez télécharger et installer des agents sur chacune des machines locales faisant partie du groupe. Si, par ailleurs, certaines de vos machines n’ont pas de connexion Internet, il vous faudra télécharger et y installer la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles
1. Dans **Vue d’ensemble**, cliquez sur **Gérer** > **Groupes**, accédez au groupe requis.
2. Dans la liste des machines, dans la colonne **Agent de dépendances**, cliquez sur **Installation requise** pour afficher les instructions relatives au téléchargement et à l’installation des agents.
3. Sur la page **Dépendances**, téléchargez et installez Microsoft Monitoring Agent (MMA) et l’agent de dépendances sur chacune des machines virtuelles faisant partie du groupe.
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

## <a name="refine-the-group-based-on-dependency-visualization"></a>Affiner le groupe en fonction de la visualisation de dépendance
Une fois que vous avez installé les agents sur toutes les machines du groupe, vous pouvez visualiser les dépendances du groupe et l’affiner en suivant les étapes ci-dessous.

1. Dans le projet Azure Migrate, sous **Gérer**, cliquez sur  **Groupes** et sélectionnez le groupe.
2. Sur la page du groupe, cliquez sur  **Afficher les dépendances** pour ouvrir le mappage de dépendances du groupe.
3. La carte des dépendances du groupe affiche les informations suivantes :
    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers/depuis toutes les machines faisant partie du groupe
        - Les machines dépendantes sur lesquelles ne sont pas installés l’agent MMA et l’agent de dépendances sont regroupées par numéros de port
        - Les machines dépendantes sur lesquelles sont installés l’agent MMA et l’agent de dépendances apparaissent sous forme de zones distinctes 
    - Processus en cours d’exécution dans la machine (vous pouvez développer chaque zone de machine pour afficher les processus correspondants)
    - Propriétés de chaque machine telles que Nom de domaine complet, Système d’exploitation ou Adresse MAC (vous pouvez cliquer sur chaque zone de machine pour afficher ces détails)

     ![Afficher les dépendances de groupe](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Pour afficher des dépendances plus précises, cliquez sur l’intervalle de temps et modifiez-le. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.
4. Vérifiez les machines dépendantes, le processus en cours d’exécution pour chaque machine et identifiez les machines qui doivent être ajoutées ou supprimées du groupe.
5. Utilisez Ctrl + clic pour sélectionner des machines sur la carte et les ajouter ou supprimer du groupe.
    - Seules les machines qui ont été détectées peuvent être ajoutées.
    - L’ajout et la suppression de machines au sein d’un groupe invalide ses évaluations passées.
    - Vous pouvez, si vous le souhaitez, créer une nouvelle évaluation lorsque vous modifiez le groupe.
5. Cliquez sur **OK** pour enregistrer le groupe.

    ![Ajouter ou supprimer des machines](./media/how-to-create-group-dependencies/add-remove.png)

Si vous souhaitez vérifier les dépendances d’une machine spécifique qui s’affichent dans le mappage de dépendances de groupe, [configurez le mappage de dépendances de la machine](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
