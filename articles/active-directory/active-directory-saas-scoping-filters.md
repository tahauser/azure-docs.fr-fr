---
title: "Approvisionner des applications avec filtres d’étendue | Microsoft Docs"
description: "Découvrez comment utiliser des filtres d’étendue pour empêcher les objets dans les applications qui prennent en charge l’approvisionnement automatisé des utilisateurs d’être approvisionnés si un objet n’est pas conforme à vos besoins."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7a2322239945a529a544054c2273e37a3d65abf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Approvisionnement d’applications basé sur les attributs avec filtres d’étendue
L’objectif de cet article est d’expliquer comment utiliser des filtres d’étendue pour définir des règles basées sur des attributs qui déterminent quels utilisateurs sont approvisionnés pour une application.

## <a name="scoping-filter-use-cases"></a>Cas d’utilisation du filtre d’étendue

Un filtre d’étendue permet au service d’approvisionnement Azure Active Directory (Azure AD) d’inclure ou d’exclure tous les utilisateurs dont un attribut correspond à une valeur spécifique. Par exemple, lors d’un approvisionnement d’utilisateurs depuis Azure AD vers une application SaaS utilisée par une équipe de vente, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Département » est « Ventes » doivent être sujets à l’approvisionnement.

Les filtres d’étendue peuvent être utilisés différemment en fonction du type du connecteur d’approvisionnement :

* **Approvisionnement sortant d’Azure AD vers des applications SaaS**. Quand Azure AD est le système source, [les affectations d’utilisateur et de groupe](active-directory-coreapps-assign-user-azure-portal.md) sont les méthodes les plus courantes pour déterminer les utilisateurs sujets à un approvisionnement. Ces affectations sont également utilisées pour activer l’authentification unique et fournissent une méthode unique pour gérer l’accès et l’approvisionnement. Les filtres d’étendue peuvent être utilisés si vous le souhaitez, en plus des affectations ou à leur place, afin de filtrer les utilisateurs selon des valeurs d’attribut.

    >[!TIP]
    > Vous pouvez désactiver l’approvisionnement basé sur des affectations pour une application d’entreprise en changeant le menu [Étendue](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) sous les paramètres d’approvisionnement sur **Synchroniser tous les utilisateurs et groupes**. L’utilisation de cette option alliée aux filtres d’étendue basés sur les attributs offre de meilleures performances qu’en utilisant des affectations de groupe.  

* **Approvisionnement entrant des applications HCM vers Azure AD et Active Directory**. Quand une [application HCM telle que Workday](active-directory-saas-workday-tutorial.md) est le système source, l’utilisation de filtres d’étendue est la principale méthode pour déterminer les utilisateurs sujets à un approvisionnement de l’application HCM vers Active Directory ou Azure AD.

Par défaut, les connecteurs d’approvisionnement Azure AD ne disposent pas de filtres d’étendue basés sur les attributs. 

## <a name="scoping-filter-construction"></a>Construction d’un filtre d’étendue

Un filtre d’étendue se compose d’une ou plusieurs *clauses*. Les clauses déterminent quels utilisateurs sont autorisés à traverser le filtre d’étendue en évaluant les attributs de chaque utilisateur. Par exemple, l’une de vos clauses peut exiger que l’attribut « État » d’un utilisateur soit égal à « New York », afin que seuls les utilisateurs de New York soient approvisionnés dans l’application. 

Une seule clause définit une condition unique pour une seule valeur d’attribut. Si plusieurs clauses sont créées dans un seul filtre d’étendue, ils sont évalués ensemble en utilisant la logique « ET ». Cela signifie que chacune des clauses doit être considérée « true » pour qu’un utilisateur soit approvisionné.

Enfin, plusieurs filtres d’étendue peuvent être créés pour une seule application. Si plusieurs filtres d’étendue sont présents, ils sont évalués ensemble en utilisant la logique « OU ». Cela signifie que si toutes les clauses présentes dans un seul des filtres d’étendue configurés sont considérées « true », l’utilisateur est approvisionné.

