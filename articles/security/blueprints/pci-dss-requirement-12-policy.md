---
title: "Plan de traitement des paiements Azure - Conditions relatives aux stratégies"
description: Condition 12 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives aux stratégies pour les environnements conformes à la norme PCI DSS  
## <a name="pci-dss-requirement-12"></a>Condition 12 de la norme PCI DSS

**Gérer une stratégie de sécurité des informations pour l’ensemble du personnel**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Une stratégie de sécurité renforcée définit les règles de sécurité pour l’ensemble de l’entité et informe le personnel de ce qui est attendu de lui. Tout le personnel doit être informé du caractère sensible des données, ainsi que des responsabilités qui lui ont été confiées pour protéger ces données. Dans le cadre de la condition 12, le terme « personnel » désigne les employés à temps plein et à temps partiel, les intérimaires ainsi que les sous-traitants et les consultants qui « résident » sur le site de l’entité ou qui ont accès à l’environnement de données de titulaire de carte.

## <a name="pci-dss-requirement-121"></a>Condition 12.1 de la norme PCI DSS

**12.1** Établir, publier, maintenir et diffuser une stratégie de sécurité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de l’établissement et de la gestion d’une stratégie de sécurité des informations.|



### <a name="pci-dss-requirement-1211"></a>Condition 12.1.1 de la norme PCI DSS

**12.1.1** Examiner la stratégie de sécurité au moins une fois par an et mettre la stratégie à jour lorsque l’environnement change

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent mettre à jour leur stratégie de sécurité des informations au moins une fois par an, et lorsque des modifications sont apportées à leur environnement de données de titulaire de carte (CDE).|



## <a name="pci-dss-requirement-122"></a>Condition 12.2 de la norme PCI DSS

**12.2** Mettre en œuvre un processus d’évaluation des risques qui :
- Est effectué au moins une fois par an et à la suite des changements significatifs apportés à l’environnement (par exemple, acquisition, intégration, déménagement, etc.)
- Identifie les actifs critiques, les menaces et vulnérabilités
- Donne lieu à une évaluation formelle des risques
- > Les exemples de méthodologies d’évaluation des risques comprennent entre autres les directives OCTAVE, ISO 27005 et NIST SP 800-30.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de l’implémentation d’un processus d’évaluation des risques traitant toutes les menaces répertoriées dans la Condition 12.2.|



## <a name="pci-dss-requirement-123"></a>Condition 12.3 de la norme PCI DSS

**12.3** Développer les stratégies d’utilisation des technologies critiques et définir l’utilisation adéquate de ces technologies

> [!NOTE]
> Les exemples de technologies critiques comprennent notamment l’accès à distance et les technologies sans fil, les ordinateurs portables, les tablettes, les supports électroniques amovibles, l’utilisation de l’e-mail et d’Internet.
S’assurer que ces stratégies d’utilisation exigent ce qui suit.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1231"></a>Condition 12.3.1 de la norme PCI DSS

**12.3.1** Approbation explicite des responsables.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1232"></a>Condition 12.3.2 de la norme PCI DSS

**12.3.2** Authentification pour l’utilisation des technologies

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1233"></a>Condition 12.3.3 de la norme PCI DSS

**12.3.3** Liste de tous les périphériques et du personnel disposant d’un accès

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1234"></a>Condition 12.3.4 de la norme PCI DSS

**12.3.4** Une méthode permettant de déterminer rapidement et avec précision le propriétaire, les coordonnées et le but (par exemple, étiquetage, codage et/ou inventaire des appareils)

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1235"></a>Condition 12.3.5 de la norme PCI DSS

**12.3.5** Usages acceptables de la technologie

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1236"></a>Condition 12.3.6 de la norme PCI DSS

**12.3.6** Emplacements acceptables des technologies sur le réseau

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de déterminer des emplacements réseau acceptables pour les machines virtuelles, ainsi que pour le stockage et les services de prise en charge dans le cloud.|



### <a name="pci-dss-requirement-1237"></a>Condition 12.3.7 de la norme PCI DSS

**12.3.7** Liste des produits approuvés par la société

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de déterminer des emplacements réseau acceptables pour les machines virtuelles, ainsi que pour le stockage et les services de prise en charge dans le cloud.|



### <a name="pci-dss-requirement-1238"></a>Condition 12.3.8 de la norme PCI DSS

**12.3.8** Déconnexion automatique des sessions des technologies d’accès à distance après une période d’inactivité spécifique

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise des fonctionnalités de verrouillage de session Microsoft AD pour l’entreprise, qui verrouillent une session après une période d’inactivité donnée. Les connexions réseau sont arrêtées après 30 minutes d’inactivité. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1239"></a>Condition 12.3.9 de la norme PCI DSS

