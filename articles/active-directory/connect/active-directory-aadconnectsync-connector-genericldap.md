---
title: "Connecteur LDAP générique | Microsoft Docs"
description: "Cet article décrit comment configurer le connecteur LDAP générique Microsoft."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 984beeb0-4d91-4908-ad81-c19797c4891b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: fe8db8f2a2412a3dfdf31201678c51e4fa0cee30
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="generic-ldap-connector-technical-reference"></a>Référence technique au connecteur LDAP générique
Cet article décrit le connecteur LDAP générique. Cet article s’applique aux produits suivants :

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Nécessité d’utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

En référence aux RFC IETF, ce document est au format (RFC [numéro RFC]/[section dans le document RFC]), par exemple (RFC 4512/4.3).
Vous trouverez d’autres informations à l’adresse http://tools.ietf.org/html/rfc4500 (vous devez remplacer 4500 par le numéro de RFC correct).

## <a name="overview-of-the-generic-ldap-connector"></a>Vue d’ensemble du connecteur LDAP générique
Le connecteur LDAP générique vous permet d’intégrer le service de synchronisation dans le serveur LDAP v3.

Certaines opérations et certains éléments de schéma, notamment ceux qui sont nécessaires pour effectuer l’importation différentielle, ne sont pas spécifiés dans la RFC de l’IETF. Pour ces opérations, seuls les annuaires LDAP spécifiés explicitement sont pris en charge.

Globalement, la version actuelle du connecteur prend en charge les fonctionnalités suivantes :

