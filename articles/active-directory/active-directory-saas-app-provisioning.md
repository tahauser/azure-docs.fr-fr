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
ms.openlocfilehash: e14ba62ce2d6c48e47a6b75387bcede68bb1a5b0
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
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
* Des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible
* Des alertes par e-mail facultatives pour les erreurs d’approvisionnement
* Création de rapports et journaux d’activité pour faciliter la surveillance et la résolution des problèmes

## <a name="why-use-automated-provisioning"></a>Pourquoi utiliser l’approvisionnement automatique ?
Voici les principales raisons pour utiliser cette fonctionnalité :

* Réduction des coûts, des inefficacités et des erreurs humaines associés aux processus d’approvisionnement manuel
* Élimination des coûts liés à l’hébergement et à la gestion de scripts et de solutions d’approvisionnement personnalisées
* Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation
* Importation facile de nombreux utilisateurs dans un système ou une application SaaS spécifique
* Disposer d’un ensemble unique de stratégies pour déterminer qui est approvisionné et qui peut se connecter à une application


## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?
    
Le **service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et les autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer d’autres objets liés à l’identité, tels que les groupes et les rôles. 

![Approvisionnement](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Approvisionnement sortant](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Approvisionnement entrant](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée d’un large éventail d’applications SaaS et de systèmes de gestion des ressources humaines populaires, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du standard SCIM 2.0.

Pour obtenir la liste de toutes les applications pour lesquelles Azure AD prend en charge un connecteur d’approvisionnement préintégré, consultez la [liste des didacticiels d’applications pour l’approvisionnement des utilisateurs](active-directory-saas-tutorial-list.md).

Pour plus d’informations sur la façon d’ajouter la prise en charge de l’approvisionnement des utilisateurs Azure AD à une application, consultez [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](active-directory-scim-provisioning.md).

Pour contacter l’équipe d’ingénierie Azure AD afin de demander une prise en charge de l’approvisionnement pour des applications supplémentaires, écrivez un message sur le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Pour qu’une application puisse prendre en charge l’approvisionnement automatique des utilisateurs, elle doit d’abord fournir les API de gestion des utilisateurs requises pour permettre aux programmes externes d’automatiser la création, la gestion et la suppression des utilisateurs. Par conséquent, toutes les applications SaaS ne sont pas compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge les API de gestion des utilisateurs, l’équipe d’ingénierie Azure AD peut développer un connecteur d’approvisionnement. Ce travail s’effectue en fonction des besoins des clients actuels et à venir. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

La configuration du service d’approvisionnement Azure AD pour une application sélectionnée commence dans le **[portail Azure](https://portal.azure.com)**. Dans la section **Azure Active Directory > Applications d’entreprise**, sélectionnez **Ajouter**, **All** (Toutes), puis sélectionnez l’une des options suivantes selon votre scénario :

* Toutes les applications de la section **Applications recommandées** prennent en charge l’approvisionnement automatique. Consultez la [liste des didacticiels d’applications pour l’approvisionnement des utilisateurs] active-directory-saas-didacticiel-list.md) pour en obtenir davantage.

* Utilisez l’option Application ne figurant pas dans la galerie pour les intégrations SCIM personnalisées

![Galerie](./media/active-directory-saas-app-provisioning/gallery.png)

Dans l’écran de gestion des applications, la configuration de l’approvisionnement se fait à partir de l’onglet **Approvisionnement**.

![Paramètres](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Vous devez indiquer les **informations d’identification de l’administrateur** au service d’approvisionnement Azure AD afin qu’il puisse se connecter à l’API de gestion des utilisateurs fournie par l’application. Cette section vous permet également d’activer les notifications par e-mail en cas d’échec des informations d’identification, ou si le travail d’approvisionnement est [mis en quarantaine](#quarantine).

* Il est possible de configurer des **mappages d’attributs** pour que le contenu de champs spécifiques du système source (exemple : Azure AD) soit synchronisé avec des champs spécifiques du système cible (exemple : ServiceNow). Cette section permet également de configurer l’approvisionnement de groupes en plus de comptes d’utilisateurs si l’application prend en charge cette fonctionnalité. L’option Matching properties (Propriétés correspondantes) permet de sélectionner les champs utilisés pour faire correspondre les comptes entre les systèmes. Les [expressions](active-directory-saas-writing-expressions-for-attribute-mappings.md) permettent de modifier et de transformer les valeurs récupérées à partir du système source avant qu’elles soient écrites dans le système cible. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md).

![Paramètres](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* Les **filtres d’étendue** indiquent au service d’approvisionnement les utilisateurs et groupes du système source à provisionner ou à déprovisionner dans le système cible. Deux aspects des filtres d’étendue sont évalués ensemble pour déterminer qui est concerné par l’approvisionnement :

    * **Filter on attribute values** (Filtrer sur les valeurs d’attribut) : le menu Source Object Scope (Portée de l’objet source) dans les mappages d’attributs permet de filtrer sur des valeurs d’attribut spécifiques. Par exemple, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Service » est défini sur « Ventes » seront concernés par l’approvisionnement. Pour plus d’informations, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](active-directory-saas-scoping-filters.md).

    * **Filter on assignments** (Filtrer sur les affectations) : le menu Étendue dans la section Approvisionnement &gt; Paramètres du portail permet de spécifier si seuls les utilisateurs et les groupes « affectés » ou tous les utilisateurs du répertoire Azure AD seront concernés par l’approvisionnement. Pour plus d’informations sur l’affectation d’utilisateurs et de groupes, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* Les **paramètres** contrôlent le fonctionnement du service d’approvisionnement pour une application, qu’elle soit ou non en cours d’exécution.

* Les **journaux d’audit** fournissent des enregistrements de chaque opération effectuée par le service d’approvisionnement Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

![Paramètres](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Le service d’approvisionnement des utilisateurs Azure AD peut également être configuré et géré à l’aide de [l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Que se passe-t-il pendant l’approvisionnement ?

Lorsque Azure AD est le système source, le service d’approvisionnement utilise la [fonctionnalité de requête différentielle de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) pour surveiller les utilisateurs et groupes. Le service d’approvisionnement exécute une synchronisation initiale sur le système source et le système cible, suivie de synchronisations incrémentielles périodiques. 

### <a name="initial-sync"></a>Synchronisation initiale
Lorsque le service d’approvisionnement est démarré, la première synchronisation effectuée permettra de réaliser les opérations suivantes :

1. Interroger tous les utilisateurs et groupes à partir du système source, afin de récupérer tous les attributs définis dans les [mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md).
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](active-directory-coreapps-assign-user-azure-portal.md) ou de [filtres d’étendue basés sur un attribut](active-directory-saas-scoping-filters.md) configurés.
3. Quand un utilisateur se trouve être affecté ou dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) désignés. Exemple : si le nom userPrincipal dans le système source est l’attribut de correspondance et s’il est mappé à userName dans le système cible, le service d’approvisionnement interroge le système cible pour les userNames qui correspondent aux valeurs de nom userPrincipal dans le système source.
4. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Conserver un filigrane à la fin de la synchronisation initiale, qui fournit le point de départ pour les synchronisations incrémentielles suivantes.

Certaines applications telles que ServiceNow, Google Apps et Box prennent non seulement en charge l’approvisionnement des utilisateurs, mais également celui des groupes et de leurs membres. Dans ce type de situation, si l’approvisionnement des groupes est activé dans les [mappages](active-directory-saas-customizing-attribute-mappings.md), le service d’approvisionnement synchronise les utilisateurs et les groupes, puis les appartenances au groupe. 

### <a name="incremental-syncs"></a>Synchronisations incrémentielles
Après la synchronisation initiale, toutes les synchronisations suivantes effectueront les opérations ci-dessous :

1. Interroger le système source pour rechercher les utilisateurs et les groupes qui ont été mis à jour depuis le dernier filigrane enregistré.
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](active-directory-coreapps-assign-user-azure-portal.md) ou de [filtres d’étendue basés sur un attribut](active-directory-saas-scoping-filters.md) configurés.
3. Quand un utilisateur se trouve être affecté ou dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) désignés.
4. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est supprimé de l’étendue (y compris si son affectation est annulée), le service désactive l’utilisateur dans le système cible via une mise à jour.
8. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est désactivé ou supprimé de façon réversible dans le système source, le service désactive l’utilisateur dans le système cible via une mise à jour.
9. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est définitivement supprimé du système source, le service efface l’utilisateur dans le système cible. Dans Azure AD, les utilisateurs sont supprimés définitivement 30 jours après leur suppression de façon réversible.
10. Conserver un nouveau filigrane à la fin de la synchronisation initiale, qui fournit le point de départ pour les synchronisations incrémentielles suivantes.

