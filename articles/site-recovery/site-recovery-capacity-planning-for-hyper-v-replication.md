---
title: "Outil de planification de la capacité Hyper-V pour Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment exécuter l'outil de planification de la capacité Hyper-V pour Azure Site Recovery."
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: 80c3dcb65bd74bcfa3acc5f12dd5c45cd1c06455
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="hyper-v-capacity-planning-tool-for-site-recovery"></a>Outil de planification de la capacité Hyper-V pour Site Recovery

Une nouvelle version améliorée du [Planificateur de déploiement Azure Site Recovery de Hyper-V vers déploiement Azure](site-recovery-hyper-v-deployment-planner.md) est désormais disponible. Il remplace l’ancien outil. Utilisez le nouvel outil pour la planification de votre déploiement. L’outil fournit les recommandations suivantes :

* Évaluation de l’éligibilité de la machine virtuelle en fonction du nombre de disques, de la taille du disque, des E/S par seconde, de l’activité et de quelques caractéristiques de machine virtuelle
* Besoin de bande passante réseau et évaluation de RPO
* Exigences de l’infrastructure Azure
* Exigences de l’infrastructure locale
* Conseils de traitement par lot de réplication initiale
* Estimation du coût total de récupération d’urgence vers Azure

Dans le cadre de votre déploiement Azure Site Recovery, vous devez déterminer les conditions requises de la réplication et vos besoins en bande passante. L’outil de planification de la capacité Hyper-V pour Site Recovery vous aide à déterminer ces exigences pour la réplication des machines virtuelles Hyper-V.

Cet article décrit comment exécuter l'outil de planification de la capacité Hyper-V. Vous pouvez utiliser cet outil avec les informations présentées dans la rubrique consacrée à la [planification de la capacité pour Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Avant de commencer
Exécutez l’outil sur un serveur ou un nœud de cluster Hyper-V sur votre site principal. Pour exécuter l’outil, voici ce dont ont besoin les serveurs hôtes Hyper-V :

* Système d’exploitation : Windows Server 2012 ou 2012 R2
* Mémoire : 20 Mo (minimum)
* Processeur : surcharge de 5 pour cent (minimum)
* Espace disque : 5 Mo (minimum)

Avant d’exécuter l’outil, vous devez préparer le site principal. Si vous effectuez une réplication entre deux sites locaux et que vous voulez vérifier la bande passante, vous devez aussi préparer un serveur de réplication.

## <a name="step-1-prepare-the-primary-site"></a>Étape 1 : préparer le site principal

1. Sur le site principal, dressez la liste de toutes les machines virtuelles Hyper-V que vous voulez répliquer. Liste des hôtes ou clusters Hyper-V où ils se trouvent. L’outil peut s’exécuter pour plusieurs hôtes autonomes ou pour un même cluster, mais pas les deux à la fois. De même, il doit s’exécuter séparément pour chaque système d’exploitation. Regroupez et notez les informations de vos serveurs Hyper-V comme suit :

   * Serveurs autonomes Windows Server 2012
   * Clusters Windows Server 2012
   * Serveurs autonomes Windows Server 2012 R2
   * Clusters Windows Server 2012 R2

2. Activez l’accès à distance à Windows Management Instrumentation sur tous les hôtes et clusters Hyper-V. Exécutez cette commande sur chaque serveur ou cluster pour faire en sorte que les règles de pare-feu et les autorisations utilisateur soient définies :

        netsh firewall set service RemoteAdmin enable
3. Activez l’analyse des performances sur les serveurs et les clusters comme suit :

   a. Ouvrez le Pare-feu Windows avec le composant logiciel enfichable **Fonctions avancées de sécurité**. 
   
   b. Sélectionnez la règle entrante **Accès réseau COM + (DCOM-IN)**. Sélectionnez toutes les règles dans le groupe **Gestion à distance du journal des événements**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Étape 2 : préparer un serveur réplica (réplication de local en local)
Si vous répliquez vers Azure, vous n’avez pas besoin d’effectuer cette étape.

Nous vous recommandons de configurer un seul hôte Hyper-V en tant que serveur de récupération de façon à y répliquer une machine virtuelle factice afin de vérifier la bande passante. Vous pouvez passer cette étape, mais vous ne pourrez alors pas mesurer la bande passante.