| Fonctionnalité | Support |
| --- | --- |
| Source de données connectée |Le connecteur est pris en charge avec tous les serveurs v3 LDAP (compatibles RFC 4510). Il a été testé avec les éléments suivants :  <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Catalogue global Microsoft Active Directory (AD GC)</li><li>Serveur d’annuaire 389</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (précédemment Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>**Répertoires notables non pris en charge :** <li>Microsoft Active Directory Domain Services (AD DS) [utiliser le connecteur Active Directory intégré à la place]</li><li>Oracle Internet Directory (OID)</li> |
| Scénarios |<li>Gestion du cycle de vie des objets</li><li>Gestion des groupes</li><li>Gestion des mots de passe</li> |
| Opérations |Tous les annuaires LDAP prennent en charge les opérations suivantes :  <li>Importation complète</li><li>Exportation</li>Les opérations suivantes sont uniquement prises en charge dans les annuaires spécifiés :<li>Importation différentielle</li><li>Définition du mot de passe, modification du mot de passe</li> |
| Schéma |<li>Le schéma est détecté à partir du schéma LDAP (RFC3673 et RFC4512/4.2)</li><li>Prend en charge des classes structurelles, les classes auxiliaires et la classe d’objets extensibleObject (RFC4512/4.3)</li> |

### <a name="delta-import-and-password-management-support"></a>Importation différentielle et prise en charge de la gestion par mot de passe
Les répertoires pris en charge pour l’importation différentielle et la gestion de mot de passe :

* Microsoft Active Directory Lightweight Directory Services (AD LDS)
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe
* Catalogue global Microsoft Active Directory (AD GC)
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe
* Serveur d’annuaire 389
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Apache Directory Server
  * Ne prend pas en charge l’importation différentielle depuis ce répertoire n’a pas de journal des modifications persistantes
  * Prend en charge la définition de mot de passe
* IBM Tivoli DS
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Isode Directory
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Novell eDirectory et NetIQ eDirectory
  * Prend en charge les opérations d’ajout, de mise à jour et de renommage pour l’importation différentielle
  * Ne prend pas en charge les opérations de suppression pour l’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Open DJ
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Open DS
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Open LDAP (openldap.org)
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe
  * Ne prend pas en charge la modification de mot de passe
* Oracle (précédemment Sun) Directory Server Enterprise Edition
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* RadiantOne Virtual Directory Server (VDS)
  * Doit utiliser la version 7.1.1 ou une version ultérieure
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe
* Sun One Directory Server
  * Prend en charge toutes les opérations d’importation différentielle
  * Prend en charge la définition de mot de passe et la modification de mot de passe

### <a name="prerequisites"></a>Composants requis
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

* Microsoft .NET 4.5.2 Framework ou version ultérieure

### <a name="detecting-the-ldap-server"></a>Détection du serveur LDAP
Le connecteur utilise diverses techniques pour détecter et identifier le serveur LDAP. Le connecteur utilise la DSE racine pour trouver le nom du fournisseur et la version, et il recherche dans le schéma des objets uniques et des attributs connus pour exister dans certains serveurs LDAP. Ces données, si elles sont détectées, sont utilisées pour prérenseigner les options de configuration du connecteur.

### <a name="connected-data-source-permissions"></a>Autorisations de la source de données connectée
Pour effectuer des opérations d’importation et d’exportation d’objets dans l’annuaire connecté, le compte de connecteur doit disposer des autorisations suffisantes. Le connecteur a besoin d’autorisations en écriture pour pouvoir exporter et d’autorisations en lecture pour pouvoir importer. La configuration d’autorisation est exécutée au sein des expériences de gestion sur le répertoire cible lui-même.

### <a name="ports-and-protocols"></a>Ports et protocoles
Le connecteur utilise le numéro de port spécifié dans la configuration (par défaut, 389 pour LDAP et 636 pour LDAPS).

Pour le protocole LDAPS, vous devez utiliser SSL 3.0 ou TLS. SSL 2.0 n’est pas pris en charge et ne peut être activé.

### <a name="required-controls-and-features"></a>Fonctionnalités et contrôles requis
Les contrôles/fonctionnalités LDAP suivants doivent être disponibles sur le serveur LDAP pour que le connecteur fonctionne correctement :   
`1.3.6.1.4.1.4203.1.5.3` Filtres True/False

Le filtre True/False n’est souvent pas signalé comme pris en charge par les annuaires LDAP et peut apparaître sur la **Page Global** sous **fonctionnalités obligatoires introuvables**. Il est utilisé pour créer **ou** filtrer dans les requêtes LDAP, par exemple, en cas d’importation de plusieurs types d’objets. Si vous pouvez importer plusieurs types d’objets, votre serveur LDAP prend en charge cette fonctionnalité.

Si vous utilisez un répertoire dans lequel un identificateur unique est le point d’ancrage, les éléments suivants doivent également être disponibles (pour plus d’informations, consultez la section [Configurer les points d’ancrage](#configure-anchors)) :  
`1.3.6.1.4.1.4203.1.5.1` Tous les attributs opérationnels

Si le répertoire comporte plus d’objets que ce qu’un appel à l’annuaire peut contenir, il est recommandé d’utiliser la pagination. Pour que la pagination fonctionne, vous avez besoin de l’une des options suivantes :

**Option 1 :**  
`1.2.840.113556.1.4.319` pagedResultsControl

**Option 2 :**  
`2.16.840.1.113730.3.4.9` VLVControl  
`1.2.840.113556.1.4.473` SortControl

Si les deux options sont activées dans la configuration du connecteur, c’est pagedResultsControl qui est utilisée.

`1.2.840.113556.1.4.417` ShowDeletedControl

ShowDeletedControl est utilisé uniquement avec la méthode d’importation différentielle USNChanged pour être en mesure de voir les objets supprimés.

Le connecteur essaie de détecter les options présentes sur le serveur. Si les options ne sont pas détectées, un avertissement s’affiche sur la page globale des propriétés du connecteur. Tous les serveurs LDAP ne proposent pas l’ensemble des contrôles/fonctionnalités qu’ils prennent en charge et, même si cet avertissement s’affiche, il se peut que le connecteur fonctionne sans problème.

### <a name="delta-import"></a>Importation différentielle
L’importation différentielle est disponible uniquement lorsqu’un répertoire pris en charge a été détecté. Les méthodes suivantes sont fournies actuellement :

* Accesslog LDAP. Voir [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
* Changelog LDAP. Voir [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
* Horodatage. Sur Novell/NetIQ eDirectory, le connecteur utilise les dernières date/heure afin d’obtenir les objets créés et mis à jour. Novell/NetIQ eDirectory ne fournit pas de moyen équivalent de récupérer les objets. Cette option peut également être utilisée si aucune autre méthode d’importation différentielle n’est active sur le serveur LDAP. Cette option ne permet pas d’importer les objets supprimés.
* USNChanged. Consulter : [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Non pris en charge
Les fonctionnalités LDAP suivantes ne sont pas prises en charge :

* Références LDAP entre serveurs (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Créer un connecteur
Pour créer un connecteur générique LDAP, dans **Service de synchronisation**, sélectionnez **Agent de gestion** et **Créer**. Sélectionnez le connecteur **Generic LDAP (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Connectivité
Dans la page Connectivité, vous devez spécifier l’hôte, le port et la liaison. Selon la liaison sélectionnée, d’autres informations peuvent être fournies dans les sections suivantes.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

* Le paramètre Délai de connexion est utilisé uniquement pour la première connexion au serveur lors de la détection du schéma.
* Si la liaison est anonyme, ni le nom d’utilisateur/mot de passe ni les certificats ne sont utilisés.
* Pour les autres liaisons, entrez les informations dans les champs de nom d’utilisateur/mot de passe ou sélectionnez un certificat.
* Si vous utilisez Kerberos pour vous authentifier, indiquez également le domaine/la spécialité de l’utilisateur.

La zone de texte **Alias d’attribut** est utilisée pour les attributs définis dans le schéma avec la syntaxe RFC4522. Ceux-ci ne peuvent pas être détectés lors de la détection du schéma et le connecteur a besoin d’aide pour les identifier. Par exemple les éléments suivants doivent être saisis dans la zone d’alias d’attribut pour identifier correctement l’attribut userCertificate en tant qu’attribut binaire :

`userCertificate;binary`

Voici un exemple de cette configuration :

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Sélectionnez la case à cocher **Inclure des attributs opérationnels dans le schéma** pour inclure également les attributs créés par le serveur. Elle inclut des attributs, notamment la date à laquelle l’objet a été créé et l’heure de la dernière mise à jour.

Sélectionnez **Inclure les attributs extensibles dans le schéma** si des objets extensibles (RFC 4512/4.3) sont utilisés. L’activation de cette option permet d’utiliser chaque attribut sur n’importe quel objet. Cette option peut rendre le schéma très volumineux. Alors, à moins que l’annuaire connecté n’utilise cette fonction, il est conseillé de garder cette option désactivée.

### <a name="global-parameters"></a>Paramètres globaux
Sur la page Paramètres globaux, vous configurez un nom unique pour le journal des modifications différentielles et d’autres fonctionnalités LDAP. La page est prérenseignée avec les informations fournies par le serveur LDAP.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La partie supérieure affiche les informations fournies par le serveur lui-même, notamment le nom du serveur. Le connecteur vérifie également que les contrôles obligatoires sont présents dans la DSE racine. Si tel n’est pas le cas, un avertissement s’affiche. Certains répertoires LDAP ne répertorient pas toutes les fonctionnalités de la DSE racine. Il est possible que le connecteur fonctionne sans problème, même si un avertissement s’affiche.

Les cases à cocher des **commandes prises en charge** contrôlent le comportement de certaines opérations :

* Si la suppression de l’arborescence est sélectionnée, une hiérarchie est supprimée avec un appel LDAP. Si la suppression de l’arborescence n’est pas sélectionnée, le connecteur effectue une suppression récursive si nécessaire.
* Si les résultats paginés sont sélectionnés, le connecteur effectue une importation paginée avec la taille spécifiée dans les étapes d’exécution.
* VLVControl et SortControl sont une alternative à l’élément pagedResultsControl pour lire les données de l’annuaire LDAP.
* Si les trois options (pagedResultsControl, VLVControl et SortControl) ne sont pas sélectionnées, le connecteur importe tous les objets en une seule opération, ce qui risque d’échouer si l’annuaire est volumineux.
* ShowDeletedControl est utilisé uniquement lorsque la méthode d’importation différentielle est USNChanged.

Le DN du journal des modifications est le contexte de nommage utilisé par le journal des modifications différentielles, par exemple, **cn=changelog**. Vous devez spécifier cette valeur pour pouvoir effectuer une importation différentielle.

Voici une liste par défaut du journal de modification des noms uniques :

| Répertoire | Journal des modifications différentielles |
| --- | --- |
| Microsoft AD LDS et AD GC |Détecté automatiquement USNChanged. |
| Apache Directory Server |Non disponible |
| Directory 389 |Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| IBM Tivoli DS |Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| Isode Directory |Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| Novell/NetIQ eDirectory |Non disponible Horodatage. Le connecteur doit utiliser la date/heure de la dernière mise à jour pour obtenir les enregistrements ajoutés et mises à jour. |
| DJ/DS Open |Journal des modifications  Valeur à utiliser par défaut : **cn=changelog** |
| LDAP Open |Journal d’accès. Valeur à utiliser par défaut : **cn= accesslog** |
| Oracle DSEE |Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| RadiantOne VDS |Répertoire virtuel. Dépend de l’annuaire connecté à VDS. |
| Sun One Directory Server |Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |

L’attribut de mot de passe est le nom de l’attribut que le connecteur doit utiliser pour définir le mot de passe dans le mot de passe et les opérations de définition de mot de passe.
Par défaut, cette valeur est **userPassword** , mais vous pouvez la modifier pour un système LDAP particulier.

Dans la liste des partitions supplémentaires, il est possible d’ajouter des espaces de noms supplémentaires non automatiquement détectés. Cela peut être utile, par exemple, si plusieurs serveurs forment un cluster logique et doivent être importés simultanément. Active Directory peut compter plusieurs domaines partageant le même schéma dans une forêt. On peut simuler cette situation en saisissant les espaces de noms supplémentaires dans cette zone. Chaque espace de noms peut effectuer une importation à partir de différents serveurs et être configuré dans la page de configuration des partitions et des hiérarchies. Utilisez Ctrl + Entrée pour passer sur une nouvelle ligne.

### <a name="configure-provisioning-hierarchy"></a>Configurer la hiérarchie de l’approvisionnement
Cette page permet d’associer le composant DN (par exemple, OU) au type d’objet à configurer (par exemple, organizationalUnit).

![Hiérarchie d’approvisionnement](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

En configurant la hiérarchie d’approvisionnement, vous pouvez configurer le connecteur pour qu’il crée automatiquement une structure en cas de besoin. Par exemple, s’il existe un espace de noms dc=contoso,dc=com et qu’un nouvel objet cn=Joe,ou=Seattle,c=US,dc=contoso,dc=com est configuré, le connecteur peut créer un objet de type country pour US et un objet organizationalUnit pour Seattle si ceux-ci ne figurent déjà dans l’annuaire.

### <a name="configure-partitions-and-hierarchies"></a>Configurer des partitions et des hiérarchies
Dans la page Partitions et hiérarchies, sélectionnez tous les espaces de noms avec les objets que vous prévoyez d’importer et d’exporter.

![Partitions](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Pour chaque espace de noms, il est également possible de configurer les paramètres de connectivité qui remplacent les valeurs spécifiées dans l’écran Connectivité. Si ces valeurs ne sont pas spécifiées (valeur vide par défaut), les informations de l’écran Connectivité sont utilisées.

Il est également possible de sélectionner les conteneurs et les unités opérationnelles depuis lesquelles importer ou vers lesquelles exporter.

Lorsque vous effectuez une recherche, cette opération est effectuée sur tous les conteneurs de la partition. Si le nombre de conteneurs est important, ce comportement entraîne une dégradation des performances.

>[!NOTE]
À compter de la mise à jour du connecteur LDAP générique de mars 2017, l’étendue des recherches peut être limitée pour ne porter que sur les conteneurs sélectionnés. Pour cela, cochez la case « Search only in selected containers » (Rechercher uniquement dans les conteneurs sélectionnés), comme indiqué dans l’image ci-dessous.

![Rechercher uniquement dans les conteneurs sélectionnés](./media/active-directory-aadconnectsync-connector-genericldap/partitions-only-selected-containers.png)

### <a name="configure-anchors"></a>Configuration de points d’ancrage
Cette page a toujours une valeur préconfigurée et ne peut pas être modifiée. Si le fournisseur du serveur a été identifié, l’ancrage peut afficher un attribut non modifiable, comme l’identificateur global unique d’un objet. S’il n’a pas été détecté ou s’il est connu pour ne pas avoir d’attribut non modifiable, le connecteur utilise le nom unique (DN) comme point d’ancrage.

![Points d’ancrage](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)


Voici une liste de serveurs LDAP et du point d’ancrage utilisé :

| Répertoire | Attribut d’ancrage |
| --- | --- |
| Microsoft AD LDS et AD GC |objectGUID |
| Serveur d’annuaire 389 |dn |
| Apache Directory |dn |
| IBM Tivoli DS |dn |
| Isode Directory |dn |
| Novell/NetIQ eDirectory |GUID |
| DJ/DS Open |dn |
| LDAP Open |dn |
| Oracle ODSEE |dn |
| RadiantOne VDS |dn |
| Sun One Directory Server |dn |

## <a name="other-notes"></a>Autres remarques
Cette section fournit des informations sur les aspects propres à ce connecteur ou qui, pour d’autres raisons, sont importants à connaître.

### <a name="delta-import"></a>Importation différentielle
Le filigrane différentiel dans Open LDAP est date/heure UTC. Pour cette raison, les horloges entre le service de synchronisation FIM et Open LDAP doivent être synchronisées. Dans le cas contraire, certaines entrées du journal des modifications différentielles doivent être omises.

Dans Novell eDirectory, l’importation différentielle ne détecte pas les suppressions d’objet. C’est pourquoi il faut exécuter régulièrement une importation complète pour trouver tous les objets supprimés.

Dans les annuaires avec un journal des modifications différentielles basé sur la date/heure, il est recommandé d’effectuer une importation complète à des intervalles réguliers. Ce processus permet au moteur de synchronisation de faire la distinction entre le serveur LDAP et ce qui se trouve actuellement dans l’espace du connecteur.

## <a name="troubleshooting"></a>Résolution de problèmes
* Pour plus d’informations sur la façon d’activer la journalisation pour résoudre les problèmes du connecteur, consultez [Comment activer le suivi ETW pour les connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
