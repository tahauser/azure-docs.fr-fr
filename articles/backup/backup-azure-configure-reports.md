---
title: Configurer les rapports pour la Sauvegarde Azure
description: "Cet article traite de la configuration des rapports Power BI pour la Sauvegarde Azure à l’aide d’un coffre Recovery Services."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c6cc4ba95f440f09f11a93927fd67873f8813e8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="configure-azure-backup-reports"></a>Configurer les rapports de la Sauvegarde Azure
Cet article détaille la procédure permettant de configurer les rapports pour Sauvegarde Azure à l’aide d’un coffre Recovery Services, et d’accéder à ces rapports avec Power BI. Une fois que vous aurez suivi ces étapes, vous pourrez accéder directement à Power BI pour afficher tous les rapports, en personnaliser et en créer. 

## <a name="supported-scenarios"></a>Scénarios pris en charge
1. Les rapports de la Sauvegarde Azure sont pris en charge pour la sauvegarde de machines virtuelles Azure et de fichiers/dossiers dans le cloud à l’aide de l’agent Azure Recovery Services.
2. Pour l’instant, les rapports ne sont pas pris en charge pour SQL Azure, DPM et le serveur de sauvegarde Azure.
3. Vous pouvez afficher des rapports sur différents coffres et différents abonnements, à condition que le même compte de stockage soit configuré pour chacun des coffres. Le compte de stockage sélectionné doit se trouver dans la même région que le coffre Recovery Services.
4. La fréquence d’actualisation planifiée des rapports est de 24 heures dans Power BI. Vous pouvez également effectuer une actualisation ad hoc des rapports dans Power BI, auquel cas les données les plus récentes du compte de stockage client sont utilisées pour le rendu des rapports. 
5. Les rapports de sauvegarde Azure ne sont pas pris en charge actuellement dans les clouds nationaux.

