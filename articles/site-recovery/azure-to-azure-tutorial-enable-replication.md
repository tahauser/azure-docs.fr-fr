---
title: Configurer la récupération d’urgence pour des machines virtuelles Azure vers une région Azure secondaire avec Azure Site Recovery (préversion)
description: Découvrez comment configurer la récupération d’urgence pour des machines virtuelles Azure vers une autre région Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/16/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7dd0bfbd96e6ba7b5d2174334419797c4fd60a51
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Configurer la récupération d’urgence pour des machines virtuelles Azure vers une région Azure secondaire (préversion)

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel vous montre comment configurer la récupération d’urgence vers une région Azure secondaire pour des machines virtuelles Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un coffre Recovery Services
> * Vérifier les paramètres des ressources cibles
> * Configurer un accès sortant pour les machines virtuelles
> * Activer la réplication pour une machine virtuelle

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](concepts-azure-to-azure-architecture.md).
- Vérifiez les [exigences de prise en charge](site-recovery-support-matrix-azure-to-azure.md) pour tous les composants.

## <a name="create-a-vault"></a>création d'un coffre

Créez le coffre dans n’importe quelle région, à l’exception de la région source.

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources ou sélectionnez-en un. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="verify-target-resources"></a>Vérifier les ressources cibles

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée pour la récupération d’urgence. Contactez le support pour activer le quota requis.

2. Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge des machines virtuelles de tailles correspondant à vos machines virtuelles source. Site Recovery choisit la même taille ou la taille la plus proche possible pour la machine virtuelle cible.

## <a name="configure-outbound-network-connectivity"></a>Configurer la connectivité réseau sortante

Pour que Site Recovery fonctionne comme prévu, vous devez apporter des modifications à la connectivité réseau sortante des machines virtuelles à répliquer.

- Site Recovery ne prend pas en charge l’utilisation d’un proxy d’authentification pour contrôler la connectivité réseau.
- Si vous utilisez un proxy d’authentification, la réplication ne peut pas être activée.

### <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès aux URL suivantes utilisées par Site Recovery.

