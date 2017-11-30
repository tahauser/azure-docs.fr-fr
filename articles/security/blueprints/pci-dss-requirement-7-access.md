---
title: "Plan de traitement des paiements Azure - Conditions relatives à l’accès"
description: Condition 7 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives à l’accès pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-7"></a>Condition 7 de la norme PCI DSS

**Restreindre l’accès aux données du titulaire de carte aux seuls individus qui doivent les connaître**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Pour garantir que les données critiques ne soient accessibles qu’au personnel autorisé, des systèmes et des processus doivent être mis en place pour limiter l’accès en fonction du « besoin de connaître » et des responsabilités du personnel.

Le « besoin de connaître » correspond à la quantité strictement nécessaire de données et de privilèges qui est accordée au personnel autorisé pour la réalisation d’une tâche.

## <a name="pci-dss-requirement-71"></a>Condition 7.1 de la norme PCI DSS

**7.1** Restreindre l’accès aux composants du système et aux données du titulaire aux seuls individus qui doivent y accéder pour mener à bien leur travail.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Azure applique des stratégies ISMS existantes qui impliquent le contrôle de l’accès du personnel Azure aux composants système Azure, la vérification de l’efficacité du contrôle d’accès, la fourniture de l’accès administratif juste-à-temps, la révocation de l’accès lorsqu’il n’est plus nécessaire et l’assurance que le personnel qui accède à l’environnement de plateforme Azure a un « besoin de connaître ». L’accès d’Azure aux environnements des clients est très restreint et ne se fait qu’après approbation des clients.<br /><br />Des procédures ont été établies pour que seuls les employés, fournisseurs, prestataires et visiteurs autorisés puissent accéder physiquement au centre de données. Un système d’enregistrement et une vérification de sécurité sont mis en place pour le personnel devant accéder temporairement au site du centre de données. Les journaux des accès physiques sont examinés chaque trimestre par les équipes Azure. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Il revient aux clients de restreindre l’accès aux composants du système et aux données du titulaire aux seuls individus qui doivent y accéder pour mener à bien leur travail. Cela comprend la limitation et la restriction de l’accès au Portail de gestion Azure, ainsi que la spécification des comptes ou des rôles qui sont autorisés à créer, modifier ou supprimer des services PaaS.|



### <a name="pci-dss-requirement-711"></a>Condition 7.1.1 de la norme PCI DSS

**7.1.1** Définir les besoins d’accès pour chaque rôle, y compris :
- Les composants de système et les ressources de données dont chaque rôle a besoin pour accéder aux fonctions de son poste
- Le niveau de privilège nécessaire (par exemple, utilisateur, administrateur, etc.) pour accéder aux ressources

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de définir et de documenter le processus d’approbation des ID utilisateurs, de définir les privilèges minimum, de limiter l’accès aux données des titulaires de cartes, de faire usage d’ID uniques, de réaliser la séparation des tâches et de révoquer l’accès utilisateur lorsque celui-ci n’est plus nécessaire.|



### <a name="pci-dss-requirement-712"></a>Condition 7.1.2 de la norme PCI DSS

**7.1.2** Restreindre l’accès des ID utilisateurs privilégiés aux privilèges les plus faibles nécessaires pour la réalisation du travail

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a adopté des stratégies de sécurité d’entreprise, notamment une stratégie sur la sécurité des informations. Ces stratégies ont été approuvées, publiées et communiquées à Microsoft Azure. La stratégie de sécurité des informations de Microsoft Azure exige que l’accès aux ressources Microsoft Azure ne soit accordé qu’en présence d’une justification professionnelle et de l’autorisation préalable du propriétaire des ressources, et selon les principes du « besoin de connaître » et des « privilèges minimum ». La stratégie définit également les exigences du cycle de gestion des accès, notamment la configuration de l’accès, l’autorisation de l’accès, la suppression par authentification des droits d’accès et l’examen périodique des accès. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). Les rôles d’utilisateur sont limités aux responsabilités du scénario de démonstration documenté.|



### <a name="pci-dss-requirement-713"></a>Condition 7.1.3 de la norme PCI DSS

**7.1.3** Affecter l’accès basé sur la classification et la fonction professionnelles de chaque employé

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). Les rôles d’utilisateur sont limités aux responsabilités du scénario de démonstration documenté.|



### <a name="pci-dss-requirement-714"></a>Condition 7.1.4 de la norme PCI DSS

**7.1.4** Demander l’approbation documentée des parties responsables spécifiant les privilèges nécessaires.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Il revient aux clients de restreindre l’accès aux composants du système et aux données du titulaire aux seuls individus qui doivent y accéder pour mener à bien leur travail. Cela comprend la limitation et la restriction de l’accès au Portail de gestion Azure, ainsi que la spécification des comptes ou des rôles qui sont autorisés à créer, modifier ou supprimer des services PaaS.|



## <a name="pci-dss-requirement-72"></a>Condition 7.2 de la norme PCI DSS

**7.2** Établir un système de contrôle d’accès pour les composants de systèmes qui limitent l’accès aux seuls utilisateurs qui doivent accéder aux données et qui est configuré pour « refuser tous les accès » à moins qu’ils ne soient explicitement autorisés
Ce système de contrôle d’accès doit inclure les éléments suivants :
- 7.2.1. Couverture de tous les composants du système
- 7.2.2 L’octroi de privilèges aux individus repose sur leur classification et leurs fonctions professionnelles
- 7.2.3 Configuration par défaut du paramètre « Refuser tout ».

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Active Directory pour restreindre l’accès uniquement aux utilisateurs désignés. Pour plus d’informations, consultez [Directives PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>Condition 7.3 de la norme PCI DSS

**7.3** Assurer que les stratégies de sécurité et les procédures opérationnelles pour la restriction de l’accès aux données du titulaire sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La documentation Contoso Webstore fournit un cas d’usage concernant des individus qui utilisent les données des titulaires de cartes et la manière dont ils les utilisent.|