Chaque utilisateur ou groupe traité par le service d’approvisionnement Azure AD est toujours évalué individuellement par rapport à chaque filtre d’étendue.

Par exemple, considérez le filtre d’étendue suivant :

![Filtre d’étendue](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

D’après ce filtre d’étendue, les utilisateurs doivent satisfaire aux critères suivants pour être approvisionnés :

* Ils doivent être de New York.
* Ils doivent travailler dans le service Ingénierie.
* Leur ID d’employé de leur société doit être compris entre 1 000 000 et 2 000 000.
* Leur poste ne doit être null ou vide.

## <a name="create-scoping-filters"></a>Créer des filtres d’étendue
Les filtres d’étendue sont configurés comme parties des mappages d’attributs pour chaque connecteur d’approvisionnement d’utilisateur Azure AD. La procédure suivante suppose que l’approvisionnement automatique soit déjà configuré pour [l’une des applications prises en charge](active-directory-saas-tutorial-list.md)et que vous lui ajoutez un filtre d’étendue.

### <a name="create-a-scoping-filter"></a>Créer un filtre d’étendue
1. Dans le [Portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.

2. Sélectionnez l’application pour laquelle vous avez configuré l’approvisionnement automatique. Par exemple, « ServiceNow ».

3. Sélectionnez l’onglet **Approvisionnement**.

4. Dans la section **Mappages** , sélectionnez le mappage pour lequel vous souhaitez configurer un filtre d’étendue : par exemple, « Synchroniser les utilisateurs Azure Active Directory avec ServiceNow ».

5. Sélectionnez le menu **Étendue de l’objet source**.

6. Sélectionnez **Ajouter un filtre d’étendue**.

7. Définissez une clause en sélectionnant un **nom d’attribut** source, un **opérateur**et une **valeur d’attribut** pour effectuer la comparaison. Les opérateurs suivants sont pris en charge :

   a. **EQUALS**. La clause renvoie « true » si l’attribut évalué correspond exactement à la valeur de chaîne d’entrée (respecte la casse).

   b. **NOT EQUALS**. La clause renvoie « true » si l’attribut évalué ne correspond pas à la valeur de chaîne d’entrée (respecte la casse).

   c. **IS TRUE**. La clause renvoie « true » si l’attribut évalué contient une valeur booléenne True.

   d. **IS FALSE**. La clause renvoie « true » si l’attribut évalué contient une valeur booléenne True.

   e. **IS NULL**. La clause renvoie « true » si l’attribut évalué est vide.

   f. **IS NOT NULL**. La clause renvoie « true » si l’attribut évalué n’est pas vide.

   g. **REGEX MATCH**. La clause renvoie « true » si l’attribut évalué correspond à un modèle d’expression régulière. Exemple : ([1-9][0-9]) comprend tout nombre compris entre 10 et 99.

   h. **NOT REGEX MATCH**. La clause renvoie « true » si l’attribut évalué ne correspond pas à un modèle d’expression régulière.

8. Sélectionnez **Ajouter une nouvelle clause d’étendue**.

9. Si vous le souhaitez, répétez les étapes 7 et 8 pour ajouter d’autres clauses d’étendues.

10. Dans **Titre du filtre d’étendue**, saisissez un nom pour votre filtre d’étendue.

11. Sélectionnez **OK**.

12. Sélectionnez **OK** à nouveau sur l’écran **Filtres d’étendue**. Si vous le souhaitez, répétez les étapes 6 et 11 pour ajouter d’autres filtres d’étendue.

13. Sélectionnez **Enregistrer** sur l’écran de **mappage d’attributs**. 

>[!IMPORTANT] 
> L’enregistrement d’un nouveau filtre d’étendue déclenche une nouvelle synchronisation complète pour l’application, avec une comparaison de tous les utilisateurs dans le système source par rapport au nouveau filtre d’étendue. Si un utilisateur dans l’application répondait précédemment aux exigences d’un approvisionnement, mais que ce n’est plus le cas maintenant, son compte est désactivé ou déprovisionné dans l’application.


## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Personnaliser les mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écrire des expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Utiliser SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

