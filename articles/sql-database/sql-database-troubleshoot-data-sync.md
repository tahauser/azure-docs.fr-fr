---
title: "Résoudre les problèmes liés à Azure SQL Data Sync | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes courants liés à Azure SQL Data Sync."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 8f1d2bf95c0e34f97e7075c062ea887f33f9785c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Résoudre les problèmes liés à SQL Data Sync (préversion)

Cet article explique comment résoudre les problèmes connus de l’équipe SQL Data Sync (Préversion). S’il existe une solution de contournement pour un problème, elle est décrite ici.

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](sql-database-sync-data.md).

## <a name="troubleshooting-sync"></a>Résolution des problèmes de synchronisation

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>La synchronisation échoue dans le portail de l’interface utilisateur pour des bases de données locales associées à l’agent client

#### <a name="description-and-symptoms"></a>Description et symptômes

La synchronisation échoue sur l’interface utilisateur du portail SQL Data Sync (Préversion) pour des bases de données locales associées à l’agent. Sur l’ordinateur local exécutant l’agent, vous constatez la présence d’erreurs System.IO.IOException dans le journal des événements indiquant que l’espace est insuffisant sur le disque.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Créez davantage d’espace sur le lecteur sur lequel réside le répertoire % TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Mon groupe de synchronisation est bloqué à l’état de traitement

#### <a name="description-and-symptoms"></a>Description et symptômes

Un groupe de synchronisation dans SQL Data Sync (Préversion) est à l’état de traitement depuis longtemps, il ne répond pas à la commande d’arrêt, et les journaux n’affichent aucune nouvelle entrée.

#### <a name="causes"></a>Causes

L’une des conditions suivantes peut provoquer le blocage d’un groupe de synchronisation à l’état de traitement.

-   **L’agent client est hors connexion.** Vérifiez que l’agent client est en ligne, puis réessayez.

-   **L’agent client est désinstallé ou manquant.** Si l’agent client est désinstallé ou manquant :

    1. Supprimez le fichier XML de l’agent du dossier d’installation de SQL Data Sync (Préversion) si le fichier existe.

    2. Installez l’agent sur le même ordinateur local ou sur un autre ordinateur local. Ensuite, envoyez à partir du portail la clé de l’agent générée pour l’agent qui apparaît hors connexion.

-   **Le service SQL Data Sync (Préversion) est arrêté.**

    1. Dans le menu **Démarrer**, recherchez Services.

    2. Dans les résultats de recherche, cliquez sur Services.

    3. Recherchez le service **SQL Data Sync (Préversion)**.

    4. Si l’état du service est **Arrêté**, cliquez sur le nom du service et sélectionnez **Démarrer** dans le menu déroulant.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Si vous ne parvenez pas à résoudre le problème, l’état de votre groupe de synchronisation peut être réinitialisé par le support technique de Microsoft. Pour faire réinitialiser l’état, créez un billet de forum sur le [Forum Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted) et incluez votre ID d’abonnement et l’ID du groupe de synchronisation qui doit être réinitialisé. Un ingénieur du support Microsoft répondra à votre billet et vous fera savoir quand l’état aura été réinitialisé.

### <a name="i-see-erroneous-data-in-my-tables"></a>Mes tables contiennent des données erronées

#### <a name="description-and-symptoms"></a>Description et symptômes

Si des tables portant le même nom mais provenant de schémas différents dans une base de données sont impliquées dans la synchronisation, vous voyez des données erronées dans ces tables après la synchronisation.

#### <a name="cause-and-fix"></a>Cause et solution

Le processus de provisionnement SQL Data Sync (Préversion) utilise les mêmes tables de suivi pour les tables portant le même nom mais se trouvant dans des schémas différents. Ainsi, les modifications des deux tables sont répercutées dans la même table de suivi, et ce comportement provoque des modifications de données erronées pendant la synchronisation.

#### <a name="resolution-or-workaround"></a>Résolution ou solution de contournement

Assurez-vous que les noms des tables impliquées dans la synchronisation sont différents, même si elles appartiennent à des schémas différents.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Des données de clé primaire sont incohérentes après une synchronisation réussie

#### <a name="description-and-symptoms"></a>Description et symptômes

