---
title: Attribution de rôles d’administrateur dans Azure Active Directory | Microsoft Docs
description: Le rôle de l’administrateur peut servir à ajouter des utilisateurs, attribuer des rôles d’administrateur, réinitialiser les mots de passe utilisateur, gérer les licences utilisateur et les domaines, etc. Un utilisateur à qui un rôle d’administrateur est affecté a les mêmes autorisations pour tous les services cloud auxquels votre organisation s’est abonnée.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 8ba4787852902f194deb3eee27ff9f0fc28fa5aa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Attribution de rôles d’administrateur dans Azure Active Directory

À l’aide d’Azure Active Directory (Azure AD), vous pouvez affecter des administrateurs distincts à différentes fonctions. Les administrateurs ont accès à plusieurs fonctionnalités du portail Azure et, en fonction de leur rôle, ils peuvent créer ou modifier des utilisateurs, attribuer des rôles d’administrateur à d’autres personnes, réinitialiser les mots de passe d’utilisateur, gérer les licences utilisateur et les domaines, etc. Un utilisateur à qui un rôle d’administrateur est affecté a les mêmes autorisations sur tous les services cloud auxquels votre organisation s’est abonnée, peu importe que vous attribuiez le rôle dans le portail Office 365 ou dans le portail Azure ou encore à l’aide du module Azure AD pour Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Informations sur le rôle d’administrateur général
L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur.

