---
title: Attribution automatique des utilisateurs dans les applications SaaS dans Azure AD | Microsoft Docs
description: "Cette introduction explique comment utiliser Azure AD pour approvisionner, annuler l’approvisionnement et mettre à jour de façon continue des comptes d’utilisateurs dans diverses applications SaaS tierces."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: cd82ef109abbc5707db4c02c2f14f9d55dfb74e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Qu’est-ce que l’attribution automatique des utilisateurs dans les applications SaaS ?
Azure Active Directory (Azure AD) vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) comme Dropbox, Salesforce, ServiceNow et bien plus encore.

**Voici quelques exemples d’utilisation de cette fonctionnalité :**

* Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
* Désactivez automatiquement les comptes dans les systèmes adéquats lorsque des personnes quittent votre équipe ou votre organisation.
* Vérifiez que les identités dans vos applications et systèmes sont tenues à jour selon les modifications apportées au répertoire ou à votre système de gestion des ressources humaines.
* Procédez à l’approvisionnement d’objets non utilisateur, tels que des groupes, pour les applications qui les prennent en charge.

**L’approvisionnement automatique d’utilisateurs inclut également les fonctionnalités suivantes :**

* Possibilité de faire correspondre des identités existantes entre les systèmes source et cible
* Options de personnalisation pour permettre à Azure AD de s’ajuster aux configurations des applications et systèmes actuellement utilisés par votre organisation
* Alertes par courrier électronique pour les erreurs d’approvisionnement
* Création de rapports et journaux d’activité pour faciliter la surveillance et la résolution des problèmes

## <a name="why-use-automated-provisioning"></a>Pourquoi utiliser l’approvisionnement automatique ?
Voici les principales raisons pour utiliser cette fonctionnalité :

* Réduction des coûts, des inefficacités et des erreurs humaines associées au processus d’approvisionnement manuel
* Élimination des coûts liés à l’hébergement et à la gestion de solutions d’approvisionnement personnalisées
* Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation
* Importation facile de nombreux utilisateurs en bloc dans une application SaaS ou un système spécifique
* Exécution pratique de votre solution d’approvisionnement à partir des mêmes stratégies d’accès aux applications que celles définies pour l’authentification unique Azure AD


## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?
    
Le **service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et les autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer d’autres objets liés à l’identité, tels que les groupes et les rôles. 