1. Si vous voulez utiliser un nœud de cluster comme réplica, configurez le service Broker de réplication Hyper-V :

   a. Dans le **Gestionnaire de serveur**, ouvrez **Gestionnaire du cluster de basculement**.

   b. Connectez-vous au cluster et mettez en surbrillance le nom du cluster. Sélectionnez **Actions** > **Configurer un rôle** pour ouvrir l’Assistant Haute disponibilité.

   c. Dans **Sélectionner un rôle**, sélectionnez **Service Broker de réplication Hyper-V**. Dans l’assistant, entrez un nom pour **Nom NetBIOS**. Saisissez une adresse dans **Adresse IP** à utiliser comme point de connexion au cluster (appelé point d’accès client). Le **Service Broker de réplication Hyper-V** est alors configuré, ce qui crée un nom de point d’accès client que vous devez noter.

   d. Vérifiez que le rôle Service Broker de réplication Hyper-V est correctement mis en ligne et peut basculer entre tous les nœuds du cluster. Cliquez sur le rôle, puis sélectionnez **Déplacer** > **Sélectionner un nœud**. Sélectionnez un nœud, puis cliquez sur **OK**.

   e. Si vous utilisez l’authentification basée sur les certificats, vérifiez que le certificat est installé sur chaque nœud du cluster et sur le point d’accès client.

2. Pour activer un serveur de réplication, procédez comme suit :

   a. Pour un cluster, ouvrez le **Gestionnaire du cluster de basculement**, et connectez-vous au cluster. Sélectionnez **Rôles**, puis sélectionnez un rôle. Sélectionnez **Paramètres de réplication** > **Activer ce cluster en tant que serveur de réplication**. Si vous utilisez un cluster en tant que réplica, le rôle Service Broker de réplication Hyper-V doit aussi être présent sur le cluster du site principal.

   b. Pour un serveur autonome, ouvrez le **Gestionnaire Hyper-V**. Dans le volet **Actions**, sélectionnez **Paramètres Hyper-V** pour le serveur à activer. Dans **Configuration de la réplication**, cliquez sur **Enable this computer as a Replica server** (Activer cet ordinateur en tant que serveur de réplication).

3. Pour configurer l’authentification, effectuez les étapes suivantes :

   a. Sous **Authentification et ports**, sélectionnez le mode d’authentification du serveur principal et les ports d’authentification. Si vous utilisez un certificat, sélectionnez **Sélectionner un certificat** pour en sélectionner un. Utilisez Kerberos si les hôtes Hyper-V principal et de récupération sont dans le même domaine ou dans des domaines approuvés. Utilisez des certificats pour des domaines différents ou pour un déploiement de groupe de travail.

   b. Sous **Autorisation et stockage**, indiquez que vous autorisez **n’importe quel** serveur authentifié (principal) à envoyer des données de réplication vers ce serveur réplica.

   ![Configuration de la réplication](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

   c. Exécutez **netsh http show servicestate** pour vérifier que l’écouteur s’exécute pour le protocole/port que vous avez spécifié. 

4. Configurez des pare-feu. Pendant l’installation d’Hyper-V, des règles de pare-feu sont créées pour autoriser le trafic sur les ports par défaut (HTTPS sur 443 et Kerberos sur 80). Activez ces règles comme suit :

    - Authentification de certificat sur le cluster (443) : ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
    - Authentification Kerberos sur le cluster (80) : ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
    - Authentification de certificat sur le serveur autonome : ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
    - Authentification Kerberos sur le serveur autonome : ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planning-tool"></a>Étape 3 : exécuter l’outil de planification de la capacité
Après avoir préparé votre site principal et configuré un serveur de récupération, vous pouvez exécuter l’outil.

1. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39057) l’outil à partir du Centre de téléchargement Microsoft.

2. Exécutez l’outil à partir de l’un des serveurs principaux ou de l’un des nœuds du cluster principal. Cliquez avec le bouton droit sur le fichier .exe, puis sélectionnez **Exécuter en tant qu’administrateur**.

