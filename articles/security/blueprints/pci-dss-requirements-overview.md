---
title: "Plan de traitement des paiements Azure - Vue d’ensemble"
description: "Plan de traitement des paiements Azure - Matrice des responsabilités du client (vue d’ensemble)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Conditions de la norme PCI DSS - Vue d’ensemble

La Norme de sécurité des données de l’industrie des cartes de paiement (PCI DSS) a été développée dans le but d’encourager et de renforcer la sécurité des données de titulaires de carte ainsi que pour faciliter l’adoption de mesures de sécurité uniformes à l’échelle mondiale. La norme PCI DSS sert de référence aux conditions techniques et opérationnelles conçues pour protéger les données de compte. La norme PCI DSS s’applique à toutes les entités impliquées dans le traitement des cartes de paiement, notamment les commerçants, les entreprises de traitement, les acquéreurs, les émetteurs et les prestataires de services. La norme PCI DSS s’applique également à toutes les autres entités qui stockent, traitent ou transmettent les données de titulaires de cartes et/ou les données d’authentification sensibles. Voici une vue d’ensemble des 12 conditions de la norme PCI DSS.

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour obtenir des informations sur les procédures de test et des instructions pour chacune des conditions, reportez-vous à la documentation de la norme PCI DSS.

|   |   |
|---|---|
| **Création et gestion d’un <br/>réseau et de systèmes sécurisés** | 1. [Installer et gérer une configuration de pare-feu pour protéger les données de titulaires de carte](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Ne pas utiliser les mots de passe système et autres paramètres de sécurité par défaut définis par le fournisseur](pci-dss-requirement-2-password.md) |  
| **Protection des données de titulaires de carte** | 3. [Protéger les données de titulaires de carte stockées](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Chiffrer la transmission des données de titulaires de carte sur les réseaux publics ouverts](pci-dss-requirement-4-encryption.md) |
| **Gestion d’un <br/>programme de gestion des vulnérabilités** | 5. [Protéger tous les systèmes contre les logiciels malveillants et mettre à jour régulièrement les logiciels ou programmes antivirus](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Développer et maintenir des systèmes et des applications sécurisés](pci-dss-requirement-6-secure-system.md) |
| **Mise en œuvre de <br/>mesures de contrôle d’accès strictes** | 7. [Restreindre l’accès aux données de titulaires de carte aux seuls individus qui doivent les connaître](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identifier et authentifier l’accès aux composants de système](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Restreindre l’accès physique aux données de titulaires de carte](pci-dss-requirement-9-physical-access.md) |
| **Surveillance et <br/>test réguliers des réseaux** | 10. [Effectuer le suivi et surveiller tous les accès aux ressources réseau et aux données de titulaires de carte](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Tester régulièrement les processus et les systèmes de sécurité](pci-dss-requirement-11-testing.md) |
| **Gestion d’une <br/>politique de sécurité des informations** | 12. [Gérer une politique de sécurité des informations pour l’ensemble du personnel](pci-dss-requirement-12-policy.md) |

