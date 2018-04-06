---
title: Gestion des appareils avec le portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour gérer les appareils.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a4bdea4844c4aaa9990d619d59326a83cf0aef6c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="managing-devices-using-the-azure-portal"></a>Gestion des appareils avec le portail Azure


La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. 

Cette rubrique :

- Suppose que vous avez lu la [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)

- Fournit des informations sur la gestion des appareils avec le portail Azure

## <a name="manage-devices"></a>Gérer des appareils 

Le portail Azure centralise les opérations de gestion de vos appareils. Pour y accéder, utilisez un [lien direct](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou suivez les étapes manuelles ci-dessous :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.

2. Sur la barre de navigation gauche, cliquez sur **Active Directory**.

    ![Configurer les paramètres de l’appareil](./media/device-management-azure-portal/01.png)

3. Dans la section **Gérer**, cliquez sur **Appareils**.

    ![Configurer les paramètres de l’appareil](./media/device-management-azure-portal/11.png)
 
La page **Appareils** vous permet :

- De configurer les paramètres de gestion de vos appareils

- Localiser les appareils

- D’effectuer les tâches de gestion relatives aux appareils

- De consulter les journaux d’audit liés à la gestion des appareils  
  

## <a name="configure-device-settings"></a>Configurer les paramètres de l’appareil

Pour gérer vos appareils avec le portail Azure, vous devez [les inscrire ou les joindre](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) à Azure AD. En tant qu’administrateur, vous pouvez affiner le processus d’inscription et de jonction des appareils en configurant les paramètres de l’appareil. 

![Configurer les paramètres de l’appareil](./media/device-management-azure-portal/22.png)

La page Paramètres de l’appareil vous permet de configurer les options suivantes :

![Gérer un appareil Intune](./media/device-management-azure-portal/21.png)


- **Les utilisateurs peuvent joindre des appareils à Azure AD** : ce paramètre vous permet de sélectionner les utilisateurs qui peuvent [joindre des appareils](device-management-introduction.md#azure-ad-joined-devices) à Azure AD. La valeur par défaut est **Tous**.

- **Administrateurs locaux supplémentaires sur les appareils joints à Azure AD** : vous pouvez sélectionner les utilisateurs qui peuvent disposer de droits d’administrateur local sur un appareil. Les utilisateurs ajoutés ici sont ajoutés au rôle *Administrateurs d’appareils* dans Azure AD. Les administrateurs généraux Azure AD et les propriétaires d’appareils bénéficient de droits d’administrateur local par défaut. Cette option est une fonctionnalité de l’édition Premium disponible dans les produits comme Azure AD Premium ou EMS (Enterprise Mobility Suite). 

- **Les utilisateurs peuvent inscrire leurs appareils sur Azure AD** : vous devez configurer ce paramètre pour permettre [l’inscription](device-management-introduction.md#azure-ad-registered-devices) des appareils dans Azure AD. Si vous sélectionnez **Aucun**, les appareils ne peuvent pas être inscrits s’ils ne sont pas joints à Azure AD ou s’il ne s’agit pas d’appareils hybrides joints à Azure AD. L’inscription auprès de Microsoft Intune ou de la Gestion des appareils mobiles (MDM) pour Office 365 nécessite l’enregistrement de l’appareil. Si vous avez configuré l’un de ces services, l’option **TOUS** est sélectionnée et l’option **AUCUN** est désactivée.

- **Exiger Multi-factor Auth pour joindre des appareils** : vous pouvez demander aux utilisateurs de fournir un second facteur d’authentification pour [joindre](device-management-introduction.md#azure-ad-joined-devices) leurs appareils à Azure AD. La valeur par défaut est **Non**. Il est recommandé d’exiger une authentification multifacteur au moment de l’inscription d’un appareil. Avant d’activer l’authentification multifacteur pour ce service, vous devez vérifier que l’authentification multifacteur est configurée pour les utilisateurs qui inscrivent leurs appareils. Pour plus d’informations sur les services d’authentification multifacteur Azure, consultez [Bien démarrer avec l’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Nombre maximal d’appareils par utilisateur** : ce paramètre permet de sélectionner le nombre maximal d’appareils qu’un utilisateur peut avoir dans Azure AD. Si un utilisateur atteint ce quota, il ne pourra pas ajouter d’autres appareils tant qu’un ou plusieurs appareils existants n’auront pas été supprimés. Le quota d’appareils comptabilise tous les appareils qui sont actuellement joints à Azure AD ou inscrits à Azure AD. La valeur par défaut est de **20** appareils.

- **Les utilisateurs peuvent synchroniser les paramètres et les données d’application sur différents appareils** : par défaut, ce paramètre est défini sur **AUCUN**. La sélection de certains utilisateurs ou groupes, ou de TOUS, permet aux paramètres et aux données d’application de l’utilisateur d’être synchronisés sur ses appareils Windows 10. Découvrez comment fonctionne la synchronisation dans Windows 10.
Cette option est une fonctionnalité de l’édition Premium disponible dans les produits comme Azure AD Premium ou EMS (Enterprise Mobility Suite).
 




## <a name="locate-devices"></a>Localiser les appareils

Deux options vous permettent de localiser les appareils inscrits et joints :

- **Tous les appareils** dans la section **Gérer** de la page **Appareils**  

    ![Tous les appareils](./media/device-management-azure-portal/41.png)


- **Appareils** dans la section **Gérer** de la page **Utilisateur**
 
    ![Tous les appareils](./media/device-management-azure-portal/43.png)



Ces deux options permettent d’accéder à une vue qui :


- Permet de rechercher des appareils en utilisant leur nom d’affichage comme filtre

- Fournit une vue d’ensemble détaillée des appareils inscrits et joints

- Permet d’effectuer les tâches courantes de gestion des appareils
   

![Tous les appareils](./media/device-management-azure-portal/51.png)

Pour certains appareils iOS, les noms d’appareils qui contiennent des apostrophes peuvent utiliser d’autres caractères qui ressemblent à des apostrophes. La recherche de ces appareils peut donc être difficile. Si les résultats que vous attendez ne s’affichent pas, vérifiez que la chaîne de recherche contient les caractères d’apostrophe attendus.

## <a name="device-management-tasks"></a>Tâches de gestion des appareils

En tant qu’administrateur, vous pouvez gérer les appareils inscrits ou joints. Cette section fournit des informations sur les tâches courantes de gestion des appareils.


### <a name="manage-an-intune-device"></a>Gérer un appareil Intune

Si vous êtes administrateur Intune, vous pouvez gérer les appareils marqués comme étant des appareils **Microsoft Intune**. Un administrateur peut voir d’autres appareils. 

![Gérer un appareil Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Activer/désactiver un appareil Azure AD

Pour activer/désactiver un appareil, vous avez deux options :

- Le menu de tâches (« … ») dans la page **Tous les appareils**

    ![Gérer un appareil Intune](./media/device-management-azure-portal/71.png)

- La barre d’outils dans la page **Appareils**

    ![Gérer un appareil Intune](./media/device-management-azure-portal/32.png)


**Remarques :**

- Vous devez être administrateur général dans Azure AD pour activer/désactiver un appareil. 
- Si vous désactivez un appareil, vous l’empêchez d’accéder à vos ressources Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Supprimer un appareil Azure AD

Pour supprimer un appareil, vous avez deux options :

- Le menu de tâches (« … ») dans la page **Tous les appareils**

    ![Gérer un appareil Intune](./media/device-management-azure-portal/72.png)

- La barre d’outils dans la page **Appareils**

    ![Suppression d’un appareil](./media/device-management-azure-portal/34.png)


**Remarques :**

- Vous devez être administrateur général dans Azure AD pour supprimer un appareil.  

- La suppression d’un appareil :
 
    - Empêche celui-ci d’accéder à vos ressources Azure AD. 

    - Supprime toutes les informations associées à l’appareil, par exemple, les clés BitLocker des appareils Windows.  

    - Est une action irréversible et donc non recommandée, sauf si elle est absolument nécessaire.

Si un appareil est géré par une autre autorité de gestion (par exemple, Microsoft Intune), vérifiez que l’appareil a été réinitialisé ou mis hors service avant de le supprimer d’Azure AD.

 


### <a name="view-or-copy-device-id"></a>Afficher ou copier l’ID de l’appareil

Vous pouvez utiliser un ID d’appareil pour vérifier les informations d’ID de l’appareil ou utiliser PowerShell durant le dépannage. Pour accéder à l’option de copie, cliquez sur l’appareil.

![Afficher un ID d’appareil](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Afficher ou copier des clés BitLocker

Si vous êtes administrateur, vous pouvez afficher et copier les clés BitLocker pour permettre aux utilisateurs de récupérer leur lecteur chiffré. Ces clés sont uniquement disponibles pour les appareils Windows chiffrés dont les clés sont stockées dans Azure AD. Vous pouvez copier ces clés lorsque vous accédez aux informations de l’appareil.
 
![Afficher les clés BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Journaux d’audit


Des activités de l’appareil sont disponibles dans les journaux d’activité. Elles comprennent les activités déclenchées par le service d’inscription des appareils et par les utilisateurs :

- Création d’un appareil et ajout de propriétaires/d’utilisateurs sur l’appareil

- Modification des paramètres de l’appareil

- Opérations concernant les appareils, telles que la suppression ou la mise à jour d’un appareil
 
Les **Journaux d’audit** dans la section **Activité** de la page **Appareils** constituent le point d’entrée des données d’audit.

![Journaux d’audit](./media/device-management-azure-portal/61.png)


Un journal d’audit inclut un mode Liste par défaut, qui indique :

- La date et l’heure de l’occurrence

- Les cibles

- L’initiateur/intervenant d’une activité (qui)

- L’activité (quoi)

![Journaux d’audit](./media/device-management-azure-portal/63.png)

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.
 
![Journaux d’audit](./media/device-management-azure-portal/64.png)


Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’audit à l’aide des champs suivants :

- Catégorie
- Type de ressource d’activité
- Activité
- Plage de dates
- Cible
- Initié par (intervenant)

Vous pouvez filtrer les entrées, mais également rechercher des entrées spécifiques.

![Journaux d’audit](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)



