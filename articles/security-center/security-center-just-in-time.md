---
title: Accès Juste à temps à la machine virtuelle dans Azure Security Center | Microsoft Docs
description: Ce document montre comment l’accès Juste à temps à la machine virtuelle dans Azure Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: terrylan
ms.openlocfilehash: f1ea31d1081bc263cf85cf4dcc3d73d4cc0b842d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gérer l’accès Juste à temps à la machine virtuelle

L’accès Juste à temps à la machine virtuelle peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, réduire l’exposition aux attaques et faciliter la connexion aux machines virtuelles si nécessaire.

> [!NOTE]
> La fonctionnalité Juste à temps est disponible pour le niveau Standard de Security Center.  Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>
>

## <a name="attack-scenario"></a>Scénario d’attaque

Les attaques par force brute ciblent généralement les ports de gestion cible pour accéder à une machine virtuelle. S’il réussit, un attaquant peut prendre le contrôle sur la machine virtuelle et créer une brèche dans votre environnement.

Pour réduire l’exposition aux attaques par force brute, vous pouvez limiter la durée d’ouverture d’un port. Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Lorsque la fonctionnalité Juste à temps est activée, Security Center utilise les règles du [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) qui restreignent l’accès aux ports de gestion afin d’éviter qu’ils ne soient la cible d’attaquants.

![Scénario Juste à temps][1]

## <a name="how-does-just-in-time-access-work"></a>Comment l’accès Juste à temps fonctionne-t-il ?

Lorsque la fonctionnalité Juste à temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant sera verrouillé. Ces ports sont contrôlés par la solution Juste à temps.

Quand un utilisateur demande l’accès à une machine virtuelle, Security Center vérifie que cet utilisateur a les autorisations [Contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) qui fournissent un accès en écriture sur la machine virtuelle. S’il dispose d’une autorisation en écriture, la requête est approuvée et Security Center configure automatiquement les groupes de sécurité réseau afin d’autoriser le trafic entrant vers les ports de gestion pendant la durée que vous avez spécifiée. Après expiration du délai, Security Center restaure les groupes de sécurité réseau à leur état précédent.

