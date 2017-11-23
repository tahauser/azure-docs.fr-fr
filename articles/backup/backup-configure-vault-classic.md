---
title: "Sauvegarder un serveur ou une station de travail Windows dans Azure (modèle classique) | Microsoft Docs"
description: "Sauvegardez des serveurs ou des clients Windows dans un coffre de sauvegarde Azure. Parcourez les principes fondamentaux de la protection des fichiers et des dossiers dans un coffre de sauvegarde à l’aide de l’agent Azure Backup."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: coffre de sauvegarde ; sauvegarder un serveur Windows ; sauvegarder windows ;
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 717d1f377e1a074984a0332b978754bea26ce144
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Sauvegarder un serveur ou une station de travail Windows dans Azure à l’aide du portail Classic
> [!div class="op_single_selector"]
> * [Portail classique](backup-configure-vault-classic.md)
> * [portail Azure](backup-configure-vault.md)
>
>

Cet article vous présente les procédures à exécuter pour préparer votre environnement et sauvegarder un serveur (ou une station de travail) Windows sur Azure. Il aborde également les considérations de déploiement de votre solution de sauvegarde. Si vous souhaitez essayer Azure Backup pour la première fois, cet article vous présente le processus.

Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Resource Manager et classique. Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="before-you-start"></a>Avant de commencer
Pour sauvegarder un serveur ou un client sur Azure, vous devez disposer d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde
Pour sauvegarder des fichiers et des dossiers d’un serveur ou d’un client, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données.

> [!IMPORTANT]
> Depuis mars 2017, vous ne pouvez plus utiliser le portail Classic pour créer des coffres de sauvegarde.
>
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À partir du **30 novembre 2017**, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde. <br/> **D’ici au 30 novembre 2017** :
>- tous les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>


## <a name="download-the-vault-credential-file"></a>Téléchargement du fichier d’informations d’identification de coffre
L’ordinateur local doit être authentifié avec un coffre de sauvegarde avant de pouvoir sauvegarder des données sur Azure. L’authentification s’effectue à l’aide des *informations d’identification du coffre*. Le fichier d’informations d’identification de coffre est téléchargé via un canal sécurisé depuis le portail Classic. La clé privée du certificat n’est pas conservée dans le portail ou le service.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Pour télécharger les informations d’identification de coffre sur un ordinateur local
1. Dans le volet de navigation gauche, cliquez sur **Recovery Services**et sélectionnez le coffre de sauvegarde que vous avez créé.

    ![RI terminé](./media/backup-configure-vault-classic/rs-left-nav.png)
2. Dans la page de démarrage rapide, cliquez sur **Télécharger les informations d’identification du coffre**.

   Le portail Classic génère une information d’identification de coffre en associant le nom du coffre à la date du jour. Le fichier d’informations d’identification du coffre est utilisé uniquement pendant le processus d’inscription et expire au bout de 48 heures.

   Le fichier d’informations d’identification de coffre peut être téléchargé à partir du portail.
3. Cliquez sur **Enregistrer** pour télécharger le fichier d’informations d’identification de coffre dans le dossier Téléchargements du compte local. Vous pouvez également sélectionner l’option **Enregistrer sous** à partir du menu **Enregistrer** pour spécifier un emplacement pour le fichier d’informations d’identification de coffre.

   > [!NOTE]
   > Vérifiez que le fichier des informations d’identification de coffre est enregistré dans un emplacement accessible à partir de votre ordinateur. S’il est stocké dans un partage de fichiers ou un bloc de message serveur, vérifiez que vous disposez des autorisations pour y accéder.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Télécharger, installer et inscrire l’agent Backup
Après avoir créé le coffre de sauvegarde et téléchargé le fichier d’informations d’identification de coffre, vous devez installer un agent sur chacun de vos ordinateurs Windows.

### <a name="to-download-install-and-register-the-agent"></a>Pour télécharger, installer et inscrire l’agent
1. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde à inscrire auprès d’un serveur.
2. Sur la page de démarrage rapide, cliquez sur **Agent pour Windows Server ou System Center Data Protection Manager ou Windows Client**. Cliquez ensuite sur **Enregistrer**.

    ![Enregistrer l’agent](./media/backup-configure-vault-classic/agent.png)
3. Une fois le téléchargement de MARSagentinstaller.exe terminé, cliquez sur **Exécuter** (ou double-cliquez sur **MARSAgentInstaller.exe** dans l’emplacement d’enregistrement).
4. Choisissez le dossier d’installation et le dossier de cache requis pour l’agent, puis cliquez sur **Suivant**. L’emplacement de cache spécifié doit présenter un espace libre au moins égal à 5 % du volume des données de sauvegarde.
5. Vous pouvez continuer à vous connecter à Internet avec les paramètres du proxy par défaut.             Si vous utilisez un serveur proxy pour vous connecter à Internet, sur la page Configuration du proxy, sélectionnez **Utiliser les paramètres du proxy personnalisés** et entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe, puis cliquez sur **Suivant**.
6. Cliquez sur **Installer** pour lancer le processus d’installation de l’agent. L’agent Backup installe .NET Framework 4.5 et Windows PowerShell (s’il n’est pas déjà installé) pour terminer l’installation.
7. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement** pour poursuivre le flux de travail.
8. Sur la page Identification du coffre, recherchez et sélectionnez le fichier d’informations d’identification de coffre téléchargé précédemment.

    Le fichier d’informations d’identification de coffre est uniquement valide pendant 48 heures à partir de son téléchargement depuis le portail. Si vous rencontrez une erreur sur cette page (par exemple, « Le fichier d’informations d’identification de coffre fourni a expiré »), connectez-vous au portail et retéléchargez le fichier d’informations d’identification de coffre.

    Assurez-vous que le fichier d’informations d’identification de coffre se trouve à un emplacement accessible par le programme d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification de coffre dans un emplacement temporaire sur le même ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur d’informations d’identification de coffre (par exemple, « Les informations d’identification de coffre fournies ne sont pas valides »), cela signifie que le fichier est endommagé ou qu’il ne possède pas les dernières informations d’identification associées au service de récupération. Recommencez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre à partir du portail. Cette erreur peut également se produire si l’utilisateur clique sur l’option **Télécharger les informations d’identification du coffre** coup sur coup. Dans ce cas, seul le dernier fichier d’informations d’identification du coffre est valide.
