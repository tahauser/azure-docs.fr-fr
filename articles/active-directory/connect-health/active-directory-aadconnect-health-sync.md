---
title: Utilisation d’Azure AD Connect Health avec la synchronisation | Microsoft Docs
description: Cette page dédiée à Azure AD Connect Health explique comment surveiller la synchronisation d’Azure AD Connect.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff8be88e23d57545a9926df366289c6ba264886
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Surveiller la synchronisation Azure AD Connect avec Azure AD Connect Health
La documentation suivante s’applique à la surveillance de la synchronisation Azure AD Connect avec Azure AD Connect Health.  Pour plus d’informations sur la surveillance AD FS avec Azure AD Connect Health, consultez [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md). En outre, pour plus d’informations sur la surveillance des services de domaine Active Directory avec Azure AD Connect Health, consultez [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health pour la synchronisation](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertes d’Azure AD Connect Health pour la synchronisation
La section relative aux alertes d’Azure AD Connect Health fournit une liste des alertes actives. Chaque alerte inclut les informations associées, la procédure de résolution et des liens vers la documentation afférente. Si vous sélectionnez une alerte active ou résolue, vous verrez apparaître un nouveau panneau comportant des informations supplémentaires, ainsi qu’une procédure de résolution de l’alerte et des liens vers de la documentation supplémentaire. Vous pouvez également afficher des données d’historique sur les alertes résolues par le passé.

Si vous sélectionnez une alerte, vous aurez accès à des informations supplémentaires, à une procédure de résolution de l’alerte et à des liens vers de la documentation supplémentaire.

![Erreur de synchronisation d’Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Évaluation limitée des alertes
Si Azure AD Connect n’utilise PAS la configuration par défaut (par exemple, si le filtrage des attributs passe d’une configuration par défaut à une configuration personnalisée), l’agent Azure AD Connect Health ne télécharge pas les événements d’erreur liés à Azure AD Connect.

Cela limite l’évaluation des alertes par le service. Vous verrez une bannière indiquant cette condition dans le portail Azure, sous votre service.

![Azure AD Connect Health pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner.png)

Pour modifier ceci, cliquez sur « Paramètres », puis autorisez l’agent Azure AD Connect Health à télécharger tous les journaux d’erreurs.

![Azure AD Connect Health pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Aperçu de synchronisation
Souvent, les administrateurs veulent connaître le temps nécessaire à la synchronisation des modifications apportées à Azure AD et le nombre de modifications effectuées. Cette fonctionnalité offre un moyen facile de visualiser ceci à l’aide des graphiques ci-dessous :   

* Latence des opérations de synchronisation
* Tendance de modification d’objet

### <a name="sync-latency"></a>Latence de synchronisation
Cette fonctionnalité fournit une tendance graphique de la latence des opérations de synchronisation (importation, exportation, etc.) pour les connecteurs.  Vous disposez ainsi d’un moyen rapide et simple de connaître non seulement la latence de vos opérations (ce qui s’avère utile en cas de nombreuses modifications), mais également d’un bon outil de détection des anomalies de latence susceptibles de nécessiter un examen supplémentaire.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Par défaut, seule la latence de l’opération « Exportation » pour le connecteur Azure AD est affichée.  Pour visualiser davantage d’opérations sur le connecteur ou pour afficher les opérations d’autres connecteurs, cliquez avec le bouton droit sur le graphique, sélectionnez Modifier le graphique ou cliquez sur le bouton « Modifier le graphique de latence » et choisissez l’opération spécifique et les connecteurs.

### <a name="sync-object-changes"></a>Synchronisation des modifications d’objet
Cette fonctionnalité fournit une représentation graphique du nombre de modifications en cours d’évaluation et d’exportation vers Azure AD.  Aujourd’hui, il est difficile de rassembler ces informations dans les journaux de synchronisation.  Le graphique constitue non seulement un moyen plus simple d’analyser le nombre de modifications qui se produisent dans votre environnement, mais également un aperçu visuel des défaillances qui se produisent.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Rapport d’erreurs de synchronisation de niveau objet (version préliminaire)
Cette fonctionnalité fournit un rapport sur les erreurs de synchronisation qui peuvent se produire lorsque les données d’identité se synchronisent entre Windows Server AD et Azure AD à l’aide d’Azure AD Connect.

* Le rapport répertorie les erreurs consignées par le client de synchronisation (Azure AD Connect 1.1.281.0 ou version ultérieure)
* Il inclut les erreurs qui se sont produites dans la dernière opération de synchronisation sur le moteur de synchronisation. (« Exportation » sur le connecteur Azure AD.)
* L’agent Azure AD Connect Health pour la synchronisation doit avoir une connexion sortante vers les points de terminaison requis pour permettre au rapport d’inclure les données les plus récentes.
* Le rapport est **mis à jour toutes les 30 minutes** à l’aide des données téléchargées par l’agent Azure AD Connect Health pour la synchronisation. Il offre les fonctionnalités suivantes

  * Catégorisation des erreurs
  * Liste d’objets avec erreur par catégorie
  * Toutes les données sur les erreurs en un même endroit
  * Comparaison côte à côte d’objets avec erreur en raison d’un conflit
  * Télécharger le rapport d’erreurs au format CVS (prochainement)

### <a name="categorization-of-errors"></a>Catégorisation des erreurs
Le rapport classe les erreurs de synchronisation existantes dans les catégories suivantes :

| Catégorie | Description |
| --- | --- |
| Attribut en double |Erreurs quand Azure AD Connect tente de créer ou de mettre à jour des objets avec des valeurs dupliquées d’un ou plusieurs attributs dans Azure AD et qui doivent être uniques dans un client, par exemple proxyAddresses et UserPrincipalName. |
| Incohérence de données |Erreurs quand la correspondance souple ne parvient pas à faire correspondre des objets qui génèrent des erreurs de synchronisation. |
| Échec de validation de données |Erreurs dues à des données non valides, par exemple des caractères non pris en charge dans des attributs critiques comme UserPrincipalName, erreurs de format qui empêchent la validation des données avant leur écriture dans Azure AD. |
| Modification du domaine fédéré | Erreurs lorsque les comptes utilisent un autre domaine fédéré. |
| Attribut de grande taille |Erreurs quand un ou plusieurs attributs dépassent la taille, la longueur ou le nombre autorisés. |
| Autres |Toutes les autres erreurs qui ne figurent pas dans les catégories ci-dessus. En fonction des informations reçues, cette catégorie sera divisées en plusieurs sous-catégories. |

![Rapport de résumé des erreurs de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Catégories de rapports d’erreurs de synchronisation](./media/active-directory-aadconnect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Liste d’objets avec erreur par catégorie
L’exploration de chaque catégorie fournit la liste des objets qui rencontrent l’erreur de cette catégorie.
![Liste de rapports d’erreurs de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Détails de l’erreur
Les données suivantes sont disponibles dans la vue détaillée de chaque erreur

* Identificateurs pour *l’objet AD* impliqué
* Identificateurs pour *l’objet Azure AD* impliqué (le cas échéant)
* Description de l’erreur et solution pour y remédier
* Articles connexes

![Détails du rapport d’erreurs de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Télécharger le rapport d’erreurs au format CSV
Sélectionnez le bouton Exporter pour télécharger un fichier CSV contenant tous les détails sur l’ensemble des erreurs.

## <a name="related-links"></a>Liens connexes
* [Résolution des erreurs lors de la synchronisation](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Résilience d’attribut en double](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l'agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
