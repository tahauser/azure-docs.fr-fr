---
title: "Azure AD Connect : topologies prises en charge | Microsoft Docs"
description: "Cette rubrique détaille les topologies prises en charge et celles qui ne le sont pas pour Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.author: billmath
ms.openlocfilehash: 8003951fb0c80bda56de4718cbe94526dc118b61
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologies pour Azure AD Connect
Cet article décrit diverses topologies locales et Azure Active Directory (Azure AD) qui utilisent Azure AD Connect Sync comme solution d’intégration clé. Cet article inclut les configurations prises en charge et celles qui ne le sont pas.

Voici la légende des images de l’article :

| Description | Symbole |
| --- | --- |
| Forêt Active Directory locale |![Forêt Active Directory locale](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Active Directory local avec importation filtrée |![Active Directory avec importation filtrée](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Serveur Azure AD Connect Sync |![Serveur Azure AD Connect Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| « Mode intermédiaire » du serveur Azure AD Connect Sync |![« Mode intermédiaire » du serveur Azure AD Connect Sync](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync avec Forefront Identity Manager (FIM) 2010 ou Microsoft Identity Manager (MIM) 2016 |![GALSync avec FIM 2010 ou MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Serveur Azure AD Connect Sync, détaillé |![Serveur Azure AD Connect Sync, détaillé](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Scénario non pris en charge |![Scénario non pris en charge](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Une seule forêt, un seul client Azure AD
![Topologie pour une forêt unique et un locataire unique](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologie la plus courante est une forêt locale unique, avec un ou plusieurs domaines, et un locataire Azure AD unique. L’authentification Azure AD est effectuée avec la synchronisation de mot de passe. Il s’agit de la seule topologie prise en charge par l’installation rapide d’Azure AD Connect.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Une seule forêt, plusieurs serveurs de synchronisation connectés à un client Azure AD
![Topologie filtrée pour une forêt unique non prise en charge](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Le fait de disposer de plusieurs serveurs Azure AD Connect Sync connectés à un même locataire Azure AD n’est pas pris en charge (à l’exception d’un [serveur intermédiaire](#staging-server)). Ceci n’est pas pris en charge, même si ces serveurs sont configurés pour se synchroniser avec un ensemble d’objets mutuellement exclusifs. Vous avez peut-être considéré cette topologie si vous ne parvenez pas à atteindre l’ensemble des domaines d’une forêt à partir d’un seul serveur ou si vous souhaitez répartir une charge entre plusieurs serveurs.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Plusieurs forêts, un seul client Azure AD
![Topologie pour plusieurs forêts et un locataire unique](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

De nombreuses organisations possèdent des environnements comportant plusieurs forêts Active Directory locales. Il existe plusieurs raisons de déployer plus d’une forêt Active Directory locale. Par exemple : des modèles avec des forêts de ressources de comptes et la conséquence d’une fusion ou d’une acquisition.

Lorsque vous avez plusieurs forêts, toutes les forêts doivent être accessibles par un même serveur Azure AD Connect Sync. Vous n’êtes pas obligé de joindre le serveur à un domaine. Si nécessaire pour atteindre toutes les forêts, vous pouvez placer le serveur dans un réseau de périmètre (également appelé DMZ, zone démilitarisée et sous-réseau filtré).

L’Assistant d’installation d’Azure AD Connect offre plusieurs options différentes pour consolider les utilisateurs qui sont représentés dans plusieurs forêts. L’objectif est de représenter un utilisateur une seule fois dans Azure AD. Il existe certaines topologies courantes que vous pouvez configurer dans le chemin d’installation personnalisé de l’Assistant d’installation. Sur la page **Identification unique de vos utilisateurs**, sélectionnez l’option correspondante qui représente votre topologie. La consolidation est configurée uniquement pour les utilisateurs. Les groupes en doublon ne sont pas consolidés avec la configuration par défaut.

Des topologies courantes sont présentées dans les sections sur les [topologies distinctes](#multiple-forests-separate-topologies), le [maillage complet](#multiple-forests-full-mesh-with-optional-galsync) et la [topologie de ressources de comptes](#multiple-forests-account-resource-forest).

La configuration par défaut d’Azure AD Connect Sync suppose que :

* Chaque utilisateur n’a qu’un seul compte activé et la forêt où se trouve ce compte est utilisée pour authentifier l’utilisateur. Cette supposition comprend la synchronisation des mots de passe et la fédération. UserPrincipalName et sourceAnchor/immutableID proviennent de cette forêt.
* Chaque utilisateur n’a qu’une seule boîte aux lettres.
* la forêt qui héberge la boîte aux lettres d’un utilisateur a la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange. Si aucune boîte aux lettres n’est associée à l’utilisateur, n’importe quelle forêt peut être utilisée pour fournir ces valeurs d’attributs.
* Si vous avez une boîte aux lettres liée, il existe également un compte dans une forêt différente de celle utilisée pour la connexion.

Si votre environnement ne correspond pas à ces hypothèses, voici ce qui se produit :

* Si vous avez plusieurs comptes actifs ou plusieurs boîtes aux lettres, le moteur de synchronisation en choisit un et ignore les autres.
* Une boîte aux lettres liée sans aucun autre compte actif n’est pas exportée vers Azure AD. Le compte d’utilisateur n’est pas représenté en tant que membre d’un groupe quelconque. Dans DirSync, une boîte aux lettres liée est toujours représentée comme une boîte aux lettres normale. Il s’agit d’un comportement intentionnellement différent pour mieux prendre en charge les scénarios avec plusieurs forêts.

Pour plus de détails, consultez la page [Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Plusieurs forêts, plusieurs serveurs de synchronisation connectés à un client Azure AD
![Topologie non prise en charge pour plusieurs forêts et plusieurs serveurs de synchronisation](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Le fait de disposer de plusieurs serveurs Azure AD Connect Sync à un locataire Azure AD unique n’est pas pris en charge. L’exception est l’utilisation d’un [serveur intermédiaire](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Plusieurs forêts, topologies distinctes
![Option pour ne représenter les utilisateurs qu’une seule fois à travers tous les annuaires](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Représentation de plusieurs forêts et de topologies distinctes](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Dans cet environnement, toutes les forêts locales sont traitées comme des entités distinctes. Aucun utilisateur n’est présent dans une autre forêt. Chaque forêt a sa propre organisation Exchange et il n’existe pas de GALSync entre les forêts. Cette topologie peut se présenter suite à une fusion/acquisition ou dans une organisation où chaque division fonctionne indépendamment. Dans Azure AD, ces forêts sont dans la même organisation et s’affichent avec une liste d’adresses globale unifiée. Dans l’image précédente, chaque objet de chaque forêt est représenté une seule fois dans le métaverse et agrégé dans le locataire Azure AD cible.

### <a name="multiple-forests-match-users"></a>Plusieurs forêts : utilisateurs en correspondance
Dans tous ces scénarios figurent des groupes de distribution et de sécurité, qui peuvent contenir une combinaison d’utilisateurs, de contacts et d’entités de sécurité externes. Les entités de sécurité externes sont utilisées dans Active Directory Domain Services (AD DS) pour représenter des membres d’autres forêts dans un groupe de sécurité. Toutes les entités de sécurité externes sont résolues en leur objet réel dans Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Plusieurs forêts : maillage complet avec GALSync facultative
![Option d’utilisation de l’attribut de messagerie pour la correspondance lorsqu’il existe des identités utilisateur sur plusieurs annuaires](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topologie de maillage complet pour plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Une topologie de maillage complet permet aux utilisateurs et aux ressources de se trouver dans n’importe quelle forêt. En général, il existe des approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plusieurs forêts, il peut (éventuellement) y avoir une solution GALSync locale. Chaque utilisateur est ensuite représenté en tant que contact dans toutes les autres forêts. GALSync est fréquemment implémentée via FIM 2010 ou MIM 2016. Azure AD Connect ne peut pas être utilisé pour une GALSync locale.

Dans ce scénario, les objets d’identité sont joints via l’attribut de messagerie. Un utilisateur qui a une boîte aux lettres dans une forêt est joint aux contacts dans les autres forêts.

### <a name="multiple-forests-account-resource-forest"></a>Plusieurs forêts : forêt de ressources de comptes
![Option d’utilisation des attributs ObjectSID et msExchMasterAccountSID pour la correspondance lorsqu’il existe des identités sur plusieurs annuaires](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologie de forêt de ressources de comptes pour plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Dans une topologie de forêt de ressources de comptes, vous avez une ou plusieurs forêts de *comptes* avec des comptes d’utilisateurs actifs. Une ou plusieurs forêts de *ressources* comportent également des comptes désactivés.

Dans ce scénario, une (ou plusieurs) forêt de ressources approuve toutes les forêts de comptes. Cette forêt de ressources a généralement un schéma Active Directory étendu avec Exchange et Lync. Tous les services Exchange et Lync, et d’autres services partagés, sont situés dans cette forêt. Les utilisateurs ont un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est liée à la forêt de comptes.

## <a name="office-365-and-topology-considerations"></a>Considérations sur Office 365 et la topologie
Certaines charges de travail Office 365 ont certaines restrictions quant aux topologies prises en charge :

| Charge de travail | Restrictions |
| --------- | --------- |
| Exchange Online | Pour plus d’informations sur les topologies hybrides prises en charge par Exchange Online, consultez [Déploiements hybrides avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype Entreprise | Lorsque vous utilisez plusieurs forêts locales, seule la topologie de forêt de ressources de comptes est prise en charge. Pour plus d’informations, consultez la rubrique [Configuration environnementale pour Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Si vous êtes une plus grande organisation, vous devez envisager d’utiliser la fonctionnalité [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md). Elle vous permet de définir la région de centre de données dans laquelle se trouvent les ressources de l’utilisateur.

## <a name="staging-server"></a>Serveur de test
![Serveur intermédiaire dans une topologie](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect prend en charge l’installation d’un second serveur en *mode intermédiaire*. Un serveur dans ce mode lit les données de tous les annuaires connectés, sans rien y écrire. Il utilise le cycle de synchronisation normale et possède donc une copie des données d’identité à jour.

En cas d’échec du serveur principal (lors d’un sinistre), vous pouvez basculer sur le serveur intermédiaire. Pour cela, utilisez l’Assistant Azure AD Connect. Ce second serveur peut se trouver dans un autre centre de données, car aucune infrastructure n’est partagée avec le serveur principal. Toute modification de configuration apportée au serveur principal doit être copiée manuellement sur le second serveur.

Vous pouvez utiliser un serveur intermédiaire pour tester une nouvelle configuration personnalisée et l’effet qu’elle aura sur vos données. Vous pouvez prévisualiser les modifications et ajuster la configuration. Lorsque vous êtes satisfait de la nouvelle configuration, vous pouvez faire du serveur intermédiaire le serveur actif et placer l’ancien serveur actif en mode intermédiaire.

Vous pouvez également utiliser cette méthode pour remplacer le serveur de synchronisation actif. Préparez le nouveau serveur et configurez-le en mode intermédiaire. Assurez-vous qu’il est en bon état, désactivez le mode intermédiaire (rendant ainsi le serveur actif), puis arrêtez le serveur actif actuel.

Il est possible d’avoir plusieurs serveurs intermédiaires si vous voulez disposer de plusieurs sauvegardes dans différents centres de données.

## <a name="multiple-azure-ad-tenants"></a>Plusieurs clients Azure AD
Nous recommandons d’avoir un seul locataire dans Azure AD pour une organisation.
Avant d’envisager d’utiliser plusieurs clients Azure AD, consultez l’article [Gestion des unités administratives dans Azure AD](../active-directory-administrative-units-management.md). Il couvre les scénarios courants dans lesquels vous pouvez utiliser un locataire unique.

![Topologie pour plusieurs forêts et plusieurs locataires](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Il existe une relation 1:1 entre un serveur Azure AD Connect Sync et un locataire Azure AD. Pour chaque client Azure AD, vous avez besoin d’une installation d’un serveur de synchronisation Azure AD Connect. De par leur conception, les instances de locataire Azure AD sont isolées. Ainsi, les utilisateurs dans un locataire ne peuvent pas voir les utilisateurs dans l’autre locataire. Si vous souhaitez cette séparation, cette configuration est prise en charge. Dans le cas contraire, vous devez utiliser le modèle de locataire Azure AD unique.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Chaque objet une seule fois dans un client Azure AD
![Topologie filtrée pour une forêt unique](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Dans cette topologie, un seul serveur de synchronisation Azure AD Connect est connecté à chaque client Azure AD. Les serveurs Azure AD Connect Sync doivent être configurés pour le filtrage afin d’avoir chacun un ensemble d’objets mutuellement exclusifs. Vous pouvez, par exemple, délimiter l’étendue de chaque serveur à un domaine ou à une unité d’organisation spécifique.

Un domaine DNS peut être inscrit dans un seul locataire Azure AD. L’UPN (nom d’utilisateur principal) des utilisateurs dans l’instance Active Directory locale doit également être composé d’espaces de noms distincts. Par exemple, dans l’image précédente, trois suffixes d’UPN distincts sont inscrits dans l’instance Active Directory locale : contoso.com, fabrikam.com et wingtiptoys.com. Les utilisateurs dans chaque domaine Active Directory local utilisent un espace de noms différent.

>[!NOTE]
>La synchronisation de la liste d’adresses globale (GalSync) n’est pas effectuée automatiquement dans cette topologie et nécessite une implémentation MIM personnalisée supplémentaire pour vérifier que chaque locataire dispose d’une liste d’adresses globale (LAG) complète dans Exchange Online et Skype Entreprise Online.


Cette topologie comprend les restrictions suivantes pour les scénarios sinon pris en charge :

* Seul un des locataires Azure AD peut activer Exchange hybride avec l’instance Active Directory locale.
* Les appareils Windows 10 ne peuvent être associés qu’à un seul locataire Azure AD.
* L’option d’authentification unique (SSO) pour la synchronisation de mot de passe et l’authentification directe ne peut être utilisée qu’avec un seul locataire Azure AD.

La condition requise d’un ensemble d’objets mutuellement exclusifs s’applique également à l’écriture différée. Certaines fonctionnalités d’écriture différée ne sont pas prises en charge avec cette topologie, car elles supposent une seule configuration locale. Voici quelques fonctionnalités :

* Écriture différée des groupes avec la configuration par défaut.
* Écriture différée des appareils.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Chaque objet plusieurs fois dans un client Azure AD
![Topologie non prise en charge pour une forêt unique et plusieurs locataires](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologie non prise en charge pour une forêt unique et plusieurs connecteurs](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Les tâches suivantes ne sont pas prises en charge :

* Synchronisation d’un même utilisateur vers plusieurs locataires Azure AD.
* Modification d’une configuration pour faire en sorte que les utilisateurs dans un locataire Azure AD apparaissent comme contacts dans un autre locataire Azure AD.
* Modification d’Azure AD Connect Sync pour qu’il se connecte à plusieurs locataires Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync à l’aide de l’écriture différée
![Topologie non prise en charge pour plusieurs forêts et plusieurs annuaires, avec GALSync se concentrant sur Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologie non prise en charge pour plusieurs forêts et plusieurs annuaires, avec GALSync se concentrant sur Active Directory local](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Les locataires Azure AD sont isolés de par leur conception. Les tâches suivantes ne sont pas prises en charge :

* Modification de la configuration d’Azure AD Connect Sync pour lire des données à partir d’un autre locataire Azure AD.
* Exportation d’utilisateurs comme contacts vers une autre instance Active Directory locale avec Azure AD Connect Sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync avec le serveur de synchronisation local
![GALSync dans une topologie pour plusieurs forêts et plusieurs annuaires](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Vous pouvez utiliser FIM 2010 ou MIM 2016 local pour synchroniser les utilisateurs (via GALSync) entre deux organisations Exchange. Les utilisateurs d’une organisation apparaissent alors comme des utilisateurs/contacts externes dans l’autre organisation. Ces différentes instances Active Directory locales peuvent ensuite être synchronisées vers leurs propres locataires Azure AD.

## <a name="next-steps"></a>étapes suivantes
Pour savoir comment installer Azure AD Connect pour ces scénarios, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
