---
title: "Opérations Azure Active Directory Connect Health"
description: "Cet article décrit les opérations supplémentaires pouvant être effectuées après le déploiement d’Azure AD Connect Health."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 98dc12a857d910e8822dcfbb61209aa9ef773f47
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="azure-active-directory-connect-health-operations"></a>Opérations Azure Active Directory Connect Health
Cette rubrique décrit les différentes opérations que vous pouvez effectuer à l’aide d’Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Activer les notifications par courrier électronique
Vous pouvez configurer le service Azure AD Connect Health pour qu’il envoie des notifications par courrier électronique quand des alertes indiquent que votre infrastructure d’identité présente un défaut d’intégrité. Cela se produit quand une alerte est générée et quand elle est résolue.

![Capture d’écran des paramètres de notifications par courrier électronique Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> Les notifications par courrier électronique sont activées par défaut.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Pour activer les notifications par courrier électronique Azure AD Connect Health
1. Ouvrez le panneau **Alertes** pour le service pour lequel vous souhaitez recevoir des notifications par courrier électronique.
2. Dans la barre d’action, cliquez sur **Paramètres de notification**.
3. Placez le commutateur de notifications par courrier électronique sur la position **ON**.
4. Cochez la case si vous souhaitez que tous les administrateurs généraux reçoivent des notifications par courrier électronique.
5. Si vous souhaitez recevoir des notifications par courrier électronique à d’autres adresses, indiquez-les dans la zone **Destinataires d’e-mail supplémentaires**. Pour supprimer une adresse e-mail de cette liste, cliquez avec le bouton droit sur l’entrée, puis sélectionnez **Supprimer**.
6. Pour valider les modifications, cliquez sur **Enregistrer**. Les modifications prennent effet après l’enregistrement.

## <a name="delete-a-server-or-service-instance"></a>Supprimer une instance de serveur ou de service

>[!NOTE] 
> La procédure de suppression requiert une licence Azure AD Premium.

Dans certains cas, vous pouvez souhaiter retirer un serveur de la surveillance. Voici ce que vous devez savoir pour supprimer un serveur du service Azure AD Connect Health.

Quand vous supprimez un serveur, tenez compte des points suivants :

* Cette action arrête la collecte des données de ce serveur. Ce serveur est retiré du service de surveillance. Après l’exécution de cette action, vous n’avez plus accès aux nouvelles alertes, ni aux données de surveillance et d’analytique de l’utilisation pour ce serveur.
* Cette action ne désinstalle pas l’Agent d’intégrité de votre serveur. Si vous n’avez pas désinstallé l’agent d’intégrité avant d’exécuter cette étape, il est possible que des erreurs associées à l’agent d’intégrité s’affichent sur le serveur.
* Cette action ne supprime pas les données déjà collectées à partir de ce serveur. Les données sont supprimées conformément à la stratégie de rétention de données Azure.
* Après avoir effectué cette opération, si vous souhaitez recommencer à surveiller le même serveur, vous devez désinstaller puis réinstaller l’agent d’intégrité sur ce serveur.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Supprimer un serveur du service Azure AD Connect Health

>[!NOTE] 
> La procédure de suppression requiert une licence Azure AD Premium.

Azure AD Connect Health pour les services de fédération Active Directory (ADFS) et Azure AD Connect (Synchronisation) :

1. Sélectionnez le nom du serveur à supprimer afin d’ouvrir le panneau **Serveur** du panneau **Liste des services**.
2. Dans le panneau **Serveur**, dans la barre d’action, cliquez sur **Supprimer**.
3. Confirmez en tapant le nom du serveur dans la boîte de confirmation.
4. Cliquez sur **Supprimer**.

Azure AD Connect Health pour Azure Active Directory Domain Services :

1. Ouvrez le tableau de bord **Contrôleurs de domaine**.
2. Sélectionnez le contrôleur de domaine à supprimer.
3. Dans la barre d’action, cliquez sur **Supprimer les éléments sélectionnés**.
4. Confirmez l’action pour supprimer le serveur.
5. Cliquez sur **Supprimer**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Supprimer une instance de service du service Azure AD Connect Health
Dans certains cas, vous souhaiterez supprimer une instance de service. Voici ce que vous devez savoir pour supprimer une instance de service du service Azure AD Connect Health.

Quand vous supprimez une instance de service, tenez compte des points suivants :

* Cette action supprime l’instance de service active du service de surveillance.
* Cette action ne supprime pas et ne désinstalle pas l’agent d’intégrité des serveurs qui étaient surveillés dans le cadre de cette instance de service. Si vous n’avez pas désinstallé l’agent d’intégrité avant d’exécuter cette étape, il est possible que des erreurs associées à l’agent d’intégrité s’affichent sur les serveurs.
* Toutes les données de cette instance de service sont supprimées conformément à la stratégie de rétention de données Azure.
* Après avoir effectué cette opération, si vous souhaitez commencer à surveiller le service, vous devez désinstaller puis réinstaller l’agent d’intégrité sur tous les serveurs. Après avoir effectué cette opération, si vous souhaitez recommencer à surveiller le même serveur, vous devez désinstaller, réinstaller puis inscrire l’agent d’intégrité sur ce serveur.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Pour supprimer une instance de service du service Azure AD Connect Health
1. À partir du panneau **Liste des services**, ouvrez le panneau **Service** en sélectionnant l’identificateur de service (nom de batterie) que vous souhaitez supprimer.
2. Dans le panneau **Serveur**, dans la barre d’action, cliquez sur **Supprimer**.
3. Confirmez en tapant le nom du service dans la boîte de confirmation (par exemple : sts.contoso.com).
4. Cliquez sur **Supprimer**.
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle
Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control-configure.md) pour Azure AD Connect Health fournit l’accès aux utilisateurs et aux groupes autres que les administrateurs généraux. Le contrôle d’accès en fonction du rôle affecte des rôles aux groupes et aux utilisateurs concernés, et un mécanisme permet de limiter les administrateurs généraux dans votre annuaire.