## <a name="prerequisites"></a>configuration requise
1. Créez un [compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) afin de le configurer pour les rapports. Il est utilisé pour stocker les données associées aux rapports.
2. [Créez un compte Power BI](https://powerbi.microsoft.com/landing/signin/) pour afficher, personnaliser et créer vos propres rapports à l’aide du portail Power BI.
3. Si ce n’est pas déjà fait, inscrivez le fournisseur de ressources **Microsoft.insights** avec l’abonnement du compte de stockage ainsi que celui du coffre Recovery Services pour permettre aux données de rapports de circuler vers le compte de stockage. Pour cela, accédez au Portail Azure > Abonnement > Fournisseurs de ressources et cochez ce fournisseur pour l’inscrire. 

## <a name="configure-storage-account-for-reports"></a>Configurer le compte de stockage pour les rapports
Suivez les étapes ci-dessous afin de configurer le compte de stockage pour le coffre Recovery Services à l’aide du Portail Azure. Il s’agit d’une configuration ponctuelle : une fois le compte de stockage configuré, vous pourrez accéder directement à Power BI pour afficher le pack de contenu et exploiter les rapports.
1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Tous les services**.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

      ![Créer un coffre Recovery Services - Étape 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     La liste des archivages de Recovery Services s’affiche. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste des éléments qui s’affiche sous le coffre, cliquez sur **Rapports de sauvegarde** sous la section Analyse et rapports afin de configurer le compte de stockage pour les rapports.

      ![Sélectionner l’élément de menu Rapports de sauvegarde - Étape 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Dans le panneau Rapports de sauvegarde, cliquez sur le lien **Paramètres de diagnostic**. L’interface utilisateur des paramètres de diagnostic, utilisé pour effectuer une transmission de données de type push vers le compte de stockage client, s’ouvre.

      ![Permettre les diagnostics - Étape 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Cliquez sur le lien **Activer les diagnostics**. L’interface utilisateur s’ouvre pour la configuration du compte de stockage. 

      ![Activer les diagnostics - Étape 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Entrez le nom du paramètre dans le champ **Nom** et cochez la case **Archiver dans un compte de stockage** pour que les données de rapports puissent commencer à arriver dans le compte de stockage.

      ![Permettre les diagnostics - Étape 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Cliquez sur le sélecteur de compte de stockage et sélectionnez l’abonnement et le compte de stockage appropriés dans la liste pour stocker les données de rapports, puis cliquez sur **OK**.

      ![Sélectionner le compte de stockage - Étape 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Dans la section Journal, cochez la case **AzureBackupReport**, déplacez le curseur pour sélectionner la période de rétention de ces données de rapports. Les données de rapports du compte de stockage sont conservées pendant la période sélectionnée avec ce curseur.

      ![Enregistrer le compte de stockage - Étape 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Vérifiez toutes les modifications et cliquez sur le bouton **Enregistrer** en haut, comme le montre la figure ci-dessus. Cette action garantit que toutes vos modifications sont enregistrées et que le compte de stockage est maintenant configuré de façon à stocker les données de rapports.

9. Le tableau Paramètres de diagnostic doit désormais afficher le nouveau paramètre activé pour l’archivage. S’il n’apparaît pas, actualisez le tableau pour afficher le paramètre mis à jour.

      ![Afficher le paramètre de diagnostic - Étape 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Une fois que vous configurez des rapports en enregistrant un compte de stockage, vous devez **attendre 24 heures** pour que le push de données initial soit complété. Vous devez importer le pack de contenu Azure Backup dans Power BI uniquement après cette heure. Consultez la [section FAQ](#frequently-asked-questions) pour en savoir plus. 
>
>

## <a name="view-reports-in-power-bi"></a>Afficher les rapports dans Power BI 
Une fois le compte de stockage configuré pour les rapports à l’aide du coffre Recovery Services, il faut environ 24 heures pour que les données de rapports commencent à arriver. Après 24 heures de configuration du compte de stockage, suivez les étapes ci-dessous pour afficher les rapports dans Power BI :
1. [Connectez-vous](https://powerbi.microsoft.com/landing/signin/) à Power BI.
2. Cliquez sur **Obtenir les données**, puis cliquez sur **Obtenir** sous **Services** dans la bibliothèque de packs de contenu. Suivez les étapes de la [documentation Power BI pour accéder au pack de contenu](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importer un pack de contenu](./media/backup-azure-configure-reports/content-pack-import.png)
3. Tapez **Sauvegarde Azure** dans la barre de recherche et cliquez sur **Obtenir maintenant**.

      ![Obtenir un pack de contenu](./media/backup-azure-configure-reports/content-pack-get.png)
4. Entrez le nom du compte de stockage configuré à l’étape 5 ci-dessus, puis cliquez sur le bouton **Suivant**.

    ![Entrer le nom du compte de stockage](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Entrez la clé de ce compte de stockage. Vous pouvez [afficher et copier les clés d’accès de stockage](../storage/common/storage-create-storage-account.md#manage-your-storage-account) en accédant à votre compte de stockage sur le Portail Azure. 

     ![Entrer le compte de stockage](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Cliquez sur le bouton **Se connecter**. Lorsque la connexion est établie, vous obtenez une notification **Importation de données**.

    ![Importer un pack de contenu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Au bout d’un certain temps, vous obtenez une notification **Réussite** à la fin de l’importation. L’importation du pack de contenu peut prendre un peu plus longtemps si le compte de stockage contient un grand volume de données.
    
    ![Importer le pack de contenu - Réussite](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Une fois les données importées, le pack de contenu **Sauvegarde Azure** apparaît dans **Applications**, dans le volet de navigation. La liste affiche maintenant le tableau de bord, les rapports et le jeu de données de la Sauvegarde Azure avec une étoile jaune, qui indique les rapports nouvellement importés. 

     ![Pack de contenu de la Sauvegarde Azure](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Cliquez sur **Sauvegarde Azure** sous Tableaux de bord ; un ensemble de rapports clés épinglés s’affiche.

      ![Tableau de bord de la Sauvegarde Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Pour afficher l’ensemble des rapports, cliquez sur l’un des rapports du tableau de bord.

      ![Intégrité des travaux de la Sauvegarde Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Cliquez sur chaque onglet des rapports pour afficher les rapports du domaine associé.

      ![Onglets des rapports de la Sauvegarde Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
1. **Comment vérifier si les données de rapports ont commencé à arriver dans le compte de stockage ?**
    
    Vous pouvez accéder au compte de stockage configuré et sélectionner les conteneurs. Si le conteneur a une entrée insights-logs-azurebackupreport, cela signifie que les données de rapports ont commencé à arriver.

2. **Quelle est la fréquence des Push de données vers le compte de stockage et le pack de contenu de la Sauvegarde Azure dans Power BI ?**

   Pour les nouveaux utilisateurs, il faut environ 24 heures pour effectuer une transmission des données de type push vers le compte de stockage. Une fois cette transmission initiale effectuée, les données sont actualisées selon la fréquence suivante, comme le montre la figure ci-dessous. 
      * Les données relatives aux **Travaux, alertes, éléments de sauvegarde, coffres, serveurs protégés et stratégies** sont transmises au compte de stockage client au fur et à mesure qu’elles sont consignées.
      * Les données relatives au **Stockage** sont transmises au compte de stockage client toutes les 24 heures.
   
    ![Fréquence des Push de données des rapports de la Sauvegarde Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI effectue une [actualisation planifiée une fois par jour](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Vous pouvez effectuer une actualisation manuelle des données dans Power BI pour le pack de contenu.

3. **Combien de temps puis-je conserver les rapports ?** 

   Lors de la configuration du compte de stockage, vous pouvez sélectionner la période de rétention de données de rapports dans le compte de stockage (suivant l’étape 6 de la section Configurer le compte de stockage pour les rapports ci-dessus). En outre, vous pouvez [Analyser les rapports sous Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) et les enregistrer pour allonger la période de rétention en fonction de vos besoins. 

4. **Verrai-je toutes mes données dans des rapports après avoir configuré le compte de stockage ?**

   Toutes les données générées après la **Configuration du compte de stockage** seront transmises au compte de stockage et seront disponibles dans les rapports. Toutefois, **les travaux En cours ne sont pas transmis** pour la création de rapports. Le travail est envoyé aux rapports une fois terminé (avec succès ou non).

5. **Si j’ai déjà configuré le compte de stockage pour afficher les rapports, puis-je modifier la configuration afin d’utiliser un autre compte de stockage ?** 

   Oui, vous pouvez modifier la configuration de façon à pointer vers un autre compte de stockage. Il est conseillé d’utiliser le compte de stockage qui vient d’être configuré pour se connecter au pack de contenu de la Sauvegarde Azure. En outre, une fois qu’un autre compte de stockage est configuré, les nouvelles données arrivent dans ce compte de stockage. Mais les données plus anciennes (datant d’avant la modification de la configuration) restent dans le compte de stockage précédent.

6. **Puis-je afficher des rapports sur différents coffres et différents abonnements ?** 

   Oui, vous pouvez configurer le même compte de stockage sur différents coffres pour afficher des rapports multicoffres. Vous pouvez également configurer le même compte de stockage pour des coffres de différents abonnements. Vous pourrez ensuite utiliser ce compte de stockage pour vous connecter au pack de contenu de la Sauvegarde Azure dans Power BI afin d’afficher les rapports. Cependant, le compte de stockage sélectionné doit se trouver dans la même région que le coffre Recovery Services.
   
## <a name="troubleshooting-errors"></a>Résolution des erreurs
| Détails de l’erreur | Résolution : |
| --- | --- |
| Une fois que vous avez configuré le compte de stockage pour les rapports de sauvegarde, **Compte de stockage** indique toujours **Non configuré**. | Si vous avez configuré correctement le compte de stockage, vos données de rapport arrivent malgré ce problème. Pour résoudre ce problème, accédez au portail Azure > Tous les services > Paramètres de diagnostic > Coffre Recovery Services > Modifier le paramètre. Supprimez le paramètre configuré et créez un paramètre dans le même panneau. Cette fois-ci, définissez le champ **Nom** sur **service**. Le compte de stockage configuré doit s’afficher. |
|Après avoir importé le pack de contenu de la Sauvegarde Azure dans Power BI, l’erreur **404 : Le conteneur est introuvable** s’affiche. | Comme indiqué dans ce document, après avoir configuré les rapports dans le coffre Recovery Services, vous devez attendre 24 heures pour les afficher correctement dans Power BI. Si vous essayez d’accéder aux rapports avant le terme de ce délai, vous obtenez cette erreur, car il manque des données pour afficher des rapports valides. |

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré le compte de stockage et importé le pack de contenu de la Sauvegarde Azure, la prochaine étape consiste à personnaliser ces rapports et à utiliser le modèle de données de rapports pour créer des rapports. Pour plus d’informations, consultez les articles suivants.

* [Utiliser le modèle de données de rapports de la Sauvegarde Azure](backup-azure-reports-data-model.md)
* [Filtrer les rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Créer des rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

