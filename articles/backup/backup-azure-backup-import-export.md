---
title: "Sauvegarde Azure - Sauvegarde ou amorçage initial hors connexion à l’aide du service Azure Import/Export | Microsoft Docs"
description: "Découvrez comment la sauvegarde Azure vous permet d’envoyer des données en dehors du réseau à l’aide du service Azure Import/Export. Cet article décrit l’amorçage hors connexion des données de sauvegarde initiale à l’aide du service Azure Import/Export."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/18/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 32a48a34711a7f053a74e103deb6853150de3903
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flux de travail de la sauvegarde hors connexion dans la sauvegarde Azure
La sauvegarde Azure offre plusieurs fonctionnalités intégrées pour réduire les coûts de stockage et de réseau pendant les sauvegardes complètes initiales des données dans Azure. Les sauvegardes complètes initiales transfèrent généralement de grandes quantités de données et requièrent davantage de bande passante, en comparaison avec les sauvegardes suivantes qui transfèrent uniquement les données deltas/incrémentielles. La sauvegarde Azure compresse les sauvegardes initiales. Via le processus d’amorçage hors connexion, la sauvegarde Azure peut utiliser des disques pour charger les données de sauvegarde initiale compressées hors connexion dans Azure.  

Le processus d’amorçage hors connexion de la sauvegarde Azure est étroitement intégré au [service Azure Import/Export](../storage/common/storage-import-export-service.md) qui vous permet de transférer des données vers Azure à l’aide de disques. Si vous devez transférer des téraoctets (To) de données de sauvegarde initiale sur un réseau à latence élevée et à faible bande passante, vous pouvez utiliser le flux de travail d’amorçage hors connexion pour expédier la copie de sauvegarde initiale sur un ou plusieurs disques durs à un centre de données Azure. Cet article fournit une vue d’ensemble des étapes requises pour effectuer ce flux de travail.

## <a name="overview"></a>Vue d'ensemble
Grâce à la fonction d’amorçage hors connexion de la sauvegarde Azure et à Azure Import/Export, vous pouvez charger facilement les données dans Azure hors connexion à l’aide de disques. Au lieu de transférer la copie initiale complète sur le réseau, les données de sauvegarde sont écrites dans un *emplacement intermédiaire*. Une fois la copie effectuée vers l’emplacement intermédiaire à l’aide de l’outil Azure Import/Export, ces données sont écrites sur un ou plusieurs disques SATA, en fonction de la quantité de données. Ces disques sont expédiés au centre de données Azure le plus proche.

