---
title: Plan de traitement des paiements Azure - Conditions relatives aux tests
description: Condition 11 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives aux tests pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-11"></a>Condition 11 de la norme PCI DSS

**Tester régulièrement les processus et les systèmes de sécurité**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Des vulnérabilités sont continuellement détectées par les individus malveillants et par les chercheurs. En outre, de nouvelles vulnérabilités apparaissent avec chaque sortie de logiciel. Les composants système, les processus et les logiciels personnalisés doivent être testés fréquemment pour que les contrôles de sécurité continuent de refléter les changements de l’environnement.

## <a name="pci-dss-requirement-111"></a>Condition 11.1 de la norme PCI DSS

**11.1** Mettre en œuvre des processus pour tester la présence de points d’accès sans fil (802.11) ; détecter et identifier tous les points d’accès sans fil autorisés et non autorisés sur une base trimestrielle.

> [!NOTE]
> Les analyses de réseau sans fil, les inspections logiques/physiques des composants du système et de l’infrastructure, le contrôle d’accès réseau (NAC) ou les systèmes de détection et/ou de prévention d’intrusions sans fil sont quelques exemples de méthodes pouvant être utilisées pour ce processus.
Quelles que soient les méthodes utilisées, elles doivent être suffisantes pour détecter et identifier les appareils autorisés, ainsi que les appareils non autorisés.


**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Azure n’autorise pas les connexions sans fil dans l’environnement réseau Azure. Les équipes de sécurité internes recherchent la présence de signaux sans fil non autorisés tous les trimestres. Ces signaux non autorisés sont alors examinés puis supprimés. Les clients ne sont pas autorisés à déployer de technologies sans fil dans l’environnement Azure. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



### <a name="pci-dss-requirement-1111"></a>Condition 11.1.1 de la norme PCI DSS

