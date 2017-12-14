---
title: "Répliquer une application web multiniveau basée sur IIS à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment répliquer des machines virtuelles d’une batterie de serveurs web IIS à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: cff6a7502e80eb4ff447cc99fe31b48cb660c27e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Répliquer une application web multiniveau basée sur IIS à l’aide d’Azure Site Recovery

## <a name="overview"></a>Vue d'ensemble


Les logiciels d’application sont le moteur de la productivité dans une organisation. Des applications web diverses peuvent répondre aux différents besoins d’une organisation. Certaines, comme les applications de traitement des salaires et les applications financières, ainsi que les sites web destinés aux clients peuvent être de la plus grande importance pour une organisation. Pour l’organisation, il est important qu’elles soient opérationnelles en permanence afin de prévenir toute perte de productivité, mais surtout afin de ne pas nuire à l’image de marque de l’organisation.

Les applications web critiques sont généralement configurées en tant qu’applications multiniveaux avec le web, les bases de données et les applications sur différents niveaux. Mis à part leur répartition sur différents niveaux, les applications peuvent également utiliser plusieurs serveurs de chaque niveau afin d’équilibrer la charge du trafic. En outre, les mappages entre les différents niveaux et le serveur web peuvent être basés sur les adresses IP statiques. Lors du basculement, certains de ces mappages devront être mis à jour, en particulier, si plusieurs de vos sites web sont configurés sur le serveur web. Dans le cas d’applications web utilisant SSL, les liaisons de certificat devront être mises à jour.

Les méthodes de récupération traditionnelles sans réplication impliquent la sauvegarde de plusieurs fichiers de configuration, paramètres du Registre, liaisons, composants personnalisés (COM ou .NET), contenus et certificats, ainsi que la récupération des fichiers via un ensemble d’étapes manuelles. Ces techniques sont clairement fastidieuses, sujettes aux erreurs et non évolutives. Par exemple, vous pouvez facilement oublier de sauvegarder des certificats, et vous retrouver ainsi contraint d’en acheter de nouveaux pour le serveur après le basculement.

Une solution de récupération d’urgence satisfaisante doit autoriser la modélisation des plans de récupération autour des architectures d’application complexes décrites ci-dessus. Elle doit également permettre d’ajouter des étapes personnalisées pour gérer les mappages d’applications entre les différents niveaux, assurant ainsi une solution à clic unique en cas de sinistre entraînant un délai de récupération inférieur.


Cet article indique comment protéger une application web IIS à l’aide d’une récupération [Azure Site Recovery](site-recovery-overview.md). Cet article décrit les recommandations à suivre pour répliquer une application web IIS à trois niveaux dans Azure, et indique comment vous pouvez effectuer un exercice de récupération d’urgence et comment vous pouvez basculer l’application vers Azure.


## <a name="prerequisites"></a>Composants requis

Avant de commencer, veillez à bien comprendre ce qui suit :

