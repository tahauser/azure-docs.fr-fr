---
title: "Plan de traitement des paiements Azure - Conditions relatives aux systèmes sécurisés"
description: Condition 6 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives aux systèmes sécurisés pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-6"></a>Condition 6 de la norme PCI DSS

**Développer et maintenir des systèmes et des applications sécurisés**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour obtenir des informations sur les procédures de test et des instructions pour chacune des conditions, reportez-vous à la documentation de la norme PCI DSS.

Des individus sans scrupules peuvent exploiter les points faibles de la sécurité pour obtenir un accès privilégié aux systèmes. Bon nombre de ces vulnérabilités sont résolues par les correctifs de sécurité développés par les fournisseurs. Ces correctifs doivent être installés par les entités chargées de la gestion des systèmes. Tous les systèmes doivent être dotés des correctifs logiciels appropriés afin d’empêcher l’exploitation et l’altération des données de titulaires de carte par des individus et des logiciels malveillants.

> [!NOTE]
> Les correctifs logiciels appropriés sont ceux qui ont été suffisamment évalués et testés pour déterminer qu’ils ne présentent aucun conflit avec les configurations de sécurité existantes. De nombreuses vulnérabilités peuvent être évitées dans les applications développées en interne grâce à l’utilisation de processus de développement système standard et de techniques de codage sécurisées.

## <a name="pci-dss-requirement-61"></a>Condition 6.1 de la norme PCI DSS

**6.1** Établir un processus pour identifier les vulnérabilités de la sécurité, en utilisant des sources externes de bonne réputation pour la sécurité des informations concernant la vulnérabilité et affecter un classement du risque (par exemple « élevé », « moyen » ou « faible ») aux vulnérabilités de sécurité nouvellement découvertes.