**11.1.1** Maintenir un registre des points d’accès sans fil autorisés comprenant une justification commerciale documentée.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 11.1](#pci-dss-requirement-11-1). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



### <a name="pci-dss-requirement-1112"></a>Condition 11.1.2 de la norme PCI DSS

**11.1.2** Mettre en œuvre des procédures de réponse aux incidents au cas où des points d’accès non autorisés sont détectés.


**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 11.1](#pci-dss-requirement-11-1). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



## <a name="pci-dss-requirement-112"></a>Condition 11.2 de la norme PCI DSS

**11.2** Analyser les vulnérabilités potentielles des réseaux internes et externes au moins une fois par trimestre et après tout changement significatif des réseaux (par exemple, installation de nouveaux composants du système, modification de la topologie du réseau ou des règles des pare-feu, mise à niveau de produits). 

> [!NOTE]
> De multiples rapports d’analyse peuvent être combinés pour que le processus d’analyse trimestrielle montre que tous les systèmes ont été analysés et que toutes les vulnérabilités applicables ont été traitées. Une documentation supplémentaire peut être nécessaire pour vérifier que les vulnérabilités qui n’ont pas été résolues sont en phase de l’être.
> Pour la conformité initiale à la norme PCI DSS, il n’est pas obligatoire que quatre analyses trimestrielles aient été réalisées avec succès si l’évaluateur vérifie que 1) le résultat de la dernière analyse était réussi, 2) l’entité a documenté les stratégies et les procédures exigeant l’exécution d’analyses trimestrielles et 3) toutes les vulnérabilités relevées dans les résultats ont été corrigées, comme indiqué lors de la réexécution de l’analyse. Pour les années qui suivent la vérification PCI DSS initiale, quatre analyses trimestrielles réussies ont été réalisées.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Azure effectue des analyses trimestrielles afin de détecter les vulnérabilités internes et externes. Ces analyses sont effectuées par un personnel qualifié. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application Contoso Webstore « en l’état» a fait l’objet de tests d’intrusion et de vulnérabilités. Les résultats des tests d’intrusion peuvent être dupliqués à l’aide d’outils courants tels que nmap ou pentest-tools.com. Les résultats des tests d’intrusion vont donner une surface d’attaque non concluante, sans éléments exploitables. En outre, [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des informations sur les vulnérabilités, ainsi que des solutions.|



### <a name="pci-dss-requirement-1121"></a>Condition 11.2.1 de la norme PCI DSS

**11.2.1** Effectuer l’analyse de vulnérabilité interne trimestrielle et recommencer si nécessaire, jusqu’à ce que les vulnérabilités à « haut risque » (identifiées par la condition 6.1) soient résolues. Les analyses doivent être exécutées par un personnel qualifié.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure recherche les vulnérabilités dans une étendue de l’infrastructure sous-jacente. Microsoft Azure implémente une analyse des vulnérabilités sur les systèmes d’exploitation serveur, les bases de données et les périphériques réseau, à l’aide des outils d’analyse des vulnérabilités. Les applications web Azure sont analysées à l’aide de solutions d’analyse adaptées. Les recherches de vulnérabilités sont effectuées sur une base trimestrielle.<br /><br />Les nouvelles analyses sont effectuées en fonction des besoins sur tous les systèmes, jusqu’à ce que toutes les vulnérabilités « à haut risque » (telles qu’identifiées dans la condition 6.1) soient résolues. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application Contoso Webstore « en l’état» a fait l’objet de tests d’intrusion et de vulnérabilités. Les résultats des tests d’intrusion peuvent être dupliqués à l’aide d’outils courants tels que nmap ou pentest-tools.com. Les résultats des tests d’intrusion vont donner une surface d’attaque non concluante, sans éléments exploitables. En outre, [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des informations sur les vulnérabilités, ainsi que des solutions.|



### <a name="pci-dss-requirement-1122"></a>Condition 11.2.2 de la norme PCI DSS

**11.2.2** Des analyses de vulnérabilité externe doivent être effectuées une fois par trimestre par un prestataire d’analyses agréé par le PCI SSC (Payment Card Industry Security Standards Council - Conseil des normes de sécurité PCI). Recommencer l’analyse si nécessaire, jusqu’à ce que les analyses soient réussies. 

> [!NOTE]
> Les analyses de vulnérabilité externes doivent être effectuées une fois par trimestre par un prestataire d’analyses agréé (ASV) par le PCI SSC (Payment Card Industry Security Standards Council - Conseil des normes de sécurité PCI).
> Consulter le Guide de programme ASV publié sur le site web du PCI SSC pour connaître les responsabilités du client vis-à-vis de l’analyse, la préparation de l’analyse, etc.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure effectue des analyses externes à la recherche de vulnérabilités dans une étendue de l’infrastructure sous-jacente accessible de l’extérieur. Les analyses sont effectuées par un prestataire d’analyses agréé (ASV).<br /><br />Microsoft Azure s’abonne aux notifications mensuelles de correctifs MSRC/OSSC et recherche les vulnérabilités au minimum tous les trimestres. Les vulnérabilités identifiées sont évaluées et corrigées, dans le délai établi pour le niveau de risque.<br /><br />Chaque trimestre, une analyse de sécurité complète et ciblée est effectuée sur les composants de l’environnement Microsoft Azure, par ordre de priorité, afin d’identifier les vulnérabilités. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Lors du déploiement de Contoso Webstore, les clients de la démonstration sont chargés d’effectuer, tous les trimestres, des analyses externes des vulnérabilités, ainsi que de nouvelles analyses selon les besoins sur toutes les instances PaaS de leur environnement de données de titulaire de carte. Ces analyses doivent être effectuées par un prestataire d’analyses agréé par le Conseil des normes de sécurité PCI (Payment Card Industry).<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Condition 11.2.3 de la norme PCI DSS

**11.2.3** Effectuer les analyses internes et externes et recommencer si nécessaire, après tout changement d’importance. Les analyses doivent être exécutées par un personnel qualifié.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les résultats sont signalés aux parties prenantes, et les corrections des vulnérabilités sont suivies par l’équipe de sécurité Azure jusqu’à la fin. Les résultats des tests Azure peuvent être partagés avec les clients après signature d’un accord de confidentialité. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés d’effectuer des analyses internes et externes des vulnérabilités tous les trimestres, ainsi que toute nouvelle analyse nécessaire sur l’ensemble des instances PaaS dans leur environnement de données de titulaires de cartes (CDE). Les analyses doivent être effectuées lorsque des modifications importantes sont apportées à l’étendue de l’environnement.<br /><br />Les analyses doivent être effectuées par un prestataire d’analyses agréé (ASV) ou par des membres du personnel indépendants.|



## <a name="pci-dss-requirement-113"></a>Condition 11.3 de la norme PCI DSS

**11.3** Mettre en œuvre une méthodologie pour le test de pénétration qui :
- Se base sur les approches de test de pénétration acceptées par l’industrie (par exemple NIST SP800-115)
- Recouvre la totalité du périmètre du CDE ainsi que les systèmes critiques
- Comprend un test depuis l’intérieur et l’extérieur du système
- Comprend un test pour valider tout contrôle de segmentation et de réduction de la portée
- Définit les tests de pénétration de couche d’application pour qu’ils comprennent, au minimum les vulnérabilités indiquées dans la Condition 6.5
- Définit les tests de pénétration de couche d’application pour qu’ils comprennent les composants qui prennent en charge les fonctions réseau, tels que les systèmes d’exploitation
- Comprend l’examen et la prise en compte des menaces et des vulnérabilités subies au cours des 12 derniers mois
- Spécifie la rétention des résultats de test de pénétration et les résultats des activités de réparation

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure valide les services à l’aide de tests de pénétration tiers basés sur le projet OWASP Top 10, qui utilise des testeurs certifiés CREST. Les résultats des tests sont inscrits dans un registre des risques, qui est audité et examiné régulièrement pour garantir la conformité aux pratiques de sécurité. <br /><br />Microsoft utilise également le Red Teaming dans l’infrastructure, les services et les applications gérés par Microsoft. Notez toutefois qu’aucune donnée client n’est délibérément ciblée pendant le Red Teaming et les tests de pénétration de site actif. Les tests sont effectués sur l’infrastructure et les plateformes Microsoft Azure, ainsi que sur les applications et les données de Microsoft. Les locataires, les applications et les données clients qui sont hébergés dans Azure ne sont jamais ciblés.<br /><br />Microsoft Azure a fait appel à un évaluateur indépendant pour développer un plan d’évaluation du système et procéder à l’évaluation des contrôles. Les évaluations des contrôles sont effectuées tous les ans et les résultats sont signalés aux parties concernées. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application Contoso Webstore « en l’état» a fait l’objet de tests d’intrusion et de vulnérabilités. Les résultats des tests d’intrusion peuvent être dupliqués à l’aide d’outils courants tels que nmap ou pentest-tools.com. Les résultats des tests d’intrusion vont donner une surface d’attaque non concluante, sans éléments exploitables. En outre, [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des informations sur les vulnérabilités, ainsi que des solutions.|



### <a name="pci-dss-requirement-1131"></a>Condition 11.3.1 de la norme PCI DSS

**11.3.1** Effectuer des tests de pénétration *externes* au moins une fois par an et après tout changement ou mise à niveau significatifs de l’infrastructure ou de l’application (par exemple, mise à niveau du système d’exploitation ou ajout d’un sous-réseau ou d’un serveur web dans l’environnement).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 11.3](#pci-dss-requirement-11-3). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application Contoso Webstore « en l’état» a fait l’objet de tests d’intrusion et de vulnérabilités. Les résultats des tests d’intrusion peuvent être dupliqués à l’aide d’outils courants tels que nmap ou pentest-tools.com. Les résultats des tests d’intrusion vont donner une surface d’attaque non concluante, sans éléments exploitables. En outre, [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des informations sur les vulnérabilités, ainsi que des solutions.|



### <a name="pci-dss-requirement-1132"></a>Condition 11.3.2 de la norme PCI DSS

**11.3.2** Effectuer des tests de pénétration *internes* au moins une fois par an et après tout changement ou mise à niveau significatifs de l’infrastructure ou de l’application (par exemple, mise à niveau du système d’exploitation ou ajout d’un sous-réseau ou d’un serveur web dans l’environnement).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure passe des contrats avec des évaluateurs indépendants pour effectuer des tests de pénétration de la limite Microsoft Azure. En outre, des exercices Red Team sont régulièrement effectués, et les résultats sont utilisés pour apporter des améliorations à la sécurité. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application Contoso Webstore « en l’état» a fait l’objet de tests d’intrusion et de vulnérabilités. Les résultats des tests d’intrusion peuvent être dupliqués à l’aide d’outils courants tels que nmap ou pentest-tools.com. Les résultats des tests d’intrusion vont donner une surface d’attaque non concluante, sans éléments exploitables. En outre, [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des informations sur les vulnérabilités, ainsi que des solutions.|



### <a name="pci-dss-requirement-1133"></a>Condition 11.3.3 de la norme PCI DSS

**11.3.3** Les vulnérabilités exploitables découvertes pendant le test de pénétration sont corrigées et les tests sont recommencés pour vérifier les corrections

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Des procédures ont été établies pour surveiller la présence de vulnérabilités de sécurité connues sur la plateforme Microsoft Azure. <br /><br /><br /><br />Chaque trimestre, une analyse de sécurité complète et ciblée est effectuée sur les composants de l’environnement de production Azure, par ordre de priorité, afin d’identifier les vulnérabilités. Les résultats sont signalés aux parties prenantes, et les corrections des vulnérabilités sont suivies par l’équipe jusqu’à la fin. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations), qui fournissent des informations sur les vulnérabilités et leur correction, ont été utilisés pour vérifier que tous les problèmes ont été résolus pour l’environnement de données de titulaire de carte de l’application de démonstration Contoso Webstore.|



### <a name="pci-dss-requirement-1134"></a>Condition 11.3.4 de la norme PCI DSS

**11.3.4** Si la segmentation est utilisée pour isoler l’environnement de données de titulaire de carte des autres réseaux, effectuer des tests de pénétration au moins une fois par an après toute modification de méthode ou de contrôle de segmentation pour vérifier que les méthodes de segmentation sont opérationnelles et efficaces, et isoler les systèmes hors de portée des systèmes inclus dans la portée

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Des procédures ont été établies pour surveiller la présence de vulnérabilités de sécurité connues sur la plateforme Microsoft Azure. <br /><br /><br /><br />Chaque trimestre, une analyse de sécurité complète et ciblée est effectuée sur les composants de l’environnement de production Azure, par ordre de priorité, afin d’identifier les vulnérabilités. Les résultats sont signalés aux parties prenantes, et les corrections des vulnérabilités sont suivies par l’équipe jusqu’à la fin. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations), qui fournissent des informations sur les vulnérabilités et leur correction, ont été utilisés pour vérifier que tous les problèmes ont été résolus pour l’environnement de données de titulaire de carte de l’application de démonstration Contoso Webstore.|



### <a name="pci-dss-requirement-11341"></a>Condition 11.3.4.1 de la norme PCI DSS

**11.3.4.1** *Condition supplémentaire pour les fournisseurs de services uniquement :* si la segmentation est utilisée, vérifiez l’étendue PCI DSS en effectuant un test de pénétration sur les contrôles de segmentation au moins tous les six mois, et après toute modification apportée aux contrôles/méthodes de segmentation.

> [!NOTE]
> Cette condition sera considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle deviendra obligatoire.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 11.3.4](#pci-dss-requirement-11-3-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations), qui fournissent des informations sur les vulnérabilités et leur correction, ont été utilisés pour vérifier que tous les problèmes ont été résolus pour l’environnement de données de titulaire de carte de l’application de démonstration Contoso Webstore.|



## <a name="pci-dss-requirement-114"></a>Condition 11.4 de la norme PCI DSS

**11.4** Utiliser les techniques d’intrusion-détection et/ou d’intrusion-prévention pour détecter et/ou empêcher les intrusions dans le réseau. Surveiller la totalité du trafic au périmètre de l’environnement de données du titulaire, ainsi qu’aux points critiques de l’environnement des données du titulaire et alerter le personnel en cas de soupçons de compromis.
Tenir à jour tous les moteurs d’intrusion-détection et de prévention, les lignes de base et les signatures.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure effectue une analyse en temps réel des événements au sein de son environnement d’exploitation, et les systèmes IDS génèrent des alertes en temps quasi réel sur les événements susceptibles de compromettre le système. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore est un service PaaS, et la détection et la prévention des intrusions réseau sont de la responsabilité d’Azure. [Azure Security Center](https://azure.microsoft.com/services/security-center/) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent des alertes concernant les intrusions, ainsi qu’un moyen d’y remédier.|



## <a name="pci-dss-requirement-115"></a>Condition 11.5 de la norme PCI DSS

**11.5** Déployer des mécanismes de détection de changement (par exemple, des outils de surveillance de l’intégrité des fichiers) pour alerter le personnel de toute modification non autorisée des fichiers critiques du système, fichiers de configuration ou fichiers de contenu et configurer le logiciel pour qu’il effectue des comparaisons de fichier critique au moins une fois par semaine. 

> [!NOTE]
> Pour la détection des changements, les fichiers critiques sont généralement ceux qui ne changent pas régulièrement, mais dont la modification pourrait indiquer une altération du système ou son exposition à des risques. Les mécanismes de détection des changements tels que les produits de surveillance d’intégrité de fichier sont généralement préconfigurés avec les fichiers critiques pour le système d’exploitation connexe. D’autres fichiers stratégiques, tels que ceux associés aux applications personnalisées, doivent être évalués et définis par l’entité (c’est-à-dire le commerçant ou le fournisseur de services).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure gère et informe les clients des modifications et des événements qui peuvent avoir un impact sur la sécurité ou la disponibilité des services via un tableau de bord des services en ligne. Les modifications apportées aux engagements et aux obligations de sécurité des clients Microsoft Azure sont mises à jour régulièrement sur le site web Microsoft Azure.<br /><br />L’installation ou la modification des logiciels de l’environnement de production Microsoft Azure sont limitées au personnel administratif autorisé, et suivent les procédures de gestion des modifications. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’application de démonstration Contoso Webstore est un service PaaS, et la détection des modifications a été implémentée à l’aide d’OMS. Pour plus d’informations, consultez [Aide PCI - Solutions OMS préinstallés](payment-processing-blueprint.md#oms-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Condition 11.5.1 de la norme PCI DSS

**11.5.1** Mettre en œuvre un processus pour répondre à n’importe quelle alerte générée par la solution de détection de changement

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les règles d’événements de surveillance Azure fournissent un meilleur niveau de surveillance pour les ressources et les opérations à haut risque. Les périphériques réseau gérés par Azure sont surveillés afin de garantir leur conformité aux normes de sécurité établies. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore vous avertit des modifications qui ont été apportées par l’implémentation OMS. Pour plus d’informations, consultez [Aide PCI - Solutions OMS préinstallés](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Condition 11.6 de la norme PCI DSS

**11.6** Assurer que les stratégies de sécurité et les procédures opérationnelles pour la surveillance et les tests de sécurité sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore vous avertit des modifications qui ont été apportées par l’implémentation OMS. Pour plus d’informations, consultez [Aide PCI - Solutions OMS préinstallés](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|