Après une synchronisation signalée comme ayant réussi et où le journal n’affiche aucune ligne Échec ou Ignoré, vous constatez que des données de clé primaire sont incohérentes parmi les bases de données du groupe de synchronisation.

#### <a name="cause"></a>Cause :

Ce comportement est normal. Toute modification d’une colonne de clé primaire génère des données incohérentes sur les lignes où la clé primaire a été modifiée.

#### <a name="resolution-or-workaround"></a>Résolution ou solution de contournement

Pour éviter ce problème, vérifiez qu’aucune donnée dans une colonne de clé primaire n’est modifiée.

Pour résoudre ce problème une fois qu’il s’est produit, vous devez supprimer la ligne concernée de tous les points de terminaison dans le groupe de synchronisation, puis réinsérer la ligne.

### <a name="i-see-a-significant-degradation-in-performance"></a>Je constate une dégradation significative des performances

#### <a name="description-and-symptoms"></a>Description et symptômes

Les performances se dégradent considérablement, éventuellement jusqu’à ne même plus pouvoir lancer l’interface utilisateur Data Sync.

#### <a name="cause"></a>Cause :

La cause la plus probable est une boucle de synchronisation. Une boucle de synchronisation se produit quand une synchronisation par le groupe de synchronisation A déclenche une synchronisation par le groupe de synchronisation B, qui à son tour déclenche une synchronisation par le groupe de synchronisation A. La situation réelle peut être plus complexe et impliquer plus de deux groupes de synchronisation dans la boucle, mais le facteur déterminant est qu’il existe un déclenchement circulaire de synchronisations dû à un chevauchement des groupes de synchronisation.

#### <a name="resolution-or-workaround"></a>Résolution ou solution de contournement

Le meilleur correctif est la prévention. Vérifiez que vous n’avez pas de références circulaires dans vos groupes de synchronisation. Toute ligne synchronisée par un groupe de synchronisation ne peut pas être synchronisée par un autre groupe de synchronisation.

### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>J’ai reçu un message d’erreur indiquant : « Impossible d’insérer la valeur NULL dans la colonne *colonne*. Cette colonne n’accepte pas les valeurs NULL. » Que signifie cette erreur et comment puis-je la corriger ? 
Ce message d’erreur indique l’un des deux problèmes suivants :
1.  Une table sans clé primaire existe peut-être. Pour résoudre ce problème, ajoutez une clé primaire à toutes les tables que vous synchronisez.
2.  Une clause WHERE existe peut-être dans votre instruction CREATE INDEX. La synchronisation ne gère pas cette condition. Pour résoudre ce problème, supprimez la clause WHERE ou apportez manuellement les modifications à toutes les bases de données. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Comment Data Sync traite-t-il les références circulaires ? Autrement dit, lorsque les mêmes données sont synchronisées dans plusieurs groupes de synchronisation et changent constamment en conséquence ?
Data Sync ne traite pas les références circulaires. Veillez à les éviter. 

## <a name="troubleshooting-the-client-agent"></a>Résolution des problèmes de l’agent client

### <a name="install-uninstall-or-repair-fails"></a>L’installation, la désinstallation ou la réparation échoue

#### <a name="cause"></a>Cause :

L’échec peut avoir de nombreuses causes. Pour déterminer la cause spécifique de cet échec, vous devez consulter les journaux.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Pour rechercher la cause spécifique de l’échec rencontré, vous devez générer et consulter les journaux de Windows Installer. Vous pouvez activer la journalisation à partir de la ligne de commande. Par exemple, supposons que le fichier AgentServiceSetup.msi téléchargé est LocalAgentHost.msi. Générez et examinez les fichiers journaux à l’aide des lignes de commande suivantes :

