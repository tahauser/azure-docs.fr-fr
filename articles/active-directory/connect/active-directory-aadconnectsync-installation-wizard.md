---
title: "Réexécution de l’Assistant Installation d’Azure AD Connect | Microsoft Docs"
description: "Explique le fonctionnement de l’Assistant Installation la deuxième fois que vous l’exécutez."
keywords: "L’Assistant Installation d’Azure AD Connect vous permet de configurer les paramètres de maintenance la deuxième fois que vous l’exécutez"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: a5a55b15bbffbcfa18e07e251180820e3d9cb6d8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect Sync : exécuter l’Assistant Installation une deuxième fois
La première fois que vous exécutez l’Assistant Installation d’Azure AD Connect, il vous guide dans la procédure de configuration de votre installation. Si vous réexécutez l’Assistant Installation, il vous propose des options de maintenance.

Vous trouverez l’Assistant Installation dans le menu Démarrer sous le nom **Azure Connect AD**.

![Menu Démarrer](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Lorsque vous démarrez l’Assistant Installation, une page s’affiche avec ces options :

![Page avec une liste de tâches supplémentaires](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Si vous avez installé AD FS avec Azure AD Connect, vous avez davantage d’options. Les options supplémentaires que vous avez pour AD FS sont documentées dans [Gestion AD FS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Sélectionnez l’une des tâches et cliquez sur **Suivant** pour continuer.

> [!IMPORTANT]
> Tant que l’Assistant Installation est ouvert, toutes les opérations du moteur de synchronisation sont suspendues. Prenez soin de fermer l’Assistant Installation dès que vous avez terminé vos modifications de configuration.
>
>

## <a name="view-current-configuration"></a>Afficher la configuration actuelle
Cette option vous donne un aperçu rapide de vos options actuellement configurées.

![Page avec la liste de toutes les options et leur état](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Cliquez sur **Précédent** pour revenir en arrière. Si vous sélectionnez **Quitter**, vous fermez l’Assistant Installation.

## <a name="customize-synchronization-options"></a>Personnaliser les options de synchronisation
Cette option est utilisée pour apporter des modifications à la configuration de la synchronisation. Vous voyez un sous-ensemble d’options provenant du chemin d’installation de la configuration personnalisée. Et ce, même si vous avez initialement utilisé l’installation rapide.

* [Ajouter d’autres annuaires](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Pour supprimer un annuaire, consultez [Supprimer un connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Modifier le filtrage par domaine ou unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Supprimer le filtrage de groupe.
* [Modifier les fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features).

Les autres options provenant de l’installation initiale ne peuvent pas être modifiées et ne sont pas disponibles. Ces options sont :

* Modifier l’attribut à utiliser pour userPrincipalName et sourceAnchor.
* Modifier la méthode de jointure d’objets provenant de différentes forêts.
* Activer le filtrage de groupe.

## <a name="refresh-directory-schema"></a>Actualiser le schéma d’annuaire
Cette option est utilisée si vous avez modifié le schéma dans l’une de vos forêts AD DS locales. Par exemple, vous pouvez avoir installé Exchange ou effectué une mise à niveau vers un schéma Windows Server 2012 avec des objets de périphérique. Dans ce cas, vous devez demander à Azure AD Connect de relire le schéma AD DS et de mettre à jour son cache. Cette action régénère également les règles de synchronisation. Si vous ajoutez le schéma Exchange, par exemple, les règles de synchronisation d’Exchange sont ajoutées à la configuration.

Lorsque vous sélectionnez cette option, tous les annuaires de votre configuration sont répertoriés. Vous pouvez conserver le paramètre par défaut et actualiser toutes les forêts ou désélectionner certaines d’entre elles.

![Page avec la liste de tous les annuaires de l’environnement](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurer le mode de préproduction
Cette option permet d’activer et de désactiver le mode de préproduction sur le serveur. Vous trouverez plus d’informations sur le mode de préproduction et son utilisation dans [Opérations](active-directory-aadconnectsync-operations.md#staging-mode).

L’option s’affiche si la préproduction est actuellement activée ou désactivée :   
![Option qui affiche également l’état actuel du mode de préproduction](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Pour modifier l’état, sélectionnez cette option et cochez ou décochez la case.  
![Option qui affiche également l’état actuel du mode de préproduction](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Modifier la connexion de l’utilisateur
Cette option vous permet de passer de la synchronisation de mot de passe à la fédération ou l’inverse. Vous ne pouvez pas passer à **ne pas configurer**.

Pour plus d’informations sur cette option, consultez [connexion de l’utilisateur](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le modèle de configuration utilisé par la synchronisation d’Azure AD Connect dans [Comprendre l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
