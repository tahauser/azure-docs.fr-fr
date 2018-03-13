---
title: "Azure AD Connect : comptes et autorisations | Microsoft Docs"
description: "Cette rubrique décrit les comptes utilisés et créés, ainsi que les autorisations nécessaires."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: c10a069f5359dc148b103688355c859bd653b5d7
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Autorisations et comptes Azure AD Connect
L’Assistant d’installation d’Azure AD Connect offre deux chemins d’accès différents :

* Dans les paramètres Express, l’Assistant exige davantage de privilèges.  Cela facilitera la configuration, car vous n’aurez pas à créer d’utilisateurs ni à définir des autorisations.
* Dans les paramètres personnalisés, l’Assistant vous propose davantage d’options. Toutefois, dans certains cas, vous devrez vérifier que vous disposez des autorisations nécessaires.

## <a name="related-documentation"></a>documentation connexe
Si vous n’avez pas lu la documentation sur [l’Intégration de vos identités locales à Azure Active Directory](../active-directory-aadconnect.md), le tableau suivant fournit des liens vers des rubriques connexes.

|Rubrique |Lien|  
| --- | --- |
|Téléchargez Azure AD Connect | [Téléchargez Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Installation à l’aide des paramètres Express | [Installation rapide pour Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Installation à l’aide des paramètres personnalisés | [Installation personnalisée d’Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Effectuer une mise à niveau à partir de DirSync | [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Après l’installation | [Vérification de l’installation et affectation des licences ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Installation à l’aide de la configuration rapide
Dans les paramètres Express, l’Assistant Installation vous demande des informations d’identification d’administrateur AD DS Enterprise.  De cette façon, votre instance locale d’Active Directory peut être configurée avec les autorisations nécessaires pour Azure AD Connect. Si vous effectuez une mise à niveau à partir de DirSync, les informations d’identification d’administrateur d’entreprise AD DS sont utilisées pour réinitialiser le mot de passe pour le compte utilisé par DirSync. Vous avez également besoin de vos informations d’identification d’administrateur général Azure AD.

| Page de l’Assistant | Informations d’identification collectées | Autorisations requises | Utilisation |
| --- | --- | --- | --- |
| N/A |Utilisateur exécutant l’Assistant d’installation |Administrateur du serveur local |<li>Crée le compte local à utiliser comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account). |
| Se connecter à Azure AD |Informations d’identification Azure Active Directory |Rôle Administrateur général dans Azure AD |<li>Activation de la synchronisation dans l’annuaire Azure AD.</li>  <li>Création du [compte Azure AD](#azure-ad-service-account) à utiliser pour les opérations de synchronisation continue dans Azure AD.</li> |
| Connexion à AD DS |Informations d’identification Active Directory locales |Membre du groupe Administrateurs de l’entreprise dans Active Directory |<li>Crée un [compte](#active-directory-account) dans Active Directory et lui octroie des autorisations. Ce compte permet de lire et d’écrire les informations d’annuaire pendant la synchronisation.</li> |

### <a name="enterprise-admin-credentials"></a>Informations d’identification d’administrateur d’entreprise
Ces informations d’identification ne sont utilisées que pendant l’installation. L’administrateur d’entreprise, et non l’administrateur de domaine, doit s’assurer que les autorisations dans Active Directory peuvent être définies dans tous les domaines.

### <a name="global-admin-credentials"></a>Informations d’identification d’administrateur général
Ces informations d’identification ne sont utilisées que pendant l’installation. Elles permettent de créer le [compte Azure AD](#azure-ad-service-account) utilisé pour synchroniser les modifications apportées à Azure AD. En outre, le compte active la synchronisation en tant que fonctionnalité dans Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Autorisations pour le compte AD DS créé pour la configuration rapide
Le [compte](#active-directory-account) destiné à la lecture et à l’écriture sur les services AD DS dispose des autorisations suivantes s’il a été créé par le biais de la configuration rapide :

| Autorisation | Utilisé pour |
| --- | --- |
| <li>Répliquer les changements d’annuaires</li><li>Répliquer les changements d’annuaire Tout |Synchronisation de mot de passe |
| Toutes les propriétés en lecture/écriture Utilisateur |Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture iNetOrgPerson |Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture Groupe |Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture Contact |Importation et Exchange hybride |
| Réinitialiser le mot de passe |Préparation pour l’activation de l’écriture différée du mot de passe |

## <a name="custom-settings-installation"></a>Installation à l’aide des paramètres personnalisés
Azure AD Connect 1.1.524.0 ou version ultérieure comprend une option permettant à l’Assistant Azure AD Connect de créer le compte utilisé pour se connecter à Active Directory.  Les versions antérieures nécessitent que le compte soit créé avant l’installation. Les autorisations que vous devez accorder à ce compte se trouvent sous [Créer le compte AD DS](#create-the-ad-ds-account). 

| Page de l’Assistant | Informations d’identification collectées | Autorisations requises | Utilisation |
| --- | --- | --- | --- |
| N/A |Utilisateur exécutant l’Assistant d’installation |<li>Administrateur du serveur local</li><li>Si vous utilisez un serveur SQL Server complet, l’utilisateur doit être administrateur système dans SQL</li> |Par défaut, crée le compte local à utiliser comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account). Le compte n’est créé que si l’administrateur ne spécifie pas de compte particulier. |
| Installation des services de synchronisation, option Compte de service |Informations d’identification du compte d’utilisateur local ou AD |Utilisateur, les autorisations sont accordées par l’Assistant d’installation |Si l’administrateur spécifie un compte, ce dernier est utilisé comme compte de service pour le service de synchronisation. |
| Se connecter à Azure AD |Informations d’identification Azure Active Directory |Rôle Administrateur général dans Azure AD |<li>Activation de la synchronisation dans l’annuaire Azure AD.</li>  <li>Création du [compte Azure AD](#azure-ad-service-account) à utiliser pour les opérations de synchronisation continue dans Azure AD.</li> |
| Connexion de vos annuaires |Informations d’identification Active Directory locales pour chaque forêt connectée à Azure AD |Les autorisations varient selon les fonctionnalités que vous activez et se trouvent dans [Créer le compte AD DS](#create-the-ad-ds-account) |Ce compte permet de lire et d’écrire les informations d’annuaire pendant la synchronisation. |
| Serveurs AD FS |Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter |Administrateur de domaine |Installation et configuration du rôle de serveur AD FS. |
| Serveurs proxy d’application web |Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter |Administrateur local sur l’ordinateur cible |Installation et configuration du rôle de serveur WAP |
| Informations d’identification de confiance du proxy |Informations d’identification de confiance du service de fédération (informations d’identification que le proxy utilise pour obtenir un certificat d’approbation à partir de FS) |Compte de domaine qui est un administrateur local du serveur AD FS |Inscription initiale du certificat d’approbation FS-WAP. |
| Page Compte de service AD FS, option Utilisation d’un compte d’utilisateur de domaine |Informations d’identification du compte d’utilisateur AD |Utilisateur de domaine |Le compte d’utilisateur AD dont les informations d’identification sont fournies est utilisé comme compte de connexion au service AD FS. |

### <a name="create-the-ad-ds-account"></a>Créer le compte AD DS
Le compte que vous spécifiez dans la page **Connexion de vos annuaires** doit exister dans Active Directory avant l’installation.  Il doit également disposer des autorisations nécessaires. L’Assistant d’installation ne vérifie pas les autorisations, et les problèmes éventuels ne sont détectés que pendant la synchronisation.

Les autorisations dont vous avez besoin dépendent des fonctionnalités facultatives que vous activez. Si vous avez plusieurs domaines, les autorisations doivent être accordées pour tous les domaines de la forêt. Si vous n’activez pas ces fonctionnalités, les autorisations **Utilisateur de domaine** par défaut sont suffisantes.

| Fonctionnalité | Autorisations |
| --- | --- |
| Fonctionnalité msDS-ConsistencyGuid |Autorisations en écriture sur l’attribut msDS-ConsistencyGuid documentées dans [Principes de conception Azure AD Connect - Utilisation de msDS-ConsistencyGuid en tant que sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Synchronisation de mot de passe |<li>Répliquer les changements d’annuaires</li>  <li>Répliquer les changements d’annuaire Tout |
| Déploiement Exchange hybride |Autorisations d’écriture sur les attributs documentés dans [Écriture différée d’Exchange hybride](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) pour les utilisateurs, les groupes et les contacts. |
| Dossier public de messagerie Exchange |Autorisations de lecture sur les attributs documentées dans [Dossier public de messagerie Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) pour les dossiers publics. | 
| Réécriture du mot de passe |Autorisations d’écriture sur les attributs documentés dans [Prise en main de la gestion de mot de passe](../active-directory-passwords-writeback.md) pour les utilisateurs. |
| Écriture différée des appareils |Autorisations accordées avec un script PowerShell comme décrit dans [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md). |
| Écriture différée de groupe |Lire, créer, mettre à jour et supprimer des objets de groupe pour les **groupes Office 365** synchronisés.  Pour plus d’informations, consultez [Écriture différée de groupe](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Mise à niveau
Lors de la mise à niveau vers une nouvelle version d’Azure AD Connect, vous devez disposer des autorisations suivantes :

>[!IMPORTANT]
>À partir de la version 1.1.484, Azure AD Connect a introduit un bogue de régression qui nécessite des autorisations d’administrateur système pour mettre à niveau la base de données SQL.  Ce bogue est corrigé dans la build 1.1.647.  Si vous effectuez une mise à jour vers cette version, vous aurez besoin des autorisations d’administrateur système.  Les autorisations dbo ne sont pas suffisantes.  Si vous tentez de mettre à niveau Azure AD Connect sans avoir les autorisations d’administrateur système, la mise à niveau échoue et Azure AD Connect ne fonctionne plus correctement.  Microsoft en est conscient et recherche actuellement une solution.


| Principal | Autorisations requises | Utilisé pour |
| --- | --- | --- |
| Utilisateur exécutant l’Assistant d’installation |Administrateur du serveur local |Mettre à jour des fichiers binaires. |
| Utilisateur exécutant l’Assistant d’installation |Membre d'ADSyncAdmins |Modifier les règles de synchronisation et d'autre configurations. |
| Utilisateur exécutant l’Assistant d’installation |Si vous utilisez un serveur SQL complet : propriétaire (DBO, ou rôle similaire) de la base de données du moteur de synchronisation |Apporter des modifications au niveau de la base de données, telles que la mise à jour des tables avec de nouvelles colonnes. |

## <a name="more-about-the-created-accounts"></a>Plus d’informations sur les comptes créés
### <a name="active-directory-account"></a>Compte Active Directory
Si vous utilisez la configuration rapide, un compte à utiliser pour la synchronisation est créé dans Active Directory. Le compte créé se trouve dans le domaine racine de forêt au sein du conteneur Utilisateurs et se voit attribuer le préfixe **MSOL_**. Le compte est créé avec un mot de passe long et complexe qui n’expire pas. Si vous avez une stratégie de mot de passe dans votre domaine, assurez-vous que les mots de passe longs et complexes sont autorisés pour ce compte.

![Compte AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Si vous utilisez des paramètres personnalisés, vous devez créer le compte vous-même avant de commencer l’installation.

### <a name="azure-ad-connect-sync-service-account"></a>Compte de service de synchronisation d'Azure AD Connect
Le service de synchronisation peut s’exécuter sous des comptes différents. Il peut s’exécuter sous un **Compte de service virtuel** (VSA), un **Compte de service géré de groupe** (gMSA/sMSA), ou un compte d’utilisateur normal. Les options prises en charge ont été modifiées avec la version d’avril 2017 de Connect, lorsque vous effectuez une nouvelle installation. Si vous mettez à niveau depuis une version antérieure d’Azure AD Connect, ces options supplémentaires ne sont pas disponibles.

| Type de compte | Option d’installation | Description |
| --- | --- | --- |
| [Compte de service virtuel](#virtual-service-account) | Rapide et personnalisée, avril 2017 et versions ultérieures | Il s’agit de l’option utilisée pour toutes les installations rapides, à l’exception des installations sur un contrôleur de domaine. Pour l’installation personnalisée, il s’agit de l’option par défaut, sauf si une autre option est utilisée. |
| [Compte de service géré de groupe](#group-managed-service-account) | Installation personnalisée, avril 2017 et versions ultérieures | Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un compte de service géré de groupe. |
| [Compte d’utilisateur](#user-account) | Rapide et personnalisée, avril 2017 et versions ultérieures | Un compte d’utilisateur avec le préfixe AAD_ est créé uniquement lors de l’installation sur Windows Server 2008 ou sur un contrôleur de domaine. |
| [Compte d’utilisateur](#user-account) | Installation rapide et personnalisée, mars 2017 et versions antérieures | Un compte local avec le préfixe AAD_ est créé pendant l’installation. Lorsque vous utilisez l’installation personnalisée, vous pouvez spécifier un autre compte. |

Si vous utilisez Connect avec une version de mars 2017 ou antérieure, vous ne devez pas réinitialiser le mot de passe du compte de service, sinon Windows détruit les clés de chiffrement pour des raisons de sécurité. Vous ne pouvez pas modifier le compte sur un autre compte sans réinstaller Azure AD Connect. Si vous mettez à niveau vers la version d’avril 2017 ou une version ultérieure, il est possible de modifier le mot de passe du compte de service, mais vous ne pouvez pas modifier le compte utilisé.

> [!Important]
> Vous pouvez uniquement définir le compte de service lors de la première installation. Il n’est pas possible de modifier le compte de service une fois l’installation terminée.

Il s’agit d’un tableau avec les options par défaut, recommandées et prises en charge pour le compte de service de synchronisation.

Légende :

- Le texte **Gras** indique l’option par défaut et, dans la plupart des cas, l’option recommandée.
- Le texte *Italique* indique l’option recommandée lorsqu’il ne s’agit pas de l’option par défaut.
- 2008 - Option par défaut lors de l’installation sur Windows Server 2008
- Non gras - Option prise en charge
- Compte local - Compte d’utilisateur local sur le serveur
- Compte de domaine - Compte d’utilisateur de domaine
- sMSA - [Compte de service géré autonome](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [Compte de service géré de groupe](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personnalisée | SQL à distance</br>Personnalisée |
| --- | --- | --- | --- |
| **Machine de groupe de travail/autonome** | Non pris en charge | **VSA**</br>Compte local (2008)</br>Compte local |  Non pris en charge |
| **ordinateur joint à un domaine** | **VSA**</br>Compte local (2008) | **VSA**</br>Compte local (2008)</br>Compte local</br>Compte du domaine</br>sMSA, gMSA | **gMSA**</br>Compte du domaine |
| **Contrôleur de domaine** | **Compte du domaine** | *gMSA*</br>**Compte du domaine**</br>sMSA| *gMSA*</br>**Compte du domaine**|

#### <a name="virtual-service-account"></a>Compte de service virtuel
Un compte de service virtuel est un type spécial de compte qui ne dispose pas d’un mot de passe et qui est géré par Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

VSA est destiné à être utilisé dans les scénarios où le moteur de synchronisation et SQL sont sur le même serveur. Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un [compte de service géré de groupe](#managed-service-account) à la place.

Cette fonctionnalité nécessite Windows Server 2008 R2 ou version ultérieure. Si vous installez Azure AD Connect sur Windows Server 2008, l’installation bascule sur l’utilisation d’un [compte d’utilisateur](#user-account) à la place.

#### <a name="group-managed-service-account"></a>Compte de service géré de groupe
Si vous utilisez un serveur SQL distant, nous recommandons d’utiliser un **compte de service administré de groupe**. Pour plus d’informations sur la préparation de votre annuaire Active Directory pour le compte de service géré de groupe, consultez [Présentation des comptes de service géré de groupe](https://technet.microsoft.com/library/hh831782.aspx).

Pour utiliser cette option, sur la page [Installer les composants requis](active-directory-aadconnect-get-started-custom.md#install-required-components), sélectionnez **Utiliser un compte de service existant**, puis sélectionnez **Compte de service administré**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Il est également possible d’utiliser un [Compte de service géré autonome](https://technet.microsoft.com/library/dd548356.aspx). Toutefois, dans la mesure où vous pouvez uniquement les utiliser sur l’ordinateur local, il n’existe aucun avantage à les utiliser à la place du compte de service virtuel par défaut.

Cette fonctionnalité nécessite Windows Server 2012 ou version ultérieure. Si vous devez utiliser un ancien système d’exploitation et SQL à distance, vous devez utiliser un [compte d’utilisateur](#user-account).

#### <a name="user-account"></a>Compte d’utilisateur
Un compte de service local est créé par l’Assistant d’installation (sauf si vous spécifiez le compte à utiliser dans les paramètres personnalisés). Le compte a pour préfixe **AAD_** et est associé au service de synchronisation à utiliser. Si vous installez Azure AD Connect sur un contrôleur de domaine, le compte est créé dans le domaine. Le compte de service **AAD_** doit se trouver dans le domaine si :
   - Vous utilisez un serveur distant exécutant SQL Server.
   - Vous utilisez un proxy qui nécessite une authentification.

![Compte de service de synchronisation](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Le compte est créé avec un mot de passe long et complexe qui n’expire pas.

Ce compte permet de stocker les mots de passe des autres comptes de manière sécurisée. Ces autres mots de passe de compte sont stockés dans la base de données. Les clés privées pour les clés de chiffrement sont protégées par le chiffrement à clé secrète des services de chiffrement avec l’API de protection des données (DPAPI) Windows.

Si vous utilisez un serveur SQL Server complet, le compte de service est le propriétaire de la base de données créée pour le moteur de synchronisation. Le service ne fonctionne pas comme prévu avec d’autres autorisations. Une connexion SQL est également créée.

En outre, le compte se voit octroyer des autorisations sur les fichiers, les clés de Registre et autres objets liés au moteur de synchronisation.

### <a name="azure-ad-service-account"></a>Compte de service Azure AD
Un compte dans Azure AD est créé en vue de son utilisation par le service de synchronisation. Ce compte peut être identifié par son nom d’affichage.

![Compte AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Le nom du serveur sur lequel le compte est utilisé peut être identifié dans la deuxième partie du nom d’utilisateur. Sur l’image, le nom du serveur est FABRIKAMCON. Si vous disposez de serveurs intermédiaires, chaque serveur a son propre compte.

Le compte de service est créé avec un mot de passe long et complexe qui n’expire pas. Il se voit octroyer le rôle de **Compte de synchronisation de répertoires**. À ce titre, il est uniquement autorisé à effectuer des tâches de synchronisation de répertoires. Ce rôle intégré ne peut pas être accordé en dehors de l’Assistant Azure AD Connect. Le portail Azure affiche ce compte avec le rôle **Utilisateur**.

Il existe une limite de 20 comptes de service de synchronisation dans Azure AD. Pour obtenir la liste des comptes de service Azure AD existants dans Azure AD, exécutez l’applet de commande Azure AD PowerShell suivante : `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Pour supprimer des comptes de service AD Azure inutilisés, exécutez l’applet de commande Azure AD PowerShell suivante :`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](../active-directory-aadconnect.md).