9. Sur la page Paramètre de chiffrement, vous pouvez générer ou fournir une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.
10. Cliquez sur **Terminer**. L’Assistant Inscrire un serveur inscrit le serveur auprès d’Azure Backup.

    > [!WARNING]
    > En cas de perte ou d’oubli de la phrase secrète, Microsoft ne pourra pas vous aider à récupérer les données de sauvegarde. Vous êtes seul détenteur de la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.
    >
    >

11. Une fois la clé de chiffrement définie, laissez la case **Démarrer Microsoft Azure Recovery Services Agent** cochée, puis cliquez sur **Fermer**.

## <a name="complete-the-initial-backup"></a>Exécuter la sauvegarde initiale
La sauvegarde initiale comprend deux tâches principales :

* Création de la planification de sauvegarde
* Première sauvegarde des fichiers et dossiers

Après avoir réalisé la sauvegarde initiale, la stratégie de sauvegarde crée des points de sauvegarde que vous pouvez utiliser si vous avez besoin de récupérer les données. Pour cela, la stratégie de sauvegarde suit la planification que vous définissez.

### <a name="to-schedule-the-backup"></a>Pour planifier la sauvegarde
1. Ouvrez l’agent Microsoft Azure Backup. (Il s’ouvre automatiquement si vous avez laissé la case **Démarrer Microsoft Azure Recovery Services Agent** cochée à la fermeture de l’Assistant Inscrire un serveur.) Vous pouvez le trouver en recherchant **Microsoft Azure Backup**sur votre ordinateur.

    ![Démarrer l’agent Azure Backup](./media/backup-configure-vault-classic/snap-in-search.png)
2. Dans l’agent Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. Sur la page Mise en route de l’Assistant Planifier la sauvegarde, cliquez sur **Suivant**.
4. Sur la page Sélectionner les éléments à sauvegarder, cliquez sur **Ajouter des éléments**.
5. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.
6. Cliquez sur **Suivant**.
7. Sur la page **Spécifier une planification de la sauvegarde**, spécifiez la **planification de la sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > Pour plus d’informations sur la spécification de la planification de la sauvegarde, consultez l’article [Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. Sur la page **Sélectionner une stratégie de rétention**, définissez la **stratégie de rétention** pour la copie de sauvegarde.

    La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, vous pouvez spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Vous pouvez modifier les stratégies de rétention quotidiennes, hebdomadaires, mensuelles et annuelles pour répondre à vos besoins.
9. Sur la page Choisir un type de sauvegarde initiale, sélectionnez le type de sauvegarde initiale. Laissez l’option **Automatiquement sur le réseau** sélectionnée, puis cliquez sur **Suivant**.

    Vous pouvez effectuer des sauvegardes automatiques sur le réseau ou vous pouvez sauvegarder en mode hors connexion. Le reste de cet article décrit le processus de sauvegarde automatique. Si vous préférez effectuer une sauvegarde en mode hors connexion, consultez l’article [Flux de travail de la sauvegarde hors connexion dans Azure Backup](backup-azure-backup-import-export.md) pour plus d’informations.
10. Sur la page Confirmation, passez en revue les informations, puis cliquez sur **Terminer**.
11. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

### <a name="enable-network-throttling-optional"></a>Activer la limitation du réseau (facultatif)
L’agent Backup prend en charge la limitation du réseau. La limitation contrôle l’utilisation de la bande passante réseau durant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation s’applique aux activités de sauvegarde et de restauration.

**Pour activer la limitation du réseau**

1. Dans l’agent Backup, cliquez sur **Modifier les propriétés**.

    ![Modifier les propriétés](./media/backup-configure-vault-classic/change-properties.png)
2. Dans l’onglet **Limitation**, activez la case à cocher **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail**et identifier les jours de la semaine considérés comme des jours de travail. Les heures non comprises dans les heures de travail définies sont considérées comme des heures chômées.
4. Cliquez sur **OK**.

### <a name="to-back-up-now"></a>Pour procéder à la sauvegarde
1. Dans l’agent Backup, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-configure-vault-classic/backup-now.png)
2. Sur la page Confirmation, vérifiez les paramètres utilisés par l’Assistant Sauvegarder maintenant pour sauvegarder les données de l’ordinateur, puis cliquez sur **Sauvegarder**.
3. Cliquez sur **Fermer** pour fermer l’assistant. Si vous fermez l’Assistant avant la fin du processus de sauvegarde, celui-ci continuera de s’exécuter en arrière-plan.

Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans la console Backup.

![RI terminé](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Étapes suivantes
* Ouvrir [gratuitement un compte Azure](https://azure.microsoft.com/free/).

Pour plus d’informations sur la sauvegarde des machines virtuelles ou d’autres charges de travail, consultez les références suivantes :

* [Sauvegarder des machines virtuelles IaaS](backup-azure-vms-prepare.md)
* [Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md)
* [Préparation de la sauvegarde des charges de travail dans Azure avec DPM](backup-azure-dpm-introduction.md)
