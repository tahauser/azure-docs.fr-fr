---
title: "Plan de traitement des paiements Azure - Conditions relatives aux données de titulaires de carte"
description: Condition 3 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives aux données de titulaires de carte pour les environnements conformes à la norme PCI DSS
## <a name="pci-dss-requirement-3"></a>Condition 3 de la norme PCI DSS

**Protéger les données de titulaires de carte stockées**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Les méthodes de protection, comme le chiffrement, la troncation, le masquage et le hachage, sont des composants stratégiques de la protection des données de titulaires de carte. Si un intrus parvient à contourner les autres contrôles de sécurité et à accéder aux données chiffrées, il ne pourra pas les lire ni les utiliser s’il n’a pas les clés de chiffrement appropriées. D’autres méthodes efficaces de protection des données stockées doivent aussi être envisagées pour potentiellement limiter les risques. Par exemple, éviter de stocker les données de titulaires de carte à moins que cela ne soit absolument nécessaire, tronquer les données de titulaires de carte si un PAN complet n’est pas nécessaire et éviter d’envoyer un PAN non protégé au moyen de technologies de messagerie pour utilisateurs finaux, comme les e-mails ou les messages instantanés.
Pour obtenir la définition d’un « chiffrement fort » et d’autres termes relatifs à PCI DSS, consulter le Glossaire des termes, abréviations et acronymes PCI DSS.

## <a name="pci-dss-requirement-31"></a>Condition 3.1 de la norme PCI DSS

**3.1** Garder le stockage de données de titulaires de carte à un niveau minimum en appliquant des stratégies, procédures et processus de conservation et d’élimination des données, qui comprennent au moins les mesures suivantes pour le stockage des données de titulaires de carte (CHD) :
- Limiter la quantité des données stockées et les délais de conservation selon les conditions imposées par les exigences légales, réglementaires et/ou commerciales
- Des conditions de conservation spécifiques pour les données de titulaires de carte
- Des processus pour la suppression sécurisée des données devenues inutiles
- Un processus trimestriel pour l’identification et la suppression sécurisée des données de titulaires de carte stockées excédant les conditions de conservation définies.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Azure doit faire en sorte que les données des clients marquées pour suppression sont mises hors service en toute sécurité au moyen de protocoles conformes à la norme NIST 800-88 (spécifiés dans les stratégies relatives à la mise hors service sécurisée). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ne supprime pas et ne détruit pas les données CHD stockées. Toutefois, toutes les données sont chiffrées et les données associées au numéro de compte principal (PAN) ne sont pas stockées.<br /><br />|



## <a name="pci-dss-requirement-32"></a>Condition 3.2 de la norme PCI DSS

**3.2** Ne stocker aucune donnée d’identification sensible après autorisation (même chiffrée). Si des données d’identification sensibles sont reçues, rendre toutes les données irrécupérables à la fin du processus d’autorisation. 
- une justification commerciale existe ; 
- les données sont stockées de manière sécurisée.
Les données d’identification sensibles sont mentionnées dans les conditions 3.2.1 à 3.2.3 suivantes :


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ne supprime pas et ne détruit pas les données CHD stockées ; les exemples de données sont stockées uniquement à des fins de démonstration. Toutefois, toutes les données sont chiffrées et les données associées au numéro de compte principal (PAN) ne sont pas stockées.<br /><br />|



### <a name="pci-dss-requirement-321"></a>Condition 3.2.1 de la norme PCI DSS

**3.2.1** Ne pas stocker la totalité du contenu d’une quelconque piste (sur la bande magnétique au verso d’une carte, sur une puce ou ailleurs) après l’autorisation. Ces données sont également désignées comme suit : piste complète, piste, piste 1, piste 2 et données de bande magnétique. 

> [!NOTE]
> Dans le cadre normal de l’activité, il est parfois nécessaire de conserver les éléments de données de la bande magnétique suivants : 
> - Nom du titulaire de la carte 
> - Numéro de compte principal (PAN) 
> - Date d'expiration 
> - Code de service 
>
> Pour minimiser le risque, stocker uniquement les éléments de données nécessaires à l’activité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ne stocke pas le contenu complet des données CHD.|



### <a name="pci-dss-requirement-322"></a>Condition 3.2.2 de la norme PCI DSS

