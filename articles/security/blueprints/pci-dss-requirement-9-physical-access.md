---
title: "Plan de traitement des paiements Azure - Conditions d’accès physique"
description: Condition 9 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Conditions d’accès physique pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-9"></a>Condition 9 de la norme PCI DSS

**Limiter l’accès physique aux données des titulaires de cartes**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour obtenir des informations sur les procédures de test et des instructions pour chacune des conditions, reportez-vous à la documentation de la norme PCI DSS.

Dans la mesure où tout accès physique à des données ou à des systèmes hébergeant des données de titulaires de carte permet à des individus d’accéder à des périphériques ou à des informations, et de supprimer des systèmes ou des copies papier, cet accès doit être restreint de façon appropriée. Dans le cadre de la condition 9, le terme « personnel du site » désigne les employés à temps plein et à temps partiel, les intérimaires ainsi que les sous-traitants et les consultants qui « résident » sur le site de l’entité. Un « visiteur » est défini comme un fournisseur, l’hôte du personnel du site, le personnel de service ou tout individu présent au sein des locaux pendant une période courte, n’excédant généralement pas une journée. « Support » se rapporte à tout support papier ou électronique contenant des données de titulaires de carte.

## <a name="pci-dss-requirement-91"></a>Condition 9.1 de la norme PCI DSS

**9.1** Utiliser des contrôles d’accès aux installations appropriés pour restreindre et surveiller l’accès physique aux systèmes installés dans l’environnement des données de titulaires de carte.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure est responsable de l’implémentation, de l’application et de la surveillance de la sécurité de l’accès physique pour les centres de données. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-911"></a>Condition 9.1.1 de la norme PCI DSS

**9.1.1** Installer des caméras vidéo et/ou des mécanismes de contrôle d’accès pour contrôler l’accès physique des individus aux zones sensibles. Examiner les données enregistrées et les mettre en corrélation avec d’autres informations. Les conserver pendant trois mois au minimum, sauf stipulation contraire de la loi.

> [!NOTE]
> Par « zones sensibles », nous entendons tout centre de données, salle de serveur ou zone abritant des systèmes qui stockent, traitent ou transmettent des données de titulaires de carte. Cette définition exclut les zones face au public où seuls les terminaux de point de vente sont présents, tels que les zones de caisse dans un magasin.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure est responsable de l’implémentation, de l’application et de la surveillance des mécanismes de contrôle d’accès vidéo et biométriques pour les centres de données. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-912"></a>Condition 9.1.2 de la norme PCI DSS

**9.1.2** Mettre en œuvre des contrôles physiques et/ou logiques pour restreindre l’accès physique aux prises réseau accessibles au public. 

Par exemple, les prises de réseau situées dans les zones publiques et les zones accessibles aux visiteurs doivent être désactivées et uniquement activées quand l’accès au réseau est autorisé de manière explicite. Autrement, des processus doivent être mis en œuvre pour assurer que les visiteurs sont accompagnés à tout moment dans les zones contenant des prises réseau actives.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Il n’y a aucune prise réseau accessible au public au sein de la plateforme Microsoft Azure. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-913"></a>Condition 9.1.3 de la norme PCI DSS

**9.1.3** Restreindre l’accès physique aux points d’accès, passerelles, dispositifs portables, matériel réseau/communications et lignes de télécommunication sans fil.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | L’accès physique au matériel réseau Microsoft Azure est étroitement contrôlé par des listes d’accès, plusieurs méthodes d’authentification, des obstacles physiques à l’entrée, et l’accès à l’équipement doit répondre à un besoin de l’entreprise et être justifié. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



## <a name="pci-dss-requirement-92"></a>Condition 9.2 de la norme PCI DSS

**9.2** Développer des procédures pour distinguer facilement le personnel du site des visiteurs, notamment :
- Identification du personnel sur le site ou des visiteurs (en assignant des badges, par exemple)
- Changement des conditions d’accès
- Révocation ou élimination de l’identification du personnel du site et des visiteurs quand elle est arrivée à expiration (telle que les badges d’identification)

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure est responsable de l’implémentation, de l’application et de la surveillance de la sécurité de l’accès physique, ainsi que de l’identification des employés et des sous-traitants quand ils visitent les centres de données. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



## <a name="pci-dss-requirement-93"></a>Condition 9.3 de la norme PCI DSS

