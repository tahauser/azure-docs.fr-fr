---
title: Utilisation du portail administrateur dans Azure Stack | Microsoft Docs
description: "En tant qu’opérateur Azure Stack, découvrez comment utiliser le portail administrateur."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Utilisation du portail administrateur dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Il existe deux portails dans Azure Stack : le portail administrateur et le portail utilisateur (parfois appelé portail du *locataire*). En tant qu’opérateur Azure Stack, vous pouvez utiliser le portail administrateur pour la gestion et les opérations quotidiennes d’Azure Stack. 

## <a name="access-the-administrator-portal"></a>Accéder au portail administrateur

Pour un environnement de Kit de développement, vous devez d’abord vérifier que vous pouvez [vous connecter à l’hôte du Kit de développement](azure-stack-connect-azure-stack.md) via Connexion Bureau à distance ou un réseau privé virtuel (VPN).

Pour accéder au portail administrateur, accédez à l’URL du portail, puis connectez-vous en utilisant les informations d’identification d’un opérateur Azure Stack. Pour un système intégré, l’URL du portail dépend du nom de la région et du nom de domaine complet (FQDN) externe de votre déploiement d’Azure Stack.

| Environnement | URL du portail administrateur |   
| -- | -- | 
| Kit de développement| https://adminportal.local.azurestack.external  |
| Systèmes intégrés | https://adminportal.&lt;*région*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![Le portail administrateur](media/azure-stack-manage-portals/image1.png)

Dans le portail administrateur, vous pouvez effectuer des opérations telles que les suivantes :

* Gérer l’infrastructure (notamment l’intégrité du système, les mises à jour, la capacité, etc.)
* Renseigner la Place de Marché
* Créer des plans et des offres
* Créer des abonnements pour les utilisateurs

La vignette du **didacticiel de démarrage rapide** comporte des liens vers de la documentation en ligne pour les tâches les plus courantes.
 
Bien qu’un opérateur puisse créer des ressources telles que des machines virtuelles, des réseaux virtuels et des comptes de stockage dans le portail administrateur, vous devez [vous connecter au portail utilisateur](user/azure-stack-use-portal.md) pour créer et tester des ressources. (Le lien **Créer une machine virtuelle** dans la vignette du didacticiel de démarrage rapide vous amène à créer une machine virtuelle dans le portail administrateur, mais cette procédure sert uniquement à valider Azure Stack après le déploiement initial.)

## <a name="subscription-behavior"></a>Comportement de l’abonnement
 
Un seul abonnement est disponible dans le portail administrateur. Il s’agit de l’*Abonnement Fournisseur par défaut*. Vous ne pouvez pas ajouter d’autres abonnements pour une utilisation dans le portail administrateur.

En tant qu’opérateur Azure Stack, vous pouvez ajouter des abonnements pour vos utilisateurs (y compris vous-même) à partir du portail administrateur. Les utilisateurs (y compris vous-même) peuvent accéder à ces abonnements et les utiliser à partir du portail utilisateur. Le portail utilisateur ne fournit pas d’accès aux fonctionnalités d’administration ou d’exploitation du portail administrateur.

Les portails administrateur et utilisateur sont secondés par des instances distinctes d’Azure Resource Manager. En raison de la séparation de Resource Manager, les abonnements ne traversent pas les portails. Par exemple, si vous-même (en tant qu’opérateur Azure Stack) vous connectez au portail utilisateur, vous ne pouvez pas accéder à l’Abonnement Fournisseur par défaut. Ainsi, vous n’avez accès à aucune fonction d’administration. Vous pouvez créer des abonnements pour vous-même à partir d’offres publiques, mais vous êtes considéré comme un utilisateur de locataire.

  >[!NOTE]
  Dans l’environnement du Kit de développement, si un utilisateur appartient au même annuaire de locataire que l’opérateur Azure Stack, rien ne l’empêche de se connecter au portail administrateur. Toutefois, il ne peut accéder à aucune fonction d’administration. En outre, à partir du portail administrateur, il ne peut pas ajouter d’abonnements ni accéder à des offres mises à sa disposition dans le portail utilisateur.

## <a name="administrator-portal-tips"></a>Conseils pour le portail administrateur

### <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord

Le tableau de bord contient une série de vignettes par défaut. Vous pouvez cliquer sur **Modifier le tableau de bord** pour modifier le tableau de bord par défaut, ou sur **Nouveau tableau de bord** pour ajouter des tableaux de bord personnalisés. Vous pouvez facilement ajouter des vignettes au tableau de bord. Par exemple, vous pouvez cliquer sur **Nouveau**, cliquer avec le bouton droit sur **Offres + plans**, puis cliquer sur **Épingler au tableau de bord**.

### <a name="quick-access-to-online-documentation"></a>Accès rapide à la documentation en ligne

Pour accéder à la documentation de l’opérateur Azure Stack, cliquez sur l’icône d’aide et de support (point d’interrogation) dans le coin supérieur droit du portail administrateur, puis sur **Aide + support**.

### <a name="quick-access-to-help-and-support"></a>Accès rapide à l’aide et au support

Si vous cliquez sur l’icône d’aide et de support (point d’interrogation) dans le coin supérieur droit du portail administrateur, puis sur **Nouvelle demande de support**, cette action effectue l’une des opérations suivantes :

- Si vous utilisez un système intégré, cette action ouvre un site sur lequel vous pouvez ouvrir directement un ticket de support auprès des services de support technique Microsoft. Reportez-vous à la section « Où obtenir un support technique ? » dans les [principes de base de l’administration Azure Stack](azure-stack-manage-basics.md) pour comprendre quand vous devez vous adresser au support technique Microsoft ou au support technique de votre fournisseur de matériel OEM.
- Si vous utilisez le Kit de développement, cette action ouvre directement le site des forums Internet d’Azure Stack. Ces forums sont consultés régulièrement. Le Kit de développement étant un environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services de support technique Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des régions dans Azure Stack](azure-stack-region-management.md)