-   Pour les installations : `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Pour les désinstallations : `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Vous pouvez également activer la journalisation pour toutes les installations effectuées par Windows Installer. L’article de la Base de connaissances Microsoft [Guide pratique pour activer la journalisation de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fournit une solution en un clic pour activer la journalisation pour Windows Installer. Il fournit également l’emplacement de ces journaux.

### <a name="my-client-agent-doesnt-work"></a>Mon agent client ne fonctionne pas

#### <a name="description-and-symptoms"></a>Description et symptômes

Les messages d’erreur suivants s’affichent quand vous essayez d’utiliser l’agent client.

« Échec de la synchronisation avec l’exception Une erreur s’est produite durant la tentative de désérialisation du paramètre www.microsoft.com/.../05:GetBatchInfoResult. Pour plus d’informations, consultez InnerException. »

« Message de l’exception interne : Le type « Microsoft.Synchronization.ChangeBatch » est un type de collection non valide, car il n’a pas de constructeur par défaut. »

#### <a name="cause"></a>Cause :

Cette erreur est due à un problème avec SQL Data Sync (Préversion).

La cause la plus probable de ce problème est les suivante :

-   Vous exécutez Windows 8 Developer Preview, ou

-   Vous avez installé .NET 4.5.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Veillez à installer l’agent client sur un ordinateur qui n’exécute pas Windows 8 Developer Preview, et vérifiez que le .NET Framework 4.5 n’est pas installé.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Mon agent client ne fonctionne pas après l’annulation de la désinstallation

#### <a name="description-and-symptoms"></a>Description et symptômes

L’agent client ne fonctionne pas, alors que vous avez annulé sa désinstallation.

#### <a name="cause"></a>Cause :

Ce problème se produit car l’agent client SQL Data Sync (Préversion) ne stocke pas les informations d’identification.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Il existe deux solutions :

-   Tout d’abord, utilisez services.msc afin de réentrer vos informations d’identification pour l’agent client.

-   Ensuite, désinstallez cet agent client et installez-en un nouveau. Téléchargez et installez l’agent client le plus récent à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Ma base de données n’est pas répertoriée dans la liste déroulante de l’agent

#### <a name="description-and-symptoms"></a>Description et symptômes

Quand vous essayez d’ajouter une base de données SQL Server existante à un groupe de synchronisation, la base de données n’est pas répertoriée dans la liste déroulante.

#### <a name="cause"></a>Cause :

Ce problème peut avoir plusieurs causes :

-   Le groupe de synchronisation et l’agent client se trouvent dans des centres de données différents.

-   La liste des bases de données de l’agent client n’est pas à jour.

#### <a name="solution"></a>Solution

La solution dépend de la cause.

**Le groupe de synchronisation et l’agent client se trouvent dans des centres de données différents**

L’agent client et le groupe de synchronisation doivent se trouver dans le même centre de données. Vous pouvez définir cette configuration en effectuant l’une des opérations suivantes :

-   Créez un nouvel agent dans le même centre de données que le groupe de synchronisation. Ensuite, inscrivez la base de données auprès de cet agent.

-   Supprimez le groupe de synchronisation actuel. Ensuite, recréez-le dans le même centre de données que l’agent.

**La liste des bases de données de l’agent client n’est pas à jour**

Arrêtez, puis redémarrez le service agent client.
L’agent local télécharge la liste des bases de données associées uniquement lors du premier envoi de la clé de l’agent, et non lors des envois suivants. Ainsi, les bases de données inscrites pendant le déplacement d’un agent ne sont pas visibles sur l’instance d’origine de l’agent.

### <a name="client-agent-doesnt-start-error-1069"></a>L’agent client ne démarre pas (Erreur 1069)

#### <a name="description-and-symptoms"></a>Description et symptômes

Vous découvrez que l’agent n’est pas en cours d’exécution sur un ordinateur qui héberge SQL Server. Quand vous essayez de démarrer manuellement l’agent, une boîte de dialogue affiche le message d’erreur « Erreur 1069 : L’échec d’une ouverture de session a empêché le démarrage du service. »

![Boîte de dialogue de l’erreur 1069 Data Sync](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Cause :

Une cause probable de cette erreur est que le mot de passe sur le serveur local a changé depuis que vous avez créé l’agent et que vous lui avez attribué un mot de passe de connexion.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Mettez à jour le mot de passe de l’agent avec votre mot de passe de serveur actuel.

1. Recherchez le service Préversion de l’agent client SQL Data Sync (Préversion).

    a. Cliquez sur **Start**.

    b. Dans la zone de recherche, tapez « services.msc ».

    c. Dans les résultats de recherche, cliquez sur « Services ».

    d. Dans la fenêtre **Services**, faites défiler jusqu’à l’entrée **Aperçu de l’agent SQL Data Sync (préversion)**.

2. Cliquez avec le bouton droit sur l’entrée et sélectionnez **Arrêter**.

3. Cliquez avec le bouton droit sur l’entrée, puis cliquez sur **Propriétés**.

4. Dans la fenêtre **Propriétés de Aperçu de l’agent SQL Data Sync (Préversion)**, cliquez sur l’onglet **Connexion**.

5. Entrez votre mot de passe dans la zone de texte Mot de passe.

6. Confirmez votre mot de passe dans la zone de texte Confirmer le mot de passe.

7. Cliquez sur **Appliquer**, puis sur **OK**.

8. Dans la fenêtre **Services**, cliquez avec le bouton droit sur le service **Aperçu de l’agent SQL Data Sync (Préversion)**, puis cliquez sur **Démarrer**.

9. Fermez la fenêtre **Services**.

### <a name="i-cannot-submit-the-agent-key"></a>Je ne parviens pas à envoyer la clé d’agent

#### <a name="description-and-symptoms"></a>Description et symptômes

Après avoir créé ou recréé une clé pour un agent, vous essayez d’envoyer cette clé par le biais de l’application SqlAzureDataSyncAgent, mais l’envoi échoue.

![Boîte de dialogue d’erreur de synchronisation - Impossible d’envoyer la clé d’agent](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Avant de continuer, vérifiez que les conditions suivantes sont remplies.

-   Le service Windows SQL Data Sync (Préversion) est en cours d’exécution.

-   Le compte de service pour le service Windows SQL Data Sync (Préversion) a accès au réseau.

-   L’agent client peut contacter le service de localisation. Vérifiez que la clé de Registre suivante a la valeur « https://locator.sync.azure.com/LocatorServiceApi.svc »

    -   Sur un ordinateur x86 :`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   Sur un ordinateur x64 :`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Cause :

La clé d’agent identifie de façon unique chaque agent local. Pour fonctionner, la clé doit remplir deux conditions :

-   La clé de l’agent client sur le serveur SQL Data Sync (préversion) et l’ordinateur local doivent être identiques.

-   La clé de l’agent client ne peut être utilisée qu’une seule fois.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Si votre agent ne fonctionne pas, cela signifie que l’une de ces conditions (ou les deux) n’est pas remplie. Pour que votre agent fonctionne de nouveau :

1. Générez une nouvelle clé.

2. Appliquez la nouvelle clé à l’agent.

Pour appliquer la nouvelle clé à l’agent, effectuez les étapes suivantes :

1. Utilisez l’Explorateur de fichiers pour accéder au répertoire d’installation de votre agent. Le répertoire d’installation par défaut est `c:\\program files (x86)\\microsoft sql data sync`.

2. Double-cliquez sur le sous-répertoire `bin`.

3. Lancez l’application `SqlAzureDataSyncAgent`.

4. Cliquez sur **Envoyer la clé d’agent**.

5. Collez la clé à partir de votre Presse-papiers dans l’espace fourni.

6. Cliquez sur **OK**.

7. Fermez le programme.

### <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>L’agent client ne peut pas être supprimé du portail si sa base de données locale associée est inaccessible

#### <a name="description-and-symptoms"></a>Description et symptômes

Si un point de terminaison local (autrement dit, une base de données) inscrite auprès d’un agent client SQL Data Sync (Préversion) devient inaccessible, l’agent client ne peut pas être supprimé.

#### <a name="cause"></a>Cause :

L’agent local ne peut pas être supprimé, car la base de données inaccessible est encore inscrite auprès de l’agent. Quand vous tentez de supprimer l’agent, le processus de suppression essaie d’atteindre la base de données, et il échoue.

#### <a name="resolution-or-workaround"></a>Résolution ou solution de contournement

Utilisez « Forcer la suppression » pour supprimer la base de données inaccessible.

> [!NOTE]
> Si après une opération « Forcer la suppression » il reste des tables de métadonnées de synchronisation, utilisez deprovisioningutil.exe pour les nettoyer.

### <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>L’application locale de l’agent de synchronisation ne peut pas se connecter au service de synchronisation local

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Essayez les étapes suivantes :

1. Quittez l’application.

2. Ouvrez le panneau Services de composants.

    a. Dans la zone de recherche dans la barre des tâches, tapez « services.msc ».

    b. Dans les résultats de recherche, double-cliquez sur « Services ».

3. Arrêtez, puis redémarrez le service « SQL Data Sync (Préversion) ».

4. Redémarrez l’application.

## <a name="troubleshooting-setup-and-maintenance"></a>Résolution des problèmes de configuration et maintenance

### <a name="i-get-a-disk-out-of-space-message"></a>Je reçois un message « Espace disque insuffisant »

#### <a name="cause"></a>Cause :

Le message « Espace disque insuffisant » peut s’afficher quand des fichiers qui devraient être supprimés sont conservés. Cette condition peut se produire en raison d’un logiciel antivirus, ou car des fichiers sont ouverts alors que des opérations de suppression sont tentées.

#### <a name="solution"></a>Solution

La solution consiste à supprimer manuellement les fichiers de synchronisation sous `%temp%` (`del \*sync\* /s`), puis à supprimer également les sous-répertoires.

> [!IMPORTANT]
> Attendez que la synchronisation soit terminée avant de supprimer des fichiers.

### <a name="i-cannot-delete-my-sync-group"></a>Je ne parviens pas à supprimer mon groupe de synchronisation

#### <a name="description-and-symptoms"></a>Description et symptômes

Votre tentative de suppression d’un groupe de synchronisation échoue.

#### <a name="causes"></a>Causes

Chacun des points suivants peut provoquer l’échec de la suppression d’un groupe de synchronisation.

-   L’agent client est hors connexion

-   L’agent client est désinstallé ou manquant 

-   Une base de données est hors connexion 

-   Le groupe de synchronisation est en cours de provisionnement ou de synchronisation 

#### <a name="solutions"></a>Solutions

Pour éviter l’échec de suppression de groupe de synchronisation, vérifiez les points suivants :

-   Vérifiez que l’agent client est en ligne, puis réessayez.

-   Si l’agent client est désinstallé ou manquant :

    a. Supprimez le fichier XML de l’agent du dossier d’installation de SQL Data Sync (Préversion) si le fichier existe.

    b. Installez l’agent sur le même ordinateur local ou sur un autre ordinateur local, puis envoyez à partir du portail la clé de l’agent générée pour l’agent qui apparaît hors connexion.

-   **Le service SQL Data Sync (Préversion) est arrêté.**

    a. Dans le menu **Démarrer**, recherchez Services.

    b. Dans les résultats de recherche, cliquez sur Services.

    c. Recherchez le service **SQL Data Sync (Préversion)**.

    d. Si l’état du service est **Arrêté**, cliquez sur le nom du service et sélectionnez **Démarrer** dans le menu déroulant.

-   Vérifiez que vos bases de données SQL Server et SQL Database sont toutes en ligne.

-   Attendez que le processus de provisionnement ou de synchronisation soit terminé. Ensuite, réessayez de supprimer le groupe de synchronisation.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Je ne peux pas annuler l’inscription d’une base de données SQL Server locale

#### <a name="cause"></a>Cause :

Il est très probable que vous essayez d’annuler l’inscription d’une base de données qui a déjà été supprimée.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Pour annuler l’inscription d’une base de données SQL Server locale, sélectionnez la base de données, puis cliquez sur **Forcer la suppression**.

Si cette opération ne supprime pas la base de données du groupe de synchronisation, effectuez les étapes suivantes :

1. Arrêtez, puis redémarrez le service hôte de l’agent client.

    a. Cliquez sur le menu Démarrer.

    b. Dans la zone de recherche, entrez *services.msc*.

    c. Dans la section Programmes du volet de résultats, double-cliquez sur **Services**.

    d. Recherchez et cliquez avec le bouton droit sur le service **SQL Data Sync (Préversion)**.

    e. Si le service est en cours d’exécution, arrêtez-le.

    f. Cliquez dessus avec le bouton droit et sélectionnez **Modifier**.

    g. Vérifiez si la base de données n’est plus inscrite. Si elle n’est plus inscrite, vous avez terminé. Sinon, passez à l’étape suivante.

2. Ouvrez l’application de l’agent client (SqlAzureDataSyncAgent).

3. Cliquez sur **Modifier les informations d’identification** et fournissez les informations d’identification pour la base de données afin qu’elle soit accessible.

4. Effectuez l’annulation de l’inscription.

### <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Je n’ai pas de privilèges suffisants pour démarrer les services système

#### <a name="cause"></a>Cause :

Cette erreur se produit dans deux situations :

-   Le nom d’utilisateur et/ou le mot de passe sont incorrects.

-   Le compte d’utilisateur spécifié ne dispose pas des privilèges suffisants pour se connecter en tant que service.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Accordez des informations d’identification « Ouvrir une session en tant que service » au compte d’utilisateur.

1. Accédez à **Démarrer | Panneau de configuration | Outils d’administration |  Stratégie de sécurité locale | Stratégie locale | Gestion des droits utilisateur**.

2. Recherchez et cliquez sur l’entrée **Ouvrir une session en tant que service**.

3. Ajoutez le compte d’utilisateur dans la boîte de dialogue **Propriétés de** **Ouvrir une session en tant que service**.

4. Cliquez sur **Appliquer** puis **OK**.

5. Fermez les fenêtres.

### <a name="a-database-has-an-out-of-date-status"></a>Une base de données a un état « Obsolète »

#### <a name="cause"></a>Cause :

SQL Data Sync (Préversion) supprime du service les bases de données qui sont hors connexion depuis 45 jours ou plus (délai calculé à partir du moment où la base de données a basculé hors connexion). Si une base de données est hors connexion pendant au moins 45 jours, puis qu’elle rebascule en ligne, son état est défini sur « Obsolète ».

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Vous pouvez éviter un état « Obsolète » en veillant à ce qu’aucune de vos bases de données ne soit hors ligne pendant 45 jours ou plus.

Si l’état d’une base de données est « Obsolète », vous devez effectuer les opérations suivantes :

1. Supprimer la base de données « Obsolète » du groupe de synchronisation.

2. Réajouter la base de données dans le groupe de synchronisation.

> [!WARNING]
> Vous perdez toutes les modifications apportées à cette base de données pendant qu’elle était hors connexion.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Un groupe de synchronisation a un état « Obsolète »

#### <a name="cause"></a>Cause :

Si une ou plusieurs modifications ne sont pas appliquées pendant la période de rétention de 45 jours, un groupe de synchronisation peut devenir obsolète.

#### <a name="solution-or-workaround"></a>Solution ou solution de contournement

Pour éviter un état « Obsolète », examinez régulièrement les résultats de vos travaux de synchronisation dans la visionneuse de l’historique afin de résoudre les problèmes liées aux modifications qui ne sont pas appliquées.

Si l’état d’un groupe de synchronisation est « Obsolète », vous devez supprimer le groupe de synchronisation et le recréer.

### <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>Impossible de supprimer un groupe de synchronisation dans les trois minutes suivant la désinstallation / l’arrêt de l’agent

#### <a name="description-and-symptoms"></a>Description et symptômes

Vous ne pouvez pas supprimer un groupe de synchronisation dans les trois minutes suivant la désinstallation / l’arrêt de l’agent client SQL Data Sync (Préversion) associé.

#### <a name="resolution-or-workaround"></a>Résolution ou solution de contournement

1. Supprimez un groupe de synchronisation pendant que les agents de synchronisation associés sont en ligne (recommandé).

2. Si l’agent est hors connexion mais installé, mettez-le en ligne sur l’ordinateur local. Ensuite, attendez que l’état de l’agent apparaisse comme En ligne dans le portail SQL Data Sync (Préversion), puis supprimez le groupe de synchronisation.

3. Si l’agent est hors connexion car il a été désinstallé, effectuez les étapes suivantes. Ensuite, essayez de supprimer le groupe de synchronisation.

    a.  Supprimez le fichier XML de l’agent du dossier d’installation de SQL Data Sync (Préversion) si le fichier existe.

    b.  Installez l’agent sur le même ordinateur local ou sur un autre ordinateur local, puis envoyez à partir du portail la clé de l’agent générée pour l’agent qui apparaît hors connexion.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Que se passe-t-il quand je restaure une base de données perdue ou endommagée ?

Si vous restaurez une base de données perdue ou endommagée à partir d’une sauvegarde, vous courrez le risque d’une non-convergence des données dans le ou les groupes de synchronisation auxquels la base de données appartient.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md)
-   [Configurer Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Bonnes pratiques pour Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Surveiller Azure SQL Data Sync avec OMS Log Analytics](sql-database-sync-monitor-oms.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
