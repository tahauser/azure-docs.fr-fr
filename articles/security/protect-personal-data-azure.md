---
title: Protéger les données personnelles dans Microsoft Azure | Microsoft Docs
description: Cet article devrait vous aider à utiliser Azure pour protéger des données personnelles et vous conformer au Règlement général sur la protection des données (RGPD).
services: security
documentationcenter: na
author: Barclayn
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
ms.custom: ''
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Protéger les données personnelles dans Microsoft Azure

Cet article présente une série d’articles qui vous aident à utiliser les services et technologies de sécurité Azure pour protéger des données personnelles. La sécurité est une exigence clé pour un grand nombre d’initiatives en matière de gouvernance et de mise en conformité des entreprises et de leurs activités. Par exemple, vous pouvez utiliser les informations fournies dans cette série d’articles pour vous conformer au Règlement général sur la protection des données (RGPD). Le scénario, la définition du problème et les objectifs de l’entreprise sont inclus ici.

## <a name="scenario-and-problem-statement"></a>Scénario et définition du problème

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée, Adriatique et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni.

La compagnie utilise Microsoft Azure pour stocker les données d’entreprise dans le cloud. Ces dernières peuvent inclure des informations sur les clients et/ou les employés comme les suivantes :

- Adresses
- Numéros de téléphone
- Numéros d’identification fiscale
- Informations de carte de crédit

La compagnie doit protéger la confidentialité des données des clients et des employés tout en les rendant accessibles aux services qui en ont besoin. (Notamment, les services de paie et de réservation.)

## <a name="company-goals"></a>Objectifs de l’entreprise 

- Les sources de données qui contiennent des données personnelles sont chiffrées quand elles se trouvent dans le stockage cloud.

- Les données personnelles transférées d’un emplacement à un autre sont chiffrées pendant le transit. Ce chiffrement a lieu si les données sont transmises sur le réseau virtuel ou sur Internet entre le centre de données d’entreprise et le cloud Azure.

- La confidentialité et l’intégrité des données personnelles sont protégées contre tout accès non autorisé par de puissantes technologies de gestion des identités et de contrôle d’accès.

- Les données personnelles sont protégées contre toute exposition par violation de la sécurité au moyen d’une surveillance des vulnérabilités et des menaces.

- L’état de la sécurité des services Azure qui stockent ou transmettent des données personnelles est évalué pour identifier les opportunités de mieux protéger les données personnelles.

## <a name="data-protection-guidance"></a>Conseils de protection des données

Les articles suivants contiennent des instructions techniques qui vous aideront à atteindre les objectifs de protection des données personnelles indiqués ci-dessus :

- [Technologies de chiffrement Azure](protect-personal-data-at-rest.md)

- [Technologies de chiffrement Azure](protect-personal-data-in-transit-encryption.md)

- [Technologies d’identité et accès Azure](protect-personal-data-identity-access-controls.md)

- [Technologies de sécurité réseau Azure](protect-personal-data-network-security.md)

- [Centre de sécurité Azure](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Étapes suivantes

- [Site d’informations relatives à la sécurité Azure](https://aka.ms/AzureSecInfo)

- [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/TrustCenter/default.aspx)

- [Centre de sécurité Azure](https://azure.microsoft.com/services/security-center/)

- [Blog de l’équipe de sécurité Azure](https://www.azuresecurityorg)

- [Blog Azure.com - Sécurité](https://azure.microsoft.com/blog/topics/security/)