## <a name="assign-or-remove-administrator-roles"></a>Attribution ou suppression de rôles d’administrateur
Pour découvrir comment attribuer des rôles d’administration à un utilisateur dans Azure Active Directory, consultez l’article [Attribution de rôles d’administrateur à un utilisateur d’Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Rôles disponibles
Les rôles d’administrateur disponibles sont les suivants :

* **Administrateur de facturation**: effectue les achats, gère les abonnements ainsi que les tickets de support et surveille l’état des services.

* **Administrateur de conformité** : les utilisateurs disposant de ce rôle possèdent des autorisations de gestion dans le Centre de sécurité et conformité compatibilité de la sécurité Office 365 et le Centre d'administration Exchange. Pour plus d’informations, consultez l’article [À propos des rôles d’administrateur Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateur de l’accès conditionnel** : les utilisateurs disposant de ce rôle peuvent gérer les paramètres d’accès conditionnel Azure Active Directory.
  > [!NOTE]
  > Pour déployer la stratégie d’accès conditionnel Exchange ActiveSync dans Azure, l’utilisateur doit également être administrateur général.
  
* **Administrateur de service CRM** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft CRM Online, lorsque le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateurs d’appareil**: les utilisateurs disposant de ce rôle deviennent administrateurs d’ordinateur local sur tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets appareil dans Azure Active Directory.

* **Lecteurs de répertoire**: il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge [l’infrastructure de consentement](active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **Comptes de synchronisation de répertoire**: n’utilisez pas cela. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

* **Enregistreurs de répertoire**: il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge [l’infrastructure de consentement](active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **Administrateur de service Exchange**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Exchange Online, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateur général/administrateur de la société** : les utilisateurs disposant de ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui sont liés à Azure Active Directory, comme Exchange Online, SharePoint Online et Skype pour Entreprise Online. La personne qui s’inscrit pour le locataire Azure Active Directory devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **Inviteur** : les utilisateurs disposant de ce rôle peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B lorsque le paramètre utilisateur « Les membres peuvent inviter » est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il n’inclut pas d’autres autorisations.

* **Administrateur Information Protection** : les utilisateurs disposant de ce rôle n’ont des droits utilisateur que sur le service Azure Information Protection. Aucun droit utilisateur n’est accordé sur Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health ou Centre de sécurité et conformité Office 365. Ils peuvent configurer les étiquettes pour la stratégie Azure Information Protection, gérer les modèles de protection et activer la protection.

* **Administrateur de service Intune**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Intune Online, lorsque le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes.

* **Administrateur de la boîte aux lettres** : ce rôle n’est utilisé que dans le cadre de la prise en charge de la messagerie Exchange Online sur les appareils RIM Blackberry. Si votre organisation n’utilise pas la messagerie Exchange Online sur les appareils RIM Blackberry, n’employez pas ce rôle.

* **Support de niveau 1 de partenaire** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **Support de niveau 2 de partenaire** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **Administrateur de mots de passe/Administrateur du support technique** : les utilisateurs dotés de ce rôle peuvent modifier les mots de passe, gérer les demandes de service et surveiller l’état des services. Les administrateurs du support technique peuvent modifier uniquement les mots de passe des utilisateurs et des autres administrateurs du support technique. 

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur Helpdesk ». Il est appelé « Administrateur général » dans le [portail Azure](https://portal.azure.com/).
  >
  >
  
* **Administrateur de service Power BI** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Power BI, lorsque le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Administrateur de rôle privilégié** : les utilisateurs disposant de ce rôle peuvent gérer les attributions de rôles dans Azure Active Directory, ainsi que dans Azure AD Privileged Identity Management. En outre, ce rôle permet de gérer tous les aspects de Privileged Identity Management.

* **Lecteur de rapports** : les utilisateurs disposant de ce rôle peuvent afficher les données des rapports d’utilisation et le tableau de bord des rapports dans le centre d’administration Office 365, ainsi que le pack du contexte d’adoption dans Power BI. En outre, ce rôle donne accès aux rapports de connexion et aux activités dans Azure AD, ainsi qu’aux données renvoyées par l’API de création de rapports Microsoft Graph. Un utilisateur disposant du rôle Lecteur de rapports peut uniquement accéder au métriques d’utilisation et d’adoption pertinentes. Il n’a pas les autorisations administrateur permettant de configurer les paramètres ou d’accéder aux centres d’administration propres au produit comme Exchange. 

* **Administrateur de sécurité** : les utilisateurs disposant de ce rôle ont toutes les autorisations en lecture seule du rôle Lecteur de sécurité, plus la possibilité de gérer la configuration des services de sécurité : Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management et Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Lecteur de sécurité** : les utilisateurs disposant de ce rôle ont un accès global en lecture seule, y compris à toutes les informations dans Azure Active Directory, Identity Protection, Privileged Identity Management, ainsi que la possibilité de lire les rapports de connexion Azure Active Directory et les journaux d’audit. Le rôle accorde également l’autorisation en lecture seule dans le Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Administrateur du support technique du service** : les utilisateurs disposant de ce rôle peuvent ouvrir des demandes de support auprès de Microsoft pour les services Azure et Office 365, ainsi que consulter le tableau de bord de service et le centre des messages dans le portail Azure et le portail d’administration Office 365. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateur de service SharePoint** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, lorsque le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateur des services Lync/Skype Entreprise** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, lorsque le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’état des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur des services Lync ». Il est appelé « Administrateur Skype Entreprise » dans le [portail Azure](https://portal.azure.com/).
  >
  >

* **Administrateur des comptes d’utilisateur** : les utilisateurs disposant de ce rôle peuvent créer et gérer tous les aspects relatifs aux utilisateurs et groupes. En outre, ce rôle inclut la possibilité de gérer les tickets de support et de surveiller l’état des services. Quelques restrictions s’appliquent. Par exemple, ce rôle n’autorise pas la suppression d’un administrateur général. Les administrateurs des comptes d’utilisateur peuvent uniquement modifier les mots de passe des utilisateurs, des administrateurs du support technique et des autres administrateurs des comptes d’utilisateur.

## <a name="administrator-permissions"></a>Autorisations des administrateurs

### <a name="billing-administrator"></a>Administrateur de facturation

| Peut | Ne peut pas |
| --- | --- |
|<p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p> |<p>Réinitialiser les mots de passe utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher les journaux d’audit</p>|

### <a name="conditional-access-administrator"></a>Administrateur de l’accès conditionnel

| Peut | Ne peut pas |
| --- | --- |
|<p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les paramètres d’accès conditionnel</p> |<p>Réinitialiser les mots de passe utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher les journaux d’audit</p>|

### <a name="global-administrator"></a>Administrateur général
| Peut | Ne peut pas |
| --- | --- |
|<p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Réinitialiser les mots de passe utilisateur</p><p>Réinitialiser les mots de passe de l’autre administrateur</p> <p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Activer ou désactiver l’authentification multifacteur</p><p>Afficher les journaux d’audit</p> |N/A |

### <a name="password-administrator--helpdesk-administrator"></a>Administrateur de mot de passe / Administrateur du support technique
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Modifier les mots de passe des utilisateurs et des autres administrateurs du support technique uniquement</p>|<p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher des rapports</p>|

### <a name="information-protection-administrator"></a>Administrateur Information Protection
Dans | Peut
-------- | ---------
Azure Information Protection | <li>Configurer les paramètres et les étiquettes dans les stratégies globales et étendues<li>Configurer et gérer des modèles de protection<li>Activer ou désactiver une protection--
 
### <a name="reports-reader"></a>Lecteur de rapports 
Peut | Ne peut pas
------ | ----------
Afficher les rapports de connexion d’Azure AD et les journaux d’audit<br>Afficher les informations sur la société et les utilisateurs<br>Accéder au tableau de bord d’utilisation d’Office 365 | Créer et gérer des vues utilisateur<br>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur<br>Déléguer des rôles administratifs à d’autres personnes<br>Modifier les informations de l’entreprise

### <a name="security-reader"></a>Lecteur de sécurité
| Dans | Peut |
| --- | --- |
| Identity Protection Center |Lire tous les rapports de sécurité et informations de paramètres pour les fonctionnalités de sécurité<ul><li>Anti-spam<li>Chiffrement<li>Prévention contre la perte de données<li>Anti-programme malveillant<li>Détection avancée des menaces<li>Anti-hameçonnage<li>Règles du flux de messagerie |
| Privileged Identity Management |<p>Dispose d’un accès en lecture seule à toutes les informations présentées dans Azure AD PIM : stratégies et rapports pour les attributions de rôle Azure AD, révisions de sécurité et prochainement accès en lecture aux données et rapports de stratégie pour les scénarios en plus de l’attribution de rôle Azure AD.<p>**Ne peut pas** s’inscrire auprès d’AD PIM ou y apporter des modifications. Dans le portail PIM ou via PowerShell, un membre ayant ce rôle peut activer des rôles supplémentaires (par exemple, un administrateur général ou un administrateur de rôle privilégié), s’il est éligible. |
| <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365</p> |<ul><li>Lire et gérer les alertes<li>Lire les stratégies de sécurité<li>Lire les informations sur les menaces, Cloud App Discovery et Mise en quarantaine dans Recherche et enquêtes<li>Lecture de tous les rapports |

### <a name="security-administrator"></a>Security Administrator
| Dans | Peut |
| --- | --- |
| Identity Protection Center |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>En outre, possibilité d’effectuer toutes les opérations IPC à l’exception de la réinitialisation des mots de passe. |
| Privileged Identity Management |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>**Ne peut pas** gérer les appartenances aux rôles Azure AD ou les paramètres. |
| <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365 |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>Peut configurer tous les paramètres de la fonctionnalité de protection avancée contre les menaces (protection contre les virus et logiciels malveillants, configuration des URL malveillantes, suivi des ’URL, etc.). |

### <a name="service-administrator"></a>Administrateur de services fédérés
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p> |<p>Réinitialiser les mots de passe utilisateur</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher les journaux d’audit</p> |

### <a name="user-account-administrator"></a>Administrateur des comptes d’utilisateur
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Modifier les mots de passe des utilisateurs, des administrateurs du support technique et des autres administrateurs des comptes d’utilisateur uniquement</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur, avec des restrictions. Il lui est impossible de supprimer un administrateur général ou de créer d’autres administrateurs.</p> |<p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Activer ou désactiver l’authentification multifacteur</p><p>Afficher les journaux d’audit</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Pour ajouter un collègue en tant qu’administrateur général

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire du client.

   ![Ouvrez le Centre d’administration Azure Active Directory](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Sélectionnez **Utilisateurs et groupes&gt; Tous les utilisateurs**

3. Recherchez l’utilisateur que vous souhaitez désigner comme administrateur général et ouvrez le panneau de cet utilisateur.

4. Dans le panneau de l’utilisateur, sélectionnez **Rôle Directory**.
 
5. Dans le panneau du rôle Directory, sélectionnez le rôle **Administrateur général** et enregistrez.

## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Créateur d’utilisateur vérifié par e-mail
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Jonction d’appareils d’espace de travail

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la modification des administrateurs d’un abonnement Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’association entre Azure Active Directory et votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestion des utilisateurs](active-directory-create-users.md)
* [Gestion des mots de passe](active-directory-manage-passwords.md)
* [Gestion des groupes](active-directory-manage-groups.md)