| **URL** | **Détails** |
| ------- | ----------- |
| *.blob.core.windows.net | Permet d’écrire les données dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. |
| login.microsoftonline.com | Fournit l’autorisation et l’authentification aux URL du service Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permet à la machine virtuelle de communiquer avec le service Site Recovery. |
| *.servicebus.windows.net | Permet à la machine virtuelle d’écrire des données de surveillance et de diagnostic Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Quand vous utilisez des règles de groupe de sécurité réseau, de proxy ou de pare-feu basées sur une adresse IP pour contrôler la connectivité sortante, les plages d’adresses IP suivantes doivent se trouver dans la liste verte. Téléchargez une liste de plages à partir des liens suivants :

  - [Plages IP de centres de données Microsoft Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Plages IP de centres de données Microsoft Azure en Allemagne](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Plages IP de centres de données Microsoft Azure en Chine](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Adresses IP de points de terminaison du service Site Recovery](https://aka.ms/site-recovery-public-ips)

Utilisez ces listes pour configurer les contrôles d’accès réseau dans votre réseau. Vous pouvez utiliser ce [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) pour créer des règles de groupe de sécurité réseau requises.

## <a name="verify-azure-vm-certificates"></a>Vérifier les certificats des machines virtuelles Azure

Assurez-vous que tous les certificats racines les plus récents sont présents sur les machines virtuelles Windows ou Linux à répliquer. Si les certificats racines les plus récents ne le sont pas, la machine virtuelle ne peut pas être inscrite auprès du service Site Recovery en raison de contraintes de sécurité.

- Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.

- Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.

## <a name="set-permissions-on-the-account"></a>Définir les autorisations sur le compte

Azure Site Recovery fournit trois rôles intégrés pour contrôler les opérations de gestion de Site Recovery.

- **Site Recovery Contributor** - Ce rôle dispose de toutes les autorisations requises pour gérer les opérations d’Azure Site Recovery dans un coffre Recovery Services. Toutefois, un utilisateur disposant de ce rôle ne peut pas créer ou supprimer un coffre Recovery Services, ni affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour les administrateurs de récupération d’urgence, qui peuvent activer et gérer la récupération d’urgence pour des applications ou organisations.

- **Site Recovery Operator** - Ce rôle dispose des autorisations d’exécution et de gestion des opérations de basculement et de restauration automatique. Un utilisateur disposant de ce rôle ne peut pas activer ou désactiver la réplication, créer ou supprimer des coffres, enregistrer une nouvelle infrastructure ou affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour un opérateur de récupération d’urgence, qui peut basculer des machines virtuelles ou des applications quand il en reçoit la demande des propriétaires et administrateurs informatiques. Une fois l’incident résolu, l’opérateur de récupération d’urgence peut reprotéger et restaurer automatiquement les machines virtuelles.

- **Site Recovery Reader** : Ce rôle dispose des autorisations pour afficher toutes les opérations de gestion de Site Recovery. Ce rôle est tout indiqué pour un responsable de suivi informatique, qui peut surveiller l’état de protection actuel et envoyer des billets d’assistance.

En savoir plus sur les [rôles intégrés Azure RBAC](../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Activer la réplication

### <a name="select-the-source"></a>Sélectionner la source

1. Dans Coffres Recovery Services, cliquez sur le nom du coffre > **+Répliquer**.
2. Dans **Source**, sélectionnez **Azure - PREVIEW**.
3. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
4. Sélectionnez le **Modèle de déploiement de machines virtuelles Azure** de vos machines : **Resource Manager** ou **Classique**.
5. Sélectionnez le **Groupe de ressources source** pour les machines virtuelles Resource Manager, ou **service cloud** pour les machines virtuelles classiques.
6. Cliquez sur **OK** pour enregistrer les paramètres.

### <a name="select-the-vms"></a>Sélectionner les machines virtuelles

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources/service cloud.

1. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez répliquer.
2. Cliquez sur **OK**.

### <a name="configure-replication-settings"></a>Configurer les paramètres de réplication

Site Recovery crée les paramètres par défaut et la stratégie de réplication pour la région cible. Vous pouvez changer les paramètres selon vos besoins.

1. Cliquez sur **Paramètres** pour afficher les paramètres de cible et de réplication.
2. Pour remplacer les paramètres de cible par défaut, cliquez sur **Personnaliser** en regard de **Groupe de ressources, réseau, stockage et groupes à haute disponibilité**.

  ![Configurer les paramètres](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Emplacement cible** : région cible utilisée pour la récupération d’urgence. Il est recommandé que l’emplacement cible corresponde à l’emplacement du coffre Site Recovery.

- **Groupe de ressources cible** : groupe de ressources dans la région cible qui héberge les machines virtuelles Azure après le basculement. Par défaut, Site Recovery crée un groupe de ressources dans la région cible avec un suffixe « asr ».

- **Réseau virtuel cible** : réseau dans la région cible où se trouvent les machines virtuelles après le basculement.
  Par défaut, Site Recovery crée un réseau virtuel (et des sous-réseaux) dans la région cible avec un suffixe « asr ».

- **Comptes de stockage de cache** : Site Recovery utilise un compte de stockage dans la région source. Les modifications apportées aux machines virtuelles sources sont envoyées sur ce compte avant la réplication vers l’emplacement cible.

- **Comptes de stockage cibles (si la machine virtuelle source n’utilise pas de disques managés)** : par défaut, Site Recovery crée un compte de stockage dans la région cible qui reflète le compte de stockage de la machine virtuelle source.

- **Disques managés de réplica (si la machine virtuelle source utilise des disques managés)** : par défaut, Site Recovery crée des disques managés de réplica dans la région cible pour mettre en miroir les disques managés de la machine virtuelle source avec le même type de stockage (Standard ou Premium) que celui du disque managé de la machine virtuelle source.

- **Groupes à haute disponibilité cibles** : par défaut, Site Recovery crée un groupe à haute disponibilité dans la région cible avec le suffixe « asr ». Vous pouvez uniquement ajouter des groupes à haute disponibilité si les machines virtuelles font partie d’un ensemble de la région source.

Pour remplacer les paramètres de stratégie de réplication par défaut, cliquez sur **Personnaliser** en regard de **Stratégie de réplication**.  

- **Nom de la stratégie de réplication** : nom de la stratégie.

- **Rétention des points de récupération** : par défaut, Site Recovery conserve les points de récupération pendant 24 heures. Vous pouvez configurer une valeur comprise entre 1 et 72 heures.

- **Fréquence des instantanés de cohérence des applications** : par défaut, Site Recovery prend un instantané de cohérence des applications toutes les 4 heures. Vous pouvez configurer une valeur comprise entre 1 et 12 heures. Un instantané de cohérence des applications est un instantané à un point dans le temps des données d’application à l’intérieur de la machine virtuelle. Le service VSS (Volume Shadow Copy Service) s’assure que les applications sur la machine virtuelle sont dans un état cohérent au moment de la prise des captures instantanées.

- **Groupe de réplication** : si votre application a besoin d’une cohérence multimachine virtuelle sur les machines virtuelles, vous pouvez créer un groupe de réplication pour ces machines virtuelles. Par défaut, les machines virtuelles sélectionnés ne font pas partie d’un groupe de réplication.

  Cliquez sur **Personnaliser** en regard de **Stratégie de réplication**, puis sélectionnez **Oui** pour la cohérence multimachine virtuelle afin d’intégrer les machines virtuelles à un groupe de réplication. Vous pouvez créer un groupe de réplication ou utiliser un groupe de réplication existant. Sélectionnez les machines virtuelles faisant partie du groupe de réplication, puis cliquez sur **OK**.

> [!IMPORTANT]
  Toutes les machines d’un groupe de réplication ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent. L’activation de la cohérence multimachine virtuelle peut influer sur les performances de la charge de travail. Elle ne doit être utilisée que si les machines exécutent la même charge de travail et si vous avez besoin de cohérence entre plusieurs machines virtuelles.

> [!IMPORTANT]
  Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Vérifiez qu’aucun dispositif de pare-feu ne bloque la communication interne entre les machines virtuelles sur le port 20004. Si vous voulez que les machines virtuelles Linux fassent partie d’un groupe de réplication, vérifiez que le trafic sortant sur le port 20004 est ouvert manuellement conformément aux instructions de la version Linux spécifique.

### <a name="track-replication-status"></a>Suivre l’état de la réplication

1. Dans **Paramètres**, cliquez sur **Actualiser** pour obtenir l’état le plus récent.

2. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.

3. Dans **Paramètres** > **Éléments répliqués**, vous pouvez afficher l’état des machines virtuelles et la progression de la réplication initiale. Cliquez sur la machine virtuelle pour explorer ses paramètres.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré la récupération d’urgence pour une machine virtuelle Azure. L’étape suivante consiste à tester votre configuration.

> [!div class="nextstepaction"]
> [Exécuter une simulation de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md)
