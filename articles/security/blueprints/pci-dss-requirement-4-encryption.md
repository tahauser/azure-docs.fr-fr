---
title: Plan de traitement des paiements Azure - Exigences de chiffrement
description: Condition 4 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Exigences de chiffrement pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-4"></a>Condition 4 de la norme PCI DSS

**Chiffrer la transmission des données de titulaires de carte sur les réseaux ouverts publics**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour obtenir des informations sur les procédures de test et des instructions pour chacune des conditions, reportez-vous à la documentation de la norme PCI DSS.

Les informations sensibles doivent être chiffrées pendant leur transmission sur des réseaux accessibles à des individus malveillants. Les réseaux sans fil mal configurés et les vulnérabilités dans les protocoles traditionnels de chiffrement et d’authentification sont les cibles permanentes des individus malveillants qui profitent de ces faiblesses pour obtenir un accès privilégié aux environnements des données de titulaires de carte.

## <a name="pci-dss-requirement-41"></a>Condition 4.1 de la norme PCI DSS

**4.1** Utiliser un chiffrement robuste et des protocoles de sécurité (par exemple, TLS, IPSEC ou SSH) afin de protéger les données de titulaires de carte sensibles durant leur transmission sur des réseaux publics ouverts, y compris :
- Seuls des clés et certificats approuvés sont acceptés.
- Le protocole utilisé prend uniquement en charge les versions ou configurations sécurisées.
- La force du chiffrement est appropriée pour la méthodologie de chiffrement employée. 

> [!NOTE]
> Les conditions dans l’annexe A2 doivent être remplies avec l’utilisation du SSL/TLS initial.
>
> Voici quelques exemples, parmi d’autres, de réseaux publics ouverts :
> - Internet
> - Technologies sans fil, y compris 802.11 et Bluetooth
> - Les technologies cellulaires, par exemple, Système global pour les communications mobiles (GSM), Accès multiple de division de code (CDMA)
> - GPRS (service général de radiocommunication en mode paquet)
> - Communications par satellite


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le Contoso Webstore est une solution PaaS qui implémente un chiffrement renforcé pour le déploiement comme suit :<br /><br />Pour répondre aux exigences du chiffrement des données au repos, [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise les éléments suivants :<br /><br /><ul><li>[Chiffrement du service de stockage (SSE) Azure pour les données au repos](/azure/storage/storage-service-encryption)</li><li>SQL Database : une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Guide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).</li><li>[Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Utiliser Azure Key Vault permet un alignement sur les spécifications Microsoft Azure Government, PCI DSS et HIPAA.|



### <a name="pci-dss-requirement-411"></a>Condition 4.1.1 de la norme PCI DSS

**4.1.1** S’assurer que les réseaux sans fil, sur lesquels sont transmises les données de titulaires de carte ou qui sont connectés à l’environnement des données de titulaires de carte, utilisent les bonnes pratiques du secteur (par exemple, IEEE 802.11i) pour appliquer un chiffrement robuste pour l’authentification et la transmission.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les modes sans fil et SNMP ne sont pas implémentés dans la solution.|



## <a name="pci-dss-requirement-42"></a>Condition 4.2 de la norme PCI DSS

**4.2** Ne jamais envoyer de PAN non protégés à l’aide de technologies de messagerie pour les utilisateurs finaux (par exemple e-mail, messagerie instantanée, SMS, chat, etc.).

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Aucune solution de messagerie susceptible d’envoyer des données de numéro de compte principal non protégées n’est implémentée dans le Contoso Webstore.|



## <a name="pci-dss-requirement-43"></a>Condition 4.3 de la norme PCI DSS

**4.3** Assurer que les politiques de sécurité et les procédures opérationnelles pour le chiffrement des données de titulaires de carte sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Les clients sont responsables de la documentation et du chiffrement des transmissions contenant des données de titulaire de carte.|