**12.3.9** Activation des technologies d’accès à distance pour les fournisseurs et les partenaires commerciaux, uniquement lorsque c’est nécessaire, avec désactivation immédiate après usage

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-12310"></a>Condition 12.3.10 de la norme PCI DSS

**12.3.10** Lors de l’accès du personnel aux données du titulaire, au moyen de technologies d’accès à distance, interdire la copie, le déplacement et le stockage de données du titulaire sur des disques durs locaux et des supports électroniques amovibles, sauf autorisation expresse pour un besoin professionnel défini
Lorsqu’il existe un besoin professionnel autorisé, la stratégie d’utilisation doit exiger que les données soient protégées selon toutes les conditions applicables de la norme PCI DSS.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Il revient aux clients d’interdire au personnel qui accèdent aux données de titulaire de carte, au moyen de technologies d’accès à distance, de copier, déplacer et stocker ces données sur des disques durs locaux et des supports électroniques amovibles, sauf autorisation expresse pour un besoin professionnel défini.|



## <a name="pci-dss-requirement-124"></a>Condition 12.4 de la norme PCI DSS

**12.4** S’assurer que la stratégie et les procédures de sécurité définissent clairement les responsabilités de tout le personnel en matière de sécurité

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1241"></a>Condition 12.4.1 de la norme PCI DSS

**12.4.1** Une condition supplémentaire s’applique aux fournisseurs de services uniquement : la direction doit établir la responsabilité de la protection des données des titulaires de cartes, ainsi qu’un programme de conformité PCI DSS devant inclure :
- Une responsabilité globale pour le maintien de la conformité PCI DSS
- La définition d’une charte pour un programme de conformité PCI DSS et la communication avec la direction 

> [!NOTE]
> Cette condition sera considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle deviendra obligatoire.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients qui sont des fournisseurs de services doivent documenter leur programme de conformité PCI.|



## <a name="pci-dss-requirement-125"></a>Condition 12.5 de la norme PCI DSS

**12.5** Attribuer à un individu ou à une équipe les responsabilités suivantes de gestion de la sécurité des informations

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de définir et d’attribuer les responsabilités de sécurité des informations à leurs employés.|



### <a name="pci-dss-requirement-1251"></a>Condition 12.5.1 de la norme PCI DSS

**12.5.1** Définir, documenter et diffuser les stratégies et les procédures de sécurité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de définir et d’attribuer les responsabilités de sécurité des informations à leurs employés.|



### <a name="pci-dss-requirement-1252"></a>Condition 12.5.2 de la norme PCI DSS

**12.5.2** Contrôler et analyser les informations et les alertes de sécurité, et les diffuser au personnel compétent.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de définir et d’attribuer les responsabilités de sécurité des informations à leurs employés.|



### <a name="pci-dss-requirement-1253"></a>Condition 12.5.3 de la norme PCI DSS

**12.5.3** Définir, documenter et diffuser les procédures de remontée et de réponse aux incidents liés à la sécurité pour garantir une gestion rapide et efficace de toutes les situations

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1254"></a>Condition 12.5.4 de la norme PCI DSS

**12.5.4** Administrer les comptes d’utilisateur, notamment l’ajout, la suppression et la modification des comptes

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1255"></a>Condition 12.5.5 de la norme PCI DSS

**12.5.5** Surveiller et contrôler tous les accès aux données

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de la création et de la gestion de stratégies dictant l’utilisation, l’implémentation et l’authentification appropriées pour les technologies critiques présentes dans leur environnement de données de titulaire de carte.|



## <a name="pci-dss-requirement-126"></a>Condition 12.6 de la norme PCI DSS

**12.6** Mettre en œuvre un programme formel de sensibilisation à la sécurité pour sensibiliser les employés à l’importance de la sécurité des données du titulaire

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la création et de la gestion de stratégies concernant la sensibilisation à la sécurité pour le personnel ayant accès à l’environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1261"></a>Condition 12.6.1 de la norme PCI DSS

**12.6.1** Former le personnel au moment du recrutement et au moins une fois par an 

> [!NOTE]
> Les méthodes varient selon les postes occupés et le niveau d’accès du personnel aux données du titulaire.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent fournir à leur personnel une formation de sensibilisation à la sécurité des informations et à la conformité PCI-DSS, et ce, au moins une fois par an.|