**9.3** Contrôler l’accès physique du personnel du site aux zones sensibles comme suit :
- L’accès doit être autorisé et basé sur les fonctions professionnelles individuelles.
- L’accès est immédiatement révoqué à la cessation de fonction et tous les mécanismes d’accès physique, tels que les clés, cartes d’accès, etc., sont rendus ou désactivés.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les autorisations d’accès aux centres de données de Microsoft sont contrôlées à l’aide d’une liste d’accès autorisé approuvée par l’équipe en charge des centres de données selon le principe des privilèges minimum. La liste de contrôle d’accès est examinée, vérifiée et mise à jour trimestriellement.<br /><br />Les centres de données Microsoft Azure utilisent des appareils d’accès physiques tels que les passerelles de périmètre, les lecteurs de badge d’accès électronique, les lecteurs biométriques, les sas de sécurité/portiques de sécurité et les dispositifs anti-retour. Les dispositifs de badge d’accès sont surveillés en continu. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



## <a name="pci-dss-requirement-94"></a>Condition 9.4 de la norme PCI DSS

**9.4** Mettre en œuvre des procédures pour identifier et autoriser les visiteurs. Les procédures doivent inclure les points suivants.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure s’assure que les livraisons préalablement approuvées sont reçues dans une aire de chargement sécurisée qui est physiquement isolée des installations de traitement des informations et qu’elles sont contrôlées par un personnel autorisé. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-941"></a>Condition 9.4.1 de la norme PCI DSS

**9.4.1** Les visiteurs sont autorisés avant d’entrer et accompagnés en permanence dans les zones où sont traitées et conservées les données de titulaires de carte.


**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure s’assure que les livraisons préalablement approuvées sont reçues dans une aire de chargement sécurisée qui est physiquement isolée des installations de traitement des informations et qu’elles sont contrôlées par un personnel autorisé. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-942"></a>Condition 9.4.2 de la norme PCI DSS

**9.4.2** Les visiteurs sont identifiés et un badge ou autre forme d’identification leur est remis avec une date limite d’utilisation, qui distingue clairement les visiteurs du personnel du site.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | L’accès aux centres de données Microsoft doit être préalablement approuvé, et les visiteurs autorisés doivent passer par un dispositif de sécurité physique au point d’arrivée et fournir une pièce d’identité valide avant l’entrée. Les badges identifient clairement les employés. Les sous-traitants et les visiteurs reçoivent des badges temporaires qu’ils doivent restituer quand ils quittent les locaux. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-943"></a>Condition 9.4.3 de la norme PCI DSS

**9.4.3** Les visiteurs doivent rendre le badge ou autre forme d’identification physique avant de quitter les locaux ou à la date d’expiration.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les visiteurs doivent rendre les badges quand ils quittent un local Microsoft. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-944"></a>Condition 9.4.4 de la norme PCI DSS

**9.4.4** Un registre des visites est utilisé pour maintenir un suivi d’audit de l’activité des visiteurs aux locaux ainsi qu’aux salles informatiques et aux centres de données où sont stockées ou transmises les données de titulaires de carte.
Y consigner le nom du visiteur, l’entreprise qu’il représente et le personnel du site qui autorise son accès physique.
Conserver ce registre pendant trois mois au minimum, sauf stipulation contraire de la loi.

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure se charge de tenir à jour un registre des visites en guise de suivi d’audit de l’activité des visiteurs aux locaux ainsi qu’aux salles informatiques et aux centres de données où sont stockées ou transmises les données de titulaires de carte. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



## <a name="pci-dss-requirement-95"></a>Condition 9.5 de la norme PCI DSS

**9.5** Assurer la sécurité physique de tous les supports.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>Condition 9.5.1 de la norme PCI DSS

**9.5.1** Ranger les sauvegardes sur support en lieu sûr, de préférence hors des locaux de l’installation, par exemple sur un autre site ou un site de secours, ou encore un site de stockage commercial. Inspecter la sécurité du site au moins une fois par an.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>Condition 9.6 de la norme PCI DSS

**9.6** Assurer un contrôle strict de la distribution interne ou externe de tout type de support, notamment ce qui suit :

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>Condition 9.6.1 de la norme PCI DSS

**9.6.1** Classer les supports afin de déterminer la sensibilité des données qu’ils contiennent.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>Condition 9.6.2 de la norme PCI DSS

**9.6.2** Envoyer les supports par coursier sécurisé ou toute autre méthode d’expédition qui peut faire l’objet d’un suivi précis.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>Condition 9.6.3 de la norme PCI DSS

**9.6.3** Assurer que les responsables approuvent le déplacement de tout support déplacé d’une zone sécurisée (en particulier s’ils sont distribués à des individus).

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>Condition 9.7 de la norme PCI DSS

**9.7** Assurer un contrôle strict du stockage et de l’accessibilité des supports.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>Condition 9.7.1 de la norme PCI DSS

**9.7.1** Tenir de manière appropriée les journaux d’inventaire de tous les supports et effectuer un inventaire des supports au moins une fois par an.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>Condition 9.8 de la norme PCI DSS