> [!NOTE]
> L’accès Juste à temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Pour en savoir plus sur les modèles de déploiement de type Classic et Resource Manager, consultez [Déploiement Azure Resource Manager et déploiement Classic](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Utilisation de l’accès Juste à temps

1. Ouvrez le tableau de bord **Security Center**.

2. Dans le volet gauche, sélectionnez **Accès Juste à temps à la machine virtuelle**.

![Vignette Accès Juste à temps à la machine virtuelle][2]

La fenêtre **Accès Juste à temps à la machine virtuelle** s’ouvre.

![Vignette Accès Juste à temps à la machine virtuelle][10]

**Accès Juste à temps à la machine virtuelle** fournit des informations sur l’état de vos machines virtuelles :

- **Configuré** : machines virtuelles configurées pour prendre en charge l’accès Juste à temps à la machine virtuelle. Les données présentées concernent la semaine qui vient de s’écouler et incluent, pour chaque machine virtuelle le nombre de requêtes approuvées, la date et l’heure du dernier accès, mais aussi le dernier utilisateur.
- **Recommandé** : machines virtuelles qui peuvent prendre en charge l’accès Juste à temps à la machine virtuelle, mais n’ont pas été configurées dans cette optique. Nous vous recommandons d’activer le contrôle d’accès Juste à temps à la machine virtuelle pour ces machines virtuelles. Consultez [Configuration d’une stratégie d’accès Juste à temps](#configuring-a-just-in-time-access-policy).
- **Aucune recommandation** : voici les raisons pour lesquelles une machine virtuelle peut ne pas être recommandée :
  - Groupe de sécurité réseau manquant : la solution Juste à temps nécessite la présence d’un groupe de sécurité réseau.
  - Machine virtuelle classique : l’accès Juste à temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Le déploiement classique n’est pas pris en charge par la solution Juste à temps.
  - Autre : catégorie d’une machine virtuelle si la solution Juste à temps est désactivée dans la stratégie de sécurité de l’abonnement ou du groupe de ressources, ou si la machine virtuelle ne dispose pas d’une adresse IP publique ni d’un groupe de sécurité réseau.

## <a name="configuring-a-just-in-time-access-policy"></a>Configuration d’une stratégie d’accès Juste à temps

Pour sélectionner les machines virtuelles que vous souhaitez activer :

1. Sous **Accès Juste à temps à la machine virtuelle**, sélectionnez l’onglet **Recommandé**.

  ![Activer l’accès Juste à temps à la machine virtuelle][3]

2. Sous **MACHINE VIRTUELLE**, sélectionnez les machines virtuelles à activer. Une coche est alors placée en regard des machines virtuelles concernées.
3. Sélectionnez **Enable JIT on VMs** (Activer JIT sur les machines virtuelles).
4. Sélectionnez **Enregistrer**.

### <a name="default-ports"></a>Ports par défaut

Vous pouvez afficher les ports par défaut pour lesquels Security Center recommande l’activation de la solution Juste à temps.

1. Sous **Accès Juste à temps à la machine virtuelle**, sélectionnez l’onglet **Recommandé**.

  ![Afficher les ports par défaut][6]

2. Sous **Machines virtuelles**, sélectionnez une machine virtuelle. Une coche s’affiche à côté de la machine virtuelle et **JIT VM access configuration** (Configuration de l’accès Juste à temps à la machine virtuelle) s’ouvre. Ce panneau affiche les ports par défaut.

### <a name="add-ports"></a>Ajouter des ports

Sous **JIT VM access configuration** (Configuration de l’accès Juste à temps à la machine virtuelle), vous pouvez également ajouter et configurer un port sur lequel vous souhaitez activer la solution Juste à temps.

1. Sous **JIT VM access configuration** (Configuration de l’accès Juste à temps à la machine virtuelle), sélectionnez **Ajouter**. **Add port configuration** (Ajouter une configuration de port) s’ouvre.

  ![Configuration du port][7]

2. Sous **Add port configuration** (Ajouter une configuration de port), définissez le port, le type de protocole, les adresses IP sources autorisées et le délai de requête maximal.

  Les adresses IP sources autorisées sont les plages d’adresses IP autorisées à accéder à une requête approuvée.

  La durée de requête maximale correspond à la fenêtre de temps maximale pendant laquelle un port spécifique peut être ouvert.

3. Sélectionnez **OK**.

## <a name="requesting-access-to-a-vm"></a>Demande d’accès à une machine virtuelle

Pour demander l’accès à une machine virtuelle :

1. Sous **Accès Juste à temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2. Sous **Machines virtuelles**, sélectionnez les machines virtuelles dont vous souhaitez activer l’accès. Une coche est alors placée en regard des machines virtuelles concernées.
3. Sélectionnez **Demander l’accès**. Cette opération ouvre **Demander l’accès**.

  ![Demander l’accès à une machine virtuelle][4]

4. Sous **Demander l’accès**, configurez pour chaque machine virtuelle les ports à ouvrir, ainsi que l’adresse IP source vers laquelle le port est ouvert, et la fenêtre de temps pendant laquelle le port est ouvert. Vous pouvez demander l’accès uniquement aux ports configurés dans la stratégie Juste à temps. Chaque port dispose d’un délai maximal autorisé issu de la stratégie Juste à temps.
5. Sélectionnez **Ports ouverts**.

## <a name="editing-a-just-in-time-access-policy"></a>Modification d’une stratégie d’accès Juste à temps

Vous pouvez modifier la stratégie Juste à temps d’une machine virtuelle en ajoutant et en configurant un port à ouvrir pour cette machine virtuelle, ou en modifiant tout autre paramètre lié à un port déjà protégé.

Pour modifier la stratégie Juste à temps existante d’une machine virtuelle, utilisez l’onglet **Configuré** :

1. Sous **Machines virtuelles**, sélectionnez la machine virtuelle à laquelle ajouter un port en cliquant sur l’ellipse qui se trouve sur la ligne de cette machine virtuelle. Un menu s’ouvre.
2. Sélectionnez **Modifier** dans le menu. Cette opération ouvre **JIT VM access configuration** (Configuration de l’accès Juste à temps à la machine virtuelle).

  ![Modifier la stratégie][8]

3. Sous **JIT VM access configuration** (Configuration de l’accès Juste à temps à la machine virtuelle), modifiez les paramètres existants d’un port déjà protégé en cliquant sur ce port, ou sélectionnez **Ajouter**. **Add port configuration** (Ajouter une configuration de port) s’ouvre.

  ![Ajouter un port][7]

4. Sous **Add port configuration** (Ajouter une configuration de port), définissez le port, le type de protocole, les adresses IP sources autorisées et le délai de requête maximal.
5. Sélectionnez **OK**.
6. Sélectionnez **Enregistrer**.

## <a name="auditing-just-in-time-access-activity"></a>Audit de l’activité d’accès Juste à temps

Vous pouvez obtenir des informations sur les activités des machines virtuelles à l’aide de la recherche dans les journaux. Pour consulter les journaux :

1. Sous **Accès Juste à temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2. Sous **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez consulter les informations en cliquant sur l’ellipse qui se trouve sur la ligne de cette machine virtuelle. Un menu s’ouvre.
3. Sélectionnez **Journal d’activité** dans le menu. Cette opération ouvre **Journal d’activité**.

  ![Sélectionner un journal d’activité][9]

  **Journal d’activité** fournit une vue filtrée des opérations précédentes pour cette machine virtuelle, ainsi que l’abonnement, la date et l’heure.

  ![Afficher le journal d’activité][5]

Vous pouvez télécharger les informations du journal en sélectionnant **Cliquez ici pour télécharger tous les éléments au format CSV**.

Modifiez les filtres et sélectionnez **Appliquer** pour créer une recherche et un journal.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Utilisation de l’accès Juste à temps à la machine virtuelle par le biais de PowerShell

Pour utiliser la solution Juste à temps par le biais de PowerShell, assurez-vous de disposer de la [dernière](/powershell/azure/install-azurerm-ps) version d’Azure PowerShell.
Une fois cette vérification effectuée, vous devez installer la [dernière](https://aka.ms/asc-psgallery) version d’Azure Security Center à partir de la galerie PowerShell.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Configuration d’une stratégie Juste à temps pour une machine virtuelle

Pour configurer une stratégie Juste à temps sur une machine virtuelle spécifique, vous devez exécuter la commande suivante dans votre session PowerShell : Set-ASCJITAccessPolicy.
Consultez la documentation de la cmdlet pour en savoir plus.

### <a name="requesting-access-to-a-vm"></a>Demande d’accès à une machine virtuelle

Pour accéder à une machine virtuelle spécifique qui est protégée par la solution Juste à temps, vous devez exécuter la commande suivante dans votre session PowerShell : Invoke-ASCJITAccess.
Consultez la documentation de la cmdlet pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a fait découvrir en quoi l’accès Juste à temps à la machine virtuelle dans Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaire](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
- [Blog sur la sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