>[!NOTE]
> Vous pouvez éventuellement désactiver les opérations de création, mise à jour et suppression à l’aide des cases à cocher **Actions d’objet cible** dans la section [Mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md). La logique pour désactiver un utilisateur pendant une mise à jour est également contrôlée via un mappage d’attributs à partir d’un champ tel que « accountEnabled ».

Le service d’approvisionnement continuera à exécuter indéfiniment des synchronisations incrémentielles dos à dos, à des intervalles définis dans le [didacticiel spécifique à chaque application](active-directory-saas-tutorial-list.md), jusqu’à ce que l’un des événements suivants se produise :

* Le service est arrêté manuellement à l’aide du portail Azure, ou à l’aide de la commande API Graph appropriée 
* Une nouvelle synchronisation initiale est déclenchée à l’aide de l’option **Clear state and restart** (Effacer l’état et redémarrer) dans le portail Azure, ou à l’aide de la commande API Graph appropriée. Cela permet d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
* Une nouvelle synchronisation initiale est déclenchée en raison d’une modification dans les mappages d’attributs ou les filtres d’étendue. Cela permet également d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
* Le processus d’approvisionnement est mis en quarantaine (voir ci-dessous) en raison d’un taux d’erreur élevé et reste en quarantaine pendant plus de quatre semaines. Dans ce cas, le service sera automatiquement désactivé.