**3.2.2** Ne pas stocker le code ou la valeur de vérification de carte (nombre à trois ou quatre chiffres figurant au recto ou au verso de la carte de paiement, utilisé pour vérifier les transactions carte absente) après l’autorisation.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore chiffre toutes les données, notamment les exemples de code CVV.|



### <a name="pci-dss-requirement-323"></a>Condition 3.2.3 de la norme PCI DSS

**3.2.3** Ne pas stocker le code PIN ou le bloc PIN chiffré après l’autorisation.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ne stocke pas les informations des codes PIN.|



## <a name="pci-dss-requirement-33"></a>Condition 3.3 de la norme PCI DSS

**3.3** Masquer le PAN quand il s’affiche (les six premiers chiffres et les quatre derniers sont le maximum de chiffres affichés), de manière à ce que seul le personnel, dont le besoin commercial est légitime, puisse voir le PAN dans sa totalité. 

> [!NOTE]
> Cette condition ne se substitue pas aux conditions plus strictes qui sont en place et qui régissent l’affichage des données de titulaires de carte, par exemple, pour les reçus des points de vente.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fait appel à plusieurs technologies pour masquer le numéro de compte principal (PAN) : chiffrement TDE (Transparent Data Encryption), colonnes Always Encrypted et masquage DDM (Dynamic Data Masking). Pour plus d’informations, consultez [Aide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>Condition 3.4 de la norme PCI DSS

**3.4** Rendre le PAN illisible où qu’il soit stocké (notamment les données sur support numérique portable, support de sauvegarde et journaux), en utilisant l’une des approches suivantes :
- Hachage unilatéral s’appuyant sur un chiffrement fort (la totalité du PAN doit être hachée)
- Troncation (le hachage ne peut pas être utilisé pour remplacer le segment tronqué du PAN)
- Jetons et compléments d’index (les compléments doivent être stockés de manière sécurisée)
- Chiffrement fort associée aux processus et procédures de gestion des clés 

> [!NOTE]
> Il s’agit d’un effort relativement peu important pour un individu malveillant de reconstruire les données du PAN d’origine, s’il a à la fois accès à la version tronquée et à la version hachée d’un PAN. Quand la version tronquée et la version hachée du même PAN sont présentes dans l’environnement d’une entité, des contrôles supplémentaires doivent être en place pour garantir qu’elle ne puissent pas être mises en corrélation pour reconstituer le PAN d’origine.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore chiffre toutes les données de carte de paiement et utilise Azure Key Vault pour gérer les clés, ce qui empêche la récupération des données de titulaires de carte.<br /><br />Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>Condition 3.4.1 de la norme PCI DSS

**3.4.1** Si un chiffrement de disque est utilisé (au lieu d’un chiffrement de base de données au niveau fichier ou colonne), l’accès logique doit être géré séparément et indépendamment des mécanismes de contrôle d’accès au système d’exploitation natif (par exemple, en n’utilisant pas des bases de données de comptes d’utilisateur locales, ou des informations d’identification génériques de connexion au réseau). Les clés de déchiffrement ne doivent pas être associées à des comptes d’utilisateur. 

> [!NOTE]
> En outre, cette condition s’applique à toutes les autres conditions de gestion des clés et de chiffrement PCI DSS.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore chiffre toutes les données stockées et isole le trafic pour éviter toute élévation de privilège des fonctions DevOps.<br /><br />Étant donné que l’environnement App Service est sécurisé et verrouillé, il est nécessaire d’avoir un mécanisme qui permet d’autoriser toutes les versions de DevOps et tout changement nécessaire, par exemple la capacité à surveiller une application web à l’aide de Kudu.<br /><br />Une machine virtuelle est établie en tant que serveur de rebond (hôte bastion) avec les configurations suivantes :<br /><br /><ul><li>[Extension Antimalware](/azure/security/azure-security-antimalware)</li><li>[Extension Monitoring OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Extension VM Diagnostics](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Disque chiffré BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>L’utilisation d’Azure Key Vault permet un alignement avec les conditions définies par Azure Government, PCI DSS et HIPAA.|



## <a name="pci-dss-requirement-35"></a>Condition 3.5 de la norme PCI DSS

**3.5** Documenter et implémenter des procédures pour protéger les clés utilisées pour sécuriser le données de titulaires de carte stockées contre la divulgation et l’utilisation abusive. 

> [!NOTE]
> Cette condition s’applique également aux clés utilisées pour chiffrer les données de titulaires de carte stockées, ainsi qu’aux clés de chiffrement de clé utilisées pour protéger les clés de chiffrement de données (ces clés de chiffrement de clé doivent offrir une protection aussi forte que la clé de chiffrement de données).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Microsoft Azure garantit l’isolation logique des coffres de clés des clients : d’une part, les uns des autres et, d’autre part, par rapport au plan de gestion du service Key Vault. Key Vault est conçu de telle sorte que Microsoft ne dispose pas d’un accès permanent au coffre de clés du client. <br /><br />Les clés sont protégées par Microsoft Azure au moyen d’algorithmes standard, de longueurs de clé et de modules de sécurité matériels (HSM).<br /><br />Une clé stockée dans Microsoft Azure Key Vault peut être utilisée pour protéger une autre clé. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit une documentation pour illustrer et faciliter le déploiement d’une solution de clés protégée afin de sécuriser les données CHD de démonstration.|



### <a name="pci-dss-requirement-351"></a>Condition 3.5.1 de la norme PCI DSS

**3.5.1** *Conditions supplémentaires pour les prestataires de services uniquement :* Conserver une description documentée de l’architecture de chiffrement qui comprend ce qui suit :
- Détails de tous les algorithmes, protocoles et clés utilisés pour protéger les données de titulaires de carte, y compris la robustesse des clés et la date d’expiration
- Description de l’utilisation de chaque clé
- Inventaire des HSM et autres SCD dans le cadre de la gestion des clés 

> [!NOTE]
> Cette condition est considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle sera obligatoire.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Microsoft Azure garantit l’isolation logique des coffres de clés des clients : d’une part, les uns des autres et, d’autre part, par rapport au plan de gestion du service Key Vault. Key Vault est conçu de telle sorte que Microsoft ne dispose pas d’un accès permanent au coffre de clés du client. <br /><br />Les clés sont protégées par Microsoft Azure au moyen d’algorithmes standard, de longueurs de clé et de modules de sécurité matériels (HSM).<br /><br />Une clé stockée dans Microsoft Azure Key Vault peut être utilisée pour protéger une autre clé. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit une documentation pour illustrer et faciliter le déploiement d’une solution de clés protégée afin de sécuriser les données CHD de démonstration.|



### <a name="pci-dss-requirement-352"></a>Condition 3.5.2 de la norme PCI DSS

**3.5.2** Restreindre l’accès aux clés de chiffrement au plus petit nombre d’opérateurs possible.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Key Vault prend en charge des stratégies d’accès détaillées. Ainsi, un propriétaire Key Vault peut accorder l’accès à des fonctionnalités spécifiques pour réaliser des opérations spécifiques sur des entités spécifiques. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La gestion des clés dans Contoso Webstore est isolée sur un compte d’utilisateur (admin##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Condition 3.5.3 de la norme PCI DSS

**3.5.3** Stocker les clés secrètes et privées utilisées pour chiffrer/déchiffrer les données de titulaires de carte sous l’une (ou sous plusieurs) des formes suivantes à tout moment :
- Chiffrées avec une clé de chiffrement de clé qui offre une protection au moins aussi forte que la clé de chiffrement de données et qui est stockée séparément de la clé de chiffrement de données
- Au sein d’un périphérique de chiffrement sécurisé (comme un module de sécurité matériel [hôte] [HSM] ou un périphérique de point d’interaction PTS approuvé)
- En tant que deux composants de clé ou partages de clé de pleine longueur au moins, conformément à la méthode acceptée par l’industrie 

> [!NOTE]
> Il n’est pas nécessaire que les clés publiques soient stockées sous l’une de ces formes.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Les clés sont stockées dans des coffres de clés spécifiques identifiés par le client.<br /><br />Key Vault est accessible globalement et simultanément par plusieurs applications, ce qui réduit la nécessité de copier une clé et de la stocker à plusieurs emplacements. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>Condition 3.5.4 de la norme PCI DSS

**3.5.4** Stocker les clés de chiffrement dans aussi peu d’emplacements que possible.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Les clés sont stockées dans des coffres de clés spécifiques identifiés par le client. <br /><br />Key Vault est accessible globalement et simultanément par plusieurs applications, ce qui réduit la nécessité de copier une clé et de la stocker à plusieurs emplacements. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>Condition 3.6 de la norme PCI DSS

**3.6** Documenter en détail et déployer les processus et les procédures de gestion des clés de chiffrement servant au chiffrement des données de titulaires de carte, notamment ce qui suit. 

> [!NOTE]
> Diverses ressources du secteur proposent des normes pour la gestion des clés, notamment NIST, que vous pouvez trouver à l’adresse suivante : http://csrc.nist.gov.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>Condition 3.6.1 de la norme PCI DSS

**3.6.1** Génération de clés de chiffrement fort

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :** <br /><br />Azure vérifie que les clés générées dans Key Vault respectent les spécifications du client. Les clés sont générées à l’aide d’un HSM. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>Condition 3.6.2 de la norme PCI DSS

**3.6.2** Sécuriser la distribution des clés de chiffrement

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />L’outil BYOK (Bring Your Own Key) encapsule la clé du client et cible un coffre de sécurité spécifique lié à un abonnement Azure spécifique. La clé peut uniquement être importée dans le coffre de clés de l’abonnement défini, dans la région spécifiée. Ce processus utilise les procédures de chiffrement fournies par le fabricant de matériel. Les clients sont notifiés de la réussite du transfert. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>Condition 3.6.3 de la norme PCI DSS

**3.6.3** Sécuriser le stockage des clés de chiffrement

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Les clés sont stockées dans les HSM et sont sécurisées à l’aide du chiffrement du fabricant de matériel. Les métadonnées sur les clés sont stockées dans Stockage Azure dans un état chiffré qui est unique à chaque coffre de clés. <br /><br /> |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>Condition 3.6.4 de la norme PCI DSS

**3.6.4** Changements de clé de chiffrement pour les clés ayant atteint la fin de leur période de chiffrement (par exemple, après la fin d’une période définie et/ou après la production d’une certaine quantité de textes de chiffrement par une clé donnée), comme l’a défini le fournisseur de l’application associée ou le propriétaire de la clé, et selon les bonnes pratiques et directives du secteur (par exemple, la publication spéciale NIST 800-57).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Key Vault prend en charge la mise à jour ou la régénération des clés, ces fonctionnalités étant définies par le client. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>Condition 3.6.5 de la norme PCI DSS

**3.6.5** Mise hors service ou remplacement des clés (par exemple, en les archivant, détruisant et/ou en les révoquant), si nécessaire quand le degré d’intégrité d’une clé est affaibli (par exemple, départ d’un employé ayant connaissance du texte clair d’une clé) ou quand des clés sont suspectées d’avoir été compromises. 

> [!NOTE]
> Si les clés de chiffrement mises hors service ou remplacées doivent être conservées, ces clés doivent être archivées de manière sécurisée (par exemple, en utilisant une clé de chiffrement de clé). Les clés de chiffrement archivées doivent être utilisées uniquement à des fins de déchiffrement ou de vérification.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Key Vault prend en charge la mise hors service ou le remplacement des clés, ces fonctionnalités étant définies par le client. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>Condition 3.6.6 de la norme PCI DSS

**3.6.6** Si des opérations de gestion manuelle de clés de chiffrement en texte clair sont utilisées, elles doivent être gérées par le fractionnement des connaissances et un double contrôle. 

> [!NOTE]
> La génération, la transmission, le chargement, le stockage et la destruction de clés sont quelques-uns des exemples d’interventions de gestion manuelle des clés.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>Condition 3.6.7 de la norme PCI DSS

**3.6.7** Prévention de la substitution non autorisée des clés de chiffrement.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | **Pour les clients utilisant Key Vault :**<br /><br />Les coffres de clé sont séparés de manière logique et ne prennent pas en charge les autorisations entre annuaires. Ceci permet d’éviter les substitutions non autorisées. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>Condition 3.6.8 de la norme PCI DSS

**3.6.8** Condition selon laquelle les opérateurs chargés de la gestion de clés de chiffrement reconnaissent formellement qu’ils comprennent et acceptent leurs responsabilités en tant que telles.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La gestion des clés dans Contoso Webstore est isolée sur un compte d’utilisateur (admin##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Condition 3.7 de la norme PCI DSS

**3.7** Assurer que les stratégies de sécurité et les procédures opérationnelles pour la protection des données de titulaires de carte sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise Azure Key Vault pour l’ensemble des opérations de gestion des clés. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|




