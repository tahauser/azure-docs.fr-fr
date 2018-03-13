---
title: Connecteur Lotus Domino | Microsoft Docs
description: "Cet article décrit comment configurer le connecteur Lotus Domino de Microsoft."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/119/2017
ms.author: barclayn
ms.openlocfilehash: 6c412be1c54e0378166791c61469c951bca3a583
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="lotus-domino-connector-technical-reference"></a>Référence technique du connecteur Lotus Domino
Cet article décrit le connecteur Lotus Domino. Cet article s’applique aux produits suivants :

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Nécessité d’utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Vue d’ensemble du connecteur Lotus Domino
Le connecteur Lotus Domino vous permet d’intégrer le service de synchronisation au serveur Lotus Domino d’IBM.

Globalement, la version actuelle du connecteur prend en charge les fonctionnalités suivantes :

| Fonctionnalité | Support |
| --- | --- |
| Source de données connectée |Serveur :  <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client :<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Scénarios |<li>Gestion du cycle de vie des objets</li><li>Gestion des groupes</li><li>Gestion des mots de passe</li> |
| Opérations |<li>Importation complète et différentielle</li><li>Exportation</li><li>Définition et modification du mot de passe HTTP</li> |
| Schéma |<li>Personne (utilisateurs itinérants, contacts (personnes sans certificat))</li><li>Groupe</li><li>Ressources (ressource, salle, réunion en ligne)</li><li>Base courrier en arrivée</li><li>Découverte dynamique des attributs pour les objets pris en charge</li><li>Prise en charge de 250 certificateurs personnalisés avec une organisation et des unités d’organisation</li> |

Le connecteur Lotus Domino utilise le client Lotus Notes pour communiquer avec le serveur Lotus Domino. Conséquence de cette dépendance, un client Lotus Notes pris en charge doit être installé sur le serveur de synchronisation. La communication entre le client et le serveur est mise en œuvre via l’interface d’interopérabilité .NET Lotus Notes (Interop.domino.dll). Cette interface facilite la communication entre la plateforme Microsoft.NET et le client Lotus Notes, et prend en charge l’accès aux documents et vues Lotus Domino. Pour une importation différentielle, il est également possible d’utiliser l’interface C++ native (en fonction de la méthode d’importation différentielle sélectionnée).

### <a name="prerequisites"></a>configuration requise
Avant d’utiliser le connecteur, vérifiez que vous disposez des composants requis suivants sur le serveur de synchronisation :

* Microsoft .NET 4.5.2 Framework ou version ultérieure
* Client Lotus Notes installé sur votre serveur de synchronisation
* Le connecteur Lotus Domino requiert que la base de données de schémas LDAP Lotus Domino par défaut (schema.nsf) figure sur le serveur d’annuaire Domino. Si tel n’est pas le cas, vous pouvez l’installer en exécutant ou en redémarrant le service LDAP sur le serveur Domino.

### <a name="connected-data-source-permissions"></a>Autorisations de la source de données connectée
Pour effectuer les tâches prises en charge dans le connecteur Lotus Domino, vous devez être membre des groupes suivants :

* Administrateurs avec accès total
* Administrateurs
* Administrateurs de base de données

Le tableau suivant répertorie les autorisations obligatoires pour chaque opération :

| Opération | Droits d’accès |
| --- | --- |
| Importer |<li>Lire les documents publics</li><li> Administrateur avec accès total (lorsque vous êtes membre du groupe Administrateurs avec accès total, vous bénéficiez automatiquement de l’accès effectif à une liste de contrôle d’accès.)</li> |
| Exporter et définir le mot de passe |Accès effectif :  <li>Création de documents</li><li>Supprimer des documents</li><li>Lire les documents publics</li><li>Écrire des documents publics</li><li>Répliquer ou copier des documents</li>Pour les opérations d’exportation, vous devez également avoir les rôles suivants : <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Opérations directes et AdminP
Les opérations vont directement dans l’annuaire Domino ou passent par le processus AdminP. Les tableaux suivants répertorient tous les objets et opérations pris en charge et, le cas échéant, la méthode d’implémentation connexe :

**Carnet d’adresses principal**

| Object | Créer | Mettre à jour | Supprimer |
| --- | --- | --- | --- |
| Personne |AdminP |Directement |AdminP |
| Groupe |AdminP |Directement |AdminP |
| MailInDB |Directement |Directement |Directement |
| Ressource |AdminP |Directement |AdminP |

**Carnet d’adresses secondaire**

| Object | Créer | Mettre à jour | Supprimer |
| --- | --- | --- | --- |
| Personne |N/A |Directement |Directement |
| Groupe |Directement |Directement |Directement |
| MailInDB |Directement |Directement |Directement |
| Ressource |N/A |N/A |N/A |

Lorsqu’une ressource est créée, un document Notes est créé. De même, lorsqu’une ressource est supprimée, le document Notes est supprimé.