### <a name="errors-and-retries"></a>Erreurs et nouvelles tentatives 
Si un utilisateur ne peut pas être ajouté, mis à jour ou supprimé dans le système cible en raison d’une erreur dans le système cible, une nouvelle tentative sera effectuée lors du prochain cycle de synchronisation. Si l’utilisateur continue d’échouer, les nouvelles tentatives ne seront possibles qu’à une fréquence réduite, pour finalement n’être autorisées qu’une seule fois par jour. Pour résoudre le problème, les administrateurs devront vérifier les [journaux d’audit](active-directory-saas-provisioning-reporting.md) pour identifier les événements « traitement d’escrow », afin de déterminer la cause racine et prendre les mesures nécessaires. Les échecs courants peuvent inclure :

* Utilisateurs n’ayant pas d’attribut renseigné dans le système source alors qu’il est requis dans le système cible
* Utilisateurs ayant une valeur d’attribut dans le système source pour laquelle il existe une contrainte unique dans le système cible, et la même valeur se trouve dans un autre enregistrement utilisateur

Ces échecs peuvent être résolus en ajustant les valeurs d’attribut de l’utilisateur concerné dans le système source, ou en ajustant les mappages d’attributs pour ne pas provoquer de conflits.   

### <a name="quarantine"></a>Mise en quarantaine
Si la plupart ou la totalité des appels effectués sur le système cible échouent en permanence en raison d’une erreur (comme dans le cas d’informations d’identification non valides), le travail d’approvisionnement passe à un état « mis en quarantaine ». Cela est indiqué dans le [rapport de synthèse sur l’approvisionnement](active-directory-saas-provisioning-reporting.md) et via les notifications par e-mail si elles ont été configurées dans le portail Azure. 

Lors de la mise en quarantaine, la fréquence des synchronisations incrémentielles est progressivement réduite à une fois par jour. 

Le travail d’approvisionnement sera supprimé de la mise en quarantaine après la correction de toutes les erreurs qui posent problème, et le prochain cycle de synchronisation démarre. Si le travail d’approvisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé.


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Combien de temps faut-il pour approvisionner mes utilisateurs ?**

Les performances seront différentes selon que votre travail d’approvisionnement effectue une synchronisation initiale ou une synchronisation incrémentielle.

Pour les synchronisations initiales, le temps nécessaire dépendra directement du nombre d’utilisateurs, de groupes et de membres du groupe présents dans le système source. Des systèmes source très petits comptant des centaines d’objets peuvent effectuer des synchronisations initiales en quelques minutes. À l’inverse, les synchronisations initiales des systèmes source avec des centaines de milliers, voire des millions d’objets combinés peuvent prendre beaucoup de temps.

Pour les synchronisations incrémentielles, le temps nécessaire dépend du nombre de modifications détectées dans ce cycle de synchronisation. S’il existe moins de 5 000 modifications d’appartenance au groupe ou d’utilisateur détectées, elles peuvent souvent être synchronisées en 40 minutes. 

Notez que les performances globales dépendent des systèmes source et cible. Certains systèmes cible implémentent des limites de taux de requête qui peuvent affecter les performances lors des opérations de synchronisation importantes, et les connecteurs d’approvisionnement Azure AD préintégrés de ces systèmes en tiennent compte.

Les performances sont également ralenties s’il existe de nombreuses erreurs (enregistrées dans les [journaux d’audit](active-directory-saas-provisioning-reporting.md)) et si le service d’approvisionnement est passé à l’état de « quarantaine ».

**Comment puis-je améliorer les performances de synchronisation ?**

La plupart des problèmes de performances se produisent pendant les synchronisations initiales des systèmes qui ont un grand nombre de groupes et de membres de groupe.

Si la synchronisation des groupes ou des appartenances au groupe n’est pas obligatoire, les performances de synchronisation peuvent considérablement être améliorées en effectuant les actions suivantes :

1. Définissez le menu **Approvisionnement > Paramètres > Étendue** sur **Sync all** (Tout synchroniser), plutôt que de synchroniser les groupes et utilisateurs affectés.
2. Utilisez des [filtres d’étendue](active-directory-saas-scoping-filters.md) au lieu des affectations pour filtrer la liste des utilisateurs approvisionnés.

> [!NOTE]
> Pour les applications qui prennent en charge l’approvisionnement des propriétés et des noms de groupe (comme ServiceNow et Google Apps), désactiver cette option réduit également le temps nécessaire à une synchronisation initiale. Si vous ne souhaitez pas approvisionner les noms de groupes et les appartenances à des groupes pour votre application, vous pouvez désactiver cette fonctionnalité dans les [mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md) de votre configuration d’approvisionnement.

**Comment puis-je suivre la progression de la tâche d’approvisionnement actuelle ?**

Consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

**Comment savoir si les utilisateurs ne sont pas approvisionnés correctement ?**

Tous les échecs sont enregistrés dans les journaux d’audit Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](active-directory-saas-provisioning-reporting.md).

**Comment puis-je créer une application qui fonctionne avec le service d’approvisionnement ?**

Consultez [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Comment puis-je envoyer des commentaires à l’équipe d’ingénierie ?**

Contactez-nous via le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Articles connexes
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)

