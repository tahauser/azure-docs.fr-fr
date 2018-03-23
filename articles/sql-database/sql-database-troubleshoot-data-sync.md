---
title: Résoudre les problèmes liés à Azure SQL Data Sync (Préversion) | Microsoft Docs
description: Découvrez comment résoudre les problèmes courants liés à Azure SQL Data Sync (Préversion).
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: c174f5120ba2e5bf8018cce0f0e34c1fc3f8eb3f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Résoudre les problèmes liés à SQL Data Sync (préversion)

Cet article explique comment résoudre les problèmes connus liés à Azure SQL Data Sync (Préversion). S’il existe un moyen de résoudre un problème, celui-ci est décrit ici.

Pour obtenir une vue d’ensemble de SQL Data Sync (préversion), consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problèmes de synchronisation

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>La synchronisation de bases de données locales associées à l’agent client échoue dans l’interface utilisateur du portail

#### <a name="description-and-symptoms"></a>Description et symptômes

La synchronisation de bases de données locales associées à l’agent échoue dans l’interface utilisateur du portail SQL Data Sync (Préversion). Sur l’ordinateur local qui exécute l’agent, le journal des événements affiche des erreurs System.IO.IOException. Ces erreurs indiquent que l’espace disponible sur le disque est insuffisant.

#### <a name="resolution"></a>Résolution :

Libérez de l’espace sur le lecteur où se trouve le répertoire %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Mon groupe de synchronisation est bloqué à l’état de traitement

#### <a name="description-and-symptoms"></a>Description et symptômes

Un groupe de synchronisation de SQL Data Sync (Préversion) est en cours de traitement depuis un certain temps. Il ne répond pas à la commande d’**arrêt** et aucune nouvelle entrée n’apparaît dans les journaux.

#### <a name="cause"></a>Cause :

L’une des conditions suivantes peut provoquer le blocage d’un groupe de synchronisation sur l’état de traitement :

-   **L’agent client est hors connexion**. Vérifiez que l’agent client est en ligne, puis réessayez.

-   **L’agent client est désinstallé ou manquant**. Si l’agent client est désinstallé ou manquant :

    1. Accédez au dossier d’installation de SQL Data Sync (Préversion) et supprimez le fichier XML de l’agent, si ce fichier existe.
    2. Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.

-   **Le service SQL Data Sync est arrêté**.

    1. Dans le menu **Démarrer**, recherchez **Services**.
    2. Dans les résultats de la recherche, sélectionnez **Services**.
    3. Recherchez le service **SQL Data Sync (Préversion)**.
    4. Si l’état du service est **Arrêté**, cliquez avec le bouton droit sur le nom du service, puis sélectionnez **Démarrer**.

#### <a name="resolution"></a>Résolution :

