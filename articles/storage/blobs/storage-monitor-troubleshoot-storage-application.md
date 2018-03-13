---
title: "Surveiller et résoudre les problèmes d’une application de stockage cloud dans Azure | Microsoft Docs"
description: "Utilisez des outils de diagnostic, des métriques et la génération d’alertes pour surveiller et résoudre les problèmes d’une application cloud."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: a1b3a1d4bb397e19f033b8f3bfe68ca6a63725c4
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Surveiller et résoudre les problèmes d’une application de stockage cloud

Ce didacticiel est le quatrième et dernier volet d’une série. Vous apprenez à surveiller et résoudre les problèmes d’une application de stockage cloud.

Dans ce quatrième volet, vous apprenez à :

> [!div class="checklist"]
> * Activer la journalisation et les métriques
> * Activer les alertes pour les erreurs d’autorisation
> * Exécuter un trafic de test avec des jetons SAS incorrects
> * Télécharger et analyser des journaux

[Azure Storage Analytics](../common/storage-analytics.md) fournit la journalisation et les données de métriques d’un compte de stockage. Ces données fournissent des insights sur l’intégrité de votre compte de stockage. Avant d’obtenir une visibilité de votre compte de stockage, vous devez configurer la collecte de données. Ce processus implique l’activation de la journalisation, la configuration des métriques et l’activation des alertes.

La journalisation et les métriques des comptes de stockage sont activées sous l’onglet **Diagnostics** du portail Azure. Il existe deux types de métriques. Les métriques d’**agrégation** collectent les pourcentages d’entrée/de sortie, de disponibilité, de latence et de réussite. Ces métriques sont agrégées pour les services Blob, File d’attente, Table et Fichier. **Par API** collecte le même ensemble de métriques pour chaque opération de stockage de l’API du service de stockage Azure. La journalisation du stockage permet d’enregistrer les détails des demandes ayant réussi ou échoué dans votre compte de stockage. Ces journaux permettent d'afficher les détails des opérations de lecture, d'écriture et de suppression sur vos tables, files d'attente et objets blob Azure. Ils permettent également de connaître la raison des échecs de demande comme les délais d’attente, la limitation et les erreurs d’autorisation.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Activer la journalisation et les métriques

Dans le menu de gauche, sélectionnez **Groupes de ressources**, **myResourceGroup**, puis votre compte de stockage dans la liste des ressources.

Sous **Diagnostics**, définissez **État** sur **Activé**. Vérifiez que **Métriques d’agrégation d’objet blob**, **Métriques d’objet blob par API** et **Journaux d’objets blob** sont activés.

Quand vous avez terminé, cliquez sur **Enregistrer**

![Volet Diagnostics](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Activer les alertes

Les alertes sont un moyen d’envoyer par e-mail aux administrateurs ou de déclencher un webhook quand une métrique dépasse un seuil. Dans cet exemple, vous activez une alerte pour la métrique `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Accédez à votre compte de stockage dans le Portail Azure

Dans le menu de gauche, sélectionnez **Groupes de ressources**, **myResourceGroup**, puis votre compte de stockage dans la liste des ressources.

Dans la section **Surveillance**, sélectionnez **Règles d’alerte**.

Sélectionnez **+ Ajouter une alerte**, sous **Ajouter une règle d’alerte**, renseignez les informations demandées. Choisissez `SASClientOtherError` dans la liste déroulante **Métrique**.

![Volet Diagnostics](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simuler une erreur

Pour simuler une alerte valide, vous pouvez tenter de demander un objet blob qui n’existe pas à partir de votre compte de stockage. Pour ce faire, remplacez la valeur `<incorrect-blob-name>` par une valeur qui n’existe pas. Exécutez l’exemple de code suivant plusieurs fois pour simuler des demandes d’objet blob ayant échoué.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

L’illustration suivante représente un exemple d’alerte basée sur l’échec simulé exécuté dans l’exemple précédent.

 ![Exemple d’alerte](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Télécharger et afficher des journaux

Les journaux de stockage stockent des données dans un ensemble d’objets blob dans un conteneur d’objets blob nommé **$logs** dans votre compte de stockage. Ce conteneur n’apparaît pas si vous répertoriez tous les conteneurs d’objets blob de votre compte, mais vous pouvez voir son contenu si vous y accédez directement.

Dans ce scénario, vous utilisez [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) pour interagir avec votre compte de stockage Azure.

### <a name="download-microsoft-message-analyzer"></a>Télécharger Microsoft Message Analyzer

Téléchargez [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) et installez l’application.

Lancez l’application et choisissez **Fichier** > **Ouvrir** > **À partir d’autres sources de fichiers**.

Dans la boîte de dialogue **Sélecteur de fichiers**, sélectionnez **+ Ajouter une connexion Azure**. Entrez le **nom du compte de stockage** et la **clé de compte**, puis cliquez sur **OK**.

![Boîte de dialogue Microsoft Message Analyzer - Ajouter une connexion de stockage Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Une fois que vous êtes connecté, développez les conteneurs dans l’arborescence de stockage pour afficher les objets blob de journal. Sélectionnez le dernier journal et cliquez sur **OK**.

![Boîte de dialogue Microsoft Message Analyzer - Ajouter une connexion de stockage Azure](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Dans la boîte de dialogue **Nouvelle session**, cliquez sur **Démarrer** pour afficher le journal.

Une fois le journal ouvert, vous pouvez consulter les événements de stockage. Comme vous pouvez le voir dans l’image suivante, un événement `SASClientOtherError` a été déclenché sur le compte de stockage. Pour plus d’informations sur la journalisation du stockage, visitez [Storage Analytics](../common/storage-analytics.md).

![Microsoft Message Analyzer - Affichage des événements](media/storage-monitor-troubleshoot-storage-application/figure5.png)

L’[Explorateur Stockage](https://azure.microsoft.com/features/storage-explorer/) est un autre outil qui peut être utilisé pour interagir avec vos comptes de stockage, y compris le conteneur **$logs** et les fichiers journaux qu’il contient.

## <a name="next-steps"></a>Étapes suivantes

Dans le quatrième et dernier volet de la série, vous avez appris à surveiller et résoudre les problèmes de votre compte de stockage, notamment :

> [!div class="checklist"]
> * Activer la journalisation et les métriques
> * Activer les alertes pour les erreurs d’autorisation
> * Exécuter un trafic de test avec des jetons SAS incorrects
> * Télécharger et analyser des journaux

Suivez ce lien pour consulter des exemples de stockage préconçus.

> [!div class="nextstepaction"]
> [Exemples de script de stockage Azure](storage-samples-blobs-cli.md)