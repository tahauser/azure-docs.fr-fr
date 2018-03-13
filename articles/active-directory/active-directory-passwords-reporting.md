---
title: "Rapports de réinitialisation de mot de passe libre-service : Azure Active Directory"
description: "Rapports sur les événements de réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: ae9475218141f4fdf08ef6b39fb85f3b18711e61
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Options de création de rapports pour la gestion des mots de passe Azure AD

Après le déploiement, de nombreuses organisations souhaitent savoir comment ou si la réinitialisation de mot de passe en libre-service (SSPR) est réellement utilisée. La fonctionnalité de création de rapports fournie par Azure Active Directory (Azure AD) vous aide à répondre aux questions à l’aide de rapports prédéfinis. Si vous disposez d’une licence appropriée, vous pouvez également créer des requêtes personnalisées.

![Reporting][Reporting]

Les questions suivantes peuvent trouvent réponse dans les rapports qui existent dans le [portail Azure] (https://portal.azure.com/) :

> [!NOTE]
> Vous devez être [un administrateur global](active-directory-assign-admin-roles-azure-portal.md) et vous devez vous abonner pour que ces données soient collectées pour votre organisation. Pour vous abonner, vous devez consulter l’onglet **Rapports** ou les journaux d’audit au moins une fois. Tant que cela n’est pas fait, les données ne sont pas collectées pour votre organisation.
>

* Combien de personnes se sont inscrites pour la réinitialisation des mots de passe ?
* Qui s’est inscrit pour la réinitialisation des mots de passe ?
* Quelles sont les données inscrites par ces personnes ?
* Combien de personnes ont réinitialisé leurs mots de passe au cours des sept derniers jours ?
* Quelles sont les méthodes les plus courantes employées par les utilisateurs ou les administrateurs pour réinitialiser leurs mots de passe ?
* Quels sont les problèmes courants rencontrés par les utilisateurs ou les administrateurs lors des tentatives d’utilisation de la fonctionnalité de réinitialisation des mots de passe ?
* Quels sont les administrateurs qui réinitialisent fréquemment leurs mots de passe ?
* Y a-t-il des activités suspectes en rapport avec la réinitialisation des mots de passe ?

## <a name="power-bi-content-pack"></a>Pack de contenu Power BI

Si vous utilisez Power BI, il existe un pack de contenu pour Azure AD qui permet de générer facilement des rapports pour SSPR. Pour plus d’informations sur l’utilisation et le déploiement du pack de contenu, consultez [Utilisation du pack de contenu Power BI Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md). Avec le pack de contenu, vous pouvez créer vos propres tableaux de bord et les partager avec d’autres membres de votre organisation.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Comment visualiser les rapports de gestion des mots de passe dans le portail Azure

Dans l’interface du portail Azure, nous avons amélioré la méthode d’affichage de l’activité de réinitialisation de mot de passe et d’inscription de réinitialisation de mot de passe. Utilisez les étapes suivantes pour trouver les événements de réinitialisation de mot de passe et d’inscription de réinitialisation de mot de passe :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le volet gauche.
3. Recherchez **Azure Active Directory** dans la liste des services, puis sélectionnez-le.
4. Sélectionnez **Utilisateurs et groupes**.
5. Sélectionnez **Journaux d’audit** dans le menu **Utilisateurs et groupes**. Vous affichez ainsi tous les événements d’audit qui ont concerné tous les utilisateurs de votre annuaire. Vous pouvez filtrer cette vue pour voir tous les événements associés aux mots de passe.
6. Pour filtrer cette vue et afficher uniquement les événements associés à la réinitialisation de mot de passe, sélectionnez le bouton **Filtrer** en haut du volet.
7. Dans le menu **Filtrer**, sélectionnez la liste déroulante **Catégorie** et changez pour le type de catégorie **Gestion des mots de passe en libre-service**.
8. Si vous le souhaitez, vous pouvez affiner le filtre de la liste en choisissant l’**Activité** spécifique qui vous intéresse.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Comment récupérer les événements de gestion des mots de passe à partir de l’API de rapports et d’événements Azure AD

L’API de rapports et d’événements Azure AD prend désormais en charge la récupération de toutes les informations incluses dans les rapports de réinitialisation de mot de passe et d’inscription de réinitialisation de mot de passe. En utilisant cette API, vous pouvez télécharger les événements de réinitialisation de mot de passe individuels et les événements d’inscription de réinitialisation de mot de passe, et les intégrer à la technologie de création de rapports de votre choix.

> [!IMPORTANT]
> Actuellement, l’API de rapports et d’événements Azure AD récupère jusqu’à *75 000 événements individuels* des types [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) et [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent). L’API couvre les 30 derniers jours.
> 
> Si vous avez besoin de récupérer ou de stocker des données au-delà de cette période, nous vous suggérons de les conserver dans une base de données externe à l’aide de l’API pour interroger les deltas résultants. Nous vous recommandons de commencer à récupérer ces données lorsque vous commencez à utiliser SSPR dans votre organisation. Conservez-les en externe, puis continuez à suivre les deltas à partir de ce stade.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Prise en main de l’API de création de rapports

Pour accéder à ces données, vous devrez écrire une petite application ou un petit script permettant de les extraire de nos serveurs. Pour plus d’informations, consultez [Prise en main de l’API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md).

Lorsque votre script est opérationnel, vous souhaiterez examiner les événements de réinitialisation et d’inscription de mots de passe que vous pouvez récupérer pour répondre à vos scénarios :

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) : répertorie les colonnes disponibles pour les événements de réinitialisation de mot de passe.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) : répertorie les colonnes disponibles pour les événements d’inscription de réinitialisation de mot de passe.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Description des colonnes du rapport dans le portail Azure