La [mise à jour de la sauvegarde Azure du mois d’août 2016 (et ultérieure)](http://go.microsoft.com/fwlink/?LinkID=229525) comprend un *outil de préparation des disques Azure*nommé AzureOfflineBackupDiskPrep qui :

* vous aide à préparer vos disques pour Azure Import à l’aide de l’outil Azure Import/Export ;
* crée automatiquement un travail d’importation Azure pour le service Azure Importer/Exporter dans le [portail Azure](https://ms.portal.azure.com).

Une fois les données de sauvegarde chargées dans Azure, la sauvegarde Azure copie les données de sauvegarde dans le coffre de sauvegarde, et des sauvegardes incrémentielles sont planifiées.

> [!NOTE]
> Pour utiliser l’outil de préparation des disques Azure, assurez-vous d’avoir installé la mise à jour de la sauvegarde Azure du mois d’août 2016 (ou ultérieure) et effectuez toutes les étapes du flux de travail associé. Si vous utilisez une version antérieure de la sauvegarde Azure, vous pouvez préparer le disque SATA à l’aide de l’outil Azure Import/Export comme détaillé dans les sections suivantes de cet article.
>
>

## <a name="prerequisites"></a>Conditions préalables
* [Familiarisez-vous avec le flux de travail Azure Import/Export](../storage/common/storage-import-export-service.md).
* Avant de lancer le flux de travail, vérifiez les points suivants :
  * Un coffre de sauvegarde Azure a été créé.
  * Les informations d’identification du coffre ont été téléchargées.
  * L’agent de sauvegarde Azure a été installé sur Windows Server/le client Windows ou sur le serveur de System Center Data Protection Manager, et l’ordinateur est inscrit auprès du coffre de sauvegarde Azure.
* [Téléchargez les paramètres du fichier de publication Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) sur l’ordinateur à partir duquel vous prévoyez de sauvegarder vos données.
* Préparez un emplacement intermédiaire, qui peut être un partage réseau ou un disque supplémentaire sur l’ordinateur. L’emplacement intermédiaire est un stockage temporaire utilisé pendant ce flux de travail. Assurez-vous que l’emplacement intermédiaire dispose de suffisamment d’espace disque pour stocker votre copie initiale. Par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone intermédiaire dispose d’au moins 500 Go (bien qu’une quantité inférieure soit utilisée en raison de la compression).
* Assurez-vous d’utiliser un disque pris en charge. Le service Import/Export ne prend en charge que les disques durs internes SSD de 2,5 pouces ou SATA II ou III de 2,5 ou 3,5 pouces. La capacité maximale par disque dur est de 10 To. Consultez la [documentation sur le service Azure Import/Export](../storage/common/storage-import-export-service.md#hard-disk-drives) pour connaître la dernière série de disques pris en charge par le service.
* Activez BitLocker sur l’ordinateur auquel est connecté l’enregistreur de disque SATA.
* [Téléchargez l’outil Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) sur l’ordinateur auquel est connecté l’enregistreur de disque SATA. Cette étape n’est pas nécessaire si vous avez téléchargé et installé la mise à jour de la sauvegarde Azure du mois d’août 2016 (ou ultérieure).

## <a name="workflow"></a>Workflow
Les informations de cette section vous permettent d’effectuer le flux de travail de sauvegarde hors connexion, afin que vos données puissent être remises à un centre de données Azure et chargées dans le stockage Azure. Si vous avez des questions sur le service Import ou sur un aspect du processus, consultez la documentation sur la [vue d’ensemble du service Import](../storage/common/storage-import-export-service.md) susmentionnée.

### <a name="initiate-offline-backup"></a>Lancer la sauvegarde hors connexion
1. Lorsque vous planifiez une sauvegarde, l’écran suivant s’affiche (dans Windows Server, le client Windows ou System Center Data Protection Manager).

    ![Écran d’importation](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Voici l’écran correspondant dans System Center Data Protection Manager :  <br/>
    ![Écran d’importation DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    Les champs sont décrits ci-dessous :

    * **Emplacement intermédiaire**: l’emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrite. Cela peut être sur un partage réseau ou un ordinateur local. Si l’ordinateur de copie et l’ordinateur source sont différents, nous vous recommandons de spécifier le chemin d’accès réseau complet de l’emplacement intermédiaire.
    * **Nom de la tâche d’importation Azure**: le nom unique utilisé par le service Azure Import et la sauvegarde Azure pour effectuer le suivi du transfert des données envoyées dans Azure à l’aide de disques.
    * **Paramètres de publication Azure**: un fichier XML qui contient des informations sur le profil de votre abonnement. Il contient également les informations d’identification sécurisées associées à votre abonnement. Vous pouvez [télécharger le fichier](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade). Fournissez le chemin d’accès local au fichier de paramètres de publication.
    * **ID d’abonnement Azure**: l’ID d’abonnement Azure correspondant à l’abonnement dans lequel vous prévoyez de lancer le travail d’importation Azure. Si vous avez plusieurs abonnements Azure, utilisez l’ID de l’abonnement auquel vous souhaitez associer le travail d’importation.
    * **Compte de stockage Azure** : le compte de stockage de l’abonnement Azure associé au travail d’importation Azure.
    * **Conteneur de stockage Azure**: le nom de l’objet blob de stockage cible dans le compte de stockage Azure où les données de ce travail sont importées.

    > [!NOTE]
    > Si vous avez inscrit votre serveur dans un coffre Recovery Services à partir du [portail Azure](https://portal.azure.com) pour vos sauvegardes et que vous ne disposez d’aucun abonnement Fournisseur de solutions cloud (CSP), vous pouvez créer un compte de stockage à partir du portail Azure et l’utiliser pour le flux de travail de sauvegarde hors connexion.
    >
    >

     Enregistrez toutes ces informations, car vous devrez les saisir à nouveau dans les étapes suivantes. Seul *l’emplacement intermédiaire* est requis si vous utilisez l’outil de préparation des disques Azure pour préparer les disques.    
2. Effectuez le flux de travail et sélectionnez **Sauvegarder maintenant** dans la console de gestion de la sauvegarde Azure pour lancer la copie de sauvegarde hors connexion. La sauvegarde initiale est écrite dans la zone intermédiaire à cette étape.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow.png)

    Pour effectuer le flux de travail correspondant dans System Center Data Protection Manager, cliquez avec le bouton droit sur le **groupe de protection**, puis sélectionnez l’option **Créer un point de récupération**. Sélectionnez ensuite l’option **Protection en ligne** .

    ![Sauvegarder maintenant DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Une fois l’opération terminée, l’emplacement intermédiaire est prêt à être utilisé pour la préparation des disques.

    ![Progression de la sauvegarde](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Préparer un disque SATA et créer un travail d’importation Azure à l’aide de l’outil de préparation des disques Azure
L’outil de préparation des disques Azure est disponible dans le répertoire d’installation de l’agent Recovery Services (mise à jour d’août 2016 et ultérieure) via le chemin d’accès suivant.

   *\Microsoft**Azure**Recovery**Services* *Agent\Utils\*

1. Accédez au répertoire et copiez le répertoire **AzureOfflineBackupDiskPrep** sur un ordinateur de copie sur lequel les disques à préparer ont été montés. Vérifiez les éléments suivants concernant l’ordinateur de copie :

    * L’ordinateur de copie peut accéder à l’emplacement intermédiaire spécifié pour le flux de travail d’amorçage hors connexion via le même chemin d’accès réseau que celui fourni dans le flux de travail **Lancer la sauvegarde hors connexion** .
    * BitLocker est activé sur l’ordinateur.
    * L’ordinateur peut accéder au portail Azure.

    Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.
2. Ouvrez une invite de commande avec élévation de privilèges sur l’ordinateur de copie avec le répertoire de l’outil de préparation des disques Azure comme répertoire actuel et exécutez la commande suivante :

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Paramètre | DESCRIPTION |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |Entrée obligatoire utilisée pour fournir le chemin d’accès vers l’emplacement intermédiaire que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion** . |
    | p:&lt;*Path to PublishSettingsFile*&gt; |Entrée optionnelle utilisée pour fournir le chemin d’accès vers le fichier de **paramètres de publication Azure** que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion**. |

    > [!NOTE]
    > La valeur &lt;Chemin d’accès vers le fichier de paramètres de publication&gt; est obligatoire quand l’ordinateur de copie et l’ordinateur source sont différents.
    >
    >

    Quand vous exécutez la commande, l’outil vous demande de sélectionner le travail d’importation Azure qui correspond aux disques devant être préparés. S’il existe un seul travail d’importation associé à l’emplacement intermédiaire spécifié, un écran similaire à l’écran suivant s’affiche.

    ![Entrée d’outil de préparation des disques Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Entrez la lettre du disque, sans les deux-points de fin, correspondant au disque monté que vous souhaitez préparer pour le transfert vers Azure. Confirmez le formatage du disque lorsque vous y êtes invité.

    L’outil commence ensuite à préparer le disque avec les données de sauvegarde. Vous devrez peut-être ajouter des disques supplémentaires lorsque vous y êtes invité par l’outil, au cas où le disque fourni n’aurait pas suffisamment d’espace pour les données de sauvegarde. <br/>

    À la fin de l’exécution correcte de l’outil, un ou plusieurs disques que vous avez fournis sont préparés pour l’expédition à Azure. En outre, un travail d’importation avec le nom que vous avez fourni lors du flux de travail **Lancer la sauvegarde hors connexion** est créé sur le portail Azure. Enfin, l’outil affiche l’adresse du centre de données Azure auquel les disques doivent être expédiés, ainsi que le lien vers le travail d’importation sur le portail Azure.

    ![Préparation des disques Azure terminée](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Expédiez les disques à l’adresse indiquée par l’outil et conservez le numéro de suivi pour référence.<br/>

5. Lorsque vous ouvrez le lien affiché par l’outil, vous voyez le compte de stockage Azure que vous avez spécifié dans le flux de travail **Lancer la sauvegarde hors connexion** . Vous pouvez voir le travail d’importation nouvellement créé dans l’onglet **IMPORT/EXPORT** du compte de stockage.

    ![Travail d’importation créé](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Cliquez sur **INFORMATIONS D’EXPÉDITION** en bas de la page pour mettre à jour vos coordonnées comme indiqué sur l’écran suivant. Microsoft utilise ces informations pour vous renvoyer vos disques une fois le travail d’importation terminé.

    ![Informations de contact](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Entrez les informations d’expédition sur l’écran suivant. Fournissez les coordonnées du **transporteur** et le **numéro de suivi** correspondant aux disques que vous avez expédiés au centre de données Azure.

    ![INFORMATIONS D’EXPÉDITION](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Terminer le flux de travail
Une fois le travail d’importation terminé, les données de sauvegarde initiale sont disponibles dans votre compte de stockage. L’agent Recovery Services copie alors le contenu des données à partir de ce compte vers le coffre de sauvegarde Azure ou le coffre Recovery Services, selon ce qui est applicable. Lors de la prochaine sauvegarde planifiée, l’agent Azure Backup ajoute la sauvegarde incrémentielle à la copie de sauvegarde initiale.

> [!NOTE]
> Les sections suivantes s’appliquent aux utilisateurs de versions antérieures de la sauvegarde Azure qui n’ont pas accès à l’outil de préparation des disques Azure.
>
>

### <a name="prepare-a-sata-drive"></a>Préparer un disque SATA
1. Téléchargez [l’outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) sur l’ordinateur de copie. Assurez-vous que l’emplacement intermédiaire est accessible à partir de l’ordinateur sur lequel vous souhaitez exécuter le jeu de commandes suivant. Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.

2. Décompressez le fichier WAImportExport.zip. Exécutez l’outil WAImportExport qui formate le disque SATA, écrit les données de sauvegarde sur le disque SATA et les chiffre. Avant d’exécuter la commande suivante, assurez-vous que BitLocker est activé sur l’ordinateur. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Si vous avez installé la mise à jour de la sauvegarde Azure du mois d’août 2016 (ou ultérieure), assurez-vous que l’emplacement intermédiaire que vous avez spécifié est identique à celui affiché sur l’écran **Sauvegarder maintenant** et qu’il contient les fichiers AIB et Base Blob.
    >
    >

| Paramètre | DESCRIPTION |
| --- | --- |
| /j: <*JournalFile*> |Chemin d’accès du fichier journal. Chaque disque doit avoir un seul fichier journal. Le fichier journal ne doit pas se trouver sur le disque cible. L’extension du fichier journal est .jrn, elle est créée à l’exécution de cette commande. |
| /id: <*SessionId*> |L’ID de session identifie une session de copie. Il est utilisé pour assurer la précision de la récupération d’une session de copie interrompue. Les fichiers copiés dans une session de copie sont stockés dans un répertoire dont le nom fait référence à l’ID de session sur le disque cible. |
| /sk: <*StorageAccountKey*> |Clé du compte de stockage vers lequel les données sont importées. Elle doit être identique à celle qui a été entrée lors de la création de la stratégie de sauvegarde/du groupe de protection. |
| /BlobType |Type d’objet blob. Ce flux de travail réussit uniquement si **PageBlob** est spécifié. Ce paramètre n’est pas l’option par défaut et doit être indiqué dans cette commande. |
| /t: <*TargetDriveLetter*> |La lettre du disque dur cible pour la session de copie actuelle, sans les deux-points de fin. |
| /format |L’option pour formater le disque. Spécifiez ce paramètre quand le disque doit être formaté, sinon, ignorez-le. Avant de formater le disque, l’outil demande confirmation à partir de la console. Pour supprimer la confirmation, spécifiez le paramètre /silentmode. |
| /encrypt |L’option pour chiffrer le disque. Spécifiez ce paramètre quand le disque n’a pas encore été chiffré avec BitLocker et doit être chiffré par l’outil. Si le disque a déjà été chiffré avec BitLocker, ignorez ce paramètre, spécifiez le paramètre /bk et saisissez la clé BitLocker existante. Si vous spécifiez le paramètre /format, vous devez également spécifier le paramètre /encrypt. |
| /srcdir: <*SourceDirectory*> |Répertoire source qui contient les fichiers à copier sur le disque cible. Assurez-vous que le nom de répertoire spécifié dispose d’un chemin d’accès complet plutôt que relatif. |
| /dstdir: <*DestinationBlobVirtualDirectory*> |Le chemin d’accès au répertoire virtuel cible dans votre compte de stockage Azure. Veillez à utiliser des noms de conteneur valides quand vous spécifiez les objets blob ou répertoires virtuels cibles. N’oubliez pas que les noms de conteneur doivent être en minuscules.  Ce nom de conteneur doit être identique à celui qui a été saisi au cours de la création de la stratégie de sauvegarde/du groupe de protection. |

> [!NOTE]
> Un fichier journal est créé dans le dossier WAImportExport et capture toutes les informations du flux de travail. Vous avez besoin de ce fichier lorsque vous créez un travail d’importation dans le portail Azure.
>
>

  ![Sortie PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Créer un travail d’importation dans le portail Azure
1. Accédez à votre compte de stockage dans le [portail Azure](https://ms.portal.azure.com/), cliquez sur **Import/Export**, puis sur **Créer un travail d’importation** dans le volet des tâches.

    ![Onglet Import/Export dans le portail Azure](./media/backup-azure-backup-import-export/azureportal.png)

2. À l’étape 1 de l’Assistant, précisez que vous avez préparé votre disque et que le fichier journal du disque est disponible.

3. À l’étape 2 de l’Assistant, fournissez les coordonnées de la personne responsable de ce travail d’importation.

4. À l’étape 3, téléchargez les fichiers journaux de disque que vous avez obtenus dans la section précédente.

5. À l’étape 4, entrez un nom descriptif pour le travail d’importation qui a été entré lors de la création de la stratégie de sauvegarde/du groupe de protection. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, doit commencer par une lettre et ne peut pas contenir d’espaces. Le nom que vous choisissez vous servira à suivre vos travaux pendant et après leur exécution.

6. Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis.

    ![Sélectionner la région de centre de données](./media/backup-azure-backup-import-export/dc.png)

7. À l’étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos disques une fois le travail d’importation terminé.

8. Expédiez le disque et entrez le numéro de suivi pour suivre l’état de l’expédition. Dès que le disque arrive au centre de données, il est copié dans le compte de stockage et l’état est mis à jour.

    ![État terminé](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Terminer le flux de travail
Une fois les données de sauvegarde initiale disponibles dans votre compte de stockage, l’agent Microsoft Azure Recovery Services copie le contenu des données à partir de ce compte vers le coffre de sauvegarde Azure ou le coffre Recovery Services, selon ce qui est applicable. Lors de la prochaine sauvegarde planifiée, l’agent Azure Backup ajoute la sauvegarde incrémentielle à la copie de sauvegarde initiale.

## <a name="next-steps"></a>étapes suivantes
* Pour toute question au sujet du flux de travail Azure Import/Export, voir [Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Reportez-vous à la section Sauvegarde hors connexion du [Forum Aux Questions](backup-azure-backup-faq.md) de la sauvegarde Azure pour toute question concernant le flux de travail.
