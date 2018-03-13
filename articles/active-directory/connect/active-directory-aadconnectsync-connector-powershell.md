---
title: Connecteur PowerShell | Microsoft Docs
description: "Cet article décrit comment configurer le connecteur Windows PowerShell de Microsoft."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 2caf8dd8a657f116df0342893763829676602cd6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="windows-powershell-connector-technical-reference"></a>Référence technique du connecteur PowerShell Windows
Cet article décrit le connecteur PowerShell Windows Cet article s’applique aux produits suivants :

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Nécessité d’utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Vue d’ensemble du connecteur PowerShell
Le connecteur PowerShell vous permet d’intégrer le service de synchronisation dans des systèmes externes qui offrent des API Windows PowerShell. Le connecteur offre un pont entre les fonctions de l’agent de gestion de connectivité extensible basé sur une structure appel 2 (ECMA2) et Windows PowerShell. Pour plus d’informations sur l’infrastructure d’ECMA, consultez la section [Référence de l’agent gestion de connectivité extensible 2.2](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>configuration requise
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

* Microsoft .NET 4.5.2 Framework ou version ultérieure
* Windows PowerShell 2.0, 3.0 ou 4.0

La stratégie d’exécution sur le serveur du service de synchronisation doit être configurée pour autoriser le connecteur à exécuter des scripts Windows PowerShell. À moins que les scripts que le connecteur exécute portent une signature numérique, configurez la stratégie d’exécution en exécutant la commande   
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Créer un connecteur
Pour créer un connecteur Windows PowerShell dans le service de synchronisation, vous devez fournir une série de scripts Windows PowerShell qui exécutent les opérations demandées par le service de synchronisation. Selon la source de données à laquelle vous vous connectez et la fonction dont vous avez besoin, les scripts que vous devez implémenter varient. Cette section décrit chacun des scripts pouvant être mis en œuvre ainsi que les situations dans lesquelles ils sont requis.

Le connecteur Windows PowerShell est conçu pour stocker chacun des scripts à l’intérieur de la base de données du service de synchronisation. Bien qu’il soit possible d’exécuter des scripts stockés dans le système de fichiers, il est beaucoup plus simple d’insérer le corps de chaque script directement dans la configuration du connecteur.

Pour créer un connecteur PowerShell, dans **Synchronization Service**, sélectionnez **Management Agent** et **Create**. Sélectionnez le connecteur **PowerShell (Microsoft)** .

![Créer un connecteur](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Connectivité
Fournissez des paramètres de configuration pour la connexion à un système distant. Ces valeurs sont stockées en toute sécurité par le service de synchronisation et accessibles à vos scripts Windows PowerShell lorsque le connecteur est exécuté.

![Connectivité](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Vous pouvez configurer les paramètres de connectivité suivants :

**Connectivité**

| Paramètre | Valeur par défaut | Objectif |
| --- | --- | --- |
| Serveur |<Blank> |Nom du serveur auquel le connecteur doit se connecter. |
| Domaine |<Blank> |Domaine des informations d’identification à stocker pour utilisation au moment d’exécuter le connecteur. |
| Utilisateur |<Blank> |Nom d’utilisateur des informations d’identification à stocker pour utilisation au moment d’exécuter le connecteur. |
| Mot de passe |<Blank> |Mot de passe des informations d’identification à stocker pour utilisation lors de l’exécution du connecteur. |
| Emprunter l’identité du compte de connecteur |False |Si la valeur est true, le service de synchronisation exécute les scripts Windows PowerShell dans le contexte d’identification fourni. Lorsque c’est possible, il est conseillé d’utiliser le paramètre **$Credentials** transmis à chaque script plutôt que de recourir à l’emprunt d’identité. Pour plus d’informations sur les autorisations supplémentaires nécessaires à l’utilisation de cette option, consultez [Configuration supplémentaire pour l’emprunt d’identité](#additional-configuration-for-impersonation). |
| Charger le profil utilisateur lors de l’emprunt d’identité |False |Indique à Windows de charger le profil utilisateur des informations d’identification du connecteur pendant l’emprunt d’identité. Si l’utilisateur dont il faut emprunter l’identité possède un profil itinérant, le connecteur ne charge pas le profil itinérant. Pour plus d’informations sur les autorisations supplémentaires nécessaires à l’utilisation de ce paramètre, consultez [Configuration supplémentaire pour l’emprunt d’identité](#additional-configuration-for-impersonation). |
| Type d’ouverture en cas d’emprunt d’identité |Aucun |Type de connexion pendant l’emprunt d’identité. Pour plus d’informations, consultez la documentation [dwLogonType][dw]. |
| Scripts signés uniquement |False |Si la valeur est true, le connecteur de Windows PowerShell valide le fait que chaque script possède une signature numérique valide. Si la valeur est false, assurez-vous que la stratégie d’exécution Windows PowerShell du serveur de service de synchronisation est de type RemoteSigned ou Unrestricted. |

**Module commun**  
Le connecteur vous permet de stocker un module Windows PowerShell partagé dans la configuration. Lorsque le connecteur exécute un script, le module Windows PowerShell est extrait dans le système de fichiers pour qu’il puisse être importé par chaque script.

Pour les scripts d’importation, d’exportation et de synchronisation de mot de passe, le module commun est extrait dans le dossier du connecteur MAData. Pour les scripts de découverte de schéma, de validation, de hiérarchie et de partition, le module commun est extrait dans le dossier %TEMP%. Dans les deux cas, le script de module commun extrait est nommé en fonction du paramètre de nom de script module commun.

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 à partir du dossier MAData, utilisez l’instruction suivante : `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 à partir du dossier %TEMP%, utilisez l’instruction suivante : `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validation des paramètres**  
Le script de validation est un script Windows PowerShell facultatif qui peut être utilisé pour vérifier que les paramètres de configuration du connecteur fournis par l’administrateur sont valides. La validation du serveur, les informations d’identification de connexion et les paramètres de connectivité sont des utilisations communes d’un script de validation. Le script de validation est appelé après modification des onglets et des boîtes de dialogue :

* Connectivité
* Paramètres globaux
* Configuration de partition

Le script de validation reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |Boîte de dialogue ou l’onglet de configuration qui a déclenché la demande de validation. |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |

Le script de validation doit renvoyer un seul objet ParameterValidationResult au pipeline.

**Découverte de schéma**  
Le script de découverte de schéma est obligatoire. Ce script renvoie les types d’objets, les attributs et les contraintes d’attribut que le service de synchronisation utilise lors de la configuration des règles de flux d’attribut. Le script de découverte de schéma s’exécute lors de la création du connecteur et remplit le schéma du connecteur. Il est également utilisé par l’action Actualiser le schéma dans Synchronization Service Manager.

Le script de découverte reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |

Le script doit renvoyer un objet [Schéma][schema] unique au pipeline. L’objet Schéma est composé d’objets [SchemaType][schemaT] qui représentent des types d’objets (par exemple, les utilisateurs et les groupes). L’objet SchemaType renferme un ensemble d’objets [SchemaAttribute][schemaA] qui représentent les attributs (par exemple, prénom, nom et adresse postale) du type.

**Paramètres supplémentaires**  
Outre les paramètres de configuration standard, vous pouvez définir des paramètres de configuration personnalisés supplémentaires qui sont spécifiques à l’instance du connecteur. Ces paramètres peuvent être spécifiés au niveau du connecteur, de la partition, ou exécuter des niveaux et accéder à partir du script Windows PowerShell approprié. Les paramètres de configuration personnalisés peuvent être stockés dans la base de données du Service de synchronisation au format de texte brut ou ils peuvent être chiffrés. Le service de synchronisation chiffre et déchiffre de façon automatique les paramètres de configuration de sécurité lorsque c’est nécessaire.

Pour spécifier les paramètres de configuration personnalisés, séparez le nom de chaque paramètre par une virgule (,).

Pour accéder aux paramètres de configuration personnalisés à partir d’un script, vous devez ajouter un trait de soulignement (\_) et la portée du paramètre (Global, Partition ou RunStep) comme suffixes du nom. Par exemple, pour accéder au paramètre FileName Global, utilisez cet extrait de code : `$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Fonctionnalités
L’onglet fonctionnalités de Management Agent Designer définit le comportement et les fonctionnalités du connecteur. Les sélections effectuées dans cet onglet ne peuvent pas être modifiées une fois le connecteur créé. Cette table répertorie les paramètres de capacité.

![Fonctionnalités](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Fonctionnalité | Description |
| --- | --- |
| [Style de nom unique][dnstyle] |Indique si le connecteur prend en charge les noms uniques et le cas échéant, le style. |
| [Type d’exportation][exportT] |Détermine le type des objets qui sont présentés au script d’exportation. <li>AttributeReplace : inclut l’ensemble des valeurs d’un attribut à valeurs multiples lorsque l’attribut change.</li><li>AttributeUpdate : inclut uniquement les écarts d’un attribut à valeurs multiples lorsque l’attribut change.</li><li>MultivaluedReferenceAttributeUpdate - contient un ensemble complet de valeurs d’attributs à valeurs multiples sans référence et uniquement pour les écarts des attributs de référence à valeurs multiples.</li><li>ObjectReplace – inclut tous les attributs d’un objet en cas de modification d’attribut</li> |
| [Normalisation des données][DataNorm] |Fait en sorte que le service de synchronisation normalise les attributs d’ancrage avant qu’ils soient fournis à des scripts. |
| [Confirmation d’objet][oconf] |Configure le comportement d’importation en attente dans le service de synchronisation. <li>Normal – comportement par défaut qui attend la confirmation de toutes les modifications exportées par importation</li><li>NoDeleteConfirmation – lorsqu’un objet est supprimé, aucune importation en attente n’est générée.</li><li>NoAddAndDeleteConfirmation – lorsqu’un objet est créé ou supprimé, aucune importation en attente n’est générée.</li> |
| Utiliser le nom unique en tant que point d’ancrage |Si le Style de nom unique est défini sur LDAP, l’attribut d’ancrage de l’espace de connecteur est également le nom unique. |
| Opérations simultanées de plusieurs connecteurs |Lorsqu’elle est activée, plusieurs connecteurs de Windows PowerShell peuvent s’exécuter simultanément. |
| Partitions |Lorsqu’elle est sélectionnée, le connecteur prend en charge plusieurs partitions et découvertes de partition. |
| Hiérarchie |Lorsqu’elle est activée, le connecteur prend en charge une structure hiérarchique de type LDAP. |
| Activer l’importation |Lorsque cette option est cochée, le connecteur importe les données via des scripts d’importation. |
| Activer l’importation d’écart |Lorsque cette option est cochée, le connecteur peut demander des écarts par rapport aux scripts d’importation. |
| Activer l’exportation |Lorsque cette option est cochée, le connecteur exporte les données via des scripts d’exportation. |
| Activer l’exportation complète |Lorsqu’elle est activée, les scripts d’exportation prennent en charge l’exportation de la totalité de l’espace de connecteur. Si vous utilisez cette option, l’activation de l’exportation doit elle aussi être activée. |
| Aucune valeur de référence dans le premier transfert d’exportation |Lorsqu’elle est activée, les attributs de référence sont exportés lors d’un deuxième transfert d’exportation. |
| Activer renommer l’objet |Lorsqu’elle est activée, les noms uniques peuvent être modifiés. |
| Supprimer-Ajouter en remplacement |Lorsqu’elle est activée, les opérations supprimer, ajouter sont exportées en tant que remplacement. |
| Activer les opérations de mot de passe |Lorsqu’elle est activée, les scripts de synchronisation de mot de passe sont pris en charge. |
| Activer l’exportation de mot de passe lors d’un premier passage |Lorsqu’elle est activée, les mots de passe définis lors de la configuration sont exportés lorsque l’objet est créé. |

### <a name="global-parameters"></a>Paramètres globaux
L’onglet Paramètres globaux du Concepteur de l’Agent de gestion vous permet de configurer les scripts Windows PowerShell qui sont exécutés par le connecteur. Vous pouvez également configurer des valeurs globales pour les paramètres de configuration personnalisés définis dans l’onglet Connectivité.

**Détection de partition**  
Une partition est un espace de noms distinct au sein d’un seul schéma partagé. Par exemple, dans Active Directory, chaque domaine est une partition d’une forêt. Une partition est le regroupement logique d’opérations d’importation et d’exportation. L’importation et l’exportation se trouvent dans un environnement de partition et toutes les opérations ont lieu dans ce contexte. Les partitions sont supposées représenter une hiérarchie dans l’annuaire LDAP. Le nom unique d’une partition permet de vérifier que tous les objets retournés se trouvent bien dans l’étendue de la partition. Le nom unique de la partition est également utilisé lors de la préparation de la métaverse vers l’espace de connecteur pour déterminer la partition à laquelle un objet va être associé au cours de l’exportation.

Le script de découverte de la partition reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |

Le script doit renvoyer un objet [Partition][part] unique ou une liste[T] d’objets de partition au pipeline.

**Découverte de la hiérarchie**  
Le script de découverte de hiérarchie est utilisé uniquement lorsque le style de nom unique employé est LDAP. Le script est utilisé pour vous permettre de rechercher et de sélectionner un ensemble de conteneurs qui sont considérés comme étant dans la portée ou hors de portée pour les opérations d’importation et d’exportation. Le script doit fournir uniquement une liste de nœuds enfants directs du nœud racine transmis au script.

Le script de découverte de partition reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| ParentNode |[HierarchyNode][hn] |Nœud racine de la hiérarchie sous lequel le script doit renvoyer des enfants directs. |

Le script doit retourner soit un objet HierarchyNode enfant unique, soit une liste [T] d’objets enfant HierarchyNode au pipeline.

#### <a name="import"></a>Importer
Les connecteurs qui prennent en charge les opérations d’importation doivent implémenter trois scripts.

**Début de l’importation**  
Le script de début d’importation est exécuté au début de l’exécution d’une opération d’importation. Au cours de cette étape, vous pouvez établir une connexion au système source et exécuter les étapes préparatoires avant d’importer des données depuis le système connecté.

Le script de démarrage reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informe le script sur le type d’importation à exécuter (delta ou complète), la partition, la hiérarchie, le filigrane et le format de page attendu. |
| Types |[Schema][schema] |Schéma de l’espace de connecteur qui est importé. |

Le script doit renvoyer un objet [OpenImportConnectionResults][oicres] au pipeline, par exemple : `Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importer des données**  
Le script d’importation de données est appelé par le connecteur jusqu’à ce que le script indique qu’il n’existe plus aucune donnée à importer. Le connecteur de Windows PowerShell a une taille de page de 9 999 objets. Si votre script renvoie plus de 9 999 objets pour importation, vous devez prendre en charge la pagination. Le connecteur expose une propriété de données personnalisée que vous pouvez utiliser dans un magasin de filigrane, pour qu’à chaque fois que le script d’importation de données est appelé, votre script reprenne l’importation d’objets là où elle s’était arrêtée.

Le script d’importation reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |Contient le filigrane (CustomData) qui peut être utilisé pendant les importations paginées et les écarts d’importation. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informe le script sur le type d’importation à exécuter (delta ou complète), la partition, la hiérarchie, le filigrane et le format de page attendu. |
| Types |[Schema][schema] |Schéma de l’espace de connecteur qui est importé. |

Le script d’importation de données doit écrire un objet List[[CSEntryChange][csec]] dans le pipeline. Cette collection se compose d’attributs CSEntryChange qui représentent chaque objet importé. Au cours d’une importation intégrale, cette collection doit comporter un jeu complet d’objets CSEntryChange qui dispose de tous les attributs de chaque objet. Pendant une importation d’écart, l’objet CSEntryChange contient les deltas de niveau d’attribut pour chaque objet à importer, ou une représentation complète des objets qui ont été modifiés (mode de remplacement).

**Fin de l’importation**  
À l’issue de l’importation, le script de fin d’importation s’exécute. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermeture des connexions aux systèmes et réponse aux défaillances).

Le script de fin reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informe le script sur le type d’importation à exécuter (delta ou complète), la partition, la hiérarchie, le filigrane et le format de page attendu. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |Informe le script sur la raison de l’interruption de l’importation. |

Le script doit renvoyer un objet [CloseImportConnectionResults][cicres] au pipeline, par exemple : `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportation
Identique à l’architecture d’importation du connecteur. Les connecteurs qui prennent en charge l’exportation doivent mettre en œuvre trois scripts.

**Début de l’exportation**  
Le script de début d’exportation est lancé au début de l’exécution d’une opération d’exportation. Au cours de cette étape, vous pouvez établir une connexion au système source et exécuter toutes les étapes préparatoires avant d’exporter des données depuis le système connecté.

Le script de début d’exportation reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informe le script sur le type d’exportation (delta ou complète) à exécuter, la partition, la hiérarchie et le format de page attendu. |
| Types |[Schema][schema] |Schéma de l’espace de connecteur qui est exporté. |

Le script ne doit renvoyer aucune sortie vers le pipeline.

**Exporter les données**  
Le service de synchronisation appelle le script d’exportation de données aussi souvent que nécessaire afin de traiter toutes les exportations en attente. Si l’espace de connecteur contient davantage d’exportations en attente que la taille de page du connecteur, le script d’exportation de données peut être appelé plusieurs fois, voire à plusieurs reprises pour le même objet.

Le script d’exportation reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| CSEntries |IList[CSEntryChange][csec] |Liste de tous les objets d’espace de connecteur avec les exportations de traitement au cours de ce passage. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informe le script sur le type d’exportation (delta ou complète) à exécuter, la partition, la hiérarchie et le format de page attendu. |
| Types |[Schema][schema] |Schéma de l’espace de connecteur qui est exporté. |

Le script de données d’exportation doit renvoyer un objet [PutExportEntriesResults][peeres] au pipeline. Cet objet n’a pas besoin d’inclure des informations de résultat pour chaque connecteur exporté, sauf si une erreur ou une modification de l’attribut d’ancrage change. Par exemple, pour renvoyer un objet PutExportEntriesResults au pipeline : `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Fin d’exportation**  
À l’issue de l’exportation, le script de fin d’exportation s’exécute. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermeture des connexions aux systèmes et réponse aux défaillances).

Le script d’exportation de fin reçoit les paramètres suivants du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informe le script sur le type d’exportation (delta ou complète) à exécuter, la partition, la hiérarchie et le format de page attendu. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |Informe le script sur la raison de l’interruption de l’exportation. |

Le script ne doit renvoyer aucune sortie vers le pipeline.

#### <a name="password-synchronization"></a>Synchronisation du mot de passe
Les connecteurs PowerShell Windows peuvent servir de cible pour les modifications/réinitialisations du mot de passe.

Le script de mot de passe reçoit les paramètres suivants de la part du connecteur :

| NOM | Type de données | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Tableau des paramètres de configuration pour le connecteur. |
| Informations d'identification |[PSCredential][pscred] |Contient les informations d’identification saisies par l’administrateur sur l’onglet Connectivité. |
| Partition |[Partition][part] |Partition d’annuaire dans laquelle se trouve CSEntry. |
| CSEntry |[CSEntry][cse] |Entrée d’espace de connecteur de l’objet reçu de à la suite d’un changement ou d’une réinitialisation de mot de passe. |
| OperationType |Chaîne |Indique si l’opération est une réinitialisation (**SetPassword**) ou une modification (**ChangePassword**). |
| PasswordOptions |[PasswordOptions][pwdopt] |Indicateurs qui spécifient le comportement de réinitialisation de mot de passe prévu. Ce paramètre est disponible uniquement si le type d’opération est défini sur **SetPassword**. |
| OldPassword |Chaîne |Remplie avec le mot de passe de l’ancien objet pour les modifications de mot de passe. Ce paramètre est disponible uniquement si le type d’opération est **ChangePassword**. |
| NewPassword |Chaîne |Rempli avec le nouveau mot de passe de l’objet que le script doit définir. |

En principe, le script de mot de passe ne doit pas renvoyer des résultats dans le pipeline Windows PowerShell. Si une erreur se produit dans le script de mot de passe, le script doit lever l’une des exceptions qui suivent pour informer le service de synchronisation sur le problème suivant :

* [PasswordPolicyViolationException][pwdex1] : levée si le mot de passe ne respecte pas la stratégie de mot de passe dans le système connecté.
* [PasswordIllFormedException][pwdex2] : levée si le mot de passe n’est pas acceptable pour le système connecté.
* [PasswordExtension][pwdex3] : levée pour toutes les autres erreurs dans le script de mot de passe.

## <a name="sample-connectors"></a>Exemple de connecteurs
Pour une présentation complète des exemples de connecteurs disponibles, consultez [Collection exemple de connecteurs Windows PowerShell][samp].

## <a name="other-notes"></a>Autres remarques
### <a name="additional-configuration-for-impersonation"></a>Configuration supplémentaire pour l’emprunt d’identité
Accordez à l’utilisateur qui subit l’emprunt d’identité les autorisations suivantes sur le serveur de service de synchronisation :

Accès en lecture aux clés de Registre suivantes :

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Environment

Pour déterminer l’identificateur de sécurité (SID) du compte de service synchronisation de service, exécutez les commandes PowerShell suivantes :

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Accès en lecture aux dossiers de système de fichiers suivants :

* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Remplacez le nom du connecteur Windows PowerShell par l’espace réservé {ConnectorName}.

## <a name="troubleshooting"></a>Résolution de problèmes
* Pour plus d’informations sur la façon d’activer la journalisation pour résoudre les problèmes du connecteur, consultez [Comment activer le suivi ETW pour les connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