La liste suivante décrit chacune des colonnes du rapport dans le portail Azure en détail :

* **Utilisateur** : utilisateur ayant tenté d’effectuer une opération d’inscription de réinitialisation de mot de passe.
* **Rôle** : rôle de l’utilisateur dans l’annuaire.
* **Date et heure** : date et heure de la tentative.
* **Données inscrites** : données d’authentification fournies par l’utilisateur lors de l’inscription de réinitialisation de mot de passe.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Description des valeurs du rapport dans le portail Azure

Le tableau suivant décrit les différentes valeurs que vous pouvez définir pour chaque colonne dans le portail Azure :

| Colonne | Valeurs autorisées et leur signification |
| --- | --- |
| Données inscrites |**Adresse de messagerie de secours** : l’utilisateur a utilisé une autre adresse de messagerie ou une adresse électronique d’authentification pour s’authentifier.<p><p>**Téléphone professionnel** : l’utilisateur a utilisé un téléphone professionnel pour s’authentifier.<p>**Téléphone mobile** : l’utilisateur a utilisé un téléphone mobile ou un téléphone d’authentification pour s’authentifier.<p>**Questions de sécurité** : l’utilisateur a utilisé des questions de sécurité pour s’authentifier.<p>**Toute combinaison des méthodes précédentes, par exemple, adresse de messagerie de secours + téléphone mobile** : se produit lorsqu’une stratégie à deux verrous est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe. |

## <a name="self-service-password-management-activity-types"></a>Types d’activités de gestion des mots de passe en libre-service

Les types d’activités suivants s’affichent dans la catégorie d’événement d’audit **Self-Service Password Management** (Gestion des mots de passe en libre-service) :