**9.8** Détruire les supports quand ils ne sont plus nécessaires à des fins professionnelles ou légales comme suit :

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>Condition 9.8.1 de la norme PCI DSS

**9.8.1** Déchiqueter, brûler ou réduire en pâte les documents papier de sorte que les données de titulaires de carte ne puissent pas être reconstituées. Sécuriser les conteneurs de stockage utilisés pour les documents qui doivent être détruits.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore stocke toutes les données dans Azure SQL Database. Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>Condition 9.8.2 de la norme PCI DSS

**9.8.2** Rendre les données de titulaires de carte sur support électronique irrécupérables de sorte que les informations ne puissent pas être reconstituées.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les techniques de destruction de données varient selon le type d’objet de données détruit, qu’il s’agisse d’abonnements, de stockage, de machines virtuelles ou de bases de données. Dans l’environnement multilocataire Microsoft Azure, une attention particulière est accordée pour que les données d’un client ne puissent pas « fuir » dans les données d’un autre client, ou si un client supprime des données, qu’aucun autre client (y compris, dans la plupart des cas, le client qui a détenu les données) ne puisse accéder à ces données supprimées.<br /><br />Microsoft Azure suit les recommandations NIST 800-88 sur le nettoyage des supports, qui concernent la préoccupation principale de s’assurer que les données ne sont pas libérées par inadvertance. Ces recommandations englobent le nettoyage électronique et physique. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Vous pouvez supprimer le Contoso Webstore entièrement en supprimant le groupe de ressources utilisé pendant le déploiement.|



## <a name="pci-dss-requirement-99"></a>Condition 9.9 de la norme PCI DSS

**9.9** Protéger les dispositifs qui capturent les données de carte de paiement par interaction physique directe avec la carte des manipulations malveillantes et des substitutions.

> [!NOTE]
> Ces conditions s’appliquent aux dispositifs de lecture de carte utilisés dans les transactions pour lesquelles la carte est présente (c’est-à-dire, une lecture de piste ou de puce) au point de vente. Cette condition n’est pas destinée à être appliquée pour les composants d’entrée manuelle à touches tels que les claviers d’ordinateur et les claviers de PDV. 

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore utilise OMS pour journaliser tous les changements système.<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) fournit une journalisation complète des changements. Vous pouvez passer en revue les changements et vérifier leur exactitude. Pour obtenir des instructions spécifiques, consultez [Guide PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>Condition 9.9.1 de la norme PCI DSS

**9.9.1** Maintenir une liste d’appareils à jour. La liste doit inclure les points suivants :
- Marque et modèle de l’appareil
- Emplacement de l’appareil (par exemple, l’adresse du site ou de l’installation où se trouve l’appareil)
- Numéro de série de l’appareil ou autre méthode d’identification unique

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore fournit une architecture de référence et une liste de tous les services utilisés dans la documentation de son déploiement.|



### <a name="pci-dss-requirement-992"></a>Condition 9.9.2 de la norme PCI DSS

**9.9.2** Inspecter régulièrement la surface des appareils pour voir si elle présente des signes de manipulations malveillantes (par exemple, l’ajout de copieur de carte sur l’appareil), ou de substitution (par exemple, en inspectant le numéro de série ou autre caractéristique de l’appareil pour vérifier qu’il n’a pas été substitué par un appareil frauduleux).

> [!NOTE]
> Les exemples de signes qu’un appareil aurait pu être la victime de manipulations malveillantes ou de substitutions comprennent les fixations de câble ou de dispositifs inattendus à l’appareil, les étiquettes de sécurité manquantes ou modifiées, un boîtier cassé ou de couleur différente, ou un changement du numéro de série ou autres marques externes.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-993"></a>Condition 9.9.3 de la norme PCI DSS

**9.9.3** Assurer la formation du personnel afin qu’il soit conscient des tentatives de manipulation malveillantes ou de remplacement des appareils. La formation doit inclure les points suivants :
- Vérifier l’identité de tout tiers prétendant faire partie du personnel de maintenance ou de réparation, avant de lui accorder l’accès pour modifier ou dépanner les appareils.
- Ne pas installer, remplacer ou renvoyer l’appareil sans vérification.
- Être conscient des comportements suspects autour des appareils (par exemple, les tentatives de débrancher ou d’ouvrir les appareils par des personnes inconnues).
- Signaler les comportements suspects et les indications de manipulation malveillante ou de substitution de l’appareil au personnel approprié (par exemple, à un responsable ou à un agent de la sécurité).

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



## <a name="pci-dss-requirement-910"></a>Condition 9.10 de la norme PCI DSS

**9.10** Assurer que les politiques de sécurité et les procédures opérationnelles pour la restriction de l’accès aux données de titulaires de carte sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|