Si les informations précédentes ne permettent pas à votre groupe de synchronisation de quitter l’état de traitement, le Support Microsoft peut réinitialiser son état. Pour demander la réinitialisation de l’état de votre groupe de synchronisation, créez un billet dans le [Forum Microsoft Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Dans le billet, indiquez votre ID d’abonnement et l’ID du groupe de synchronisation à réinitialiser. Un ingénieur du Support Microsoft répondra à votre billet et vous préviendra dès que l’état aura été réinitialisé.

### <a name="i-see-erroneous-data-in-my-tables"></a>Mes tables contiennent des données erronées

#### <a name="description-and-symptoms"></a>Description et symptômes

Si des tables portant le même nom mais provenant de schémas de base de données différents sont incluses dans une synchronisation, des données erronées apparaissent dans les tables à l’issue de la synchronisation.

#### <a name="cause"></a>Cause :

Le processus de déploiement de SQL Data Sync (Préversion) utilise les mêmes tables de suivi pour les tables qui portent le même nom mais proviennent de schémas différents. Par conséquent, les modifications des deux tables sont répercutées dans la même table de suivi. Et cela génère des modifications de données erronées pendant la synchronisation.

#### <a name="resolution"></a>Résolution :

Assurez-vous que les noms des tables impliquées dans une synchronisation sont différents, même si elles appartiennent à des schémas de base de données différents.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Des données de clé primaire incohérentes s’affichent après une synchronisation réussie

#### <a name="description-and-symptoms"></a>Description et symptômes

Une synchronisation est signalée comme réussie, et le journal n’affiche aucune ligne Échec ou Ignoré, mais vous constatez la présence de données de clé primaire incohérentes dans les bases de données du groupe de synchronisation.

#### <a name="cause"></a>Cause :

Ce résultat est lié à la conception. Toute modification d’une colonne de clé primaire génère des données incohérentes sur les lignes où la clé primaire a été modifiée.

#### <a name="resolution"></a>Résolution :

Pour éviter ce problème, vérifiez qu’aucune donnée dans une colonne de clé primaire n’est modifiée.

Pour résoudre ce problème, supprimez la ligne contenant les données incohérentes sur tous les points de terminaison du groupe de synchronisation. Puis réinsérez la ligne.

### <a name="i-see-a-significant-degradation-in-performance"></a>Je constate une dégradation significative des performances

#### <a name="description-and-symptoms"></a>Description et symptômes

Les performances se dégradent considérablement, jusqu’à ce que vous ne soyez même plus en mesure d’ouvrir l’interface utilisateur de Data Sync.

#### <a name="cause"></a>Cause :

Le problème est probablement lié à une boucle de synchronisation. Une boucle de synchronisation se produit lorsqu’une synchronisation initiée par un groupe de synchronisation A déclenche une synchronisation par un groupe de synchronisation B, qui déclenche à son tour une synchronisation par le groupe de synchronisation A. La réalité est parfois plus complexe, puisque plus de deux groupes de synchronisation peuvent être impliqués dans la boucle. Le problème est lié au déclenchement circulaire de la synchronisation, lui-même provoqué par le chevauchement des groupes de synchronisation.

#### <a name="resolution"></a>Résolution :

Le meilleur correctif est la prévention. Vérifiez l’absence de références circulaires dans vos groupes de synchronisation. Lorsqu’une ligne est synchronisée par un groupe de synchronisation, elle ne peut pas l’être par un autre groupe de synchronisation.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Le message suivant s’affiche : « Impossible d’insérer la valeur NULL dans la colonne \<colonne\>. Cette colonne n’accepte pas les valeurs NULL. » Que signifie cette erreur et comment puis-je la corriger ? 
Ce message d’erreur indique que l’un des deux problèmes suivants est survenu :
-  Une table ne possède pas de clé primaire. Pour résoudre ce problème, ajoutez une clé primaire à toutes les tables que vous synchronisez.
-  Votre instruction CREATE INDEX contient une clause WHERE. Data Sync (Préversion) ne traite pas cette condition. Pour résoudre ce problème, supprimez la clause WHERE ou apportez manuellement les modifications à toutes les bases de données. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Comment Data Sync (Préversion) traite-t-il les références circulaires ? Autrement dit, lorsque les mêmes données sont synchronisées dans plusieurs groupes de synchronisation et changent constamment en conséquence ?
Data Sync (Préversion) ne traite pas les références circulaires. Veillez à les éviter. 

## <a name="client-agent-issues"></a>Problèmes liés à l’agent client

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>L’installation, la désinstallation ou la réparation de l’agent client échoue

#### <a name="cause"></a>Cause :

Cet échec peut avoir de nombreuses causes. Pour déterminer la cause spécifique de cet échec, consultez les journaux.

#### <a name="resolution"></a>Résolution :

Pour rechercher la cause spécifique de l’échec rencontré, générez et consultez les journaux Windows Installer. Vous pouvez activer la journalisation à partir d’une invite de commandes. Par exemple, si le fichier AgentServiceSetup.msi téléchargé est LocalAgentHost.msi, générez et examinez les fichiers journaux à l’aide des lignes de commande suivantes :

-   Pour les installations : `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Pour les désinstallations : `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Vous pouvez également activer la journalisation pour toutes les installations effectuées par Windows Installer. L’article de la Base de connaissances Microsoft [Guide pratique pour activer la journalisation de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fournit une solution en un clic pour activer la journalisation pour Windows Installer. Il indique également l’emplacement des journaux.

### <a name="my-client-agent-doesnt-work"></a>Mon agent client ne fonctionne pas

#### <a name="description-and-symptoms"></a>Description et symptômes

Les messages suivants s’affichent lorsque vous essayez d’utiliser l’agent client :

« Échec de la synchronisation avec l’exception Une erreur s’est produite durant la tentative de désérialisation du paramètre www.microsoft.com/.../05:GetBatchInfoResult. Consultez InnerException pour obtenir plus d’informations. »

« Message de l’exception interne : Le type « Microsoft.Synchronization.ChangeBatch » est un type de collection non valide, car il n’a pas de constructeur par défaut. »

#### <a name="cause"></a>Cause :

Il s’agit d’un problème connu avec l’installation de SQL Data Sync (Préversion). L’affichage de ce message est probablement dû à l’une des causes suivantes :

-   Vous exécutez Windows 8 Developer Preview.
-   .NET Framework 4.5 est installé.

#### <a name="resolution"></a>Résolution :

Veillez à installer l’agent client sur un ordinateur qui n’exécute pas Windows 8 Developer Preview et sur lequel .NET Framework 4.5 n’est pas installé.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Mon agent client ne fonctionne pas après l’annulation de la désinstallation

#### <a name="description-and-symptoms"></a>Description et symptômes

L’agent client ne fonctionne pas, même après l’annulation de sa désinstallation.

#### <a name="cause"></a>Cause :

Ce problème survient car l’agent client SQL Data Sync (Préversion) ne stocke pas les informations d’identification.

#### <a name="resolution"></a>Résolution :

Vous pouvez essayer les deux solutions suivantes :

-   Utilisez services.msc afin de réentrer les informations d’identification pour l’agent client.
-   Désinstallez cet agent client, puis installez-en un nouveau. Téléchargez et installez l’agent client le plus récent à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Ma base de données n’est pas répertoriée dans la liste des agents

#### <a name="description-and-symptoms"></a>Description et symptômes

Lorsque vous essayez d’ajouter une base de données SQL Server existante à un groupe de synchronisation, la base de données n’apparaît pas dans la liste des agents.

#### <a name="cause"></a>Cause :

Ce problème peut avoir les causes suivantes :

-   L’agent client et le groupe de synchronisation se trouvent dans des centres de données différents.
-   La liste des bases de données de l’agent client n’est pas à jour.

#### <a name="resolution"></a>Résolution :

La résolution dépend de la cause.

- **L’agent client et le groupe de synchronisation se trouvent dans des centres de données différents**

    L’agent client et le groupe de synchronisation doivent se trouver dans le même centre de données. Pour cela, vous disposez de deux options :

    -   Créez un agent dans le centre de données où se trouve le groupe de synchronisation. Puis inscrivez la base de données auprès de cet agent.
    -   Supprimez le groupe de synchronisation actuel. Recréez ensuite le groupe de synchronisation dans le centre de données où se trouve l’agent.

- **La liste des bases de données de l’agent client n’est pas à jour**

    Arrêtez, puis redémarrez le service agent client.

    L’agent local télécharge la liste des bases de données associées uniquement lors du premier envoi de la clé de l’agent. Il ne la télécharge pas lors des envois suivants. Les bases de données inscrites pendant le déplacement d’un agent ne sont pas visibles sur l’instance d’origine de l’agent.

### <a name="client-agent-doesnt-start-error-1069"></a>L’agent client ne démarre pas (Erreur 1069)

#### <a name="description-and-symptoms"></a>Description et symptômes

Vous découvrez que l’agent n’est pas exécuté sur un ordinateur qui héberge SQL Server. Lorsque vous essayez de démarrer manuellement l’agent, une boîte de dialogue affiche le message « Erreur 1069 : L’échec d’une ouverture de session a empêché le démarrage du service ».

![Boîte de dialogue de l’erreur 1069 Data Sync](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Cause :

Cette erreur peut être due au fait que le mot de passe du serveur local a changé depuis que vous avez créé l’agent et son mot de passe.

#### <a name="resolution"></a>Résolution :

Remplacez le mot de passe de l’agent par le mot de passe actuel du serveur :

1. Recherchez le service Préversion de l’agent client SQL Data Sync (Préversion).  
    a. Sélectionnez **Démarrer**.  
    b. Dans la zone de recherche, entrez **services.msc**.  
    c. Dans les résultats de la recherche, sélectionnez **Services**.  
    d. Dans la fenêtre **Services**, faites défiler jusqu’à l’entrée **Aperçu de l’agent SQL Data Sync (préversion)**.  
2. Cliquez avec le bouton droit sur **Aperçu de l’agent SQL Data Sync (Préversion)**, puis sélectionnez **Arrêter**.
3. Cliquez avec le bouton droit sur **Aperçu de l’agent SQL Data Sync (Préversion)**, puis sélectionnez **Propriétés**.
4. Dans **Propriétés de l’Aperçu de l’agent SQL Data Sync (Préversion)**, sélectionnez l’onglet **Connexion**.
5. Dans la zone **Mot de passe**, entrez votre mot de passe.
6. Dans la zone **Confirmer le mot de passe**, entrez de nouveau votre mot de passe.
7. Sélectionnez **Apply** (Appliquer), puis **OK**.
8. Dans la fenêtre **Services**, cliquez avec le bouton droit sur le service **Aperçu de l’agent SQL Data Sync (Préversion)**, puis cliquez sur **Démarrer**.
9. Fermez la fenêtre **Services**.

### <a name="i-cant-submit-the-agent-key"></a>Je ne parviens pas à envoyer la clé d’un agent

#### <a name="description-and-symptoms"></a>Description et symptômes

Une fois que vous avez créé ou recréé la clé d’un agent, vous essayez d’envoyer cette clé par le biais de l’application SqlAzureDataSyncAgent. Mais l’envoi échoue.

![Boîte de dialogue d’erreur de synchronisation - Impossible d’envoyer la clé d’agent](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Avant de continuer, vérifiez que les conditions suivantes sont réunies :

-   Le service Windows SQL Data Sync (Préversion) est en cours d’exécution.  
-   Le compte de service pour le service Windows SQL Data Sync (Préversion) a accès au réseau.    
-   L’agent client peut contacter le service Localisateur. Vérifiez que la clé de Registre suivante possède la valeur « https://locator.sync.azure.com/LocatorServiceApi.svc » :  
    -   Sur un ordinateur x86 :`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   Sur un ordinateur x64 :`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Cause :

La clé d’agent identifie de façon unique chaque agent local. La clé doit remplir deux conditions :

-   La clé de l’agent client sur le serveur SQL Data Sync (préversion) et l’ordinateur local doivent être identiques.
-   La clé de l’agent client ne peut être utilisée qu’une seule fois.

#### <a name="resolution"></a>Résolution :

Si votre agent ne fonctionne pas, cela signifie que l’une de ces conditions (ou les deux) n’est pas remplie. Pour que votre agent fonctionne de nouveau :

1. Générez une nouvelle clé.
2. Appliquez la nouvelle clé à l’agent.

Pour appliquer la nouvelle clé à l’agent :

1. Dans l’Explorateur de fichiers, accédez au répertoire d’installation de votre agent. Le répertoire d’installation par défaut est C:\\Program Files (x86)\\Microsoft SQL Data Sync.
2. Double-cliquez sur le sous-répertoire bin.
3. Ouvrez l’application SqlAzureDataSyncAgent.
4. Sélectionnez **Envoyer la clé d’agent**.
5. Collez la clé placée dans votre Presse-papiers à l’espace prévu à cet effet.
6. Sélectionnez **OK**.
7. Fermez le programme.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>L’agent client ne peut pas être supprimé du portail si la base de données locale associée est inaccessible

#### <a name="description-and-symptoms"></a>Description et symptômes

Si un point de terminaison local (autrement dit, une base de données) inscrite auprès d’un agent client SQL Data Sync (Préversion) devient inaccessible, l’agent client ne peut pas être supprimé.

#### <a name="cause"></a>Cause :

L’agent local ne peut pas être supprimé, car la base de données inaccessible est encore inscrite auprès de l’agent. Quand vous tentez de supprimer l’agent, le processus de suppression essaie d’atteindre la base de données, et il échoue.

#### <a name="resolution"></a>Résolution :

Utilisez « Forcer la suppression » pour supprimer la base de données inaccessible.

> [!NOTE]
> S’il reste des tables de métadonnées de synchronisation après une opération « Forcer la suppression », utilisez deprovisioningutil.exe pour les nettoyer.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>L’application locale Agent de synchronisation ne peut pas se connecter au service de synchronisation local

#### <a name="resolution"></a>Résolution :

Essayez les étapes suivantes :

1. Quittez l’application.  
2. Ouvrez le panneau Services de composants.  
    a. Dans la zone de recherche de la barre des tâches, entrez **services.msc**.  
    b. Dans les résultats de la recherche, double-cliquez sur **Services**.  
3. Arrêtez le service **Aperçu de SQL Data Sync (Préversion)**.
4. Redémarrez le service **Aperçu de SQL Data Sync (Préversion)**.  
5. Rouvrez l’application.

## <a name="setup-and-maintenance-issues"></a>Problèmes d’installation et de maintenance

### <a name="i-get-a-disk-out-of-space-message"></a>Je reçois un message « Espace disque insuffisant »

#### <a name="cause"></a>Cause :

Le message « Espace disque insuffisant » peut s’afficher s’il reste des fichiers à supprimer. Ce problème peut être dû à un logiciel antivirus. Il peut également survenir lorsque des fichiers sont ouverts pendant une tentative de suppression.

#### <a name="resolution"></a>Résolution :

Supprimez manuellement les fichiers de synchronisation situés dans le dossier %Temp% (`del \*sync\* /s`). Puis supprimez les sous-répertoires du dossier %Temp%.

> [!IMPORTANT]
> Ne supprimez aucun fichier tant que la synchronisation est en cours.

### <a name="i-cant-delete-my-sync-group"></a>Je ne parviens pas à supprimer mon groupe de synchronisation

#### <a name="description-and-symptoms"></a>Description et symptômes

Votre tentative de suppression d’un groupe de synchronisation échoue.

#### <a name="causes"></a>Causes

La suppression d’un groupe de synchronisation peut échouer dans les situations suivantes :

-   L’agent client est hors connexion
-   L’agent client est désinstallé ou manquant 
-   Une base de données est hors connexion 
-   Le groupe de synchronisation est en cours de déploiement ou de synchronisation. 

#### <a name="resolution"></a>Résolution :

Pour résoudre l’échec de suppression d’un groupe de synchronisation :

-   Vérifiez que l’agent client est en ligne, puis réessayez.
-   Si l’agent client est désinstallé ou manquant :  
    a. Accédez au dossier d’installation de SQL Data Sync (Préversion) et supprimez le fichier XML de l’agent, si ce fichier existe.  
    b. Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.
-   Vérifiez que le service SQL Data Sync (Préversion) est en cours d’exécution :  
    a. Dans le menu **Démarrer**, recherchez **Services**.  
    b. Dans les résultats de la recherche, sélectionnez **Services**.  
    c. Recherchez le service **SQL Data Sync (Préversion)**.  
    d. Si l’état du service est **Arrêté**, cliquez avec le bouton droit sur le nom du service, puis sélectionnez **Démarrer**.
-   Vérifiez que vos bases de données SQL et SQL Server sont toutes en ligne.
-   Attendez que le processus de déploiement ou de synchronisation se termine, puis essayez à nouveau de supprimer le groupe de synchronisation.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Je ne peux pas annuler l’inscription d’une base de données SQL Server locale

#### <a name="cause"></a>Cause :

Vous essayez probablement d’annuler l’inscription d’une base de données qui a déjà été supprimée.

#### <a name="resolution"></a>Résolution :

Pour annuler l’inscription d’une base de données SQL Server locale, sélectionnez la base de données, puis sélectionnez **Forcer la suppression**.

Si cette opération ne permet pas de supprimer la base de données du groupe de synchronisation :

1. Arrêtez le service hôte de l’agent client, puis redémarrez-le :  
    a. Sélectionnez le menu **Démarrer**.  
    b. Dans la zone de recherche, entrez **services.msc**.  
    c. Dans la section **Programmes** du volet des résultats de la recherche, double-cliquez sur **Services**.  
    d. Cliquez avec le bouton droit sur le service **SQL Data Sync (Préversion)**.  
    e. Si le service est en cours d’exécution, arrêtez-le.  
    f. Cliquez avec le bouton droit sur le service, puis sélectionnez **Démarrer**.  
    g. Vérifiez si la base de données est toujours inscrite. Si elle n’est plus inscrite, vous avez terminé. Sinon, passez à l’étape suivante.
2. Ouvrez l’application de l’agent client (SqlAzureDataSyncAgent).
3. Sélectionnez **Modifier les informations d’identification**, puis entrez les informations d’identification de la base de données.
4. Effectuez l’annulation de l’inscription.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Je ne dispose pas des privilèges suffisants pour démarrer les services système

#### <a name="cause"></a>Cause :

Cette erreur se produit dans deux situations :
-   Le nom d’utilisateur et/ou le mot de passe sont incorrects.
-   Le compte d’utilisateur spécifié ne dispose pas des privilèges suffisants pour se connecter en tant que service.

#### <a name="resolution"></a>Résolution :

Accordez des informations d’identification « Ouvrir une session en tant que service » au compte d’utilisateur :

1. Accédez à **Démarrer** > **Panneau de configuration** > **Outils d’administration** > **Stratégie de sécurité locale** > **Stratégie locale** > **Gestion des droits de l’utilisateur**.
2. Sélectionnez **Ouvrir une session en tant que service**.
3. Dans la boîte de dialogue **Propriétés**, ajoutez le compte d’utilisateur.
4. Sélectionnez **Apply** (Appliquer), puis **OK**.
5. Fermez toutes les fenêtres.

### <a name="a-database-has-an-out-of-date-status"></a>Une base de données a un état « Obsolète »

#### <a name="cause"></a>Cause :

SQL Data Sync (Préversion) supprime du service les bases de données qui sont hors connexion depuis 45 jours ou plus (délai calculé à partir du moment où la base de données a basculé hors connexion). Si une base de données est hors connexion pendant au moins 45 jours, puis qu’elle rebascule en ligne, son état est défini sur **Obsolète**.

#### <a name="resolution"></a>Résolution :

Pour éviter l’état **Obsolète**, veillez à ce qu’aucune de vos bases de données ne reste hors ligne pendant 45 jours ou plus.

Si l’état d’une base de données est **Obsolète** :

1. Supprimez la base de données dont l’état est **Obsolète** du groupe de synchronisation.
2. Réajouter la base de données dans le groupe de synchronisation.

> [!WARNING]
> Vous perdez toutes les modifications apportées à cette base de données pendant qu’elle était hors connexion.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Un groupe de synchronisation a un état « Obsolète »

#### <a name="cause"></a>Cause :

Si une ou plusieurs modifications ne sont pas appliquées pendant la période de rétention de 45 jours, un groupe de synchronisation peut devenir obsolète.

#### <a name="resolution"></a>Résolution :

Pour éviter que l’état d’un groupe de synchronisation ne devienne **Obsolète**, examinez régulièrement les résultats de vos travaux de synchronisation dans la visionneuse de l’historique. Recherchez et résolvez les problèmes liés aux modifications qui ne sont pas appliquées.

Si l’état d’un groupe de synchronisation est **Obsolète**, supprimez le groupe et recréez-le.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Impossible de supprimer un groupe de synchronisation dans les trois minutes qui suivent la désinstallation ou l’arrêt de l’agent

#### <a name="description-and-symptoms"></a>Description et symptômes

Vous ne pouvez pas supprimer un groupe de synchronisation dans les trois minutes qui suivent la désinstallation ou l’arrêt de l’agent client SQL Data Sync (Préversion) associé.

#### <a name="resolution"></a>Résolution :

1. Supprimez un groupe de synchronisation pendant que les agents de synchronisation associés sont en ligne (recommandé).
2. Si l’agent est hors connexion mais installé, mettez-le en ligne sur l’ordinateur local. Attendez que l’état de l’agent apparaisse comme **En ligne** sur le portail SQL Data Sync (Préversion). Puis supprimez le groupe de synchronisation.
3. Si l’agent est hors connexion parce qu’il a été désinstallé :  
    a.  Accédez au dossier d’installation de SQL Data Sync (Préversion) et supprimez le fichier XML de l’agent, si ce fichier existe.  
    b.  Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.  
    c. Essayez de supprimer le groupe de synchronisation.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Que se passe-t-il quand je restaure une base de données perdue ou endommagée ?

Si vous restaurez une base de données perdue ou endommagée à partir d’une sauvegarde, un problème de non-convergence des données peut survenir dans les groupes de synchronisation auxquels la base de données appartient.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Sync (préversion), consultez :

-   [Synchroniser des données entre plusieurs bases de données sur site et cloud avec Azure SQL Data Sync (préversion)](sql-database-sync-data.md)  
-   [Configurer Azure SQL Data Sync (Préversion)](sql-database-get-started-sql-data-sync.md)  
-   [Bonnes pratiques pour Azure SQL Data Sync (Préversion)](sql-database-best-practices-data-sync.md)  
-   [Surveiller Azure SQL Data Sync (Préversion) avec OMS Log Analytics](sql-database-sync-monitor-oms.md)  
-   Exemples PowerShell complets sur la configuration de SQL Data Sync (préversion) :  
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Télécharger la documentation de l’API REST de SQL Data Sync (préversion)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