3. Dans **Avant de commencer**, indiquez la durée de collecte souhaitée des données. Nous vous recommandons d’exécuter l’outil pendant les heures de production pour vous assurer que les données sont représentatives. Si vous souhaitez seulement valider la connectivité réseau, vous pouvez effectuer une collecte d’une minute seulement.

    ![Avant de commencer](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Dans **Détails du site principal**, spécifiez le nom du serveur ou le nom de domaine complet d’un hôte autonome. Pour un cluster, spécifiez le FQDN du point d’accès client, le nom du cluster ou n’importe quel nœud du cluster. Sélectionnez **Suivant**. L’outil détecte automatiquement le nom du serveur sur lequel il s’exécute. L’outil choisit les machines virtuelles qui peuvent être surveillées pour les serveurs spécifiés.

    ![Détails du site principal](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Dans **Replica Site Details** (Détails du site de réplication), si vous répliquez dans Azure ou sur un centre de données secondaire et que vous n’avez pas configuré de serveur réplica, sélectionnez **Skip tests involving replica site** (Ignorer les tests impliquant un site de réplication). Si vous répliquez sur un centre de données secondaire et que vous avez configuré un réplica, entrez le FQDN du serveur autonome ou le point d’accès client du cluster dans **Server name (or) Hyper-V Replica Broker CAP**.

    ![Détails du site de réplication](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Dans **Extended Replica Details** (Détails de réplicas étendus), sélectionnez **Skip the tests involving Extended Replica site** (Ignorer les tests impliquant un site de réplication étendu). Ces tests ne sont pas pris en charge par Site Recovery.

7. Dans **Choisir les machines virtuelles à répliquer**, l’outil se connecte au serveur ou au cluster. Il affiche les machines virtuelles et les disques qui s’exécutent sur le serveur principal, en fonction des paramètres que vous avez spécifiés dans la page **Primary Site Details** (Détails du site principal). Les machines virtuelles déjà activées pour la réplication ou qui ne sont pas en cours d’exécution ne s’affichent pas. Sélectionnez les machines virtuelles pour lesquelles vous voulez collecter les métriques. Si vous sélectionnez les disques durs virtuels, les données sont aussi collectées automatiquement pour les machines virtuelles.

8. Si vous avez configuré un serveur réplica ou un cluster, spécifiez dans **Network information** (Informations sur le réseau) la quantité approximative de bande passante WAN qui sera utilisée entre les sites principal et de réplication. Si vous avez configuré l’authentification par certificat, sélectionnez les certificats.

    ![Informations sur le réseau](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

9. Dans **Résumé**, vérifiez les paramètres. Sélectionnez **Suivant** pour commencer à collecter les métriques. La progression et l’état de l’outil s’affichent dans la page **Calculate Capacity**. Quand l’outil a terminé, sélectionnez **View Report** (Afficher le rapport) pour afficher le résultat. Par défaut, les rapports et les journaux sont stockés dans **%systemdrive%\Utilisateurs\Public\Documents\Capacity Planner**.

   ![Calculer la capacité](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Étape 4 : interpréter les résultats

Les principales métriques sont indiquées ci-dessous. Vous pouvez ignorer les métriques qui ne sont pas mentionnées ici. Elles n’ont aucune utilité pour Site Recovery.

### <a name="on-premises-to-on-premises-replication"></a>Réplication d’un site local vers un autre

* Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
* Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde de l’hôte principal et de l’hôte de récupération
* Bande passante totale requise pour la réplication delta (Mbits/s)
* Bande passante réseau observée entre l’hôte principal et l’hôte de restauration (Mbits/s)
* Suggestion quant au nombre idéal de transferts parallèles actifs entre les deux hôtes ou clusters

### <a name="on-premises-to-azure-replication"></a>Réplication d’un site local vers Azure

* Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
* Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde de l’hôte principal
* Bande passante totale requise pour la réplication delta (Mbits/s)

## <a name="more-resources"></a>Autres ressources

* Pour en savoir plus sur l’outil, lisez le document qui accompagne le téléchargement de l’outil.
* Consultez une procédure pas à pas de l’outil sur le [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) de Keith Mayer.
* [Obtenez les résultats](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) des tests de performance pour la réplication Hyper-V d’un site local vers un autre.

## <a name="next-steps"></a>étapes suivantes

Une fois que vous avez terminé la planification de la capacité, vous pouvez déployer Site Recovery :
* [Répliquer des machines virtuelles Hyper-V dans des clouds VMM vers Azure.](site-recovery-vmm-to-azure.md)
* [Répliquer des machines virtuelles Hyper-V (sans VMM) dans Azure](site-recovery-hyper-v-site-to-azure.md)
* [Répliquer des machines virtuelles Hyper-V entre des sites VMM](site-recovery-vmm-to-vmm.md)
