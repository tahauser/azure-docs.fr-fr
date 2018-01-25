---
title: 'Synchronisation Azure AD Connect : Apporter une modification dans la synchronisation Azure AD Connect | Microsoft Docs'
description: "Cet article vous guide dans les changements de configuration d’Azure AD Connect Sync."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: billmath
ms.openlocfilehash: ed71272d2d10cd8b71fd3b2722d3ba033f1b51f9
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect Sync : comment modifier la configuration par défaut
L’objectif de cette rubrique est de vous expliquer comment apporter des modifications à la configuration par défaut dans Azure AD Connect Sync. Elle explique pas à pas la procédure pour les scénarios courants. Après avoir lu cette page, vous devriez être en mesure d’apporter des modifications simples à votre configuration en fonction de vos propres règles d’entreprise.

## <a name="synchronization-rules-editor"></a>Éditeur de règles de synchronisation
L’éditeur de règles de synchronisation sert à afficher et modifier la configuration par défaut. Il est disponible dans le menu Démarrer, sous le groupe **Azure Connect AD** .  
![Menu Démarrer avec l’Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Lorsque vous l’ouvrez, vous accédez directement aux règles par défaut.

![Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigation dans l’Éditeur
Les listes déroulantes situées en haut de l’Éditeur vous permettent d’accéder rapidement à une règle particulière. Par exemple, si vous souhaitez afficher les règles comprenant l’attribut proxyAddresses, vous pouvez modifier les listes déroulantes comme suit :   
![Filtrage SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
pour redéfinir le filtrage et charger une nouvelle configuration, appuyez sur la **F5** du clavier.

En haut à droite de l’écran se trouve le bouton **Ajouter une nouvelle règle**. Ce bouton vous permet de créer vos propres règles personnalisées.

En bas, vous disposez de boutons permettant d’agir sur une règle de synchronisation sélectionnée. Les boutons **Modifier** et **Supprimer** permettent de modifier et de supprimer des règles. **Exporterer** génère un script PowerShell pour recréer la règle de synchronisation. Cette procédure vous permet de déplacer une règle de synchronisation d’un serveur vers un autre.

## <a name="create-your-first-custom-rule"></a>Création de votre première règle personnalisée
Le changement le plus courant consiste à modifier les flux d’attributs. Les données contenues dans votre répertoire source ne sont peut-être pas telles que vous souhaiteriez les restituer dans Azure AD. Dans l’exemple de cette section, vous voulez faire en sorte que le nom d’un utilisateur donné apparaisse toujours au format **Nom propre**.

### <a name="disable-the-scheduler"></a>Désactivation du planificateur
Par défaut, le [planificateur](active-directory-aadconnectsync-feature-scheduler.md) s’exécute toutes les 30 minutes. Vous souhaitez vous assurer qu’il ne démarre pas pendant que vous apportez des modifications et corrigez les problèmes de vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrez PowerShell et exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false`

![Désactivation du planificateur](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Création de la règle
1. Cliquez sur **Ajouter une nouvelle règle**.
2. Sur la page **Description**, entrez les informations suivantes :  
   ![Filtrage des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * Nom : donnez à la règle un nom descriptif.
   * Description : texte descriptif permettant aux autres utilisateurs de comprendre l’objet de la règle.
   * Connected system (système connecté) : système dans lequel se trouve l’objet. Dans ce cas, sélectionnez le connecteur Active Directory.
   * Connected System/Metaverse Object Type (Type de système connecté/d’objet métaverse) : sélectionnez **Utilisateur** et **Personne**, respectivement.
   * Type de lien : remplacez cette valeur par **Jointure**.
   * Priorité : indiquez une valeur unique dans le système. Une valeur numérique inférieure indique une priorité plus élevée.
   * Balise : laissez ce champ vide. Seules les règles par défaut de Microsoft doivent contenir une valeur dans cette zone.
3. Sur la page **Scoping Filter** (Filtre d’étendue), entrez **givenName ISNOTNULL**.  
   ![Filtre d’étendue des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Cette section permet de définir les objets auxquels la règle s’applique. Si vous la laissez vide, la règle s’appliquera à tous les objets utilisateur, y compris les salles de conférence, les comptes de service et les autres objets d’utilisateurs non humains.
4. Ne renseignez pas le champ **Join rule**(Règles de jointure).
5. Sur la page **Transformations**, définissez le type de flux sur **Expression**. Sélectionnez l’attribut cible **givenName** et, dans Source, entrez `PCase([givenName])`.
   ![Transformations des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Le moteur de synchronisation respecte la casse aussi bien pour le nom de la fonction que pour le nom de l’attribut. Si vous faites une erreur de saisie, un message d’avertissement s’affiche lorsque vous ajoutez la règle. L’éditeur vous permet d’enregistrer et de continuer, mais vous devrez donc rouvrir la règle pour la corriger.
6. Cliquez sur **Ajouter** pour enregistrer la règle.

Votre nouvelle règle personnalisée doit être visible pour les autres règles de synchronisation du système.

### <a name="verify-the-change"></a>Vérification des modifications
Après avoir apporté une nouvelle modification, il est préférable de s’assurer que tout fonctionne comme prévu et qu’aucune erreur n’est générée. Selon le nombre d’objets dont vous disposez, il existe deux façons de procéder.

1. Exécuter une synchronisation complète de tous les objets
2. Exécuter un aperçu et une synchronisation complète sur un seul objet

Démarrez le **Service de synchronisation** depuis le menu Démarrer. Les étapes décrites dans cette section se trouvent toutes dans cet outil.

1. **Synchronisation complète de tous les objets**  
   Sous Actions, sélectionnez **Connecteurs** en haut de la page. Identifiez le connecteur que vous avez modifié dans la section précédente (dans ce cas, les services de domaine Active Directory), puis sélectionnez-le. Sous Actions, sélectionnez **Exécuter**, puis **Synchronisation complète** et **OK**.
   ![Synchronisation complète](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Les objets ne sont pas mis à jour dans le métaverse. Vous voulez à présent examiner l’objet dans le métaverse.
2. **Aperçu et synchronisation complète d’un seul objet**  
   Sous Actions, sélectionnez **Connecteurs** en haut de la page. Identifiez le connecteur que vous avez modifié dans la section précédente (dans ce cas, les services de domaine Active Directory), puis sélectionnez-le. Sélectionnez **Search Connector Space**(Rechercher l’espace de connecteur). Utilisez l’étendue pour rechercher un objet que vous souhaitez utiliser pour tester les modifications. Sélectionnez l’objet et cliquez sur **Aperçu**. Dans le nouvel écran, sélectionnez **Commit Preview**(Valider l’aperçu).  
   ![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   La modification est maintenant validée dans le métaverse.

**Consulter l’objet dans le métaverse**  
Vous souhaitez maintenant choisir quelques exemples d’objets pour vous assurer que la valeur correspond à celle attendue et que la règle est bien appliquée. Sélectionnez **Metaverse Search** (Recherche dans le métaverse) en haut de l’écran. Ajoutez les filtres dont vous avez besoin pour rechercher les objets appropriés. Dans les résultats de la recherche, ouvrez un objet. Examinez les valeurs d’attribut et vérifiez dans la colonne **Règles de synchronisation** que la règle a bien été appliquée comme prévu.  
![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Activation du planificateur
Si tout fonctionne comme prévu, vous pouvez réactiver le planificateur. À partir de PowerShell, exécutez `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Autres modifications courantes du flux d’attributs
Dans la section précédente, nous avons vu comment apporter des modifications à un flux d’attributs. Dans cette section, vous trouverez d’autres exemples. Les étapes de création de la règle de synchronisation ont été condensées, mais vous trouverez la procédure complète dans la section précédente.

### <a name="use-another-attribute-than-the-default"></a>Utiliser un autre attribut que l’attribut par défaut
Chez Fabrikam, il existe une forêt où l’alphabet local est utilisé pour les prénoms, noms et noms complets. La représentation sous forme de caractères latins de ces attributs est stockée dans les attributs d’extension. Au moment de la création de la liste d’adresses globale dans Azure AD et Office 365, l’organisation souhaite que ces attributs soient utilisés.

Avec une configuration par défaut, un objet de la forêt locale ressemble à ceci :   
![Flux d’attributs 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Pour créer une règle avec d’autres flux d’attributs, procédez comme suit :

* Ouvrez l’ **Éditeur de règles de synchronisation** depuis le menu Démarrer.
* En maintenant l’option **Entrant** sélectionnée sur la gauche, cliquez sur le bouton **Ajouter une nouvelle règle**.
* Attribuez à la règle un nom et une description. Sélectionnez Active Directory local et les types d’objets appropriés. Dans **Type de lien**, sélectionnez **Jointure**. Pour le choix de la priorité, sélectionnez un nombre qui n’est pas utilisé par une autre règle. Les règles par défaut commencent à 100, donc, il est possible d’utiliser la valeur 50 dans cet exemple.
  ![Flux d’attributs 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* Laissez l’option d’étendue vide (elle doit s’appliquer à tous les objets utilisateur de la forêt).
* Laissez les règles de jointure vides (c’est la règle par défaut qui doit gérer toutes les jointures).
* Dans Transformations, créez les flux suivants :   
  ![Flux d’attributs 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* Cliquez sur **Ajouter** pour enregistrer la règle.
* Accédez à **Synchronization Service Manager**. Sous **Connecteurs**, sélectionnez le connecteur auquel nous avons ajouté la règle. Sélectionnez **Exécuter** et **Synchronisation complète**. Une synchronisation complète recalcule tous les objets en utilisant les règles en cours.

Il s’agit du résultat obtenu pour le même objet avec cette règle personnalisée :   
![Flux d’attributs 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Longueur des attributs
Les attributs de chaîne sont par défaut définis pour être indexables et la longueur maximale est de 448 caractères. Si vous travaillez avec des attributs de chaîne qui peuvent contenir davantage de caractères, assurez-vous d’inclure ce qui suit dans le flux d’attributs :   
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Modification de userPrincipalSuffix
L’attribut userPrincipalName dans Active Directory n’est pas toujours connu des utilisateurs et peut ne pas convenir comme ID de connexion. L’Assistant d’installation d’Azure AD Connect Sync permet de choisir un autre attribut, par exemple un e-mail, mais dans certains cas l’attribut doit être calculé. Par exemple, la société Contoso possède deux annuaires AD, un pour la production et un pour les tests. Elle souhaite que les utilisateurs de son client test utilisent un autre suffixe dans l’ID de connexion.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Dans cette expression, prenons tout ce qui figure à gauche du premier signe @-sign (Word) et concaténons avec une chaîne fixe.

### <a name="convert-a-multi-value-to-a-single-value"></a>Convertir un attribut à valeurs multiples en attribut à valeur unique
Certains attributs dans Active Directory sont à valeurs multiples dans le schéma, même s’ils semblent être à valeur unique dans Utilisateurs et ordinateurs Active Directory. L’attribut description constitue un exemple.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Dans cette expression, au cas où l’attribut a une valeur, prenez le premier élément (Item) dans l’attribut, supprimez les espaces à gauche et à droite (Trim), puis conservez les 448 premiers caractères (Left) de la chaîne.

### <a name="do-not-flow-an-attribute"></a>Ne transmettez pas d'attribut
Pour plus d’informations sur le scénario de cette section, consultez la section [Contrôler le processus de flux d’attributs](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Il existe deux manières de ne pas transmettre un attribut. La première est disponible dans l'Assistant d'installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option fonctionne si vous n'avez jamais synchronisé l'attribut auparavant. Toutefois, si vous avez commencé à synchroniser cet attribut et que vous le supprimez ultérieurement avec cette fonctionnalité, le moteur de synchronisation cesse de gérer l’attribut et les valeurs existantes sont maintenues dans Azure AD.

Si vous souhaitez supprimer la valeur d’un attribut et vous assurer qu’il ne sera pas transmis à l’avenir, vous devez créer une règle personnalisée à la place.

Chez Fabrikam, nous nous sommes rendus compte que certains des attributs que nous synchronisons vers le cloud ne devraient pas s’y trouver. Nous souhaitons garantir que ces attributs sont supprimés d’Azure AD.  
![Attributs d’extension erronés](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* Créer une nouvelle règle de synchronisation entrante et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* Créer des flux d’attributs de type **Expression** et avec la source **AuthoritativeNull**. Le littéral **AuthoritativeNull** indique que la valeur devrait être vide dans l'ordinateur virtuel même si une règle de synchronisation de précédence inférieure essaie de remplir la valeur.
  ![Transformation des attributs d’extension](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* Enregistrer la règle de synchronisation. Démarrez **Service de synchronisation**, recherchez le connecteur, sélectionnez **Exécuter**, puis **Synchronisation complète**. Cette étape permet de recalculer tous les flux d’attributs.
* Vérifiez que les modifications prévues vont être exportées en recherchant l'espace de connecteur.
  ![Suppression progressive](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Création de règles avec PowerShell
L’éditeur de règles de synchronisation est adapté lorsque vous avez seulement quelques modifications à apporter. Si vous devez apporter de nombreuses modifications, PowerShell peut être une meilleure option. Certaines fonctionnalités avancées sont uniquement disponibles avec PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obtenir le script PowerShell pour une règle out-of-box
Pour voir le script PowerShell qui a créé une règle out-of-box, sélectionnez la règle dans l’éditeur de règles de synchronisation, puis cliquez sur **Exporter**. Cette action vous donne le script PowerShell qui a créé la règle.

### <a name="advanced-precedence"></a>Priorité avancée
Les règles de synchronisation out-of-box commencent avec une valeur de priorité de 100. Si vous avez de nombreuses forêts et que vous devez apporter de nombreuses modifications personnalisées, 99 règles de synchronisation pourraient ne pas suffire.

Vous pouvez indiquer au moteur de synchronisation que vous souhaitez que des règles supplémentaires soient insérées avant les règles out-of-box. Pour obtenir ce comportement, procédez comme suit :

1. Marquez la première règle de synchronisation d’out-of-box (cette règle est **Obtenu par jointure d’utilisateurs AD**) dans l’éditeur de règles de synchronisation, puis sélectionnez **Exporter**. Copiez la valeur d’identificateur SR.  
![PowerShell avant modification](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Créez la nouvelle règle de synchronisation. Vous pouvez utiliser l’éditeur de règles de synchronisation pour la créer. Exportez la règle dans un script PowerShell.
3. Dans la propriété **PrecedenceBefore**, insérez la valeur d’identificateur de la règle out-of-box. Définissez la **priorité** sur **0**. Assurez-vous que l’attribut d’identificateur est unique et que vous ne réutilisez pas un GUID à partir d’une autre règle. Assurez-vous également que la propriété **ImmutableTag** n’est pas définie, car cette propriété doit uniquement être définie pour une règle out-of-box. Enregistrez le script PowerShell et exécutez-le. Le résultat est que votre règle personnalisée se voit affecter la priorité 100 et que toutes les autres règles out-of-box sont incrémentées.  
![PowerShell après modification](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Vous pouvez avoir plusieurs règles de synchronisation personnalisées utilisant la même valeur **PrecedenceBefore** si nécessaire.

## <a name="enable-synchronization-of-preferreddatalocation"></a>Activer la synchronisation de l’attribut PreferredDataLocation
Par défaut, les ressources Office 365 pour vos utilisateurs se trouvent dans la même région que votre client Azure AD. Par exemple, si votre client est situé en Amérique du Nord, les boîtes aux lettres Exchange des utilisateurs sont également situées en Amérique du Nord. Pour une organisation multinationale, cela n’est peut-être pas optimal. En définissant l’attribut preferredDataLocation, la région de l’utilisateur peut être définie.

Les régions d’Office 365 sont les suivantes :

| Région | DESCRIPTION |
| --- | --- |
| NAM | Amérique du Nord |
| EUR | Europe |
| APC | Asie-Pacifique |
| JPN | Japon |
| AUS | Australie |
| CAN | Canada |
| GBR | Grande-Bretagne |
| LAM | Amérique latine |

Toutes les charges de travail Office 365 ne prennent pas en charge l’utilisation de la définition de la région d’un utilisateur.

Azure AD Connect prend en charge la synchronisation de l’attribut **PreferredDataLocation** pour les objets **Utilisateur** dans la version 1.1.524.0 et versions ultérieures. Plus spécifiquement, les modifications introduites sont les suivantes :

* Le schéma du type d’objet **Utilisateur** dans le connecteur Azure AD est étendu pour inclure l’attribut PreferredDataLocation qui est de type chaîne à une seule valeur.

* Le schéma du type d’objet **Personne** dans le Metaverse est étendu pour inclure l’attribut PreferredDataLocation, qui est de type chaîne et n’a qu’une seule valeur.

Par défaut, l’attribut PreferredDataLocation n’est pas activé pour la synchronisation, car il n’existe aucun attribut PreferredDataLocation correspondant dans l’Active Directory local. Vous devez activer manuellement la synchronisation.

> [!IMPORTANT]
> Actuellement, Azure AD permet que l’attribut PreferredDataLocation sur des objets utilisateur personnalisés et des objets utilisateur cloud soit directement configuré à l’aide d’Azure AD PowerShell. Une fois que vous avez activé la synchronisation de l’attribut PreferredDataLocation, vous devez cesser d’utiliser Azure AD PowerShell pour configurer l’attribut sur des **objets utilisateur synchronisés**, car Azure AD Connect les remplace sur la base des valeurs d’attribut source dans l’Active Directory local.

> [!IMPORTANT]
> Depuis le 1er septembre 2017, Azure AD ne permet plus la configuration de l’attribut PreferredDataLocation sur des **objets utilisateur synchronisés** directement à l’aide d’Azure AD PowerShell. Pour configurer l’attribut PreferredLocation sur des objets utilisateur synchronisés, vous devez utiliser Azure AD Connect.

Avant d’activer la synchronisation de l’attribut PreferredDataLocation, vous devez :

 * Déterminer l’attribut de l’Active Directory local à utiliser en tant qu’attribut source. Il doit être de type **chaîne à valeur unique**. Dans les étapes ci-dessous, un des extensionAttributes est utilisé.

 * Si vous avez déjà configuré l’attribut PreferredDataLocation sur des objets utilisateur synchronisés existants dans Azure AD à l’aide d’Azure AD PowerShell, vous devez **rétroporter** les valeurs d’attribut vers les objets utilisateur correspondants dans l’Active Directory local.

    > [!IMPORTANT]
    > Si vous ne rétroportez pas les valeurs d’attribut pour les objets utilisateur correspondants dans l’Active Directory local, Azure AD Connect supprime les valeurs d’attribut existantes dans Azure AD lorsque la synchronisation de l’attribut PreferredDataLocation est activée.

 * Nous vous recommandons de configurer maintenant l’attribut source sur au moins deux objets utilisateur AD locaux qui pourront être utilisés à des fins de vérification ultérieure.

Les étapes d’activation de la synchronisation de l’attribut PreferredDataLocation peuvent se résumer comme suit :

1. Désactiver le planificateur de synchronisation et vérifier qu'aucune synchronisation n’est en cours
2. Ajouter l’attribut source au schéma du connecteur AD local
3. Ajouter PreferredDataLocation au schéma du connecteur Azure AD
4. Créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
5. Créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
6. Exécuter un cycle de synchronisation complète
7. Activer le planificateur de synchronisation

> [!NOTE]
> Le reste de cette section décrit ces étapes en détail. Ils sont décrits dans le cadre d’un déploiement d’Azure AD avec une topologie de forêt unique et sans règles de synchronisation personnalisées. Si vous avez une topologie à forêts multiples, des règles de synchronisation personnalisées configurées ou un serveur intermédiaire, vous devez ajuster les étapes en conséquence.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Étape 1 : désactiver le planificateur de synchronisation et vérifier qu’aucune synchronisation n’est en cours
Veillez à ce qu’aucune synchronisation ne se produise quand vous êtes en train de mettre à jour des règles de synchronisation afin d’éviter l’exportation de modifications inattendues vers Azure AD. Pour désactiver le planificateur de synchronisation intégré :

1. Lancez une session PowerShell sur le serveur Azure AD Connect.
2. Désactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Lancez **Synchronization Service Manager** en accédant au menu **DÉMARRER** > **Service de synchronisation**.
4. Accédez à l’onglet **Opérations** et confirmez qu’aucune opération n’est à l’état *En cours*.

![Synchronization Service Manager - Vérifier qu’aucune opération n’est en cours](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Étape 2  : ajouter l’attribut source au schéma du connecteur AD local
Certains attributs AD ne sont pas importés dans l’espace du connecteur AD local. Si vous avez choisi d’utiliser un attribut non synchronisé par défaut, vous devez l’importer. Pour ajouter l’attribut source à la liste des attributs importés :

1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
2. Cliquez avec le bouton droit sur le **Connecteur Active Directory local**, puis sélectionnez **Propriétés**.
3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
4. Assurez-vous que l’attribut source que vous avez sélectionné est activé dans la liste d’attributs.
5. Cliquez sur **OK** pour enregistrer.

![Ajoutez l’attribut source au schéma du connecteur AD local](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Étape 3 : ajouter l’attribut PreferredDataLocation au schéma du connecteur Azure AD
Par défaut, l’attribut PreferredDataLocation n’est pas importé dans l’espace Azure AD Connect. Pour ajouter l’attribut PreferredDataLocation à la liste des attributs importés :

1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
2. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Propriétés**.
3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
4. Sélectionnez l’attribut PreferredDataLocation dans la liste d’attributs.
5. Cliquez sur **OK** pour enregistrer.

![Ajouter un attribut source au schéma du connecteur Azure AD](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Étape 4 : créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
La règle de synchronisation de trafic entrant permet de transmettre la valeur de l’attribut de l’attribut source de l’Active Directory local au Metaverse :

1. Lancez **Synchronization Rules Editor** dans le menu **DÉMARRER** > **Éditeur de règles de synchronisation**.
2. Définissez le filtre de recherche **Direction** sur **Entrant**.
3. Cliquez sur **Ajouter une nouvelle règle** pour créer une règle de trafic entrant.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | --- | --- | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, *« Entrant depuis AD – Utilisateur PreferredDataLocation”* |
    | DESCRIPTION | *Fournissez une description personnalisée* |  |
    | Système connecté | *Sélectionnez le connecteur AD local* |  |
    | Type d’objet système connecté | **Utilisateur** |  |
    | Type d’objet Metaverse | **Person** |  |
    | Type de lien | **Join** |  |
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. Ne sélectionnez pas de valeur utilisée par une autre règle de synchronisation. |

5. Conservez le **filtre d’étendue** vide pour inclure tous les objets. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.
6. Accédez à l’onglet **Transformation**, puis implémentez la règle de transformation suivante :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    |Directement | PreferredDataLocation | Sélectionnez l’attribut source | Désactivé | Mettre à jour |

7. Cliquez sur **Ajouter** pour créer la règle de trafic entrant.

![Créer une règle de synchronisation de trafic entrant](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Étape 5 : créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
La règle de synchronisation de trafic sortant permet de transmettre la valeur de l’attribut du Metaverse à l’attribut PreferredDataLocation dans Azure AD :

1. Accédez à l’**Éditeur de règles de synchronisation**.
2. Définissez le filtre de recherche **Direction** sur **Sortante**.
3. Cliquez sur le bouton **Ajouter une nouvelle règle**.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | ----- | ------ | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, « Sortant vers AAD – Utilisateur PreferredDataLocation » |
    | DESCRIPTION | *Fournissez une description* ||
    | Système connecté | *Sélectionnez le connecteur AAD* ||
    | Type d’objet système connecté | Utilisateur ||
    | Type d’objet métaverse | **Person** ||
    | Type de lien | **Join** ||
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. Ne sélectionnez pas de valeur utilisée par une autre règle de synchronisation. |

5. Accédez à l’onglet **Filtre d’étendue**, puis ajoutez un **groupe de filtre d’étendue unique avec deux clauses** :

    | Attribut | Operator | Valeur |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilisateur |
    | cloudMastered | NOTEQUAL | True |

    Le filtre d’étendue détermine les objets Active Directory auxquels cette règle de synchronisation de trafic sortant s’applique. Dans cet exemple, nous utilisons le filtre d’étendue de la règle de synchronisation OOB « Sortant vers AAD – Utilisateur Identity ». Il empêche que la règle de synchronisation soit appliquée aux objets utilisateur non synchronisés à partir de l’Active Directory local. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.

6. Accédez à l’onglet **Transformation**, puis implémentez la règle de transformation suivante :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    | Directement | PreferredDataLocation | PreferredDataLocation | Désactivé | Mettre à jour |

7. Fermez **Ajouter** pour créer la règle de trafic sortant.

![Créer une règle de synchronisation de trafic sortant](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>Étape 6 : exécuter un cycle de synchronisation complète
En règle générale, un cycle de synchronisation complète est nécessaire parce que nous avons ajouté de nouveaux attributs à l’AD et au schéma du connecteur Azure AD, et introduit des règles de synchronisation personnalisées. Il est recommandé de vérifier les modifications avant de les exporter vers Azure AD. Vous pouvez procéder comme suit pour vérifier les modifications tandis que vous exécutez manuellement les étapes d’un cycle de synchronisation complète.

1. Exécutez l’étape d’**importation complète** sur le **Connecteur Active Directory local** :

   1. Accédez à l’onglet **Opérations** dans Synchronization Service Manager.

   2. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.

   3. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.

   4. Attendez que l’opération soit terminée.

    > [!NOTE]
    > Vous pouvez ignorer l’importation complète sur le connecteur AD local si l’attribut source est déjà inclus dans la liste des attributs importés. En d’autres termes, vous n’avez pas dû apporter de modification au cours de l’[Étape 2  : ajouter l’attribut source au schéma du connecteur AD local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Exécutez l’étape d’**importation complète** sur le **Connecteur Azure AD** :

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.

   2. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.

   3. Attendez que l’opération soit terminée.

3. Vérifiez le changement de la règle de synchronisation sur un objet utilisateur existant :

L’attribut source de l’Active Directory local et l’attribut PreferredDataLocation d’Azure AD ont été importés dans leur espace de connecteur respectif. Avant de passer à l’étape de synchronisation complète, il est recommandé d’afficher un **Aperçu** d’un objet utilisateur existant dans l’espace du connecteur AD local. L’attribut source de l’objet que vous avez choisi doit avoir une valeur définie. Un bon **Aperçu** avec l’attribut PreferredDataLocation renseigné dans le Metaverse est un bon indicateur que vous avez correctement configuré les règles de synchronisation. Pour plus d’informations sur la façon de générer un **Aperçu**, voir la section [Vérifier la modification](#verify-the-change).

4. Exécutez l’étape **Synchronisation complète** sur le **Connecteur AD local** :

   1. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.

   2. Dans la boîte de dialogue contextuelle, sélectionnez **Synchronisation complète**, puis cliquez sur **OK**.

   3. Attendez que l’opération soit terminée.

5. Vérifiez les **Exportations en attente** vers Azure AD :

   1. Cliquez avec le bouton droit sur le connecteur **Azure AD** et sélectionnez **Rechercher dans l’espace connecteur**.

   2. Dans la fenêtre contextuelle Rechercher dans l’espace connecteur :

      1. Définissez l’**Étendue** sur **En attente d’exportation**.

      2. Activez les trois cases à cocher : **Ajouter, Modifier et Supprimer**.

      3. Cliquez sur le bouton **Rechercher** pour obtenir la liste d’objets contenant des modifications à exporter. Pour examiner les modifications apportées à un objet donné, double-cliquez sur celui-ci.

      4. Vérifiez que les modifications sont celles auxquelles vous vous attendiez.

6. Exécutez l’étape **Exporter** du **Connecteur Azure AD**.

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.

   2. Dans la boîte de dialogue contextuelle Exécuter le connecteur, sélectionnez **Exporter**, puis cliquez sur **OK**.

   3. Patientez jusqu’à ce l’exportation vers Azure AD soit terminée.

> [!NOTE]
> Vous pouvez remarquer que les étapes n’incluent pas la synchronisation complète sur le connecteur Azure AD et l’exportation sur le connecteur Azure AD. Ces étapes ne sont pas obligatoires car les valeurs d’attribut sont transmises uniquement de l’Active Directory local à Azure AD.

### <a name="step-7-re-enable-sync-scheduler"></a>Étape 7 : réactiver le planificateur de synchronisation
Réactivez le planificateur de synchronisation intégré :

1. Lancez une session PowerShell.
2. Réactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="enable-synchronization-of-usertype"></a>Activer la synchronisation de UserType
Azure AD Connect prend en charge la synchronisation de l’attribut **UserType** pour les objets **Utilisateur** dans la version 1.1.524.0 et versions ultérieures. Plus spécifiquement, les modifications introduites sont les suivantes :

- Le schéma du type d’objet **Utilisateur** dans le connecteur Azure AD est étendu pour inclure l’attribut UserType qui est de type chaîne et n’a qu’une seule valeur.
- Le schéma du type d’objet **Personne** dans le Metaverse est étendu pour inclure l’attribut UserType qui est de type chaîne et n’a qu’une seule valeur.

Par défaut, l’attribut UserType n’est pas activé pour la synchronisation, car il n’existe aucun attribut UserType correspondant dans l’Active Directory local. Vous devez activer manuellement la synchronisation. Avant d’activer la synchronisation de l’attribut UserType, vous devez noter le comportement suivant qui est appliqué par Azure AD :

- Azure AD accepte uniquement deux valeurs pour l’attribut UserType : **Membre** et **Invité**.
- Si l’attribut UserType n’est pas activé pour la synchronisation dans Azure AD Connect, les utilisateurs Azure AD créés via la synchronisation d’annuaires ont l’attribut UserType défini sur **Membre**.
- Azure AD n’autorise pas la modification par Azure AD Connect de l’attribut UserType sur les utilisateurs Azure AD existants. Il peut uniquement être défini lors de la création des utilisateurs Azure AD.

Avant d’activer la synchronisation de l’attribut UserType, vous devez d’abord déterminer comment l’attribut UserType sera dérivé de l’AD local. Il existe deux approches courantes :

- Vous pouvez désigner un attribut AD local inutilisé (par exemple, extensionAttribute1) à utiliser en tant qu’attribut source. L’attribut AD local désigné doit être de type local **chaîne**, à valeur unique et contenir la valeur **Membre** ou **Invité**. Si vous choisissez cette approche, vous devez vous assurer que l’attribut désigné est rempli avec la valeur correcte pour tous les objets utilisateur existants dans Active Directory local qui sont synchronisés avec Azure AD avant d’activer la synchronisation de l’attribut UserType.
- Autrement, vous pouvez dériver la valeur de l’attribut de UserType à partir d’autres propriétés. Par exemple, vous souhaitez synchroniser tous les utilisateurs en tant qu’Invité si leur attribut UserPrincipalName AD local se termine par la partie domaine « @partners.fabrikam123.org ». Comme précisé précédemment, Azure AD Connect ne peut pas modifier l’attribut UserType sur les utilisateurs Azure AD existants. Par conséquent, vous devez vous assurer que la logique que vous avez choisie est cohérente avec la manière dont l’attribut UserType est déjà configuré pour tous les utilisateurs Azure AD existants dans votre client.

Les étapes d’activation de la synchronisation de l’attribut UserType peuvent se résumer comme suit :

>[!NOTE]
> Le reste de cette section décrit ces étapes. Ils sont décrits dans le cadre d’un déploiement d’Azure AD avec une topologie de forêt unique et sans règles de synchronisation personnalisées. Si vous avez une topologie à forêts multiples, des règles de synchronisation personnalisées configurées ou un serveur intermédiaire, vous devez ajuster les étapes en conséquence.

1.  Désactiver le **planificateur de synchronisation** et vérifier qu’aucune synchronisation n’est en cours
2.  Ajouter **l’attribut source** au schéma du connecteur AD local
3.  Ajouter **UserType** au schéma du connecteur Azure AD
4.  Créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
5.  Créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
6.  Exécuter un cycle de **synchronisation complète**
7.  Activer le **planificateur de synchronisation**


### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Étape 1 : désactiver le planificateur de synchronisation et vérifier qu’aucune synchronisation n’est en cours
Veillez à ce qu’aucune synchronisation ne se produise quand vous êtes en train de mettre à jour des règles de synchronisation afin d’éviter l’exportation de modifications inattendues vers Azure AD. Pour désactiver le planificateur de synchronisation intégré :

 1. Lancez une session PowerShell sur le serveur Azure AD Connect.
 2. Désactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $false`
 3. Lancez **Synchronization Service Manager** en accédant au menu DÉMARRER → Service de synchronisation.
 4. Accédez à l’onglet **Opérations** et confirmez qu’aucune opération n’est à l’état *« En cours ».*

![Synchronization Service Manager - Vérifier qu’aucune opération n’est en cours](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Étape 2  : ajouter l’attribut source au schéma du connecteur AD local
Certains attributs AD ne sont pas importés dans l’espace du connecteur AD local. Pour ajouter l’attribut source à la liste des attributs importés :

 1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
 2. Cliquez avec le bouton droit sur le **Connecteur Active Directory local**, puis sélectionnez **Propriétés**.
 3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
 4. Assurez-vous que l’attribut source est activé dans la liste d’attributs.
 5. Cliquez sur **OK** pour enregistrer.
![Ajouter l’attribut source au schéma du connecteur AD local](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-usertype-to-the-azure-ad-connector-schema"></a>Étape 3 : Ajouter UserType au schéma du connecteur Azure AD
Par défaut, l’attribut UserType n’est pas importé dans l’espace Azure AD Connect. Pour ajouter l’attribut UserType à la liste des attributs importés :

 1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
 2. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Propriétés**.
 3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
 4. Assurez-vous que l’attribut PreferredDataLocation est activé dans la liste des attributs.
 5. Cliquez sur **OK** pour enregistrer.

![Ajouter un attribut source au schéma du connecteur Azure AD](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Étape 4 : créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
La règle de synchronisation de trafic entrant permet de transmettre la valeur de l’attribut de l’attribut source de l’Active Directory local au Metaverse :

1. Lancez **Synchronization Rules Editor** dans le menu DÉMARRER → Éditeur de règles de synchronisation.
2. Définissez le filtre de recherche **Direction** sur **Entrant**.
3. Cliquez sur **Ajouter une nouvelle règle** pour créer une règle de trafic entrant.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | --- | --- | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, *« Entrant depuis AD – Utilisateur UserType »* |
    | DESCRIPTION | *Fournissez une description* |  |
    | Système connecté | *Sélectionnez le connecteur AD local* |  |
    | Type d’objet système connecté | **Utilisateur** |  |
    | Type d’objet Metaverse | **Person** |  |
    | Type de lien | **Join** |  |
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. Ne sélectionnez pas de valeur utilisée par une autre règle de synchronisation. |

5. Accédez à l’onglet **Filtre d’étendue**, puis ajoutez un **groupe de filtre d’étendue unique avec la clause suivante** :

    | Attribut | Operator | Valeur |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Utilisateur\_ |

    Le filtre d’étendue détermine les objets de l’Active Directory local auxquels cette règle de synchronisation de trafic entrant s’applique. Dans cet exemple, nous utilisons le même filtre d’étendue que celui utilisé dans la règle de synchronisation OOB « Entrant depuis AD – Utilisateur Common», ce qui permet d’éviter que la règle de synchronisation soit appliquée à des objets utilisateur créés via la fonctionnalité d’écriture différée d’utilisateur Azure AD. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.

6. Accédez à **l’onglet Transformation**, puis implémentez la règle de transformation souhaitée. Par exemple, vous avez désigné un attribut Active Directory local inutilisé (par exemple, extensionAttribute1) en tant qu’attribut UserType source, vous pouvez implémenter un flux d’attribut direct :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    | Directement | UserType | extensionAttribute1 | Désactivé | Mettre à jour |

    Autre exemple : vous souhaitez dériver la valeur de l’attribut de UserType à partir d’autres propriétés. Par exemple, vous souhaitez synchroniser tous les utilisateurs en tant qu’Invité si leur attribut UserPrincipalName AD local se termine par la partie domaine « @partners.fabrikam123.org ». Vous pouvez implémenter une expression :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    | Directement | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | Désactivé | Mettre à jour |

7. Cliquez sur **Ajouter** pour créer la règle de trafic entrant.

![Créer une règle de synchronisation de trafic entrant](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Étape 5 : créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
La règle de synchronisation de trafic sortant permet de transmettre la valeur de l’attribut du Metaverse à l’attribut PreferredDataLocation dans Azure AD :

1. Accédez à l’**Éditeur de règles de synchronisation**.
2. Définissez le filtre de recherche **Direction** sur **Sortante**.
3. Cliquez sur le bouton **Ajouter une nouvelle règle**.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | ----- | ------ | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, « Entrant depuis AAD – Utilisateur UserType » |
    | DESCRIPTION | *Fournissez une description* ||
    | Système connecté | *Sélectionnez le connecteur AAD* ||
    | Type d’objet système connecté | Utilisateur ||
    | Type d’objet métaverse | **Person** ||
    | Type de lien | **Join** ||
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. YDo ne récupère pas de valeur utilisée par une autre règle de synchronisation. |

5. Accédez à l’onglet **Filtre d’étendue**, puis ajoutez un **groupe de filtre d’étendue unique avec deux clauses** :

    | Attribut | Operator | Valeur |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilisateur |
    | cloudMastered | NOTEQUAL | True |

    Le filtre d’étendue détermine les objets Active Directory auxquels cette règle de synchronisation de trafic sortant s’applique. Dans cet exemple, nous utilisons le filtre d’étendue de la règle de synchronisation OOB « Sortant vers AAD – Utilisateur Identity ». Il empêche que la règle de synchronisation soit appliquée aux objets utilisateur non synchronisés à partir de l’Active Directory local. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.

6. Accédez à l’onglet **Transformation**, puis implémentez la règle de transformation suivante :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    | Directement | UserType | UserType | Désactivé | Mettre à jour |

7. Fermez **Ajouter** pour créer la règle de trafic sortant.

![Créer une règle de synchronisation de trafic sortant](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-full-synchronization-cycle"></a>Étape 6 : exécuter un cycle de synchronisation complète
En règle générale, un cycle de synchronisation complète est nécessaire parce que nous avons ajouté de nouveaux attributs à l’AD et au schéma du connecteur Azure AD, et introduit des règles de synchronisation personnalisées. Il est recommandé de vérifier les modifications avant de les exporter vers Azure AD. Vous pouvez procéder comme suit pour vérifier les modifications tandis que vous exécutez manuellement les étapes d’un cycle de synchronisation complète.

1. Exécutez l’étape d’**importation complète** sur le **Connecteur Active Directory local** :

   1. Accédez à l’onglet **Opérations** dans Synchronization Service Manager.
   2. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.
   3. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.
   4. Attendez que l’opération soit terminée.

    > [!NOTE]
    > Vous pouvez ignorer une importation complète sur le connecteur AD local si l’attribut source est déjà inclus dans la liste des attributs importés. En d’autres termes, vous n’avez pas dû apporter de modification au cours de l’[Étape 2  : ajouter l’attribut source au schéma du connecteur AD local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Exécutez l’étape d’**importation complète** sur le **Connecteur Azure AD** :

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.
   3. Attendez que l’opération soit terminée.

3. Vérifiez le changement de la règle de synchronisation sur un objet utilisateur existant :

    L’attribut source de l’Active Directory local et l’attribut UserType d’Azure AD ont été importés dans leur espace de connecteur respectif. Avant de passer à l’étape de synchronisation complète, il est recommandé d’afficher un **Aperçu** d’un objet utilisateur existant dans l’espace du connecteur AD local. L’attribut source de l’objet que vous avez choisi doit avoir une valeur définie. Un **Aperçu** réussi avec l’attribut UserType renseigné dans le Metaverse indique que vous avez correctement configuré les règles de synchronisation. Pour plus d’informations sur la façon de générer un **Aperçu**, voir la section [Vérifier la modification](#verify-the-change).

4. Exécutez l’étape **Synchronisation complète** sur le **Connecteur AD local** :

   1. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle, sélectionnez **Synchronisation complète**, puis cliquez sur **OK**.
   3. Attendez que l’opération soit terminée.

5. Vérifiez les **Exportations en attente** vers Azure AD :

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Rechercher dans l’espace connecteur**.

   2. Dans la fenêtre contextuelle Rechercher dans l’espace connecteur :

      1. Définissez l’**Étendue** sur **En attente d’exportation**.
      2. Activez les trois cases à cocher : **Ajouter, Modifier et Supprimer**.
      3. Cliquez sur le bouton **Rechercher** pour obtenir la liste d’objets contenant des modifications à exporter. Pour examiner les modifications apportées à un objet donné, double-cliquez sur celui-ci.
      4. Vérifiez que les modifications sont celles auxquelles vous vous attendiez.

6. Exécutez l’étape **Exporter** du **Connecteur Azure AD**.

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle Exécuter le connecteur, sélectionnez **Exporter**, puis cliquez sur **OK**.
   3. Patientez jusqu’à ce l’exportation vers Azure AD soit terminée.

> [!NOTE]
> Vous pouvez remarquer que les étapes n’incluent pas la synchronisation complète et l’exportation sur le connecteur Azure AD. Ces étapes ne sont pas obligatoires car les valeurs d’attribut sont transmises uniquement de l’Active Directory local à Azure AD.

### <a name="step-7-re-enable-sync-scheduler"></a>Étape 7 : réactiver le planificateur de synchronisation
Réactivez le planificateur de synchronisation intégré :

1. Lancez une session PowerShell.
2. Réactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $true`


## <a name="next-steps"></a>étapes suivantes
* En savoir plus sur le modèle de configuration dans [Comprendre l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* En savoir plus sur le langage d’expression dans [Comprendre les expressions d’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