### <a name="pci-dss-requirement-1262"></a>Condition 12.6.2 de la norme PCI DSS

**12.6.2** Exiger que le personnel reconnaisse au moins une fois par an avoir lu et compris les procédures et la stratégie de sécurité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent fournir à leur personnel une formation de sensibilisation à la sécurité des informations et à la conformité PCI-DSS, et ce, au moins une fois par an.|



## <a name="pci-dss-requirement-127"></a>Condition 12.7 de la norme PCI DSS

**12.7** Effectuer une sélection préalable à l’embauche du personnel pour minimiser les risques d’attaques par des sources internes (ces contrôles doivent inclure, par exemple, les antécédents professionnels, le casier judiciaire, les renseignements de solvabilité et la vérification des références). 

> [!NOTE]
> Pour le personnel dont l’embauche potentielle concerne des postes tels que celui de caissier dans un magasin, et qui n’a accès qu’à un numéro de carte à la fois, à l’occasion du traitement d’une transaction, cette condition n’est qu’une recommandation.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent garantir que le personnel ayant accès à l’environnement de données de titulaire de carte a fait l’objet d’une vérification d’antécédents approfondie.|



## <a name="pci-dss-requirement-128"></a>Condition 12.8 de la norme PCI DSS

**12.8** Maintenir et mettre en œuvre des stratégies et des procédures de gestion des fournisseurs de services avec lesquelles les données du titulaire sont partagées, ou qui pourraient affecter la sécurité des données du titulaire.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de surveiller la conformité PCI des fournisseurs de services avec lesquels sont partagées les données des titulaires de cartes ou qui pourraient porter atteinte à la sécurité de l’environnement de données de titulaire de carte. Les clients doivent établir une liste de tous les fournisseurs de services qui ont utilisé leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1281"></a>Condition 12.8.1 de la norme PCI DSS

**12.8.1** Établir la liste des fournisseurs de services (avec la description des services fournis).


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont chargés de surveiller la conformité PCI des fournisseurs de services avec lesquels sont partagées les données des titulaires de cartes ou qui pourraient porter atteinte à la sécurité de l’environnement de données de titulaire de carte. Les clients doivent établir une liste de tous les fournisseurs de services qui ont utilisé leur environnement de données de titulaire de carte.|



### <a name="pci-dss-requirement-1282"></a>Condition 12.8.2 de la norme PCI DSS

**12.8.2** Maintenir un accord écrit par lequel les fournisseurs de services reconnaissent qu’ils sont responsables de la sécurité des données de titulaire de carte qu’ils détiennent, stockent, traitent ou transmettent de la part du client, dans la mesure où ils pourraient avoir un impact sur la sécurité de l’environnement des données du titulaire de carte 

> [!NOTE]
> La formulation exacte de ce document dépendra de l’accord entre les deux parties, des détails du service fourni et des responsabilités attribuées à chaque partie. La reconnaissance n’a pas besoin d’inclure la formulation exacte précisée dans cette condition.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent maintenir des accords écrits avec les fournisseurs de services dans lesquels ils reconnaissent leur responsabilité quant à la gestion de la sécurité et des données des titulaires de cartes.|



### <a name="pci-dss-requirement-1283"></a>Condition 12.8.3 de la norme PCI DSS

**12.8.3** S’assurer que le processus de sélection des fournisseurs de services est bien défini, et qu’il inclut notamment des contrôles préalables à l’engagement

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent s’assurer que le processus de sélection des fournisseurs de services est bien défini, et qu’il inclut notamment des contrôles préalables à l’engagement|



### <a name="pci-dss-requirement-1284"></a>Condition 12.8.4 de la norme PCI DSS

**12.8.4** Maintenir un programme qui contrôle la conformité des fournisseurs de services à la norme PCI DSS au moins une fois par an.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent maintenir un programme qui contrôle la conformité des fournisseurs de services à la norme PCI DSS au moins une fois par an.|



### <a name="pci-dss-requirement-1285"></a>Condition 12.8.5 de la norme PCI DSS