### <a name="roles"></a>contrôleur
Azure AD Connect Health prend en charge les rôles intégrés suivants :

| Rôle | Autorisations |
| --- | --- |
| Propriétaire |Les propriétaires peuvent *gérer l’accès* (par exemple, affecter un rôle à un utilisateur ou à un groupe), *afficher toutes les informations* (par exemple, afficher des alertes) à partir du portail et *modifier les paramètres* (par exemple, envoyer des notifications par courrier électronique) au sein d’Azure AD Connect Health. <br>Par défaut, ce rôle est affecté de manière immuable aux administrateurs généraux Azure AD. |
| Contributeur |Les contributeurs peuvent *afficher toutes les informations* (par exemple, afficher des alertes) à partir du portail et *modifier les paramètres* (par exemple, envoyer des notifications par courrier électronique) au sein d’Azure AD Connect Health. |
| Lecteur |Les lecteurs peuvent *afficher toutes les informations* (par exemple, afficher des alertes) à partir du portail au sein d’Azure AD Connect Health. |

Tous les autres rôles (comme Administrateurs de l’accès utilisateur ou Utilisateurs DevTest Labs) n’ont aucun impact sur l’accès au sein d’Azure AD Connect Health, même s’ils sont disponibles au cours de l’utilisation du portail.

### <a name="access-scope"></a>Étendue de l’accès
Azure AD Connect Health prend en charge la gestion des accès à deux niveaux :

* **Toutes les instances de service** : il s’agit du chemin recommandé dans la plupart des cas. Il contrôle l’accès pour toutes les instances de service (par exemple, une batterie de serveurs AD FS) parmi tous les types de rôle surveillées par Azure AD Connect Health.
* **Instance de service** : dans certains cas, vous pouvez être amené à paramétrer l’accès en fonction d’un type de rôle ou d’une instance de service. Dans ce cas, vous pouvez gérer l’accès au niveau de l’instance de service.  

