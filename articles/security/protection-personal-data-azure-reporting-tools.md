---
title: Protection des données personnelles dans les documents avec les outils de rapport Azure | Microsoft Docs
description: utiliser les services de rapports Azure pour protéger la confidentialité des données personnelles dans le cadre d’initiatives ayant pour objectif la conformité avec le Règlement général de protection des données (RGPD).
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: 2c677a1dea001c6a414304fbaa683c3787b355b1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Protection des données personnelles dans les documents avec les outils de rapport Azure

Cet article décrit comment utiliser les technologies et services de création de rapports Azure pour mieux protéger la confidentialité des données personnelles. Ces informations peuvent être utilisées pour se conformer aux réglementations industrielles ou gouvernementales, telles que le Règlement général règlement de Protection des données (RGPD).
## <a name="scenario"></a>Scénario

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée, Adriatique et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni.

La compagnie utilise Microsoft Azure pour le traitement et le stockage des données d’entreprise. Ces dernières incluent des informations d’identification personnelle telles que les noms, les adresses, les numéros de téléphone et les informations de carte de crédit de sa base de clients globale. Elles incluent également des informations de ressources humaines traditionnelles telles que les adresses, les numéros de téléphone, les numéros d’identification fiscale, etc. des employés de toutes les agences de la compagnie. La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense qui inclut des informations personnelles pour effectuer le suivi des relations avec les clients actuels et anciens.

Les employés de la compagnie accèdent au réseau à partir de ses agences distantes et les agents de voyage répartis dans le monde entier ont accès à certaines ressources de l’entreprise.

## <a name="problem-statement"></a>Définition du problème

La compagnie doit protéger la confidentialité des données personnelles des employés et des clients par le biais d’une stratégie de sécurité multicouche qui utilise les fonctionnalités de gestion et de sécurité Azure pour imposer des contrôles stricts sur l’accès et le traitement des données personnelles. Elle doit être capable de démontrer ses mesures de protection à des auditeurs internes et externes.

## <a name="company-goal"></a>Objectif de l’entreprise

Dans le cadre de sa stratégie de sécurité de défense en profondeur, un objectif de l’entreprise vise à effectuer le suivi de tous les accès aux données personnelles et de leur traitement, ainsi qu’à vérifier que la documentation des protections adéquates de la confidentialité des données personnelles est en place et fonctionne.

## <a name="solutions"></a>solutions

Microsoft Azure offre des outils de surveillance, de journalisation et de diagnostic complets pour faciliter le suivi et l’enregistrement des activités et événements associés à l’accès et au traitement des données personnelles, au flux géographique des données et à l’accès par des tiers aux données personnelles. Étant donné que la sécurité des données personnelles dans le cloud est une responsabilité partagée, Microsoft fournit également à ses clients ce qui suit :

- Informations détaillées sur son propre traitement des données des clients

- Mesures de sécurité administrées par Microsoft

- Emplacement et méthodes d’envoi des données des clients

- Détails du propre processus de révision de la confidentialité de Microsoft

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion d’annuaires et d’identités multilocataire basé sur le cloud. Les fonctionnalités de création de rapports de connexion et d’audit du service vous fournissent des informations détaillées sur les activités de connexion et d’utilisation des applications pour vous aider à surveiller et à garantir l’accès adéquat aux données personnelles des employés et clients.

Il existe deux types de rapports d’activité :

- Les [rapports/journaux d’activité d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) fournissent un enregistrement détaillé des activités/tâches système.

- Le [rapport/journal d’activité de connexion](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) vous indique qui a effectué chaque activité répertoriée dans le rapport d’audit.

L’utilisation conjointe des deux vous permet de suivre l’historique de chaque tâche exécutée et de savoir qui les a exécutées. Les deux types de rapports sont personnalisables et peuvent être filtrés.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Comment accéder aux journaux d’audit et de sécurité ?

Les journaux d’audit et de sécurité sont accessibles à partir du portail Active Directory de trois manières différentes : via la section **Activité** (sélectionnez **Journaux d’audit** ou **Connexions**) ou à partir de **Utilisateurs et groupes** ou **Applications d’entreprise** sous **Gérer** dans Active Directory. Les rapports sont également accessibles via l’API de création de rapports Azure Active Directory. 

1. Dans le portail Azure, sélectionnez **Azure Active Directory**.

2. Dans la section **Activité**, sélectionnez **Journaux d’audit.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Personnalisez le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

4.  Sélectionnez un élément en mode Liste pour afficher toutes les informations disponibles le concernant.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

La création de rapports Azure Active Directory inclut également deux types de rapports de sécurité, **Utilisateurs avec indicateur de risque** et **Connexions à risque**, qui peuvent vous aider à surveiller les risques potentiels dans votre environnement Azure.