**12.8.5** Maintenir les informations concernant les conditions de la norme PCI DSS qui sont gérées par chaque fournisseur de services et celles qui sont gérées par l’organisation.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients doivent conserver une copie de la [Matrice de responsabilités](https://aka.ms/pciblueprintcrm32), dans laquelle sont répertoriées les conditions de la norme PCI DSS qui sont sous la responsabilité du client, ainsi que celles qui sont sous la responsabilité de Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Condition 12.9 de la norme PCI DSS

**12.9** Une condition supplémentaire s’applique aux fournisseurs de services uniquement : les fournisseurs de services reconnaissent par écrit qu’ils sont responsables de la sécurité des données de titulaire de carte qu’ils détiennent, stockent, traitent ou transmettent de la part du client, dans la mesure où ils pourraient avoir un impact sur la sécurité de l’environnement de données de titulaire de carte. 

> [!NOTE]
> La formulation exacte de ce document dépendra de l’accord entre les deux parties, des détails du service fourni et des responsabilités attribuées à chaque partie. La reconnaissance n’a pas besoin d’inclure la formulation exacte précisée dans cette condition.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients qui sont des fournisseurs de services doivent reconnaître leurs responsabilités quant au maintien de la conformité PCI. |



## <a name="pci-dss-requirement-1210"></a>Condition 12.10 de la norme PCI DSS

**12.10** Mettre en œuvre un plan de réponse aux incidents. Être prêt à réagir immédiatement à toute intrusion dans le système

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12101"></a>Condition 12.10.1 de la norme PCI DSS

**12.10.1** Élaborer le plan de réponse aux incidents à mettre en place en cas d’intrusion dans le système. S’assurer que le plan prévoit au moins les points suivants :
- Rôles, responsabilités et stratégies de communication et de contact en cas d’incident, notamment la notification des sociétés de cartes de paiement, au minimum
- Les procédures de réponse aux incidents spécifiques
- Les procédures de continuité et de reprise de l’activité
- Processus de sauvegarde des données
- Analyse des exigences légales concernant le signalement des incidents
- Couverture et réponses de tous les composants stratégiques du système
- Référence ou inclusion des procédures de réponse aux incidents des sociétés de cartes de paiement

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12102"></a>Condition 12.10.2 de la norme PCI DSS

**12.10.2** Évaluer et tester le plan, y compris tous les éléments de la condition 12.10.1, au moins une fois par an


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12103"></a>Condition 12.10.3 de la norme PCI DSS

**12.10.3** Désigner le personnel spécifique disponible 24 heures sur 24, et 7 jours sur 7, pour répondre aux alertes

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12104"></a>Condition 12.10.4 de la norme PCI DSS

**12.10.4** Organiser la formation appropriée du personnel responsable de la réponse aux violations de sécurité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12105"></a>Condition 12.10.5 de la norme PCI DSS

**12.10.5** Inclure les alertes des systèmes de surveillance de sécurité, notamment les systèmes d’intrusion-détection, intrusion-prévention, les pare-feu et les systèmes de surveillance de l’intégrité des fichiers

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



### <a name="pci-dss-requirement-12106"></a>Condition 12.10.6 de la norme PCI DSS

**12.10.6** Définir un processus de modification et de développement du plan de réponse aux incidents en fonction des leçons apprises, et tenir compte de l’évolution du secteur

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la mise au point de plans de réponse aux incidents et de tests qui prennent en compte tous les contrôles clients relatifs aux points de contact partagés et toutes les applications clients qui utilisent l’infrastructure Azure. Il est de la responsabilité du client de fournir des informations de contact correctes à Azure lorsqu’il doit signaler un incident pouvant avoir un impact sur son application ou ses données.|



## <a name="pci-dss-requirement-1211"></a>Condition 12.11 de la norme PCI DSS

**12.11** **Une condition supplémentaire s’applique aux fournisseurs de services uniquement :** effectuer des évaluations au minimum une fois par trimestre, pour vérifier que le personnel respecte les stratégies de sécurité et les procédures opérationnelles.
Les évaluations doivent comprendre les processus suivants :
- Examen quotidien des journaux
- Évaluation de l’ensemble de règles du pare-feu
- Application des normes de configuration aux nouveaux systèmes
- Répondre à des alertes de sécurité
- Processus de gestion des changements 

> [!NOTE]
> Cette condition sera considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle deviendra obligatoire.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients qui sont des fournisseurs de services doivent documenter leurs évaluations des processus afin de maintenir la conformité PCI.|



### <a name="pci-dss-requirement-12111"></a>Condition 12.11.1 de la norme PCI DSS

**12.11.1** Une condition supplémentaire s’applique aux fournisseurs de services uniquement : documenter les processus d’évaluations trimestrielles, y compris :
- La documentation des résultats des évaluations
- La vérification et l’approbation des résultats par le personnel chargé du programme de conformité PCI DSS 

> [!NOTE]
> Cette condition sera considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle deviendra obligatoire.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients qui sont des fournisseurs de services doivent documenter leurs évaluations des processus afin de maintenir la conformité PCI.|