> [!NOTE]
> Le classement des risques doit se baser sur les bonnes pratiques du secteur, ainsi que sur la prise en compte de l’impact potentiel. Par exemple, les critères de classement des vulnérabilités peuvent inclure la prise en compte du score de base CVSS et/ou la classification par le fournisseur et/ou le type de système affecté. 
> 
> Les méthodes d’évaluation de vulnérabilité et d’affectation des classements de risque varient selon l’environnement de l’organisation et la stratégie d’évaluation des risques. Le classement de risque doit, au minimum, identifier toutes les vulnérabilités considérées comme posant un « risque élevé » pour l’environnement. En plus du classement de risque, les vulnérabilités peuvent être considérées comme « critiques » si elles constituent une menace imminente pour l’environnement, ont un impact critique sur les systèmes et/ou si elles sont susceptibles de compromettre l’application si elles ne sont pas résolues. Les exemples de systèmes critiques peuvent inclure les systèmes de sécurité, les dispositifs et systèmes ouverts au public, les bases de données et autres systèmes qui stockent, traitent ou transmettent des données de titulaires de carte.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Des procédures ont été établies et implémentées pour rechercher les vulnérabilités sur les hôtes hyperviseurs dans la limite d’étendue. L’analyse des vulnérabilités est effectuée sur les systèmes d’exploitation serveur, sur les bases de données et sur les périphériques réseau avec les outils appropriés. Les recherches de vulnérabilités sont effectuées sur une base trimestrielle au minimum. Microsoft Azure passe des contrats avec des évaluateurs indépendants pour effectuer des tests de pénétration de la limite Microsoft Azure. En outre, des exercices Red-Team sont régulièrement effectués, et les résultats sont utilisés pour apporter des améliorations à la sécurité. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore réduit le risque de failles de sécurité à l’aide d’Application Gateway et du WAF, où est activé l’ensemble de règles OWASP. Pour plus d’informations, consultez [Guide PCI - Atténuation des risques de vulnérabilités de sécurité](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>Condition 6.2 de la norme PCI DSS

**6.2** S’assurer que tous les logiciels et les composants de système sont protégés de vulnérabilités connues en installant les correctifs de sécurité applicables fournis par le fournisseur. Installer les correctifs de sécurité critiques dans le mois qui suit leur commercialisation.

> [!NOTE]
> Les correctifs de sécurité critiques doivent être identifiés selon le processus de classement des risques défini par la condition 6.1.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure se charge de protéger tous les périphériques réseau et logiciels de système d’exploitation hyperviseur des vulnérabilités connues en installant les correctifs de sécurité appropriés fournis par le fournisseur. Sauf si un client demande à ne pas utiliser le service, un processus de gestion des correctifs s’assure que les vulnérabilités au niveau du système d’exploitation sont prévenues et résolues en temps voulu. Tous les mois, les serveurs de production font l’objet d’une analyse visant à valider la conformité du correctif. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore est une solution de service PaaS. Azure fournit la gestion de tous les correctifs de service.|



## <a name="pci-dss-requirement-63"></a>Condition 6.3 de la norme PCI DSS

**6.3** Développer des applications logicielles internes et externes (y compris l’accès administratif aux applications par le web) en tenant compte des points suivants :
- Conformité à la norme PCI DSS (par exemple, authentification et connexion sécurisées)
- Respect des normes/bonnes pratiques du secteur
- Incorporation de la sécurité des informations au cours du cycle de vie de la conception d’un logiciel. 

> [!NOTE]
> Ce point s’applique à tous les logiciels développés en interne, ainsi qu’aux logiciels sur mesure ou personnalisés qui sont développés par un tiers.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les points de terminaison et les applications Microsoft Azure sont développés conformément à la méthodologie Microsoft Security Development Lifecycle (SDL), qui est conforme aux conditions de la norme DSS. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore a été conçu pour suivre les bonnes pratiques du secteur concernant la protection des données de titulaires de carte. Un guide de déploiement fournit les détails des mesures de sécurité, et la journalisation est activée.|



### <a name="pci-dss-requirement-631"></a>Condition 6.3.1 de la norme PCI DSS

**6.3.1** Supprimer les comptes de développement, de test et/ou les comptes d’application personnalisés, les ID d’utilisateur et les mots de passe avant l’activation des applications ou leur mise à la disposition des clients.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Une revue de sécurité finale est effectuée pour les versions majeures avant leur déploiement dans un environnement de production par un conseiller en sécurité désigné en dehors de l’équipe de développement Azure pour assurer que seules soient publiées les applications prêtes pour la production. Dans le cadre de cette vérification finale, il est assuré que tous les comptes de test et les données de test ont été supprimés. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore fournit un service de préproduction qui est journalisé et isolé. Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>Condition 6.3.2 de la norme PCI DSS

**6.3.2** Revoir le code personnalisé avant la mise en production ou la mise à la disposition des clients afin d’identifier toute vulnérabilité de codage éventuelle (à l’aide de processus manuels ou automatiques) pour inclure au moins les points suivants :
- Les modifications de code sont examinées par des individus autres que l’auteur initial du code et par des individus compétents en matière de techniques de revue de code et de pratiques de codage sécurisées.
- Les revues de code garantissent que le code est développé conformément aux bonnes pratiques de codage sécurisé.
- Les corrections appropriées sont implémentées avant la publication.
- Les résultats de la revue du code sont passés en revue et approuvés par les responsables avant le lancement. 

> [!NOTE]
> Cette condition s’applique à l’intégralité du code personnalisé (aussi bien interne qu’orienté public), dans le cadre du cycle de conception du système. 
>
> Les revues de code peuvent être réalisées par le personnel interne compétent ou par des prestataires tiers. Les applications web destinées au public font également l’objet de contrôles supplémentaires afin de résoudre les menaces et les vulnérabilités éventuelles après leur déploiement, comme défini par la condition 6.6 de la norme PCI DSS.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les points de terminaison et les applications Microsoft Azure sont développés conformément à la méthodologie Microsoft Security Development Lifecycle (SDL). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore fournit un service de préproduction qui est journalisé et isolé. Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>Condition 6.4 de la norme PCI DSS

**6.4** Suivre les processus et procédures de contrôle des changements pour toutes les modifications apportées à des composants de système. Ces processus doivent inclure les points ci-après (voir les conditions 6.4.1 à 6.4.6).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft suit les instructions de l’institut NIST concernant les considérations de sécurité relatives au développement de logiciels, selon lesquelles la sécurité des informations doit être intégrée au cycle SDLC dès la création du système. L’intégration continue de pratiques de sécurité à Microsoft Security Development Lifecycle (SDL) permet :<ul><li>L’identification et l’atténuation précoces des failles de sécurité et les erreurs de configuration</li><li>La reconnaissance des difficultés de codage de logiciel éventuelles liées aux contrôles de sécurité requis</li><li>L’identification des services de sécurité partagés et la réutilisation des bonnes pratiques et des outils relatifs à la sécurité, ce qui améliore le dispositif de sécurité par le biais de méthodes et de techniques éprouvées</li><li>La mise en œuvre du programme de gestion des risques déjà complet de Microsoft</li></ul>Microsoft Azure a établi des processus de gestion des mises en production et des changements pour contrôler la mise en œuvre des changements majeurs, notamment :<ul><li>Identification et documentation du changement planifié</li><li>Identification des objectifs de l’entreprise, des priorités et des scénarios au cours de la planification du produit</li><li>Spécification de la conception des composants/fonctionnalités</li><li>Révision de l’aptitude opérationnelle selon des critères ou une liste de vérification prédéfinis pour évaluer l’impact ou le risque global</li><li>Gestion des tests, des autorisations et des changements en fonction de critères d’entrée/sortie pour les environnements de développement (DEV), de test d’intégration (INT), de préproduction (STAGE) et de production (PROD). Les clients sont responsables de leurs propres applications hébergées sur Microsoft Azure.</li></ul> |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La démonstration Contoso Webstore fournit un service de préproduction qui est journalisé et isolé. <br /><br />Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](payment-processing-blueprint.md#network-security-groups).<br /><br />Les changements sont enregistrés à l’aide d’Operations Management Suite, et des runbooks sont utilisés pour collecter les journaux. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fournit une journalisation complète des changements. Vous pouvez passer en revue les changements et vérifier leur exactitude. Pour obtenir des instructions spécifiques, consultez [Guide PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>Condition 6.4.1 de la norme PCI DSS

**6.4.1** Séparer les environnements de test/développement des environnements de production et appliquer la séparation à l’aide de contrôles d’accès.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore fournit un service de préproduction qui est journalisé et isolé. Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>Condition 6.4.2 de la norme PCI DSS

**6.4.2** Séparation des obligations entre les environnements de développement/test et de production

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore fournit un service de préproduction qui est journalisé et isolé. Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>Condition 6.4.3 de la norme PCI DSS

**6.4.3** Les données de production (PAN actifs) ne sont pas utilisées à des fins de test ou de développement.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore n’a pas de données de numéro de compte principal actif.|



### <a name="pci-dss-requirement-644"></a>Condition 6.4.4 de la norme PCI DSS

**6.4.4** Suppression des données et comptes de tests dans les composants de système avant que le système ne devienne actif ou passe en phase de production.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore n’a pas de comptes de tests.|



### <a name="pci-dss-requirement-645"></a>Condition 6.4.5 de la norme PCI DSS

**6.4.5** Les procédures de contrôle de changement pour l’implémentation des correctifs de sécurité et des modifications de logiciel doivent inclure ce qui suit :
- **6.5.4.1** Documentation de l’impact.
- **6.5.4.2** Documentation du changement approuvée par les parties autorisées.
- **6.5.4.3** Test de fonctionnalité pour vérifier que le changement ne compromet pas la sécurité du système.
- **6.5.4.4** Procédures de suppression.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore est une solution de service PaaS. En outre, Azure fournit la gestion de tous les correctifs de service.|



### <a name="pci-dss-requirement-646"></a>Condition 6.4.6 de la norme PCI DSS

**6.4.6** Suite à un changement important, toutes les conditions pertinentes PCI DSS doivent être mises en œuvre sur tous les systèmes et réseaux, qu’ils soient nouveaux ou modifiés, et la documentation mise à jour, le cas échéant.

> [!NOTE]
> Cette condition est considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle sera obligatoire.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore est une solution de service PaaS. En outre, Azure fournit la gestion de tous les correctifs de service.|



## <a name="pci-dss-requirement-65"></a>Condition 6.5 de la norme PCI DSS

**6.5** Traiter les vulnérabilités de code les plus fréquentes dans les processus de développement de logiciel, afin d’inclure les éléments suivants :
- Former les développeurs au moins une fois par an pour perfectionner leurs techniques de codage sécurisé, afin qu’ils sachent notamment comment éviter les vulnérabilités de codage courantes.
- Développer des applications basées sur les directives de codage sécurisé.

> [!NOTE]
> Les vulnérabilités décrites aux points 6.5.1 à 6.5.10 faisaient partie des bonnes pratiques du secteur au moment de la publication de cette version de la norme PCI DSS. Cependant, comme les bonnes pratiques de gestion de la vulnérabilité du secteur sont actualisées (par exemple, le guide OWASP, le Top 25 SANS CWE, le codage sécurisé CERT, etc.), se reporter aux bonnes pratiques actuelles pour ces conditions. 
> 
> [!NOTE]
> Les conditions 6.5.1 à 6.5.6, ci-dessous, s’appliquent à toutes les applications (internes ou externes). Les conditions 6.5.7 à 6.5.10, ci-dessous, s’appliquent aux applications web et aux interfaces d’application (internes ou externes). 

- **6.5.1** Attaques par injection, notamment les injections de commandes SQL. Envisager également les attaques par injection OS, LDAP et XPath ainsi que les autres attaques par injection.
- **6.5.2** Dépassements de mémoire tampon
- **6.5.3** Stockage de chiffrement non sécurisé
- **6.5.4** Communications non sécurisées
- **6.5.5** Gestion inappropriée des erreurs
- **6.5.6** Toutes les vulnérabilités à « haut risque », identifiées dans le processus d’identification de vulnérabilité (selon la condition 6.1 de la norme PCI DSS)
- **6.5.7** Script intersite (XSS)
- **6.5.8** Contrôle d’accès inapproprié (comme des références d’objet directes non sécurisées, impossibilité de limiter l’accès URL, le survol de répertoire et la non-restriction de l’accès utilisateur aux fonctions)
- **6.5.9** Falsification de requête intersite (CSRF)
- **6.5.10** Rupture dans la gestion des authentifications et des sessions

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 6.4](#pci-dss-requirement-6-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La démonstration Contoso Webstore fournit des conseils pour sécuriser le développement, ainsi qu’un diagramme de flux de données et un modèle de menace pour illustrer les pratiques de développement sécurisé.|



## <a name="pci-dss-requirement-66"></a>Condition 6.6 de la norme PCI DSS

**6.6** Pour les applications web destinées au public, traiter les nouvelles menaces et vulnérabilités de manière continue et veiller à ce que ces applications soient protégées contre les attaques connues à l’aide de l’une des méthodes suivantes :

- Examen des applications web destinées au public à l’aide d’outils ou de méthodes d’évaluation de la sécurité et de la vulnérabilité des applications automatiques ou manuelles, au moins une fois par an et après toute modification 

   > [!NOTE]
   > Cette évaluation est différente des analyses de vulnérabilité effectuées pour la condition 11.2. 

- Installer une solution technique automatisée qui détecte et empêche les attaques basées sur Internet (par exemple, le pare-feu d’une application web) devant les applications web destinées au public pour vérifier continuellement tout le trafic.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure teste les applications web destinées au public dans le cadre de son processus SDL avant que les applications ne soient déployées sur l’environnement de production. En outre, Microsoft analyse régulièrement toutes les applications web destinées au public dans l’environnement de production pour détecter toutes les failles possibles. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La solution de référence réduit le risque de failles de sécurité à l’aide d’Application Gateway et du WAF, où est activé l’ensemble de règles OWASP. Pour plus d’informations, consultez [Guide PCI - Atténuation des risques de vulnérabilités de sécurité](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>Condition 6.7 de la norme PCI DSS

**6.7** Assurer que les politiques de sécurité et les procédures opérationnelles pour le développement et la maintenance de la sécurité des systèmes et applications sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La solution de référence réduit le risque de failles de sécurité à l’aide d’Application Gateway et du WAF, où est activé l’ensemble de règles OWASP. Pour plus d’informations, consultez [Guide PCI - Atténuation des risques de vulnérabilités de sécurité](payment-processing-blueprint.md#application-gateway).|




