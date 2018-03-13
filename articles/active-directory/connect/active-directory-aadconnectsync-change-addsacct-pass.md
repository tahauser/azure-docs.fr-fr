---
title: "Synchronisation Azure AD Connect : modification de mot de passe de compte AD DS | Microsoft Docs"
description: "Cette rubrique décrit comment mettre à jour Azure AD Connect après la modification du mot de passe du compte AD DS."
services: active-directory
keywords: "Compte AD DS, compte Active Directory, mot de passe"
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 5bc296b594917f68f35d7d15653b7b0c60cde514
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="changing-the-ad-ds-account-password"></a>Modifier le mot de passe du compte AD DS
Le compte AD DS fait référence au compte d’utilisateur utilisé par Azure AD Connect pour communiquer avec le répertoire Active Directory local. Si vous modifiez le mot de passe du compte AD DS, vous devez mettre à jour le service de synchronisation Azure AD Connect avec le nouveau mot de passe. Dans le cas contraire, la synchronisation avec le répertoire Active Directory local ne s’effectue plus correctement et les erreurs suivantes apparaissent :

* Dans Synchronization Service Manager, toute opération d’importation ou d’exportation avec le répertoire local Active Directory échoue avec une erreur **no-start-credentials**.

* Dans l’Observateur d’événements Windows, le journal d’événement d’application contient une erreur avec l’**ID d’événement 6000** et le message **«  failed to run because the credentials were invalid « contoso.com » en raison d'informations d'identification non valides. »**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Mise à jour du service de synchronisation avec un nouveau mot de passe pour le compte AD DS
Pour mettre à jour le service de synchronisation avec le nouveau mot de passe :

1. Démarrez Synchronization Service Manager (DÉMARRER → Service de synchronisation).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Accédez à l’onglet **Connecteurs** .

3. Sélectionnez le **Connecteur AD** correspondant au compte AD DS dont le mot de passe a été modifié.

4. Sous **Actions**, sélectionnez **Propriétés**.

5. Dans la boîte de dialogue contextuelle, sélectionnez **Se connecter à la forêt Active Directory** :

6. Dans la zone de texte **Mot de passe**, tapez le nouveau mot de passe du compte AD DS.

7. Cliquez sur **OK** pour enregistrer le nouveau mot de passe et fermez la boîte de dialogue contextuelle.

8. Redémarrez le service de synchronisation Azure AD Connect sous le Gestionnaire de contrôle des services Windows. Cela permet de s’assurer que toute référence à l’ancien mot de passe est supprimée du cache mémoire.

## <a name="next-steps"></a>Étapes suivantes
**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