* [Blocked from self-service password reset](#activity-type-blocked-from-self-service-password-reset) (Blocage de réinitialisation de mot de passe en libre-service) : indique qu’un utilisateur a tenté de réinitialiser un mot de passe, d’utiliser un verrou spécifique ou de valider un numéro de téléphone plus de cinq fois au total en l’espace de 24 heures.
* [Change password (self-service)](#activity-type-change-password-self-service) (Modification du mot de passe (libre-service)) : indique qu’un utilisateur a effectué une modification de mot de passe volontaire ou forcée (en raison d’une expiration).
* [Reset password (by admin)](#activity-type-reset-password-by-admin) (Réinitialisation de mot de passe (par l’administrateur)) : indique qu’un administrateur a effectué une réinitialisation de mot de passe pour le compte d’un utilisateur à partir du portail Azure.
* [Reset password (self-service)](#activity-type-reset-password-self-service) (Réinitialisation de mot de passe (libre-service)) : indique qu’un utilisateur a correctement réinitialisé son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com).
* [Self-service password reset flow activity progress](#activity-type-self-serve-password-reset-flow-activity-progress) (Progression de l’activité du flux de réinitialisation de mot de passe en libre-service) : indique chaque étape spécifique effectuée par un utilisateur, comme le passage d’un verrou d’authentification de réinitialisation de mot de passe spécifique, dans le cadre du processus de réinitialisation de mot de passe.
* [Unlock user account (self-service)](#activity-type-unlock-user-account-self-service) (Déverrouillage du compte d’utilisateur (libre-service)) : indique qu’un utilisateur a correctement déverrouillé son compte Active Directory sans réinitialiser son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com) à l’aide de la fonctionnalité Active Directory de déverrouillage de compte sans réinitialisation.
* [User registered for self-service password reset](#activity-type-user-registered-for-self-service-password-reset) (Utilisateur inscrit pour la réinitialisation de mot de passe en libre-service) : indique qu’un utilisateur a inscrit toutes les informations requises pour pouvoir réinitialiser son mot de passe conformément à la stratégie de réinitialisation de mot de passe du locataire actuellement spécifiée.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Type d’activité : Blocked from self-service password reset (Blocage suite à la réinitialisation du mot de passe en libre-service)

La liste suivante explique cette activité en détail :

* **Activity description** (Description de l’activité) : indique qu’un utilisateur a tenté de réinitialiser un mot de passe, d’utiliser un verrou spécifique ou de valider un numéro de téléphone plus de cinq fois au total en l’espace de 24 heures.
* **Activity actor** (Acteur de l’activité) : l’utilisateur a été empêché d’effectuer des opérations supplémentaires de réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity target** (Cible de l’activité) : l’utilisateur a été empêché d’effectuer des opérations supplémentaires de réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity status** (État de l’activité) :
  * _Success_ (Réussite) : indique qu’un utilisateur a été empêché d’effectuer des réinitialisations supplémentaires, d’essayer des méthodes d’authentification supplémentaires ou de valider des numéros de téléphone supplémentaires pendant les prochaines 24 heures.
* **Activity status failure reason** (Motif de l’état de l’activité Failure) : non applicable.

### <a name="activity-type-change-password-self-service"></a>Type d’activité : Change password (self-service) (Modification du mot de passe (libre-service))

La liste suivante explique cette activité en détail :

* **Activity description)** (Description de l’activité) : indique qu’un utilisateur a effectué une modification de mot de passe volontaire ou forcée (en raison d’une expiration).
* **Activity actor** (Acteur de l’activité) : l’utilisateur qui a modifié son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity target** (Cible de l’activité) : l’utilisateur qui a modifié son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity statuses** (États de l'activité) :
  * _Success_ (Réussite) : indique qu’un utilisateur a modifié son mot de passe avec succès.
  * _Failure_ (Échec) : indique qu’un utilisateur n’a pas réussi à modifier son mot de passe. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec.
* **Activity status failure reason** (Motif de l’état de l’activité Failure) : 
  * _FuzzyPolicyViolationInvalidPassword_ : l’utilisateur a sélectionné un mot de passe qui a été automatiquement interdit car les fonctionnalités de détection de mot de passe interdit Microsoft l’ont jugé trop banal ou trop faible.

### <a name="activity-type-reset-password-by-admin"></a>Type d’activité : Reset password (by admin) (Réinitialisation du mot de passe (par l’administrateur))

La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un administrateur a effectué une réinitialisation de mot de passe pour le compte d’un utilisateur à partir du portail Azure.
* **Activity Actor** (Acteur de l’activité) : l’administrateur qui a effectué la réinitialisation de mot de passe pour le compte d’un autre utilisateur ou d’un administrateur. Doit être un administrateur global, un administrateur de mot de passe, administrateur de l’utilisateur ou un administrateur du support technique.
* **Activity Target** (Cible de l’activité) : l’utilisateur dont le mot de passe a été réinitialisé. L’utilisateur peut être un utilisateur final ou un autre administrateur.
* **Activity statuses** (États de l'activité) :
  * _Success_ (Réussite) : indique qu’un administrateur a correctement réinitialisé le mot de passe d’un utilisateur.
  * _Failure_ (Échec) : indique qu’un administrateur n’a pas réussi à modifier le mot de passe d’un utilisateur. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec.

### <a name="activity-type-reset-password-self-service"></a>Type d’activité : Reset password (self-service) (Réinitialisation du mot de passe (libre-service))

La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un utilisateur a correctement réinitialisé son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com).
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a réinitialisé son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a réinitialisé son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity statuses** (États de l'activité) :
  * _Success_ (Réussite) : indique qu’un utilisateur a correctement réinitialisé son propre mot de passe.
  * _Failure_ (Échec) : indique qu’un utilisateur n’a pas réussi à réinitialiser son propre mot de passe. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec.
* **Activity status failure reason** (Motif de l’état de l’activité Failure) : 
  * _FuzzyPolicyViolationInvalidPassword_ : l’administrateur a sélectionné un mot de passe qui a été automatiquement interdit car les fonctionnalités de détection de mot de passe interdit Microsoft l’ont jugé trop banal ou trop faible.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Type d’activité : Self serve password reset flow activity progress (Progression de l’activité du flux de réinitialisation du mot de passe en libre-service)

La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique chaque étape spécifique effectuée par un utilisateur (comme le passage d’un verrou d’authentification de réinitialisation de mot de passe spécifique) dans le cadre du processus de réinitialisation de mot de passe.
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a effectué une partie du flux de réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a effectué une partie du flux de réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity statuses** (États de l'activité) :
  * _Success_ (Réussite) : indique qu’un utilisateur a correctement effectué une étape spécifique du flux de réinitialisation de mot de passe.
  * _Failure_ (Échec) : indique qu’une étape spécifique du flux de réinitialisation de mot de passe a échoué. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec.
* **Activity status reasons** (Motif de l’état de l’activité) : consultez le tableau suivant pour connaître [tous les motif d’état de l’activité de réinitialisation autorisés](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Type d’activité : Unlock a user account (self-service) (Déverrouillage du compte d’utilisateur (libre-service))

La liste suivante explique cette activité en détail :

* **Activity description** (Description de l’activité) : indique qu’un utilisateur a correctement déverrouillé son compte Active Directory sans réinitialiser son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com) à l’aide de la fonctionnalité Active Directory de déverrouillage de compte sans réinitialisation.
* **Activity actor** (Acteur de l’activité) : l’utilisateur qui a déverrouillé son compte sans réinitialiser son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity target** (Cible de l’activité) : l’utilisateur qui a déverrouillé son compte sans réinitialiser son mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Allowed activity statuses** (États de l’activité autorisés) :
  * _Success_ (Réussite) : indique qu’un utilisateur a correctement déverrouillé son propre compte.
  * _Failure_ (Échec) : indique qu’un utilisateur n’a pas réussi à déverrouiller son propre compte. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Type d’activité : User registered for self-service password reset (Utilisateur inscrit pour la réinitialisation du mot de passe libre-service)

La liste suivante explique cette activité en détail :

* **Activity description** (Description de l’activité) : indique qu’un utilisateur a inscrit toutes les informations requises pour pouvoir réinitialiser son mot de passe conformément à la stratégie de réinitialisation de mot de passe du locataire actuellement spécifiée. 
* **Activity actor** (Acteur de l’activité) : l’utilisateur qui s’est inscrit pour la réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Activity target** (Cible de l’activité) : l’utilisateur qui s’est inscrit pour la réinitialisation de mot de passe. L’utilisateur peut être un utilisateur final ou un administrateur.
* **Allowed activity statuses** (États de l’activité autorisés) :
  * _Success_ (Réussite) : indique qu’un utilisateur s’est inscrit pour la réinitialisation de mot de passe conformément à la stratégie actuelle. 
  * _Failure_ (Échec) : indique qu’un utilisateur n’a pas réussi à s’inscrire pour la réinitialisation de mot de passe. Vous pouvez sélectionner la ligne pour voir la catégorie **Activity status reason** (Motif de l’état de l’activité) et en savoir plus sur la cause de l’échec. 

     >[!NOTE]
     >Un échec ne signifie pas qu’un utilisateur ne peut pas réinitialiser son propre mot de passe. Cela signifie que processus d’inscription n’est pas terminé. Si son compte contient des données correctes non vérifiées (par exemple, un numéro de téléphone non validé), même s’il n’a pas vérifié ce numéro de téléphone, il peut toujours l’utiliser pour réinitialiser son mot de passe. Pour plus d’informations, consultez [Que se passe-t-il lorsqu’un utilisateur s’inscrit ?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Exemple de journaux d’audit d’activité SSPR dans Azure AD"