Pour plus d’informations sur le service de création de rapports, consultez [Création de rapports Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

Consultez [Rapports d’activité Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) pour plus d’informations sur les rapports disponibles dans Azure Active Directory. Ce site contient d’autres informations sur la façon d’utiliser les [rapports d’activité des journaux d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) et les [rapports d’activité de connexion](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) et d’y accéder dans le portail. Il inclut également des informations sur les rapports de sécurité [Utilisateurs avec indicateur de risque](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) et [Connexions à risque](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins).

Accédez au site des [informations de référence sur l’API d’audit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) pour plus d’informations sur la façon de se connecter à la création de rapports Azure Active Directory par programme.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) peut [collecter des données à partir d’Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pour les corréler avec d’autres données et fournir une analyse supplémentaire. Azure Monitor recueille et analyse les données de surveillance de votre environnement Azure. 

Les outils d’analyse de Log Analytics, tels que les recherches dans les journaux, les vues et les solutions fonctionnent avec toutes les données collectées. Vous disposez ainsi d’une analyse centralisée de l’ensemble de votre environnement. Log Analytics peut agréger et analyser les journaux des événements Windows, les journaux IIS et les journaux Syslog, ce qui peut vous aider à détecter d’éventuelles failles susceptibles d’exposer des données personnelles à des utilisateurs non autorisés.

#### <a name="how-do-i-use-log-analytics"></a>Comment utiliser Log Analytics ?

Vous pouvez accéder à Log Analytics par le biais du portail OMS ou du portail Azure, à partir de n’importe quel navigateur web. Log Analytics inclut un langage de requête pour rapidement récupérer et consolider les données dans le référentiel. Vous pouvez créer et enregistrer des recherches dans les journaux pour analyser directement des données dans le portail.

Pour créer un espace de travail Log Analytics dans le portail Azure, procédez comme suit :

1. Sélectionnez **Log Analytics** dans la liste des services de la Place de marché.

2. Sélectionnez **Créer**, puis spécifiez le nom de votre espace de travail OMS, sélectionnez votre abonnement, le groupe de ressources, l’emplacement et le niveau tarifaire.

3. Cliquez sur **OK** pour afficher la liste de vos espaces de travail.

4. Sélectionnez un espace de travail pour en afficher les détails.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Accédez à la [documentation Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour en savoir plus sur le service.

Accédez au didacticiel [Prise en main d’un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) pour créer un espace de travail d’évaluation et apprendre les bases de l’utilisation du service.

Consultez les pages web suivantes pour plus d’informations sur la façon de se connecter pour utiliser Log Analytics avec les journaux décrits ci-dessus :

[Sources de données de journal d’événements Windows dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Journaux IIS dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Sources de données Syslog dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor/Journal d’activité Azure 

[Azure Monitor](https://azure.microsoft.com/services/monitor/) fournit des métriques de niveau de base d’infrastructure et des journaux pour la plupart des services Microsoft Azure.
La surveillance peut vous aider à obtenir des informations détaillées sur vos applications Azure. Azure Monitor s’appuie sur l’extension de diagnostics Azure pour collecter la plupart des métriques et journaux de niveau application (Windows ou Linux). [Le journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) est une des ressources que vous pouvez afficher avec Azure Monitor. Il effectue le suivi de chaque appel d’API et fournit une multitude d’informations sur les activités qui se produisent dans [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Vous pouvez rechercher le journal d’activité (précédemment appelé journal opérationnel ou d’audit) pour obtenir des informations sur vos ressources telles qu’elles sont analysées par l’infrastructure Azure. 

Bien que la plupart des informations enregistrées dans le journal d’activité aient trait aux performances et à l’intégrité du service, il existe également des informations relatives à la protection des données. Avec le journal d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur des ressources dans votre abonnement Azure.

Par exemple, il fournit un enregistrement lorsqu’un administrateur supprime un groupe de sécurité réseau, opération susceptible d’avoir un impact sur la protection des données personnelles. Les entrées du journal d’activité sont stockées dans Azure Monitor pendant 90 jours.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Comment utiliser les données collectées par Azure Monitor ?

Il existe plusieurs façons d’utiliser les données dans le journal d’activité et d’autres ressources Azure Monitor.

- Vous pouvez diffuser les données dans d’autres emplacements dans la ligne réelle.

- Vous pouvez stocker les données plus longtemps que les valeurs par défaut, en utilisant un [compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) et en définissant une stratégie de rétention.

- Vous pouvez visualiser les données dans des graphiques à l’aide du [portail Azure](https://azure.microsoft.com/features/azure-portal/), d’[Azure Application Insights](https://azure.microsoft.com/services/application-insights/), de [Microsoft PowerBI](https://powerbi.microsoft.com/) ou d’outils de visualisation tiers.

- Vous pouvez interroger les données à l’aide de l’API REST Azure Monitor, de commandes CLI, d’applets de commande [PowerShell](https://docs.microsoft.com/powershell/) ou du SDK .NET.

Pour prendre en main Azure Monitor, sélectionnez **Tous les services** dans le portail Azure.

1. Faites défiler jusqu’à **Monitor** dans la section **Surveillance et gestion**.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitor s’ouvre en mode **Journal d’activité**.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Vous pouvez créer et enregistrer des requêtes pour les filtres courants, puis épingler les requêtes les plus importantes à un tableau de bord du portail pour que vous sachiez toujours si les événements qui répondent à vos critères se sont produits.

1. Vous pouvez filtrer l’affichage par groupe de ressources, intervalle de temps et catégorie d’événement.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Vous pouvez ensuite épingler des requêtes sur un tableau de bord de portail en cliquant sur le bouton **Épingler**. Cela vous permet de créer une seule source d’informations pour les données opérationnelles sur vos services. Le nom de la requête et le nombre de résultats s’affichent dans le tableau de bord.

Vous pouvez également utiliser Monitor pour afficher des métriques pour toutes les ressources Azure, configurer des alertes et des paramètres de diagnostic et faire des recherches dans le journal. Pour plus d’informations sur la façon d’utiliser Azure Monitor et le journal d’activité, consultez [Prise en main d’Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Azure Diagnostics 

La fonctionnalité de diagnostic dans Azure active la collecte de données auprès de plusieurs sources. Les journaux des événements Windows, y compris le journal de sécurité, peuvent s’avérer particulièrement utiles dans le suivi et la documentation de la protection des données personnelles. Le journal de sécurité effectue le suivi des événements de réussite et d’échec d’ouverture de session, ainsi que des modifications apportées aux autorisations, de la détection de modèles révélateurs de certains types d’attaque, des modifications apportées aux stratégies de sécurité, des modifications de l’appartenance à un groupe de sécurité, etc.

Par exemple, un ID d’événement 4695 vous avertit d’une tentative de déprotection de données protégées pouvant être auditées. Il relève de l’API de protection des données (DPAPI), qui permet de protéger les données telles que les clés privées, les informations d’identification stockées et d’autres informations confidentielles.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Comment activer l’extension de diagnostics pour les machines virtuelles Windows ?

Vous pouvez utiliser PowerShell pour activer l’extension de diagnostics pour une machine virtuelle Windows, afin de collecter des données de journal. Les étapes de cette opération varient selon le modèle de déploiement que vous utilisez (Resource Manager ou Classic). Pour activer l’extension de diagnostics sur une machine virtuelle existante qui a été créée avec le modèle de déploiement Resource Manager, vous pouvez utiliser l’applet de commande PowerShell [Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$$diagnosticsconfig_path* est le chemin du fichier contenant la configuration des diagnostics en langage XML. Pour plus d’instructions sur l’activation d’Azure Diagnostics sur une machine virtuelle, consultez [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

L’extension de diagnostics Azure peut transférer les données collectées vers un compte de stockage Azure ou les envoyer à des services tels qu’Application Insights. Vous pouvez ensuite utiliser les données à des fins d’audit.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Comment stocker et afficher les données de diagnostics ?

Il est important de garder à l’esprit que les données de diagnostics ne sont pas définitivement stockées, sauf si vous les transférez vers l’émulateur de stockage Microsoft Azure ou dans le stockage Azure. Pour stocker et afficher des données de diagnostics dans le stockage Azure, procédez comme suit :

1. Spécifiez un compte de stockage dans le fichier ServiceConfiguration.cscfg. Azure Diagnostics peut utiliser le service BLOB ou le service de Table, selon le type de données. Les journaux des événements Windows sont stockés sous forme de tableau.

2. Transférez les données. Vous pouvez demander à transférer les données de diagnostics via le fichier de configuration. Pour le SDK 2.4 et versions précédentes, vous pouvez également exécuter la requête par programme.

3. Affichez les données à l’aide de l’[Explorateur de Stockage Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), l’[Explorateur de serveurs](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) dans Visual Studio ou [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) dans Azure Management Studio.

Pour plus d’informations sur la manière d’effectuer chacune de ces étapes, consultez [Stocker et afficher des données de diagnostics dans Stockage Azure.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Azure Storage Analytics 

Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent être utilisées pour surveiller des requêtes individuelles, ce qui peut faciliter la documentation de l’accès aux données personnelles stockées dans le service. Toutefois, la journalisation Storage Analytics n’est pas activée par défaut pour votre compte de stockage. Vous pouvez l’activer dans le portail Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Comment configurer la surveillance d’un compte de stockage ?

Pour configurer la surveillance d’un compte de stockage, procédez comme suit :

1. Sélectionnez **Comptes de stockage** dans le portail Azure, puis sélectionnez le nom du compte à surveiller.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. Dans la section **Surveillance**, sélectionnez **Diagnostics.**

3.  Sélectionnez le **type** de données métriques à surveiller pour chaque service (BLOB, Table, Fichiers). Pour demander à Stockage Azure d’enregistrer les journaux de diagnostic pour les requêtes de lecture, d’écriture et de suppression pour les services BLOB, de Table et de File d’attente, sélectionnez **Journaux de blob, Journaux de table** et **Journaux de file d’attente.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. À l’aide du curseur situé en bas, définissez la stratégie de **rétention** en jours (valeur comprise entre 1 et 365). La valeur par défaut correspond à sept jours.

5. Sélectionnez **Enregistrer** pour appliquer les paramètres de configuration.

Les entrées de journal de Journalisation du stockage contiennent les informations suivantes sur chaque requête :

- Informations de minutage comme l’heure de début, la latence de bout en bout et la latence du serveur.

- Détails de l’opération de stockage comme le type d’opération, la clé de l’objet de stockage auquel le client accède, la réussite ou l’échec, ainsi que le code d’état HTTP retourné au client.

- Détails de l’authentification comme le type d’authentification utilisé par le client.

- Informations de concurrence comme la valeur ETag et l’horodatage de la dernière modification.

- Tailles des messages de requête et de réponse.

Pour plus d’instructions sur la façon d’activer la journalisation Storage Analytics, consultez [Surveiller un compte de stockage dans le portail Azure.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Azure Security Center](https://azure.microsoft.com/services/security-center/) surveille l’état de sécurité de vos ressources Azure afin d’empêcher et de détecter les menaces, puis fournir des recommandations pour y répondre. Elle offre plusieurs moyens de faciliter la documentation de vos mesures de sécurité qui protège la confidentialité des données personnelles.

La surveillance de l’intégrité de la sécurité vous permet d’assurer la conformité à vos stratégies de sécurité. Ici, la surveillance de la sécurité est une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques. Vous pouvez surveiller l’état de la sécurité des ressources suivantes :

- Calcul (machines virtuelles et services cloud)

- Mise en réseau (réseaux virtuels)

- Stockage et données (audit de serveur et base de données et détection des menaces, TDE, chiffrement de stockage)

- Applications (problèmes potentiels de sécurité)

Des problèmes de sécurité dans une de ces catégories peuvent constituer une menace pour la confidentialité des données personnelles.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Comment afficher l’état de sécurité de mes ressources Azure ?

Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Vous pouvez afficher toutes les éventuelles failles de sécurité qu’il identifie dans la section **Prévention** du tableau de bord.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. Dans la section **Prévention**, sélectionnez la vignette **Compute**. Voici une **vue d’ensemble,** avec la liste de toutes les **machines virtuelles** et leurs états de sécurité, ainsi que la liste des **services cloud** des rôles web et de travail surveillés par Security Center.

2. Sous l’onglet **Vue d’ensemble**, sélectionnez une recommandation pour afficher plus d’informations.

3. Dans l’onglet **Machines virtuelles**, sélectionnez une machine virtuelle pour afficher des détails supplémentaires.

Une fois la collecte de données activée dans Azure Security Center, Microsoft Monitoring Agent est automatiquement configuré sur toutes les machines virtuelles existantes et nouvelles prises en charge qui sont déployées. Les données collectées à partir de cet agent sont stockées dans un espace de travail [Log Analytics](https://azure.microsoft.com/services/log-analytics/) existant associé à votre abonnement ou dans un nouvel espace de travail.

Des [rapports d’informations sur les menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) sont fournis par Security Center. Ils vous donnent des informations utiles pour déterminer l’identité, les objectifs, ainsi que les campagnes, tactiques, outils et procédures d’attaques actuelles et historiques utilisées de l’attaquant. Des informations sur l’atténuation des risques et les corrections à apporter sont également incluses.

L’objectif principal de ces rapports sur les menaces consiste à vous aider à réagir efficacement à la menace immédiate et à prendre des mesures pour atténuer le problème. Les informations contenues dans les rapports peuvent également s’avérer utiles pour documenter la réponse que vous apportez aux incidents à des fins de création de rapports et d’audit.

Les rapports d’informations sur les menaces se présentent au format .PDF, sont accessibles via un lien dans le champ **Rapports** du panneau **Processus suspect exécuté** de chaque alerte de sécurité dans Azure Security Center.

Pour plus d’informations sur la façon d’afficher et d’utiliser le rapport d’informations sur les menaces, consultez [Rapport d’informations sur les menaces d’Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Étapes suivantes :

[Prise en main de l’API de création de rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Présentation de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Vue d’ensemble de l’analyse dans Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Présentation du journal d’activité Azure (vidéo)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