L’autorisation est accordée si un utilisateur final a accès au niveau Annuaire ou Instance de service.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Autoriser l’accès des utilisateurs ou des groupes à Azure AD Connect Health
Les étapes suivantes montrent comment autoriser l’accès.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Étape 1 : Sélectionner l’étendue d’accès appropriée
Pour autoriser un accès utilisateur au niveau *toutes les instances de service* dans Azure AD Connect Health, ouvrez le panneau principal dans Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Étape 2 : Ajouter des utilisateurs et des groupes, et affecter des rôles
1. Dans la section **Configurer**, cliquez sur **Utilisateurs**.<br>
   ![Capture d’écran du panneau principal de contrôle d’accès en fonction du rôle d’Azure AD Connect Health, avec Utilisateurs mis en surbrillance](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Sélectionnez **Ajouter**.
3. Dans le volet **Sélectionner un rôle**, sélectionnez un rôle (par exemple **Propriétaire**).<br>
   ![Capture d’écran de la fenêtre Utilisateurs de contrôle d’accès en fonction du rôle d’Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Tapez le nom ou l’identificateur du groupe ou de l’utilisateur cible. Vous pouvez sélectionner un ou plusieurs utilisateurs ou groupes en même temps. Cliquez sur **Sélectionner**.
   ![Capture d’écran de la fenêtre Utilisateurs de contrôle d’accès en fonction du rôle d’Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Sélectionnez **OK**.<br>
6. Une fois l’affectation de rôle terminée, les utilisateurs et les groupes apparaissent dans la liste.<br>
   ![Capture d’écran de la fenêtre Utilisateurs de contrôle d’accès en fonction du rôle d’Azure AD Connect Health, avec les nouveaux utilisateurs mis en surbrillance](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Les utilisateurs et les groupes répertoriés ont désormais accès, en fonction des rôles qui leur sont affectés.

> [!NOTE]
> * Les administrateurs généraux disposent toujours d’un accès complet à toutes les opérations, mais les comptes d’administrateurs généraux ne sont pas présents dans la liste précédente.
> * La fonctionnalité Inviter des utilisateurs n’est pas prise en charge dans Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Étape 3 : Partager l’emplacement du panneau avec des utilisateurs ou des groupes
1. Une fois les autorisations affectées, un utilisateur peut accéder à Azure AD Connect Health à [cette adresse](https://aka.ms/aadconnecthealth).
2. Dans le panneau, l’utilisateur peut épingler le panneau (ou différentes parties de celui-ci) au tableau de bord. Il suffit de cliquer sur l’icône **Épingler au tableau de bord**.<br>
   ![Capture d’écran du panneau de contrôle d’accès en fonction du rôle d’Azure AD Connect Health, avec l’icône d’épinglage mise en surbrillance](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Un utilisateur qui détient le rôle Lecteur ne peut pas obtenir l’extension Azure AD Connect Health à partir de Place de marché Azure. L’utilisateur ne peut pas effectuer l’opération de création nécessaire pour cela. Cet utilisateur peut toujours accéder au panneau en suivant le lien précédent. Pour une utilisation ultérieure, il peut épingler le panneau au tableau de bord.
>
>

### <a name="remove-users-or-groups"></a>Supprimer des utilisateurs ou des groupes
Vous pouvez supprimer un utilisateur ou un groupe ajouté au contrôle d’accès en fonction du rôle Azure AD Connect Health. Il suffit de cliquer avec le bouton droit sur l’utilisateur ou le groupe et de sélectionner **Supprimer**.<br>
![Capture d’écran de la fenêtre Utilisateurs de contrôle d’accès en fonction du rôle d’Azure AD Connect Health, avec Supprimer mis en surbrillance](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="next-steps"></a>Étapes suivantes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l’agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historique des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