1. [Réplication d’une machine virtuelle dans Azure](site-recovery-vmware-to-azure.md)
1. [Conception d’un réseau de récupération](site-recovery-network-design.md)
1. [Réalisation d’un test de basculement vers Azure](./site-recovery-test-failover-to-azure.md)
1. [Exécution d’un basculement vers Azure](site-recovery-failover.md)
1. [Réplication d’un contrôleur de domaine](site-recovery-active-directory.md)
1. [Réplication de SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modèles de déploiement
Une application web IIS suit généralement l’un des modèles de déploiement suivants :

**Modèle de déploiement 1** Une batterie de serveurs web IIS avec ARR (Application Request Routing), un serveur IIS et Microsoft SQL Server.

![Modèle de déploiement](./media/site-recovery-iis/deployment-pattern1.png)

**Modèle de déploiement 2** Une batterie de serveurs web IIS avec ARR (Application Request Routing), un serveur IIS, un serveur d’applications et Microsoft SQL Server.


![Modèle de déploiement](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Prise en charge de Site Recovery

Pour les besoins de création de cet article, des machines virtuelles VMware avec un serveur IIS version 7.5 sur Windows Server 2012 R2 Enterprise ont été utilisés. Comme la réplication Site Recovery est indépendante des applications, les recommandations indiquées ici sont censées s’appliquer aux scénarios suivants et à d’autres versions d’IIS.

### <a name="source-and-target"></a>Source et cible

**Scénario** | **Vers un site secondaire** | **Vers Azure**
--- | --- | ---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui
**Serveur physique** | Non | Oui
**Microsoft Azure**|N/D|Oui

## <a name="replicate-virtual-machines"></a>Répliquer des machines virtuelles

Suivez [ce guide](site-recovery-vmware-to-azure.md) pour commencer à répliquer toutes les machines virtuelles de la batterie de serveurs web IIS dans Azure.

Si vous utilisez une adresse IP statique, spécifiez l’adresse IP que vous souhaitez pour la machine virtuelle dans le paramètre [**IP cible**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) des paramètres Calcul et réseau.

![IP cible](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Création d’un plan de récupération

Un plan de récupération permet de séquencer le basculement de différents niveaux d’une application multiniveau, ce qui contribue au maintien de la cohérence de l’application. Suivez les étapes indiquées ci-dessous lorsque vous créez un plan de récupération pour une application web multiniveau.  [En savoir plus sur la création d’un plan de récupération](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Ajout de machines virtuelles à des groupes de basculement
Une application web IIS multiniveau standard se compose d’une couche Base de données avec des machines virtuelles SQL, d’une couche web constituée d’un serveur IIS, et d’une couche Application. Ajoutez toutes ces machines virtuelles aux différents groupes en fonction de la couche, comme indiqué ci-dessous. [En savoir plus sur la personnalisation du plan de récupération](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Créez un plan de récupération. Ajoutez les machines virtuelles de la couche Base de données dans Groupe 1 pour vous assurer qu’elles sont arrêtées en dernier et affichées en premier.

1. Ajoutez les machines virtuelles de la couche Application dans Groupe 2 afin qu’elles s’affichent après la couche Base de données.

1. Ajoutez les machines virtuelles de la couche web dans Groupe 3 afin qu’elles s’affichent après la couche Application.

1. Ajoutez les machines virtuelles de l’équilibrage de charge dans Groupe 4 afin qu’elles s’affichent après la couche web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Ajout de scripts au plan de récupération
Vous devrez peut-être effectuer certaines opérations lors du post-basculement/test de basculement des machines virtuelles Azure pour vous assurer du bon fonctionnement de la batterie de serveurs web IIS. Vous pouvez automatiser les opérations de post-basculement comme la mise à jour des entrées DNS, la modification de la liaison de site ou de la chaîne de connexion en ajoutant les scripts correspondants dans le plan de récupération, comme indiqué ci-dessous. [En savoir plus sur l’ajout de scripts dans un plan de récupération](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Mise à jour DNS
Si DNS est configuré pour la mise à jour DNS dynamique, les machines virtuelles mettent généralement à jour DNS avec la nouvelle adresse IP dès leur démarrage. Pour ajouter une étape explicite pour mettre à jour DNS avec les nouvelles adresses IP des machines virtuelles, ajoutez ce [script pour mettre à jour les adresses IP dans DNS](https://aka.ms/asr-dns-update) en tant qu’action postérieure dans les groupes du plan de récupération.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Chaîne de connexion dans le fichier web.config d’une application
La chaîne de connexion spécifie la base de données avec laquelle le site web communique.

Si la chaîne de connexion porte le nom de la machine virtuelle base de données, aucune étape supplémentaire n’est nécessaire après le basculement, et l’application peut communiquer automatiquement avec la base de données. En outre, si l’adresse IP de la machine virtuelle base de données est conservée, il n’est pas nécessaire de mettre à jour la chaîne de connexion. Si la chaîne de connexion désigne la machine virtuelle base de données à l’aide d’une adresse IP, elle doit être mise à jour après le basculement. Par exemple, la chaîne de connexion indiquée ci-dessous pointe vers la base de données dotée de l’adresse IP 127.0.1.2.

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Vous pouvez mettre à jour la chaîne de connexion dans la couche web en ajoutant un [script de mise à jour de connexion IIS](https://aka.ms/asr-update-webtier-script-classic) après Groupe 3 dans le plan de récupération.

#### <a name="site-bindings-for-the-application"></a>Liaisons de site pour l’application
Chaque site se compose d’informations de liaison qui incluent le type de liaison, l’adresse IP à laquelle le serveur IIS écoute les demandes correspondant au site, le numéro de port et les noms d’hôte du site. Lors d’un basculement, ces liaisons devront peut-être être mises à jour en cas de modification de l’adresse IP qui leur est associée.

> [!NOTE]
>
> Si vous avez défini la liaison de site sur « Toutes non attribuées » comme dans l’exemple ci-dessous, vous n’avez pas besoin de mettre à jour cette liaison après le basculement. En outre, si l’adresse IP associée à un site n’est pas modifiée après le basculement, la liaison de site n’a pas besoin d’être mise à jour (la rétention de l’adresse IP dépend de l’architecture réseau et des sous-réseaux affectés aux sites principaux et de récupération et peut donc ou non être possible pour votre organisation.)

![Liaison SSL](./media/site-recovery-iis/sslbinding.png)

Si vous avez associé l’adresse IP à un site, vous devez mettre à jour toutes les liaisons de site avec la nouvelle adresse IP. Vous pouvez ajouter un [script de mise à jour de la couche web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) après Groupe 3 dans le plan de récupération afin de modifier les liaisons de site.


#### <a name="update-load-balancer-ip-address"></a>Mettre à jour l’adresse IP de l’équilibreur de charge
Si vous disposez d’une machine virtuelle ARR, ajoutez un [script de basculement ARR IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) après Groupe 4 pour mettre à jour l’adresse IP.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Liaison de certificat SSL d’une connexion HTTPS
Les sites web peuvent posséder un certificat SSL associé, qui garantit une communication sécurisée entre le serveur web et le navigateur de l’utilisateur. Si le site web dispose d’une connexion HTTPS et d’une liaison de site HTTPS associée à l’adresse IP du serveur IIS avec une liaison de certificat SSL, une nouvelle liaison de site doit être ajoutée pour le certificat avec l’adresse IP de la machine virtuelle IIS après le basculement.

Le certificat SSL peut être émis sur :

a) Le nom de domaine complet du site web<br>
b) Le nom du serveur<br>
c) Un certificat générique pour le nom de domaine<br>
d) Une adresse IP : si le certificat SSL est émis sur l’adresse IP du serveur IIS, un autre certificat SSL doit être émis sur l’adresse IP du serveur IIS sur le site Azure, et une autre liaison SSL pour ce certificat doit être créée. Il est donc conseillé de ne pas utiliser un certificat SSL émis sur l’adresse IP. C’est une option moins couramment utilisée, qui sera bientôt dépréciée conformément aux nouvelles modifications du forum sur l’autorité de certification/navigateur.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Mettre à jour la dépendance entre la couche web et la couche Application
Si une dépendance propre à une application est basée sur l’adresse IP des machines virtuelles, vous devez la mettre à jour après le basculement.

## <a name="doing-a-test-failover"></a>Exécution d’un test de basculement
Suivez [ce guide](site-recovery-test-failover-to-azure.md) pour effectuer un test de basculement.

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
1.  Cliquez sur le plan de récupération créé pour la batterie de serveurs web IIS.
1.  Cliquez sur Test de basculement.
1.  Sélectionnez un point de récupération et un réseau virtuel Azure pour démarrer le test de basculement.
1.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.
1.  Une fois les validations terminées, vous pouvez sélectionner « Validations terminées ». L’environnement de test de basculement est alors nettoyé.

## <a name="doing-a-failover"></a>Exécution d’un basculement
Suivez [ce guide](site-recovery-failover.md) lorsque vous effectuez un basculement.

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
1.  Cliquez sur le plan de récupération créé pour la batterie de serveurs web IIS.
1.  Cliquez sur « Basculement ».
1.  Sélectionnez un point de récupération pour démarrer le processus de basculement.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en savoir plus sur la [réplication d’autres applications](site-recovery-workload.md) à l’aide de Site Recovery.