### <a name="ports-and-protocols"></a>Ports et protocoles
Le client IBM Lotus Notes et les serveurs Domino communiquent à l’aide d’un appel de procédure distante Notes (NRPC), où NRPC doit utiliser le protocole TCP/IP. Le numéro de port par défaut est 1352, mais il peut être modifié par l’administrateur Domino.

### <a name="not-supported"></a>Non pris en charge
Les opérations suivantes ne sont pas prises en charge par la version actuelle du connecteur Lotus Domino :

* Déplacer des boîtes aux lettres entre des serveurs.

## <a name="create-a-new-connector"></a>Créer un connecteur
### <a name="client-software-installation-and-configuration"></a>Installation et configuration du logiciel client
Lotus Notes doit être installé sur le serveur **avant** l’installation du connecteur.

Lors de l’installation, assurez-vous de choisir **Single User Install**. L’installation par défaut **Multi-User Install** ne fonctionnera pas.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Dans la page des fonctionnalités, n’installez que les fonctionnalités Lotus Notes requises et **Client Single Logon**. L’ouverture de session Single Logon est requise pour que le connecteur puisse se connecter au serveur Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Remarque :** démarrez Lotus Notes une fois avec un utilisateur situé sur le même serveur que le compte que vous allez utiliser comme compte de service du connecteur. Veillez également à fermer le client Lotus Notes sur le serveur. Il ne peut pas s'exécuter en même temps que le Connecteur essaie de se connecter au serveur Domino.

### <a name="create-connector"></a>Créer un connecteur
Pour créer un connecteur Lotus Domino, dans **Synchronization Service**, sélectionnez **Management Agent** et **Create**. Sélectionnez le connecteur **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Si votre version du service de synchronisation permet de configurer **Architecture**, vérifiez que le connecteur a sa valeur par défaut pour s’exécuter dans **Process**.