![Approvisionnement](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Approvisionnement sortant](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Approvisionnement entrant](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée d’un large éventail d’applications SaaS et de systèmes de gestion des ressources humaines populaires, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du [standard SCIM 2.0](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

Toutes les applications « à la une » de la galerie d’applications Azure AD prennent en charge l’approvisionnement automatique des utilisateurs. [La liste des applications à la une peut être vue ici.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Pour qu’une application puisse prendre en charge l’approvisionnement automatique des utilisateurs, elle doit d’abord fournir les points de terminaison de gestion des utilisateurs requis pour permettre aux programmes externes d’automatiser la création, la gestion et la suppression des utilisateurs. Par conséquent, toutes les applications SaaS ne sont pas compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge les API de gestion des utilisateurs, l’équipe d’ingénierie Azure AD peut développer un connecteur d’approvisionnement. Ce travail s’effectue en fonction des besoins des clients actuels et à venir. 

Pour contacter l’équipe d’ingénierie Azure AD afin de demander une prise en charge de l’approvisionnement pour des applications supplémentaires, écrivez un message sur le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

La configuration du service d’approvisionnement Azure AD pour une application sélectionnée commence dans le **[portail Azure](https://potal.azure.com)**. Dans la section **Azure Active Directory > Applications d’entreprise**, sélectionnez **Ajouter**, **All** (Toutes), puis sélectionnez l’une des options suivantes selon votre scénario :

* Toutes les applications de la section **Featured applications** (Applications recommandées) prennent en charge l’approvisionnement automatique

* Utilisez l’option Application ne figurant pas dans la galerie pour les intégrations SCIM personnalisées

![Galerie](./media/active-directory-saas-app-provisioning/gallery.png)

Dans l’écran de gestion des applications, la configuration de l’approvisionnement se fait à partir de l’onglet **Approvisionnement**.

![Paramètres](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Vous devez indiquer les **informations d’identification de l’administrateur** au service d’approvisionnement Azure AD afin qu’il puisse se connecter à l’API de gestion des utilisateurs fournie par l’application.

* Il est possible de configurer des **mappages d’attributs** pour que le contenu de champs spécifiques du système source (exemple : Azure AD) soit synchronisé avec des champs spécifiques du système cible (exemple : ServiceNow). Cette section permet également de configurer l’approvisionnement de groupes en plus de comptes d’utilisateurs si l’application prend en charge cette fonctionnalité. L’option Matching properties (Propriétés correspondantes) permet de sélectionner les champs utilisés pour faire correspondre les comptes entre les systèmes. Les [expressions](active-directory-saas-writing-expressions-for-attribute-mappings.md) permettent de modifier et de transformer les valeurs récupérées à partir du système source avant qu’elles soient écrites dans le système cible. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md).

![Paramètres](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* Les **filtres d’étendue** indiquent au service d’approvisionnement les utilisateurs et groupes du système source à provisionner ou à déprovisionner dans le système cible. Deux aspects des filtres d’étendue sont évalués ensemble pour déterminer qui est concerné par l’approvisionnement :

* **Filter on attribute values** (Filtrer sur les valeurs d’attribut) : le menu Source Object Scope (Portée de l’objet source) dans les mappages d’attributs permet de filtrer sur des valeurs d’attribut spécifiques. Par exemple, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Service » est défini sur « Ventes » seront concernés par l’approvisionnement.

* **Filter on assignments** (Filtrer sur les affectations) : le menu Étendue dans la section Approvisionnement &gt; Paramètres du portail permet de spécifier si seuls les utilisateurs et les groupes « affectés » ou tous les utilisateurs du répertoire Azure AD seront concernés par l’approvisionnement. Pour plus d’informations sur l’affectation d’utilisateurs et de groupes, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* Les **paramètres** contrôlent le fonctionnement du service d’approvisionnement pour une application, qu’elle soit ou non en cours d’exécution.

* Les **journaux d’audit** fournissent des enregistrements de chaque opération effectuée par le service d’approvisionnement Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

![Paramètres](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>Que se passe-t-il pendant l’approvisionnement ?

1. Lorsque vous activez l’approvisionnement d’une application pour la première fois, les actions suivantes sont effectuées :
   * Azure AD tente de faire correspondre tous les utilisateurs existants dans l’application SaaS à leurs identités dans l’annuaire. Lorsqu'un utilisateur est mis en correspondance, il n’est *pas* automatiquement activé pour l’authentification unique. Pour qu’un utilisateur puisse accéder à l’application, il doit être affecté de manière explicite à l’application dans Azure AD, soit directement, soit via une appartenance au groupe.
   * Si vous avez déjà spécifié quels utilisateurs doivent être affectés à l’application et si Azure AD ne parvient pas à trouver des comptes existants pour ces utilisateurs, Azure AD configure de nouveaux comptes pour eux dans l’application.
2. Une fois la synchronisation initiale terminée comme décrit ci-dessus, Azure AD vérifie toutes les 20 minutes si les modifications suivantes ont été apportées :
   * Si de nouveaux utilisateurs ont été affectés à l’application (directement ou via l’appartenance à un groupe), ils sont approvisionnés avec un nouveau compte dans l’application SaaS.
   * Si l’accès d’un utilisateur a été supprimé, son compte dans l’application SaaS est marqué comme désactivé (les utilisateurs ne sont jamais complètement supprimés, ce qui empêche toute perte de données en cas d’erreur de configuration).
   * Si un utilisateur a été récemment affecté à une application SaaS dans laquelle il a déjà un compte, ce compte est marqué comme activé et certaines propriétés de l’utilisateur sont mises à jour si elles sont obsolètes par rapport au répertoire.
   * Si les informations d’un utilisateur (son numéro de téléphone, le lieu où se trouve son bureau, etc.) ont été modifiées dans le répertoire, elles sont également mises à jour dans l’application SaaS.


## <a name="frequently-asked-questions"></a>Forum Aux Questions
**À quelle fréquence Azure AD écrit-il les modifications d’annuaire dans l’application SaaS ?**

Une fois la synchronisation initiale complète terminée, le service d’approvisionnement Azure AD vérifie généralement toutes les 20 minutes si des modifications ont été apportées. 

Si l’application SaaS renvoie plusieurs erreurs (par exemple, dans le cas d’informations d’identification d’administration incorrectes), Azure AD ralentit progressivement sa fréquence de vérification jusqu’à une fois par jour, jusqu'à ce que les erreurs soient résolues. À ce moment-là, la tâche d’approvisionnement Azure AD passe à l’état de « quarantaine », ce qui est indiqué dans le [rapport de synthèse sur l’approvisionnement](active-directory-saas-provisioning-reporting.md).

**Combien de temps faut-il pour approvisionner mes utilisateurs ?**

Une fois la synchronisation initiale complète terminée, les modifications incrémentielles interviennent généralement dans un délai de 20 à 40 minutes. Si vous essayez d’approvisionner la quasi-totalité de votre répertoire, la durée dépend du nombre d’utilisateurs et de groupes que vous avez. Les performances dépendent des performances des API de gestion des utilisateurs utilisées par le service d’approvisionnement pour lire les données à partir du système source et écrire les données dans le système cible. 

Les performances sont également ralenties s’il existe de nombreuses erreurs (enregistrées dans les [journaux d’audit](active-directory-saas-provisioning-reporting.md)) et si le service d’approvisionnement est passé à l’état de « quarantaine ».

**Qu’est-ce qu’une synchronisation initiale complète, et pourquoi demande-t-elle plus de temps que les synchronisations suivantes ?**

La première fois que le service d’approvisionnement Azure AD est exécuté pour une application donnée, il enregistre un « instantané » des utilisateurs (et éventuellement des groupes) que contient le répertoire source. Cet instantané permet au service d’approvisionnement de réduire le nombre d’allers-retours vers les API source et cible, et accroît l’efficacité des synchronisations « delta » suivantes. 

La synchronisation initiale complète des utilisateurs peut souvent être effectuée en quelques minutes pour les répertoires très petits, mais elle peut prendre beaucoup plus de temps pour les répertoires de grande taille. Par exemple, la synchronisation initiale d’un répertoire d’entreprise contenant des centaines de milliers d’utilisateurs peut demander plusieurs heures. Cependant, après la synchronisation initiale, les synchronisations « delta » suivantes seront beaucoup plus rapides.

> [!NOTE]
> Pour les applications qui prennent en charge l’approvisionnement de groupes et d’appartenances à des groupes, l’activation de cette fonctionnalité accélère considérablement l’exécution d’une synchronisation complète. Si vous ne souhaitez pas approvisionner les noms de groupes et les appartenances à des groupes pour votre application, vous pouvez désactiver cette fonctionnalité dans les [mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md) de votre configuration d’approvisionnement.

**Comment puis-je suivre la progression de la tâche d’approvisionnement actuelle ?**

Consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

**Comment savoir si les utilisateurs ne sont pas approvisionnés correctement ?**

Tous les échecs sont enregistrés dans les journaux d’audit Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

**Comment puis-je envoyer des commentaires à l’équipe d’ingénierie ?**

Contactez-nous via le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

