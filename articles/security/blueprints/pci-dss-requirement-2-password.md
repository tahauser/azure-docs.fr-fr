---
title: Plan de traitement des paiements Azure - Conditions relatives aux mots de passe
description: Condition 2 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 4ae9fc7d5b53d33f9feb98c450970e0560afa2af
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives aux mots de passe pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-2"></a>Condition 2 de la norme PCI DSS

**Ne pas utiliser les mots de passe système et autres paramètres de sécurité par défaut définis par le fournisseur**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Les personnes malveillantes, qu’elles soient à l’intérieur ou à l’extérieur d’une entreprise, utilisent souvent les mots de passe et autres paramètres par défaut définis par le fournisseur pour compromettre des systèmes. Ces mots de passe et paramètres sont bien connus des communautés de pirates et sont facilement détectables à partir d’informations publiques.

## <a name="pci-dss-requirement-21"></a>Condition 2.1 de la norme PCI DSS
 
**2.1** Changer systématiquement les paramètres par défaut définis par le fournisseur et supprimer ou désactiver les comptes par défaut inutiles **avant** d’installer un système sur le réseau.
Cette pratique s’applique à TOUS les mots de passe par défaut, notamment ceux utilisés par les systèmes d’exploitation, les logiciels qui assurent des services de sécurité, les comptes d’application et de système, les terminaux de point de vente (PDV) et les chaînes de communauté SNMP (Simple Network Management Protocol).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les conditions de la stratégie de mot de passe Microsoft Azure Active Directory sont appliquées pour les nouveaux mots de passe fournis par les clients dans le portail AADUX. Les changements de mot de passe effectués en libre-service à la demande de l’utilisateur nécessitent la validation du mot de passe précédent. Les mots de passe de réinitialisation des administrateurs doivent être changés à la suite d’une connexion. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore demande aux utilisateurs de définir des mots de passe forts pour tous les utilisateurs. Aucun exemple de compte ou compte invité n’est activé dans la démonstration.<br /><br />Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



### <a name="pci-dss-requirement-211"></a>Condition 2.1.1 de la norme PCI DSS

**2.1.1** Pour les environnements sans fil connectés à l’environnement des données de titulaires de carte ou qui transmettent des données de titulaires de carte, changer TOUS les paramètres par défaut définis par le fournisseur à l’installation, notamment les clés de chiffrement sans fil, les mots de passe et les chaînes de communauté SNMP.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



## <a name="pci-dss-requirement-22"></a>Condition 2.2 de la norme PCI DSS

**2.2** Développer des normes de configuration pour tous les composants système. Vérifier que ces normes couvrent toutes les vulnérabilités de la sécurité et sont compatibles avec toutes les normes renforçant les systèmes en vigueur dans le secteur.
Exemples de sources de normes renforçant les systèmes en vigueur dans le secteur :
- CIS (Center for Internet Security)
- ISO (International Organization for Standardization)
- SANS (SysAdmin Audit Network Security)
- NIST (National Institute of Standards Technology)

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Pour Microsoft Azure, l’équipe des services de sécurité technique OSSC développe des normes de configuration de sécurité pour les systèmes dans l’environnement Microsoft Azure qui sont cohérentes avec les normes renforçant les systèmes en vigueur dans le secteur. Ces configurations sont documentées dans les bases de référence système, et les changements de configuration pertinents sont communiqués aux équipes impactées (par exemple, l’équipe IPAK). Des procédures sont implémentées pour surveiller la conformité aux normes de configuration de sécurité. Les normes de configuration de sécurité pour les systèmes dans l’environnement Microsoft Azure sont cohérentes avec les normes renforçant les systèmes en vigueur dans le secteur et sont passées en revue au moins une fois par an. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore renforce la sécurité de tous les services dans l’étendue de l’environnement de données de titulaires de carte (CDE). <br /><br />Contoso Webstore déploie également [Azure Security Center](https://azure.microsoft.com/services/security-center/), qui fournit une vue centralisée de l’état de la sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention.<br /><br />Contoso Webstore utilise Operations Management Suite pour journaliser tous les changements système. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fournit une journalisation complète des changements. Vous pouvez passer en revue les changements et vérifier leur exactitude. Pour obtenir des instructions spécifiques, consultez [Aide PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>Condition 2.2.1 de la norme PCI DSS

**2.2.1** Implémenter une seule fonction principale par serveur pour éviter la coexistence, sur le même serveur, de fonctions exigeant des niveaux de sécurité différents. (Par exemple, les serveurs web, les serveurs de base de données et les serveurs DNS doivent être implémentés sur des serveurs distincts.) 

> [!NOTE]
> Si des technologies de virtualisation sont utilisées, implémentez une seule fonction principale par composant système virtuel.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les services Contoso Webstore sont déployés en tant que services PaaS. Tous les services sont isolés et segmentés à l’aide de la segmentation réseau.<br /><br />Contoso Webstore utilise également un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) pour appliquer des pratiques clés. Pour plus d’informations, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>Condition 2.2.2 de la norme PCI DSS