### <a name="connectivity"></a>Connectivité
Sur la page de connectivité, vous devez spécifier le nom du serveur Lotus Domino et entrer les informations d’identification d’ouverture de session.  
![Connectivité](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La propriété de serveur Domino prend en charge deux formats pour le nom du serveur :

* ServerName
* ServerName/DirectoryName

Le format **ServerName/DirectoryName** est le format préféré pour cet attribut, car il fournit une réponse plus rapide lorsque le connecteur contacte le serveur Domino.

Le fichier UserID fourni est stocké dans la base de données de configuration du service de synchronisation.

Pour **Delta Import** , vous disposez des options suivantes :

* **Aucun**. Le connecteur n’effectue aucune importation différentielle.
* **Add/Update**. Le connecteur effectue une importation différentielle des opérations d’ajout et de mise à jour. Pour la suppression, une opération **Full Import** est requise. Cette opération utilise l’interopérabilité .Net.
* **Add/Update/Delete**. Le connecteur effectue une importation différentielle des opérations d’ajout, de mise à jour et de suppression. Cette opération utilise les interfaces C++ natives.

Dans **Schema Option** , vous disposez des options suivantes :

* **Default Schema**. Le connecteur détecte le schéma à partir du serveur Domino. Cette sélection correspond à l’option par défaut.
* **DSML-Schema**. Option utilisée uniquement si le serveur Domino n’expose pas le schéma. Ensuite, vous pouvez créer un fichier DSML avec le schéma et l’importer à la place. Pour plus d’informations sur DSML, consultez [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Lorsque vous cliquez sur Suivant, les paramètres de configuration UserID et de mot de passe sont vérifiés.

### <a name="global-parameters"></a>Paramètres globaux
Dans la page Global Parameters, configurez le fuseau horaire et les options des opérations d’importation et d’exportation.  
![Paramètres globaux](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Le paramètre **Domino Server Time Zone** définit l’emplacement de votre serveur Domino.

Cette option de configuration est requise pour prendre en charge les opérations d’ **importation différentielle** , car elle permet au service de synchronisation de déterminer les modifications entre les deux dernières importations.

>[!Note]
À compter de la mise à jour de mars 2017, l’écran Paramètres globaux inclut l’option de suppression de la base de données de messagerie de l’utilisateur pendant la suppression de l’utilisateur.

![Suppression de la boîte aux lettres de l’utilisateur](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Paramètres d’importation, méthode
Le paramètre **Perform Full Import By** présente les options suivantes :

* action
* View (recommandée)

**Search** utilise l’indexation dans Domino, mais souvent les index ne sont pas mis à jour en temps réel et les données renvoyées par le serveur ne sont pas toujours correctes. Pour un système avec de nombreuses modifications, cette option ne fonctionne pas très bien et indique de fausses suppressions dans certaines situations. Cependant, **Search** est plus rapide que **View**.

**View** est l’option recommandée, car elle fournit l’état correct des données. Elle est légèrement plus lente que Search.

#### <a name="creation-of-virtual-contact-objects"></a>Création d’objets contact virtuels
Le paramètre **Enable creation of \_Contact object** présente les options suivantes :

* Aucun
* Non-Reference Values
* Reference and Non-Reference Values

Dans Domino, les attributs de référence peuvent contenir de nombreux formats différents pour référencer d’autres objets. Pour pouvoir représenter différentes variantes, le connecteur implémente les objets \_Contact également appelés **contacts virtuels** (CV). Ces objets sont créés pour pouvoir être joints à des objets MV existants ou projetés en tant que nouveaux objets. Ce qui permet de conserver les références d’attribut.

En activant ce paramètre et, si le contenu d’un attribut de référence n’est pas un format de nom unique, un objet \_Contact est créé. Par exemple, un attribut de membre d’un groupe peut contenir des adresses SMTP. Il est également possible d’avoir le paramètre shortName et d’autres attributs présents dans les attributs de référence. Pour ce scénario, sélectionnez **Non-Reference Values**. Cette configuration est le paramètre le plus courant pour les implémentations Domino.

Lorsque Lotus Domino est configuré pour avoir des carnets d’adresses distincts avec des noms uniques différents représentant le même objet, il est possible de créer également des objets \_Contact pour toutes les valeurs de référence qui se trouvent dans un carnet d’adresses. Pour ce scénario, sélectionnez l’option **Reference and Non-Reference Values** .

Si l’attribut **FullName** a plusieurs valeurs dans Domino, il est également recommandé d’autoriser la création de contacts virtuels afin de résoudre les références. Par exemple, cet attribut peut avoir plusieurs valeurs après un mariage ou un divorce. Cochez la case **Enable […] FullName has multiple values** pour ce scénario.

En joignant les attributs corrects, les objets \_Contact sont joints à l’objet MV.

La mention VC=\_Contact est ajoutée à leur nom unique.

#### <a name="import-settings-conflict-object"></a>Importer les paramètres, objet de conflit
**Exclude Conflict Object**

Dans une implémentation Domino vaste, il est possible que plusieurs objets aient le même nom unique en raison de problèmes de réplication. Dans ce cas, le connecteur voit deux objets avec des paramètres UniversalID différents mais avec le même nom unique. Ce conflit entraîne la création d’un objet temporaire dans l’espace du connecteur. Le connecteur peut ignorer les objets qui ont été sélectionnés dans Domino comme victimes de réplication. Il est recommandé de laisser cette case cochée.

#### <a name="export-settings"></a>Paramètres d’exportation
Si l’option **Use AdminP for updating references** n’est pas sélectionnée, l’exportation des attributs de référence, tels que les membres, constitue un appel direct et n’utilise pas le processus AdminP. N’utilisez cette option que si AdminP n’a pas été configuré pour préserver l’intégrité référentielle.

#### <a name="routing-information"></a>Informations de routage
Dans Domino, il est possible qu’un attribut de référence possède des informations de routage incorporées en tant que suffixe au nom unique. Par exemple, l’attribut de membre d’un groupe pourrait contenir **CN=example/organization@ABC**. Le suffixe @ABC constitue les informations de routage. Les informations de routage sont utilisées par Domino pour envoyer des e-mails au système Domino approprié, qui peut se trouver dans une autre organisation. Dans le champ Routing Information, vous pouvez spécifier les suffixes de routage utilisés dans l’organisation pour le connecteur. Si l’une de ces valeurs est détectée comme suffixe dans un attribut de référence, les informations de routage sont supprimées de la référence. Si aucune correspondance ne peut être établie entre le suffixe de routage d’une référence et l’une des valeurs spécifiées, un objet \_Contact est créé. Ces objets \_Contact sont créés avec la mention **RO=@<RoutingSuffix>** insérée dans le nom unique. Pour ces objets \_Contact, les attributs suivants sont également ajoutés pour autoriser la jonction à un objet réel si nécessaire : \_routingName, \_contactName, \_displayName et UniversalID.

#### <a name="additional-address-books"></a>Carnets d’adresses supplémentaires
Si la fonctionnalité **Directory Assistance** n’est pas installée (afin de fournir le nom des carnets d’adresses secondaires), vous pouvez entrer manuellement ces carnets d’adresses.

#### <a name="multivalued-transformation"></a>Transformation à plusieurs valeurs
Dans Lotus Domino, de nombreux attributs possèdent plusieurs valeurs. Les attributs de métaverse correspondants ont généralement une valeur unique. En configurant les options d’opération d’importation et d’exportation, vous permettez au connecteur de faciliter la conversion requise des attributs affectés.

**Export**  
L’option d’opération d’exportation prend en charge deux modes :

* Append item
* Replace item

**Replace Item** : lorsque vous sélectionnez cette option, le connecteur supprime toujours les valeurs actuelles de l’attribut dans Domino, et les remplace par les valeurs fournies. Les valeurs fournies peuvent être uniques ou multiples.

Par exemple : l’attribut Assistant d’un objet Personne possède les valeurs suivantes :

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un nouvel assistant nommé **David Alexander** est affecté à cet objet Personne, le résultat est :

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Append Item** : lorsque vous sélectionnez cette option, le connecteur conserve les valeurs existantes de l’attribut dans Domino et insère les nouvelles valeurs en haut de la liste de données.

Par exemple : l’attribut Assistant d’un objet Personne possède les valeurs suivantes :

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un nouvel assistant nommé **David Alexander** est affecté à cet objet Personne, le résultat est :

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importationation**  
L’option d’opération d’importation prend en charge deux modes :

* Default
* Multivalued to Single Value

**Default** : lorsque vous sélectionnez l’option Default, toutes les valeurs de tous les attributs sont importées.

**Multivalued to Single Value** : lorsque vous sélectionnez cette option, un attribut à plusieurs valeurs est converti en un attribut à valeur unique. Si plusieurs valeurs existent, la valeur en haut (en général, c’est aussi la plus récente) est utilisée.

Par exemple : l’attribut Assistant d’un objet Personne possède les valeurs suivantes :

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

La mise à jour la plus récente pour cet attribut est **David Alexander**. Étant donné que l’option d’opération d’importation est définie sur Multivalued to Single Value, le connecteur importe uniquement **David Alexander** dans l’espace de connecteur.

La logique qui convertit les attributs à plusieurs valeurs en attributs à valeur unique ne s’applique pas à l’attribut de membre d’un groupe, ni à l’attribut de nom complet de la personne.

Il est également possible de configurer les règles de transformation de l’importation et l’exportation en attributs à plusieurs valeurs par attribut en tant qu’exception à la règle générale. Pour configurer cette option, entrez la valeur pour [objecttype].[attributename] dans les zones de texte **import exclusion attribute list** et **export exclusion attribute list**. Par exemple, si vous entrez Person.Assistant et que la configuration générale est définie pour importer toutes les valeurs, l’assistant n’importe que la première valeur.

#### <a name="certifiers"></a>Autorités de certification
Le connecteur répertorie toutes les organisations/unités d’organisation. Pour pouvoir exporter des objets Personne vers le carnet d’adresses principal, une autorité de certification avec son mot de passe est requise.

Le champ **Password for all Certifers** peut être utilisé si toutes les autorités de certification ont le même mot de passe. Vous pouvez y entrer le mot de passe et spécifier uniquement le fichier de l’autorité de certification.

Si vous importez uniquement, vous n’avez pas à spécifier d’autorité de certification.

### <a name="configure-provisioning-hierarchy"></a>Configurer la hiérarchie de l’approvisionnement
Lorsque vous configurez le connecteur Lotus Domino, ignorez cette page de boîte de dialogue. Le connecteur Lotus Domino ne prend pas en charge la hiérarchie de l’approvisionnement.  
![Hiérarchie d’approvisionnement](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurer des partitions et des hiérarchies
Lorsque vous configurez des partitions et des hiérarchies, vous devez sélectionner le carnet d’adresses principal appelé NAB=names.nsf. Outre le carnet d’adresses principal, vous pouvez sélectionner des carnets d’adresses secondaires, s’ils existent.  
![Partitions](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Sélectionner les attributs
Lorsque vous configurez vos attributs, vous devez sélectionner tous les attributs dont le préfixe est **\_MMS\_**. Ces attributs sont obligatoires lorsque vous approvisionnez de nouveaux objets sur Lotus Domino.

![Attributs](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestion du cycle de vie des objets
Cette section fournit une vue d’ensemble des différents objets dans Domino.

### <a name="person-objects"></a>Objets Personne
L’objet Personne représente les utilisateurs dans l’organisation et dans les unités d’organisation. Outre les attributs par défaut, l’administrateur Domino peut ajouter des attributs personnalisés à un objet Personne. Au minimum, un objet Personne doit inclure tous les attributs obligatoires. Pour obtenir une liste complète des attributs obligatoires, consultez [Propriétés Lotus Notes](#lotus-notes-properties). Pour inscrire un objet Personne, les conditions préalables suivantes doivent être remplies :

* Le carnet d’adresses (names.nsf) doit avoir été défini et être le carnet d’adresses principal.
* Vous devez avoir l’ID et le mot de passe de l’autorité de certification de l’organisation/unité d’organisation pour inscrire un utilisateur particulier dans l’organisation/l’unité d’organisation.
* Vous devez définir un ensemble spécifique de propriétés Lotus Notes pour un objet Personne. Ces propriétés sont utilisées pour approvisionner cet objet. Pour plus d’informations, consultez la section [Propriétés Lotus Notes](#lotus-notes-properties) plus loin dans ce document.
* Le mot de passe HTTP initial d’un objet Personne est un attribut défini lors de l’approvisionnement.
* L’objet Personne doit être de l’un des trois types pris en charge suivants :
  1. Normal User, avec un fichier de courrier et un fichier UserID
  2. Roaming User (un Normal User qui inclut tous les fichiers de base de données de l’itinérance)
  3. Contacts (utilisateur sans fichier d’ID)

Les personnes (à l’exception des contacts) peuvent être regroupées dans US Users et International Users, selon la valeur de la propriété \_MMS\_IDRegType. Ces personnes utilisent le client Notes pour accéder aux serveurs Lotus Domino. Elles ont un ID Notes et un document Personne. Si elles utilisent la messagerie Notes, elles ont également un fichier de messagerie. L’utilisateur doit être inscrit pour être actif. Pour plus d'informations, consultez les pages suivantes :

* [Configuration d’utilisateurs Notes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [Enregistrement des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Gestion des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Changement du nom des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Toutes ces opérations sont effectuées dans Lotus Domino, puis importées dans le service de synchronisation.

### <a name="resources-and-rooms"></a>Ressources et salles
Une ressource est un autre type d’une base de données dans Lotus Domino. Les ressources peuvent être des salles de conférence avec différents types d’équipements comme des projecteurs. L’attribut de type de ressource définit certains sous-types de ressources pris en charge par le connecteur Lotus Domino :

| Type de ressource | Attribut de type de ressource |
| --- | --- |
| Salle |1 |
| Ressource (autre) |2 |
| Réunion en ligne |3 |

Pour que le type d’objet Ressource fonctionne, les conditions suivantes sont requises :

* La base de données de réservation de ressources doit déjà exister dans le serveur Domino connecté
* Le site est déjà défini pour la ressource

La base de données de réservation de ressources contient trois types de documents :

* Profil du site
* Ressource
* Réservation

Pour plus d’informations sur la configuration de la base de données de réservation de ressources, consultez [Setting up the Resource Reservations database (Configuration de la base de données de réservation de ressources)](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Création, mise à jour et suppression de ressources**  
Le connecteur Lotus Domino exécute les opérations de création, de mise à jour et de suppression dans la base de données de réservation de ressources. Les ressources sont créées en tant que documents dans Names.nsf (le carnet d’adresses principal). Pour plus d’informations sur la modification et la suppression de documents Ressource, consultez [Modification et suppression de documents Ressource](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Opérations d’importation et d’exportation de ressources**  
Les ressources peuvent être importées dans et exportées du service de synchronisation, comme tout autre type d’objet. Sélectionnez le type d’objet Ressource lors de la configuration. Pour que l’opération d’exportation réussisse, vous devez disposer d’informations pour le type de ressource, la base de données de conférence et le nom du site.

### <a name="mail-in-databases"></a>Bases courrier en arrivée
Une Base courrier en arrivée est une base de données conçue pour recevoir du courrier. Il s’agit d’une messagerie Lotus Domino qui n’est pas associée à un compte d’utilisateur Lotus Domino spécifique (elle ne possède pas son propre fichier d’ID et son propre mot de passe). Une Base courrier en arrivée possède un UserID unique (« nom court ») associé et sa propre adresse e-mail.

Si vous avez besoin d’une messagerie distincte, avec sa propre adresse e-mail partageable avec différents utilisateurs (par exemple, group@contoso.com), une base courrier en arrivée est créée. L’accès à cette messagerie est contrôlé via sa liste de contrôle d’accès (ACL), qui contient les noms des utilisateurs Notes autorisés à ouvrir la messagerie.

Pour obtenir la liste des attributs requis, consultez la section [Attributs obligatoires](#mandatory-attributes) plus loin dans cet article.

Lorsqu’une base de données est conçue pour recevoir du courrier, un document Base courrier en arrivée est créé dans Lotus Domino. Ce document doit exister dans l’annuaire Domino de chaque serveur qui stocke une copie de la base de données. Pour une description détaillée de la création d’un document Base courrier en arrivée, consultez [Création d’un document Base courrier en arrivée](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Avant de créer une Base courrier en arrivée, la base de données doit déjà exister (elle doit avoir été créée par l’administrateur de Lotus) sur le serveur Domino.

### <a name="group-management"></a>Gestion des groupes
Bénéficiez d’une vue d’ensemble précise de la gestion des groupes Lotus Domino grâce aux ressources suivantes :

* [Utilisation de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Création d’un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Création et modification de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Gestion de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Renommer un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestion des mots de passe
Pour un utilisateur Lotus Domino inscrit, il existe deux types de mots de passe :

1. Mot de passe utilisateur (stocké dans un fichier User.id)
2. Mot de passe Internet / HTTP

Le connecteur Lotus Domino prend uniquement en charge les opérations avec mot de passe HTTP.

Pour gérer les mots de passe, vous devez activer la gestion des mots de passe du connecteur dans Management Agent Designer. Pour activer la gestion des mots de passe, sélectionnez **Enable password management** dans la boîte de dialogue **Configure Extensions**.  
![Configure Extensions](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Le connecteur Lotus Domino prend en charge les opérations suivantes sur le mot de passe Internet :

* Set Password : cette option définit un nouveau mot de passe HTTP/Internet pour l’utilisateur dans Domino. Par défaut, le compte est également déverrouillé. L’indicateur de déverrouillage est affiché sur l’interface WMI du moteur de synchronisation.
* Change Password : dans ce scénario, un utilisateur peut souhaiter modifier le mot de passe ou est invité à le faire après un délai spécifié. Pour que cette opération ait lieu, les deux mots de passe (l’ancien et le nouveau) sont obligatoires. Une fois la modification effectuée, le nouveau mot de passe est mis à jour dans Lotus Domino.

Pour plus d’informations, consultez les pages suivantes :

* [Utilisation de la fonctionnalité de verrouillage Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Gestion des mots de passe Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informations de référence
Cette section répertorie des descriptions et des exigences des attributs pour le connecteur Lotus Domino.

### <a name="lotus-notes-properties"></a>Propriétés Lotus Notes
Lorsque vous approvisionnez des objets Personne sur votre annuaire Lotus Domino, vos objets doivent posséder un ensemble spécifique de propriétés avec des valeurs spécifiques renseignées. Ces valeurs sont uniquement requises pour les opérations de création.

Le tableau suivant répertorie ces propriétés ainsi que leur description.

| Propriété | Description |
| --- | --- |
| \_MMS_AltFullName |L’autre nom complet de l’utilisateur. |
| \_MMS_AltFullNameLanguage |La langue à utiliser pour spécifier l’autre nom complet de l’utilisateur. |
| \_MMS_CertDaysToExpire |Le nombre de jours avant l’expiration du certificat à compter de la date actuelle. Si la valeur n’est pas spécifiée, la date par défaut est dans deux ans à compter de la date actuelle. |
| \_MMS_Certifier |Propriété qui contient le nom de la hiérarchie organisationnelle de l’autorité de certification. Par exemple : OU=Unité d’organisation,O=Organisation,C=Pays. |
| \_MMS_IDPath |Si la propriété est vide, aucun fichier d’identification utilisateur n’est créé localement sur le serveur de synchronisation. Si la propriété contient un nom de fichier, un fichier UserID est créé dans le dossier madata. Cette propriété peut également contenir un chemin d’accès complet. |
| \_MMS_IDRegType |Les personnes peuvent être classées dans Contacts, US Users et International Users. Le tableau suivant répertorie les valeurs de sortie possibles : <li>0 - Contact</li><li>1 - Utilisateur des États-Unis</li><li>2 - Utilisateur international</li> |
| \_MMS_IDStoreType |Propriété requise pour US Users et International Users. La propriété contient une valeur entière qui spécifie si l’identification de l’utilisateur est stockée en tant que pièce jointe dans le carnet d’adresses Notes ou dans le fichier de courrier de l’objet Personne. Si le fichier User ID est une pièce jointe au carnet d’adresses, il peut éventuellement être créé en tant que fichier avec \_MMS_IDPath. <li>Vide : fichier d’ID stocké dans le coffre d’ID, aucun fichier d’identification (utilisé pour les contacts).</li><li> 1 : pièce jointe dans le carnet d’adresses Notes. La propriété \_MMS_Password doit être définie pour les fichiers d’identification utilisateur</li><li>2 : ID stocké dans le fichier de courrier de l’objet Personne. La propriété \_MMS_UseAdminP doit être définie sur false pour que le fichier de messages soit créé lors de l’enregistrement de l’objet Personne. La propriété \_MMS_Password doit être définie pour les fichiers d’identification utilisateur.</li> |
| \_MMS_MailQuotaSizeLimit |Le nombre de mégaoctets autorisés pour la base de données de fichiers e-mail. |
| \_MMS_MailQuotaWarningThreshold |Le nombre de mégaoctets autorisés pour la base de données de fichiers e-mail avant l’émission d’un avertissement. |
| \_MMS_MailTemplateName |Le fichier de modèle d’e-mail utilisé pour créer le fichier d’e-mail de l’utilisateur. Si un modèle est spécifié, le fichier de courrier est créé à l’aide du modèle spécifié. Si aucun modèle n’est spécifié, le fichier de modèle par défaut est utilisé pour créer le fichier. |
| \_MMS_OU |Propriété facultative qui représente le nom de l’UO en tant qu’autorité de certification. Cette propriété doit être vide pour les contacts. |
| \_MMS_Password |Propriété requise pour les utilisateurs. La propriété contient le mot de passe pour le fichier d’identification de l’objet. |
| \_MMS_UseAdminP |La propriété doit être définie sur true si le fichier de courrier doit être créé par le processus AdminP sur le serveur Domino (asynchrone vers le processus d’exportation). Si la propriété est définie sur false, le fichier de courrier est créé avec l’utilisateur Domino (de manière synchronisée dans le processus d’exportation). |

Pour un utilisateur avec un fichier d’identification associé, la propriété \_MMS_Password doit contenir une valeur. Pour l’accès aux e-mails via le client Lotus Notes, les propriétés MailServer et MailFile d’un utilisateur doivent contenir une valeur.

Pour accéder aux e-mails via un navigateur web, les propriétés suivantes doivent contenir des valeurs :

* MailFile : propriété requise qui contient le chemin d’accès sur le serveur Lotus Domino où le fichier de courrier est stocké.
* MailServer : propriété requise qui contient le nom du serveur Lotus Domino. Il s’agit du nom à utiliser lorsque vous créez le fichier de courrier Lotus sur le serveur Domino.
* HTTPPassword : propriété facultative qui contient le mot de passe de l’accès web pour l’objet.

Pour accéder au serveur Domino sans la fonctionnalité de messagerie, la propriété HTTPPassword doit contenir une valeur. Les propriétés MailFile et MailServer peuvent être vides.

Avec \_MMS_ IDStoreType = 2 (ID stocké dans le fichier de courrier), la propriété MailSystem de NotesRegistrationclass est définie sur REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Attributs obligatoires
Le connecteur Lotus Domino prend principalement en charge les types d’objet (types de document) :

* Groupe
* Base courrier en arrivée
* Personne
* Contact (personne sans autorité de certification)
* Ressource

Cette section répertorie les attributs obligatoires pour chaque objet pris en charge à exporter vers un serveur Domino.

| Type d'objet | Attributs obligatoires |
| --- | --- |
| Groupe |<li>ListName</li> |
| Base courrier en arrivée |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Personne |<li>LastName</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Contact (personne sans autorité de certification) |<li>\_MMS_IDRegType</li> |
| Ressource |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Site</li><li>DisplayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Questions et problèmes courants
### <a name="schema-detection-does-not-work"></a>La détection du schéma ne fonctionne pas
Pour pouvoir détecter le schéma, le fichier schema.nsf doit être présent sur le serveur Domino. Ce fichier ne s’affiche que si le protocole LDAP est installé sur le serveur. Si le schéma n’est pas détectable, vérifiez les éléments suivants :

* Le fichier schema.nsf est présent dans le dossier racine du serveur Domino.
* L’utilisateur dispose des autorisations nécessaires pour consulter le fichier schema.nsf.
* Forcez un redémarrage du serveur LDAP. Ouvrez la **console Lotus Domino** et utilisez la commande **Tell LDAP ReloadSchema** pour recharger le schéma.

### <a name="not-all-secondary-address-books-are-visible"></a>Les carnets d’adresses secondaires ne sont pas tous visibles
Le connecteur Domino utilise la fonctionnalité **Directory Assistance** pour rechercher les carnets d’adresses secondaires. Si les carnets d’adresses secondaires sont manquants, vérifiez si [Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) a été activée et configurée sur le serveur Domino.

### <a name="custom-attributes-in-domino"></a>Attributs personnalisés dans Domino
Dans Domino, il existe plusieurs façons d’étendre le schéma pour le faire apparaître comme un attribut personnalisé consommable par le connecteur.

**Approche 1 : Étendre le schéma Lotus Domino**

1. Créez une copie du modèle d’annuaire Domino {PUBNAMES.NTF} en suivant [cette procédure](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (ne personnalisez pas le modèle d’annuaire IBM Lotus Domino par défaut) :
2. Ouvrez la copie du modèle d’annuaire Domino {CONTOSO.NTF} que vous venez de créer dans Domino Designer et suivez les étapes ci-après :
   * Cliquez sur Shared Elements et développez Subforms.
   * Double-cliquez sur le sous-formulaire ${ObjectName}InheritableSchema (où {ObjectName} est le nom de la classe d’objets structurels par défaut, par exemple Person).
   * Nommez l’attribut que vous souhaitez ajouter dans le schéma {MyPersonAtrribute} et correspondant à cet attribut. Créez un champ en sélectionnant le menu **Create** puis l’option **Field**.
   * Dans le champ ajouté, définissez les propriétés en sélectionnant le type, le style, la taille, la police et d’autres paramètres associés dans la fenêtre des propriétés du champ.
   * Comme valeur par défaut, conservez le nom donné à cet attribut (par exemple, si le nom de l’attribut est MyPersonAttribute, conservez ce nom comme valeur par défaut).
   * Enregistrez le sous-formulaire ${ObjectName}InheritableSchema avec les valeurs mises à jour.
3. Remplacez le modèle d’annuaire Domino {PUBNAMES.NTF} par le nouveau modèle personnalisé {CONTOSO.NTF} en suivant [cette procédure](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Fermez l’administrateur Domino et ouvrez la console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
   * Dans la console Domino, insérez la commande sous **Domino Command** pour redémarrer le service LDAP : [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * Pour recharger le schéma LDAP, utilisez la commande « Tell LDAP » - « Tell LDAP ReloadSchema ».
5. Ouvrez l’administrateur Domino et sélectionnez l’onglet People & Groups pour voir l’attribut ajouté au paramètre Add Person dans Domino.
6. Ouvrez Schema.nsf dans l’onglet **Files** et vérifiez que l’attribut ajouté figure dans la classe d’objets LDAP dominoPerson.

**Approche 2 : Créer une auxClass avec l’attribut personnalisé et l’associer à la classe d’objets**

1. Créez une copie du modèle d’annuaire Domino {PUBNAMES.NTF} en suivant [cette procédure](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (ne personnalisez jamais le modèle d’annuaire IBM Lotus Domino par défaut) :
2. Ouvrez la copie du modèle d’annuaire Domino {CONTOSO.NTF} que vous venez de créer dans Domino Designer.
3. Dans le volet de gauche, sélectionnez Shared Code, puis Subforms.
4. Cliquez sur New Subform.
5. Procédez comme suit pour spécifier les propriétés du nouveau sous-formulaire :
   * Avec le nouveau sous-formulaire ouvert, choisissez Design - Subform Properties.
   * En regard de la propriété Name, entrez un nom pour la classe d’objets auxiliaire, par exemple, TestSubform.
   * Laissez la propriété Options « Include in Insert Subform... dialog » sélectionnée.
   * Désélectionnez la propriété Options « Render pass through HTML in Notes ».
   * Ne modifiez pas les autres propriétés, et fermez la boîte de dialogue Subform Properties.
   * Enregistrez et fermez le nouveau sous-formulaire.
6. Procédez comme suit pour ajouter un champ afin de définir la classe d’objets auxiliaire :
   * Ouvrez le sous-formulaire que vous avez créé.
   * Sélectionnez Create, Field.
   * En regard de Name sous l’onglet Basics de la boîte de dialogue Field, spécifiez le nom de votre choix, par exemple : {MyPersonTestAttribute}.
   * Dans le champ ajouté, définissez les propriétés en sélectionnant le type, le style, la taille, la police et des propriétés associées.
   * Comme valeur par défaut, conservez le nom donné à cet attribut (par exemple, si le nom de l’attribut est MyPersonTestAttribute, conservez ce nom comme valeur par défaut).
   * Enregistrez le sous-formulaire avec les valeurs mises à jour, puis procédez comme suit :
     * Dans le volet de gauche, sélectionnez Shared Code, puis Subforms.
     * Sélectionnez le nouveau sous-formulaire, puis choisissez Design, Design Properties.
     * Cliquez sur le troisième onglet à partir de la gauche, puis sélectionnez **Propagate this prohibition of design change**.
7. Ouvrez le sous-formulaire ${ObjectName}ExtensibleSchema, (où {ObjectName} est le nom de la classe d’objets structurels par défaut, comme Person).
8. Insérez un document Ressource et sélectionnez le sous-formulaire (que vous venez de créer, par exemple TestSubform) et enregistrez le sous-formulaire ${ObjectName}ExtensibleSchema.
9. Remplacez le modèle d’annuaire Domino {PUBNAMES.NTF} par le nouveau modèle personnalisé {CONTOSO.NTF} en suivant [cette procédure](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Fermez l’administrateur Domino et ouvrez la console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
    * Dans la console Domino, insérez la commande sous **Domino Command** pour redémarrer le service LDAP : [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Pour recharger le schéma LDAP, utilisez la commande **Tell LDAP ReloadSchema**.
11. Ouvrez l’administrateur Domino et sélectionnez l’onglet People & Groups pour voir l’attribut ajouté au paramètre Add Person dans Domino (sous l’onglet Others).
12. Ouvrez Schema.nsf à partir de l’onglet **Files** et observez l’attribut ajouté sous la classe d’objets auxiliaire TestSubform LDAP.

**Approche 3 : Ajouter l’attribut personnalisé à la classe ExtensibleObject**

1. Ouvrez le fichier {Schema.nsf} placé dans le répertoire racine.
2. Sélectionnez les classes d’objets LDAP dans le menu de gauche sous **All Schema Documents**, puis cliquez sur le bouton **Add Object class** :
3. Indiquez le nom LDAP au format {zzzExtensibleSchema} (où zzz est le nom de la classe d’objets structurels par défaut, par exemple Person). Par exemple, pour étendre le schéma pour la classe d’objets Person, indiquez le nom LDAP {PersonExtensibleSchema}.
4. Indiquez un nom de classe d’objets supérieure pour lequel vous souhaitez étendre le schéma. Par exemple, pour étendre le schéma pour la classe d’objets Person, indiquez le nom de la classe d’objets supérieure [dominoPerson] :
5. Indiquez un OID valide correspondant à la classe d’objets.
6. Sélectionnez les attributs étendus ou personnalisés sous les champs Mandatory ou Optional Attribute Types selon les besoins :
7. Après avoir ajouté les attributs requis à ExtensibleObjectClass, cliquez sur **Save & Close**.
8. Une classe ExtensibleObjectClass est créée pour la classe d’objets par défaut avec des attributs étendus.

## <a name="troubleshooting"></a>Résolution de problèmes
* Pour plus d’informations sur la façon d’activer la journalisation pour résoudre les problèmes du connecteur, consultez [Comment activer le suivi ETW pour les connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