**2.2.2** Activer uniquement les services, protocoles, démons, etc. nécessaires au fonctionnement du système.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les configurations matérielles et logicielles de Microsoft Azure sont examinées au moins tous les trimestres pour identifier et éliminer les fonctions, ports, protocoles et services inutiles. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les services Contoso Webstore sont déployés en tant que services PaaS. Tous les services sont isolés et segmentés à l’aide de la segmentation réseau.<br /><br />Contoso Webstore utilise également un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) pour appliquer des pratiques clés. Pour plus d’informations, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>Condition 2.2.3 de la norme PCI DSS

**2.2.3** Implémenter les fonctionnalités de sécurité supplémentaires pour tout service, protocole ou démon nécessaire et jugé comme non sécurisé. 

> [!NOTE]
> En cas d’utilisation du protocole SSL/TLS initial, les conditions dans l’annexe A2 doivent être remplies.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les services Contoso Webstore sont déployés en tant que services PaaS. Tous les services sont isolés et segmentés à l’aide de la segmentation réseau. Le déploiement renforce également la sécurité de tous les services dans l’étendue du CDE. <br /><br />Contoso Webstore déploie également [Azure Security Center](https://azure.microsoft.com/services/security-center/), qui fournit une vue centralisée de l’état de la sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention.<br /><br />Contoso Webstore utilise également un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) pour appliquer des pratiques clés. Pour plus d’informations, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>Condition 2.2.4 de la norme PCI DSS

**2.2.4** Configurer les paramètres de sécurité du système pour empêcher toute utilisation malveillante.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | En instaurant des contrôles d’accès à plusieurs facteurs et en exigeant la preuve documentée d’un besoin professionnel, Azure garantit que seules les personnes autorisées peuvent configurer les contrôles de sécurité de la plateforme Azure. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise AAD et AD RBAC pour vérifier que les paramètres de sécurité sont déployés correctement. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>Condition 2.2.5 de la norme PCI DSS

**2.2.5** Supprimer toutes les fonctions inutiles, comme les scripts, les pilotes, les fonctionnalités, les sous-systèmes et les systèmes de fichiers, ainsi que les serveurs web superflus.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit des documents sur la façon dont les limites sont établies. Le modèle de menace et le diagramme de flux de données de Contoso illustrent tous les services utilisés et les contrôles activés.|



## <a name="pci-dss-requirement-23"></a>Condition 2.3 de la norme PCI DSS

**2.3** Chiffrer tous les accès administratifs non-console à l’aide d’un chiffrement fort. 

> [!NOTE]
> En cas d’utilisation du protocole SSL/TLS initial, les conditions dans l’annexe A2 doivent être remplies.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure garantit l’application d’un chiffrement fort en cas d’accès à l’infrastructure de l’hyperviseur. Microsoft Azure garantit également que les clients qui utilisent le Portail de gestion Microsoft Azure bénéficient d’un chiffrement fort quand ils accèdent à leur console de service/IaaS. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore montre comment implémenter des mots de passe forts dans une solution. Par ailleurs, il est possible d’effectuer l’ensemble des tests pour vérifier que le chiffrement est implémenté dans toute la solution.<br /><br />Contoso Webstore utilise également un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) pour appliquer des pratiques clés. Pour plus d’informations, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>Condition 2.4 de la norme PCI DSS

**2.4** Maintenir un inventaire des composants système qui se trouvent dans l’étendue de la norme PCI DSS.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Vous pouvez passer en revue l’inventaire de la solution PaaS de démonstration Contoso Webstore dans la documentation fournie. Pour plus d’informations, consultez [Aide PCI - Solutions OMS préinstallés](payment-processing-blueprint.md#oms-solutions).|



## <a name="pci-dss-requirement-25"></a>Condition 2.5 de la norme PCI DSS

**2.5** Assurer que les stratégies de sécurité et les procédures opérationnelles pour la gestion des paramètres par défaut du fournisseur et des autres paramètres de sécurité sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La documentation de Contoso Webstore offre des insights sur les paramètres de sécurité et répertorie les éléments de service. |



## <a name="pci-dss-requirement-26"></a>Condition 2.6 de la norme PCI DSS

**2.6** Les fournisseurs d’hébergement partagé doivent protéger l’environnement hébergé et les données de titulaires de carte de chaque entité. Ces fournisseurs doivent satisfaire aux conditions spécifiques décrites dans *l’Annexe A1 : Autres clauses de la norme PCI DSS s’appliquant aux fournisseurs d’hébergement partagé*.

**Responsabilités :&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. Microsoft Azure n’est pas un fournisseur d’hébergement partagé. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable. Microsoft Azure n’est pas un fournisseur d’hébergement partagé.|




