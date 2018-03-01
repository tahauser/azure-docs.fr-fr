---
title: "Haute disponibilité et récupération d’urgence de SAP HANA sur Azure (grandes instances) | Microsoft Docs"
description: "Établir la haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances)"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ef09e33803a976e05e555ec7ae9eb872d237137
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Haute disponibilité et récupération d’urgence des grandes instances SAP HANA sur Azure 

>[!IMPORTANT]
>Cette documentation n’est en aucun cas un remplacement de la documentation d’administration de SAP HANA ou des Notes SAP. Il est probable que le lecteur ait une solide compréhension et expertise des opérations et de l’administration de SAP HANA. En particulier dans le cadre des rubriques de sauvegarde, restauration et haute disponibilité et récupération d’urgence. Dans cette documentation, des captures d’écran à partir de SAP HANA Studio sont affichées. Le contenu, la structure et la nature des écrans des outils d’Administration de SAP et les outils eux-mêmes peuvent changer d’une version SAP HANA à l’autre. Par conséquent, il est important de pratiquer des exercices relatifs aux étapes et processus issus de votre environnement et avec vos versions commerciales HANA. Certains processus décrits dans cette documentation sont simplifiés pour une meilleure compréhension générale et ne sont pas destinés à être utilisés comme des étapes détaillées des manuels d’opération éventuels. Si vous souhaitez créer des manuels d’opération pour vos configurations particulières, vous devez tester et pratiquer vos processus et documenter ces processus liés à vos configurations spécifiques. 


La haute disponibilité et la récupération d’urgence constituent des aspects fondamentaux de l’exécution de vos serveurs stratégiques SAP HANA sur Azure (grandes instances). Il est important que vous collaboriez avec SAP, votre intégrateur de systèmes ou Microsoft pour concevoir et implémenter efficacement la stratégie de haute disponibilité et récupération d’urgence. Il est également important de tenir compte de l’objectif de point de récupération (RPO) et de l’objectif de délai de récupération (RTO), qui sont spécifiques à votre environnement.

Microsoft prend en charge certaines fonctionnalités de haute disponibilité de SAP HANA avec les grandes instances HANA. Ces fonctionnalités sont les suivantes :

- **Réplication du stockage** : capacité du système de stockage à répliquer toutes les données à un autre tampon de grande instance HANA dans une autre région Azure. SAP HANA opère indépendamment de cette méthode. Cette fonctionnalité est le mécanisme de récupération d’urgence par défaut proposé pour les grandes instances HANA.
- **Réplication du système HANA** : La [réplication de toutes les données de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) pour un système SAP HANA à part. L’objectif de délai de récupération est réduit grâce à une réplication des données à intervalles réguliers. SAP HANA prend en charge les modes asynchrone, synchrone en mémoire et synchrone. Le mode synchrone est uniquement recommandé pour les systèmes SAP HANA situés dans le même centre de données ou à moins de 100 km de distance. Dans la conception actuelle des horodatages de grande instance HANA, la réplication de système HANA peut être utilisée uniquement pour garantir la haute disponibilité dans une région. Actuellement, la réplication du système HANA nécessite un composant de proxy inverse tiers ou de routage pour les configurations de récupération d’urgence dans une autre région Azure. 
- **Basculement automatique de l’hôte** : solution de récupération après incident locale pour SAP HANA à utiliser comme alternative à la réplication de système HANA. Si le nœud principal n’est plus disponible, configurez un ou plusieurs nœuds SAP HANA de secours en mode montée en puissance parallèle, et SAP HANA bascule automatiquement vers un nœud de secours.

SAP HANA sur Azure (grandes instances) est proposé dans deux régions Azure qui couvrent trois régions géopolitiques différentes (États-Unis, Australie et Europe). Avec la zone géopolitique du Japon qui sera bientôt disponible en ligne. Deux régions différentes, dans une zone géopolitique, qui hébergent des tampons de grande instance HANA sont connectées à des circuits réseau dédiés distincts utilisés pour répliquer des captures instantanées de stockage afin de fournir des méthodes de récupération d’urgence. Par défaut, la réplication n’est pas établie. Elle est définie pour les clients qui ont commandé des fonctionnalités de récupération d’urgence. La réplication de stockage dépend de l’utilisation des captures instantanées de stockage pour les grandes instances HANA. Il est impossible de choisir comme région de récupération d’urgence une région Azure qui se trouve dans une autre zone géopolitique. 

Le tableau suivant indique les combinaisons et méthodes de haute disponibilité et de récupération d’urgence actuellement prises en charge :

| Scénario pris en charge dans les grandes instances HANA | Option de haute disponibilité | Option de récupération d’urgence | Commentaires |
| --- | --- | --- | --- |
| Nœud unique | Non disponible | Configuration de récupération d’urgence dédiée.<br /> Configuration de récupération d’urgence polyvalente. | |
| Basculement automatique avec hôte : N+m<br /> y compris 1+1 | Possible avec nœud de secours en rôle actif.<br /> Contrôle par HANA de la permutation des rôles. | Configuration de récupération d’urgence dédiée.<br /> Configuration de récupération d’urgence polyvalente.<br /> Synchronisation de la récupération d’urgence à l’aide de la réplication du stockage. | Des jeux de volumes HANA sont attachés à tous les nœuds (n+m).<br /> Le site de récupération d’urgence doit avoir le même nombre de nœuds. |
| Réplication de système HANA | Possible avec configuration de réplica principal ou secondaire.<br /> Le réplica secondaire prend le rôle principal en cas de basculement.<br /> Réplication de système HANA et basculement du contrôle du système d’exploitation. | Configuration de récupération d’urgence dédiée.<br /> Configuration de récupération d’urgence polyvalente.<br /> Synchronisation de la récupération d’urgence à l’aide de la réplication du stockage.<br /> La récupération d’urgence à l’aide de la réplication de système HANA n’est pas possible sans composants tiers. | Des jeux distincts de volumes de disque sont attachés à chaque nœud.<br /> Seuls les volumes de disque de réplica secondaire sur le site de production sont répliqués à l’emplacement de la récupération d’urgence.<br /> Un jeu de volumes est requis sur le site de récupération d’urgence. | 

L’expression « configuration de récupération d’urgence dédiée » désigne une configuration où l’unité de grande instance HANA sur le site de récupération d’urgence n’est pas utilisée pour exécuter d’autres charges de travail ou systèmes de non-production. L’unité est passive et est déployée uniquement si un basculement d’urgence est exécuté. Cependant, cette configuration n’est le choix préféré de nombreux clients.

> [!NOTE]
> [Les déploiements SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (plusieurs Instances HANA sur une unité) comme des scénarios de superpositions fonctionnent avec les méthodes de haute disponibilité et de récupération d’urgence répertoriées dans le tableau. L’utilisation de la réplication du système HANA avec un cluster de basculement automatique en fonction du Pacemaker est une exception. Ce cas prend uniquement en charge une seule instance HANA par unité. Alors que pour les déploiements [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000), seules les méthodes de haute disponibilité et de récupération d’urgence sans stockage fonctionnent si plus d’un client est déployé. Avec un locataire déployé, toutes les méthodes répertoriées sont valides.  

L’expression « configuration de récupération d’urgence polyvalente » désigne une configuration où l’unité de grande instance HANA sur le site de récupération d’urgence exécute une charge de travail de non-production. En cas de panne, vous arrêtez le système de non-production, vous montez les jeux de volumes (supplémentaires) répliqués par le stockage et vous démarrez l’instance HANA de production. La plupart des clients qui utilisent la fonctionnalité de récupération d’urgence des grandes instances HANA recourent à cette configuration. 


Vous trouverez plus d’informations sur la haute disponibilité de SAP HANA dans les articles SAP suivants : 

- [SAP HANA High Availability Whitepaper (Livre blanc sur la haute disponibilité de SAP HANA)](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA Administration Guide (Guide d’administration de SAP HANA)](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP Academy Video on SAP HANA System Replication (Vidéo SAP Academy sur la réplication du système SAP HANA) ](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Support Note #1999880 – FAQ on SAP HANA System Replication (Note de support SAP n°1999880 – FAQ sur la réplication du système SAP HANA)](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Support Note #2165547 – SAP HANA Back up and Restore within SAP HANA System Replication Environment (Note de support SAP n°2165547 – Sauvegarde et restauration SAP HANA dans l’environnement de réplication du système SAP HANA)](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Support Note #1984882 – Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime (Note de support SAP n°1984882 – Utilisation de la réplication du système SAP HANA pour l’échange de matériel avec un temps d’arrêt minime ou nul)](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considérations sur le réseau pour la récupération d’urgence avec de grandes instances HANA

Pour tirer parti de la fonctionnalité de récupération d’urgence des grandes instances HANA, vous devez concevoir la connectivité réseau des deux régions Azure. Vous avez besoin d’un circuit Azure ExpressRoute assurant la connexion entre l’emplacement local dans votre région Azure principale, et d’un autre circuit pour la connexion entre l’emplacement local et votre région de récupération d’urgence. Cette mesure s’applique lorsqu’une région Azure, incluant l’emplacement MSEE (Microsoft Enterprise Edge Router), présente un problème.

En guise de seconde mesure, vous pouvez connecter tous les réseaux virtuels Azure qui sont connectés au serveur SAP HANA sur Azure (grandes instances) dans l’une des régions au circuit ExpressRoute qui connecte les grandes instances HANA dans l’autre région. Avec cette *connexion croisée*, les services en cours d’exécution dans un réseau virtuel Azure au sein la région 1 peuvent se connecter aux unités de grande instance HANA dans la région 2, et inversement. Cette mesure résout la situation dans laquelle un seul des emplacements MSEE qui connecte votre emplacement local à Azure se déconnecte.

Le graphique suivant illustre une configuration résistante aux cas de récupération d’urgence :

![Configuration optimale pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>Autres exigences pour l’utilisation de la réplication de stockage avec de grandes instances HANA dans le cadre d’une récupération d’urgence

Voici d’autres exigences pour une configuration de récupération d’urgence avec de grandes instances HANA :

- Vous devez commander des références (SKU) SAP HANA sur Azure (grandes instances) de la même taille que vos SKU de production et les déployer dans la région de récupération d’urgence. Dans les déploiements actuels des clients, ces instances sont utilisées pour exécuter des instances HANA de non-production. Ces configurations sont appelées *configurations de récupération d’urgence multi-usage*.   
- Vous devez commander du stockage supplémentaire sur le site de récupération d’urgence pour chacune des références SKU SAP HANA sur Azure (grandes instances) que vous souhaitez récupérer sur le site de récupération d’urgence. L’achat de stockage supplémentaire vous permet d’allouer les volumes de stockage. Vous pouvez allouer les volumes de stockage qui constituent la cible de la réplication de stockage entre votre région Azure de production et la région Azure de récupération d’urgence.

 

## <a name="backup-and-restore"></a>Sauvegarde et restauration

L’un des aspects les plus importants de l’utilisation de bases de données consiste à protéger ces bases de différents événements graves. Ces événements peuvent avoir une multitude de causes, des catastrophes naturelles aux simples erreurs des utilisateurs.

La sauvegarde d’une base de données, combinée à une possibilité de restauration à un moment donné quelconque (par exemple avant la suppression de données critiques), permet de restaurer la base de données à un état aussi proche que possible de celui qu’elle présentait avant l’interruption.

Pour optimiser les résultats, vous devez effectuer deux types de sauvegardes :

- Sauvegardes de base de données : sauvegardes complètes, incrémentielles ou différentielles
- Sauvegardes de fichier journal

Vous pouvez également compléter les sauvegardes de base de données complètes exécutées au niveau application par des sauvegardes effectuées à l’aide de captures instantanées de stockage. Notez que les captures instantanées de stockage ne remplacent pas les sauvegardes de fichier journal. Celles-ci demeurent importantes pour la restauration de la base de données jusqu’à un certain point dans le temps ou pour supprimer des journaux les transactions déjà validées. Toutefois, les captures instantanées de stockage peuvent accélérer la récupération en fournissant rapidement une image de restauration par progression de la base de données. 

SAP HANA sur Azure (grandes instances) offre deux options de sauvegarde et de restauration :

- Utilisation de vos propres méthodes de sauvegarde. Après avoir calculé et vérifié l’espace disque requis, effectuez des sauvegardes complètes de la base de données et des journaux à l’aide des méthodes de sauvegarde de disque. Vous pouvez sauvegarder soit directement sur des volumes attachés aux unités de grande instance HANA, soit sur une configuration de partages de fichiers réseau (NFS) sur une machine virtuelle Azure. Dans le deuxième cas, les clients paramètrent une machine virtuelle Linux dans Azure, attachent le stockage Azure à la machine virtuelle et partagent le stockage dans celle-ci par le biais d’un serveur NFS configuré. Si vous effectuez la sauvegarde sur des volumes directement attachés aux unités de grande instance HANA, vous devez copier les sauvegardes dans un compte de stockage Azure (après avoir configuré une machine virtuelle Azure qui exporte les partages NFS en fonction du stockage Azure) ou utiliser un coffre de sauvegarde Azure ou un stockage froid Azure. 

   Une autre possibilité consiste à recourir à un outil de protection des données tiers pour stocker les sauvegardes une fois ces dernières copiées dans un compte de stockage Azure. L’utilisation de vos propres méthodes de sauvegarde peut également se révéler nécessaire pour les données qui doivent être stockées sur de plus longues périodes à des fins de conformité et d’audit. Dans tous les cas, les sauvegardes sont copiées dans des partages NFS représentés par le biais d’une machine virtuelle et d’un stockage Azure.

- Utilisation des fonctionnalités de sauvegarde et de restauration fournies par l’infrastructure sous-jacente de SAP HANA sur Azure (grandes instances). Cette option répond à la nécessité d’effectuer des sauvegardes et des restaurations rapides. Le reste de cette section traite des fonctionnalités de sauvegarde et de restauration offertes avec les grandes instances HANA, ainsi que leur relation avec les fonctionnalités de récupération d’urgence offertes par les grandes instances HANA.

> [!NOTE]
> La technologie de capture instantanée utilisée par l’infrastructure sous-jacente des grandes instances HANA a une dépendance sur les captures instantanées SAP HANA. À ce jour, les captures instantanées SAP HANA ne fonctionnent pas conjointement avec plusieurs locataires de conteneurs de bases de données SAP HANA mutualisées. C’est pourquoi cette méthode de sauvegarde ne peut pas être utilisée quand vous déployez plusieurs locataires dans des conteneurs de bases de données SAP HANA mutualisées. Si seul un locataire est déployé, les captures instantanées de SAP HANA fonctionnent.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilisation des captures instantanées de stockage de SAP HANA sur Azure (grandes instances)

L’infrastructure de stockage qui sous-tend SAP HANA sur Azure (grandes instances) prend en charge les captures instantanées de volumes de stockage. La prise en charge de la sauvegarde et de la restauration d’un volume est régie par les règles suivantes :

- Au lieu d’exécuter des sauvegardes de base de données complètes, le système procède à de fréquentes captures instantanées des volumes de stockage.
- Lorsqu’une capture instantanée est déclenchée sur les volumes /hana/data, /hana/log et /hana/shared (/usr/sap compris), la capture instantanée de stockage lance une capture instantanée SAP HANA avant d’exécuter la capture instantanée de stockage. Cette capture instantanée SAP HANA est le point d’installation d’éventuelles restaurations de journaux après récupération de la capture instantanée de stockage.
- Une fois la capture instantanée de stockage exécutée avec succès, la capture instantanée SAP HANA est supprimée.
- Les sauvegardes de fichier journal sont effectuées fréquemment et stockées dans le volume /hana/logbackups ou dans Azure. Vous pouvez déclencher une capture instantanée séparément pour le volume /hana/logbackups contenant les sauvegardes de fichier journal. Dans ce cas, vous n’avez pas besoin d’exécuter une capture instantanée HANA.
- Si vous devez restaurer la base de données à un point spécifique dans le temps, contactez le support technique de Microsoft Azure (pour une interruption de production) ou l’équipe de gestion des services SAP HANA sur Azure pour solliciter une restauration à partir d’une capture instantanée de stockage donnée (par exemple, une restauration planifiée d’un système bac à sable à son état d’origine).
- La capture instantanée SAP HANA incluse dans la capture instantanée de stockage constitue un point de décalage pour l’application des sauvegardes de fichier journal qui ont été exécutées et stockées après la création de la capture instantanée de stockage.
- Ces sauvegardes de fichier journal sont effectuées pour restaurer la base de données à un point spécifique dans le temps.

Vous avez la possibilité d’effectuer des captures instantanées de stockage ciblant trois classes de volumes différentes :

- Une capture instantanée combinée sur les volumes /hana/data et /hana/shared (/usr/sap compris). Cette capture instantanée nécessite la création d’une capture instantanée de SAP HANA en préparation de la capture instantanée de stockage. La capture instantanée de SAP HANA permet de garantir que la base de données est dans un état cohérent du point de vue du stockage. Et cela pour le processus de restauration qui est un point de configuration.
- Une capture instantanée distincte sur /hana/logbackups.
- Une partition SE.


### <a name="storage-snapshot-considerations"></a>Considérations relatives aux captures instantanées de stockage

>[!NOTE]
>Les captures instantanées de stockage consomment un espace de stockage qui a été alloué aux unités de grande instance HANA. Vous devez donc prendre en compte les aspects suivants en termes de planification des captures instantanées de stockage et de conservation d’un nombre précis de captures instantanées de stockage. 

Les mécanismes spécifiques des captures instantanées de stockage de SAP HANA sur Azure (grandes instances) incluent :

- Une capture instantanée de stockage spécifique (au moment précis de sa création) consomme peu de stockage.
- Étant donné que les données changent et que le contenu des fichiers de données SAP HANA évolue sur le volume de stockage, la capture instantanée doit stocker le contenu des blocs d’origine, ainsi que les modifications de données.
- La taille de la capture instantanée de stockage augmente en conséquence. Plus la durée d’existence de la capture instantanée est longue, plus la capture instantanée de stockage devient volumineuse.
- Plus le volume de base de données SAP HANA fait l’objet de modifications pendant la durée de vie d’une capture instantanée de stockage, plus la capture instantanée de stockage consomme d’espace.

SAP HANA sur Azure (grandes instances) est fourni avec des tailles de volume fixes pour les volumes de données et de journaux SAP HANA. Effectuer des captures instantanées de ces volumes consomme votre espace de volume. Vous devez donc planifier les captures instantanées de stockage, surveiller la consommation de l’espace dédié aux volumes de stockage et gérer le nombre de captures instantanées stockées. Vous pouvez désactiver les captures instantanées de stockage lorsque vous importez des masses de données ou apportez d’autres modifications importantes à la base de données HANA. 


Les sections ci-après fournissent diverses informations concernant l’exécution de ces captures instantanées, y compris des recommandations générales :

- Bien que le matériel puisse prendre en charge 255 captures instantanées par volume, il est vivement recommandé de rester bien en deçà de ce nombre.
- Avant d’effectuer des captures instantanées du stockage, surveillez l’espace libre.
- Réduisez le nombre de captures instantanées de stockage en fonction de l’espace libre. Vous pouvez réduire le nombre de captures instantanées à conserver ou étendre les volumes. Vous pouvez commander du stockage supplémentaire par unités d’un téraoctet.
- Lorsque vous exécutez des tâches telles que le déplacement de données dans SAP HANA avec des outils de migration de la plateforme SAP (R3load) ou la restauration des bases de données SAP HANA à partir de sauvegardes, désactivez les captures instantanées de stockage sur le volume /hana/data. 
- Dans le cadre de réorganisations plus vastes des tables SAP HANA, les captures instantanées de stockage doivent être évitées dans la mesure du possible.
- Les captures instantanées de stockage constituent un prérequis pour tirer partie des fonctionnalités de récupération d’urgence de SAP HANA sur Azure (grandes instances).

### <a name="prerequisites-for-leveraging-self-service-storage-snapshots"></a>Conditions préalables pour tirer profit des captures instantanées de stockage en libre-service

Pour vous assurer que le script de captures instantanées s’exécute correctement, vérifiez que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes instances HANA. Perl est préinstallé sur votre unité de grande instance HANA. Utilisez la commande ci-dessous pour vérifier la version perl :

`perl -v`

![L’exécution de cette commande copie la clé publique.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="setting-up-storage-snapshots"></a>Configuration des captures instantanées de stockage

Les étapes pour configurer des captures instantanées de stockage avec de grandes instances HANA sont les suivantes :
1. Assurez-vous que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes instances HANA.
2. Modifiez /etc/ssh/ssh\_config en ajoutant la ligne _MACs hmac-sha1_.
3. Créez un compte d’utilisateur de sauvegarde SAP HANA sur le nœud principal de chaque instance SAP HANA que vous exécutez, le cas échéant.
4. Installez le client SAP HANA HDB sur tous les serveurs de grandes instances SAP HANA.
5. Sur le premier serveur de grandes instances SAP HANA de chaque région, créez une clé publique pour accéder à l’infrastructure de stockage sous-jacente qui contrôle la création de captures instantanées.
6. Copiez les scripts et le fichier de configuration de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) dans l’emplacement de **hdbsql** dans l’installation de SAP HANA.
7. Modifiez le fichier HANABackupDetails.txt selon vos besoins pour les spécifications client appropriées.

### <a name="consideration-for-mcod-scenarios"></a>Considération des scénarios MCOD
Si vous exécutez un [scénario MCOD](https://launchpad.support.sap.com/#/notes/1681092) de plusieurs instances SAP HANA sur une unité HANA de grande Instance, vous obtenez des volumes de stockage distincts approvisionnés pour chacune des différentes instances SAP HANA. Dans la version actuelle de l’automatisation de la capture instantanée en libre-service, il est impossible d’initier des captures instantanées distinctes sur chaque ID de sécurité. La fonctionnalité fournie vérifie les instances SAP HANA du serveur inscrites dans le fichier de configuration (voir ci-après) et exécute une capture instantanée simultanée des volumes de toutes les instances enregistrées sur l’unité.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Étape 1 : Installer le client SAP HANA HDB

Le système d’exploitation Linux installé dans SAP HANA sur Azure (grandes instances) inclut les dossiers et scripts nécessaires pour l’exécution de captures instantanées de stockage SAP HANA à des fins de sauvegarde et de récupération d’urgence. Vérifiez s’il existe des versions plus récentes dans [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). La version commerciale la plus récente des scripts est 3.x. Des scripts différents peuvent avoir différentes versions mineures dans la même version majeure.

>[!IMPORTANT]
>Le passage de la version 2.1 des scripts à la version 3.0 modifie la structure du fichier de configuration ainsi que certaines syntaxes des scripts. Consultez les interventions dans les sections spécifiques. 

Toutefois, il vous incombe d’installer le client SAP HANA HDB sur les unités de grande instance HANA quand vous installez SAP HANA. (Microsoft n’installe ni le client HDB, ni SAP HANA.)

### <a name="step-2-change-the-etcsshsshconfig"></a>Étape 2 : Modifier /etc/ssh/ssh\_config

Modifiez `/etc/ssh/ssh_config` en ajoutant la ligne _MACs hmac-sha1_ comme indiqué ici :
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Étape 3 : Créer une clé publique

Pour activer l’accès aux interfaces de capture instantanée de stockage de votre locataire de grandes instances HANA, vous devez établir une connexion par le biais d’une clé publique. Sur le premier serveur SAP HANA sur Azure (grandes instances) de votre locataire, créez une clé publique servant à accéder à l’infrastructure de stockage pour vous permettre de créer des captures instantanées. Cette clé publique garantit qu’aucun mot de passe n’est requis pour la connexion aux interfaces de capture instantanée de stockage et qu’il n’est pas nécessaire de conserver les informations d’identification par mot de passe. Sur le système d’exploitation Linux du serveur de grandes instances SAP HANA, exécutez la commande ci-après pour générer la clé publique :
```
  ssh-keygen –t dsa –b 1024
```
Le nouvel emplacement est **_/root/.ssh/id\_dsa.pub**. N’entrez pas un mot de passe réel, sans quoi vous devriez l’entrer chaque fois que vous vous connectez. À la place, appuyez deux fois sur **Entrée** pour supprimer l’exigence de saisie d’un mot de passe au moment de la connexion.

Assurez-vous que la clé publique a été corrigée comme prévu en remplaçant les dossiers par **/root/.ssh/**, puis en exécutant la commande `ls`. Si la clé est présente, vous pouvez la copier en exécutant la commande suivante :

![L’exécution de cette commande copie la clé publique.](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

À ce stade, contactez l’équipe de gestion des services SAP HANA sur Azure et fournissez-lui la clé publique. Le représentant du service utilisera la clé publique pour l’inscrire dans l’infrastructure de stockage sous-jacente qui est définie pour votre locataire de grandes instances HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Étape 4 : Créer un compte d’utilisateur SAP HANA

Pour lancer la création de captures instantanées SAP HANA, vous devez créer dans SAP HANA un compte d’utilisateur que les scripts de capture instantanée de stockage peuvent utiliser. Créez un compte d’utilisateur SAP HANA dans SAP HANA Studio à cette fin. L’utilisateur doit être créé sous le SYSTEMDB et PAS sous la base de données SID. Ce compte doit disposer des privilèges **Backup Admin** (administration des sauvegardes) et **Catalog Read** (lecture du catalogue). Dans cet exemple, le nom d’utilisateur est **SCADMIN**. Le nom du compte d’utilisateur créé dans HANA Studio respecte la casse. Assurez-vous de sélectionner **No** (Non) lorsqu’il vous est demandé si l’utilisateur doit modifier le mot de passe à la prochaine connexion.

![Création d’un utilisateur dans HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

En cas de déploiements MCOD avec plusieurs instances de SAP HANA sur une unité, cette étape doit être répétée pour chaque instance SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Étape 5 : Autoriser le compte d’utilisateur SAP HANA

Dans cette étape, vous autorisez le compte d’utilisateur SAP HANA que vous avez créé, afin que les scripts n’aient pas besoin d’envoyer des mots de passe au moment de l’exécution. La commande SAP HANA `hdbuserstore` permet la création d’une clé utilisateur SAP HANA, stockée sur un ou plusieurs nœuds SAP HANA. Cette clé utilisateur permet à l’utilisateur d’accéder à SAP HANA sans avoir à gérer les mots de passe à partir du processus d’utilisation de scripts décrit plus loin.

>[!IMPORTANT]
>Exécutez la commande suivante en tant que `root`. Dans le cas contraire, le script ne fonctionne pas correctement.

Entrez La commande `hdbuserstore` comme suit :

**Pour la configuration HANA non MDC**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Pour la configuration HANA MDC**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

Dans l’exemple ci-après, l’utilisateur est **SCADMIN01**, le nom d’hôte est **lhanad01** et le numéro d’instance est **01** :
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Si vous utilisez un déploiement HANA MCOD avec plusieurs instances de SAP HANA sur une unité, l’étape doit être répétée pour chaque instance SAP HANA et l’utilisateur de sauvegarde associé à l’unité.

Si vous avez une configuration SAP HANA avec montée en puissance parallèle, vous devez gérer tous les scripts à partir d’un seul serveur. Dans cet exemple, la clé SAP HANA **SCADMIN01** doit être modifiée pour chaque hôte de façon à refléter l’hôte qui est associé à la clé. Modifiez le compte de sauvegarde SAP HANA avec le numéro d’instance de la base de données HANA. La clé doit disposer de privilèges administratifs sur l’hôte auquel elle est affectée, et l’utilisateur de sauvegarde pour les configurations avec montée en puissance parallèle doit disposer de droits d’accès à toutes les instances SAP HANA. En supposant que les trois nœuds de montée en puissance parallèle sont nommés **lhanad01**, **lhanad02** et **lhanad03**, la séquence de commandes ressemble à ceci :

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Étape 6 : Obtenir les scripts de capture instantanée, configurer les captures instantanées et tester la connectivité et la configuration

Téléchargez la version la plus récente des scripts depuis [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Copiez les scripts téléchargés et le fichier texte dans le répertoire de travail pour **hdbsql**. Pour les installations HANA actuelles, le répertoire ressemble à /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

En tant que traitement avec les scripts perl : 

- Ne modifiez jamais les scripts sauf indication contraire par les opérations de Microsoft.
- Lorsque vous êtes invité à modifier le script ou un fichier de paramètres, utilisez toujours l’éditeur de texte Linux tels que « vi » et pas les éditeurs Windows, comme le bloc-notes. À L’utilisation de l’éditeur Windows peut altérer le format de fichier.
- Utilisez toujours les derniers scripts. Vous pouvez télécharger la dernière version à partir de GitHub.
- Utilisez la même version de scripts dans le paysage.
- Testez les scripts et familiarisez-vous avec les paramètres requis et la sortie du script avant de les utiliser directement dans le système de production.
- Ne modifiez pas le nom du point de montage du serveur approvisionné par les opérations de Microsoft. Ces scripts s’appuient sur ces points de montage standard disponibles pour une exécution réussie.


L’objectif des différents scripts et fichiers est le suivant :

- **Azure\_hana\_backup.pl** : planifiez ce script avec cron pour exécuter des captures instantanées de stockage sur les volumes HANA données et partagées, le volume /hana/logbackups ou le système d’exploitation.
- **azure\_hana\_replication\_status.pl** : ce script fournit des informations de base sur l’état de la réplication depuis le site de production vers le site de récupération d’urgence. Le script s’assure que la réplication est bien effectuée et affiche les tailles des éléments répliqués. Il fournit également des conseils si une réplication est trop longue ou si le lien est rompu.
- **azure\_hana\_snapshot\_details.pl** : ce script fournit une liste d’informations de base sur toutes les captures instantanées, par volume, qui existent dans votre environnement. Il peut être exécuté sur le serveur principal ou sur une unité de serveur à l’emplacement de la récupération d’urgence. Le script fournit les informations ventilées suivantes pour chaque volume contenant des captures instantanées :
   * Taille de toutes les captures instantanées dans un volume
   * Chaque capture instantanée dans ce volume inclut les informations suivantes : 
      - Nom de la capture instantanée 
      - Heure de création 
      - Taille de la capture instantanée
      - Fréquence de la capture instantanée
      - ID de sauvegarde HANA associé à cette capture instantanée, le cas échéant
- **azure\_hana\_snapshot\_delete.pl** : ce script supprime une capture instantanée de stockage ou un ensemble de captures instantanées. Vous pouvez utiliser l’ID de sauvegarde SAP HANA tel que figurant dans HANA Studio ou le nom de la capture instantanée de stockage. L’ID de sauvegarde est uniquement lié aux captures instantanées créées pour les volumes data/log/shared HANA. Dans le cas contraire, si l’ID de la capture instantanée est entré, il cherche toutes les captures instantanées correspondant à cet ID.  
- **testHANAConnection.pl** : ce script teste la connexion à l’instance SAP HANA et est requis pour configurer les captures instantanées de stockage.
- **testStorageSnapshotConnection.pl** : ce script a deux objectifs. Dans un premier temps, il s’assure que l’unité de grande instance HANA qui exécute les scripts a accès à la machine virtuelle de stockage assignée et à l’interface de capture instantanée de stockage de vos grandes instances HANA. Il crée ensuite une capture instantanée temporaire pour l’instance HANA que vous testez. Vous devez exécuter ce script pour chaque instance HANA sur un serveur afin de vérifier que les scripts de sauvegarde fonctionnent comme prévu.
- **removeTestStorageSnapshot.pl** : ce script supprime la capture instantanée de test créée avec le script **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_dr\_failover.pl** : script visant à initier un basculement de récupération d’urgence dans une autre région. Le script doit être exécuté sur l’unité HANA de grande Instance dans la région de récupération d’urgence. Ou l’unité sur laquelle vous souhaitez basculer. Ce script arrête la réplication du stockage du côté principal au côté secondaire, restaure la dernière capture instantanée sur les volumes de récupération d’urgence et fournit les points de montage des volumes pour la récupération d’urgence  
- **azure\_hana\_test\_dr\_failover.pl** : script pour exécuter un basculement test dans le site de récupération d’urgence. Contrairement au script azure_hana_dr_failover.pl, cette exécution n’interrompt pas la réplication de stockage du principal à secondaire. À la place les clones des volumes de stockage répliqués sont créés sur le côté de la récupération d’urgence et les points de montage des volumes clonés sont fournis. 
- **HANABackupCustomerDetails.txt** : il s’agit d’un fichier de configuration modifiable que vous devez modifier en fonction de votre configuration SAP HANA. Le fichier HANABackupCustomerDetails.txt est le fichier de contrôle et de configuration pour le script qui exécute les captures instantanées de stockage. Modifiez le fichier en fonction de vos besoins et de votre configuration. Vous devez avoir reçu les informations **Storage Backup Name (Nom de sauvegarde du stockage)** et **Storage IP Address (Adresse IP de stockage)** de la part de l’équipe de gestion des services SAP HANA sur Azure au moment où vos instances ont été déployées. Vous ne pouvez pas modifier la séquence, l’ordre ou l’espacement des variables dans ce fichier. Dans le cas contraire, les scripts ne s’exécuteront pas correctement. En outre, vous avez reçu l’adresse IP du nœud de montée en puissance ou du nœud principal (s’il est configuré pour la montée en puissance parallèle) de l’équipe de gestion des services SAP HANA sur Azure. Vous connaissez également le numéro d’instance HANA que vous avez obtenu lors de l’installation de SAP HANA. Vous devez à présent ajouter un nom de sauvegarde au fichier de configuration.

Pour un déploiement scale-up ou scale-out, le fichier de configuration ressemble à l’exemple suivant une fois que vous avez renseigné le nom du serveur de l’unité de grande instance HANA et l’adresse IP du serveur. En cas de réplication du système SAP HANA, utilisez l’adresse IP virtuelle de la configuration de la réplication du système HANA. Renseignez tous les champs nécessaires pour chaque SID SAP HANA que vous souhaitez sauvegarder ou restaurer. Vous pouvez également commenter les lignes d’instances que vous ne souhaitez pas sauvegarder pendant une période de temps en ajoutant un « # » en face d’un champ obligatoire. Vous n’avez pas non plus besoin de saisir toutes les Instances de SAP HANA qui se trouvent sur un serveur s’il n’est pas nécessaire de sauvegarder ou récupérer cette instance particulière. Le format doit être conservé pour tous les champs, sinon tous les scripts présentent un message d’erreur et le script se termine. Vous pouvez toutefois supprimer des lignes obligatoires supplémentaires de toutes les informations détaillées SID que vous n’utilisez pas après l’utilisation de la dernière instance SAP HANA.  Toutes les lignes doivent être renseignées, commentées ou supprimées.

>[!IMPORTANT]
>La structure du fichier a changé avec le déplacement de la version 2.1 vers la version 3.0. Si vous souhaitez utiliser les scripts de la version 3.0, vous devez adapter la structure du fichier de configuration. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Pour chaque instance que vous configurez sur l’unité de grande Instance HANA ou pour la configuration scale-out, vous devez définir les données comme suit

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Pour les configurations scale-out et de réplication du système HANA, il est recommandé de répéter cette configuration sur chacun des nœuds. Cette mesure permet de vous assurer qu’en cas d’échec, les sauvegardes et la réplication éventuelle de stockage fonctionnent toujours.   

Après avoir placé toutes les données de configuration dans le fichier HANABackupCustomerDetails.txt, vous devez vérifier si les configurations concernant les données d’instance HANA sont correctes à l’aide du script `testHANAConnection.pl`. Ce script est indépendant d’une configuration SAP HANA avec montée en puissance ou montée en puissance parallèle.

```
testHANAConnection.pl
```

Si vous avez une configuration SAP HANA avec montée en puissance parallèle, vérifiez que l’instance principale HANA a accès à tous les serveurs et instances HANA requis. Il n’existe aucun paramètre pour le script de test, mais vous devez ajouter vos données au fichier de configuration HANABackupCustomerDetails.txt pour que le script s’exécute correctement. Étant donné que seuls les codes d’erreur de commande d’environnement sont renvoyés, le script n’est pas en mesure de vérifier l’absence d’erreurs pour chaque instance. Même dans ce cas, le script fournit des commentaires utiles vous permettant de revérifier.

Pour exécuter le script, entrez la commande suivante :
```
 ./testHANAConnection.pl
```
Si le script parvient à obtenir le statut de l’instance HANA, il renvoie un message indiquant que la connexion HANA a réussi.


L’étape de test suivante consiste à vérifier la connectivité au stockage en fonction des données que vous avez insérées dans le fichier de configuration HANABackupCustomerDetails.txt, puis à exécuter une capture instantanée de test. Avant d’exécuter le script `azure_hana_backup.pl`, vous devez exécuter ce test. Si un volume ne comporte aucune capture instantanée, il est impossible de faire la différence entre un volume vide et un échec d’obtention des détails de capture instantanée par le protocole SSH. C’est la raison pour laquelle le script exécute deux étapes :

- Il vérifie que les interfaces et la machine virtuelle de stockage du locataire sont accessibles aux scripts qui doivent exécuter des captures instantanées.
- Il crée une capture instantanée de test, ou fictive, pour chaque volume par instance HANA.

L’instance HANA est donc incluse en tant qu’argument. En cas d’échec de l’exécution, vous ne pouvez pas vérifier l’absence d’erreurs au niveau de la connexion du stockage. Même dans ce cas, le script fournit des indications utiles.
Exécutez la séquence de commandes pour effectuer ce test :

```
ssh <StorageUserName>@<StorageIP>
```

Le nom d’utilisateur de stockage et l’adresse IP de stockage vous sont fournis à la réception de l’unité de grande Instance HANA.

En deuxième étape, exécutez le script de test comme suit :
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
Le script tente ensuite de se connecter au stockage à l’aide de la clé publique fournie lors des étapes de configuration précédentes et avec les données configurées dans le fichier HANABackupCustomerDetails.txt. Si la connexion s’effectue correctement, le contenu suivant apparaît :

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

En cas de problème lors de la connexion à la console de stockage, la sortie ressemble à ceci :

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Une fois établie la connexion aux interfaces de machine virtuelle de stockage, le script passe à la phase 2 et crée une capture instantanée de test, comme indiqué ici pour une configuration de SAP HANA à trois nœuds avec montée en puissance parallèle :

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Si la capture instantanée de test a été correctement exécutée avec le script, vous pouvez passer à la configuration des captures instantanées de stockage réelles. Si elle a échoué, identifiez le problème avant de poursuivre. La capture instantanée de test doit être conservée jusqu’à ce que les premières captures instantanées réelles soient effectuées.


### <a name="step-7-perform-snapshots"></a>Étape 7 : Effectuer des captures instantanées

Une fois réalisées toutes les étapes de préparation, vous pouvez commencer à configurer la configuration de capture instantanée de stockage réelle. Le script à planifier fonctionne avec les configurations de SAP HANA avec montée en puissance et montée en puissance parallèle. Pour l’exécution périodique et régulière de la planification du script de sauvegarde, le script via cron. 

Il est possible de créer trois types de sauvegardes de captures instantanées :
- **HANA** : sauvegarde de captures instantanées combinées dans laquelle les volumes contenant /hana/data et /hana/shared (qui contient également /usr/sap) sont couverts par la capture instantanée coordonnée. Une restauration de fichier unique est possible à partir de cette capture instantanée.
- **logs** : sauvegarde de capture instantanée du volume/hana/logbackups. Aucune capture instantanée HANA n’est déclenchée pour exécuter cette capture instantanée de stockage. Ce volume de stockage est le volume destiné à contenir les sauvegardes de fichier journal SAP HANA, qui sont effectuées plus fréquemment afin de limiter la croissance du journal et d’éviter toute perte de données. Une restauration de fichier unique est possible à partir de cette capture instantanée. Vous ne devez pas réduire la fréquence à moins de trois minutes.
- **boot** : capture instantanée du volume qui contient le numéro d’unité logique de démarrage de la grande instance HANA. Cette sauvegarde de capture instantanée est uniquement possible avec les références SKU de type I des grandes instances HANA. Vous ne pouvez pas effectuer de restaurations de fichier unique à partir de la capture instantanée du volume qui contient le numéro d’unité logique de démarrage.


>[!NOTE]
> La syntaxe d’appel de ces trois types de captures instantanées différents a été modifiée lors du passage aux scripts de la version 3.0, qui prend en charge des déploiements MCOD. Il n’est plus utile de spécifier le SID HANA d’une instance. Vous devez vous assurer que la ou les instances SAP HANA d’une unité sont configurées dans le fichier de configuration **HANABackupCustomerDetails.txt**.

>[!NOTE]
> Lorsque vous exécutez le script pour la première fois, il peut afficher des erreurs inattendues sur l’environnement multi SID. Réexécuter simplement le script et le problème devrait être résolu.



La nouvelle syntaxe d’appel pour l’exécution de captures instantanées de stockage avec le script **azure_hana_backup.pl** ressemble à :

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Les détails des paramètres sont les suivants : 

- Le premier paramètre définit le type de la sauvegarde de captures instantanées. Les valeurs autorisées sont **hana**, **logs** et **boot**. 
- Le paramètre  **<HANA Large Instance Type>**  est nécessaire pour les sauvegardes de volume de démarrage uniquement. Il existe deux valeurs valides avec « TypeI » ou « TypeII » en fonction de l’unité de grande instance HANA. Pour connaître le « Type » de votre unité, veuillez lire cette [documentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Le paramètre **<snapshot_prefix>** est le nom de la capture instantanée ou de la sauvegarde du type de capture instantanée. Il a deux objectifs. Il permet d’une part de lui attribuer un nom pour savoir à quoi correspondent ces captures instantanées. Le script Azure \_hana\_backup.pl détermine d’autre part le nombre de captures instantanées de stockage qui sont conservées sous cette étiquette spécifique. Si vous planifiez deux sauvegardes de captures instantanées de stockage du même type (par exemple, **hana**), avec deux étiquettes différentes, et définissez que 30 captures instantanées doivent être conservées pour chacune d’elles, vous obtiendrez au total 60 captures instantanées de stockage des volumes concernés. 
- Le paramètre **< snapshot_frequency >** est réservé pour de futurs développements l’évolution et n’a aucun impact. Nous vous recommandons de le définir maintenant à « 3 min » lors de l’exécution des sauvegardes du journal de type et à « 15 min », lors de l’exécution d’autres types de sauvegardes
- Le paramètre **<number of snapshots retained>** définit la rétention des captures instantanées indirectement en définissant le nombre de captures instantanées avec le même préfixe de capture instantanée (étiquette) à conserver. Ce paramètre est important pour une exécution planifiée par le biais de cron. Si le nombre de captures instantanées avec le même snapshot_prefix dépasse le nombre indiqué par ce paramètre, la capture instantanée la plus ancienne est supprimée avant l’exécution d’une nouvelle capture instantanée de stockage.

Dans le cas d’une montée en puissance parallèle, le script effectue certaines vérifications supplémentaires pour contrôler que tous les serveurs HANA sont accessibles Le script vérifie également que toutes les instances HANA renvoient le statut des instances approprié, avant de procéder à la création d’une capture instantanée SAP HANA. suivie d’une capture instantanée de stockage.

L’exécution du script `azure_hana_backup.pl` crée la capture instantanée de stockage à travers les trois phases suivantes :

1. Exécution d’une capture instantanée SAP HANA
2. Exécution d’une capture instantanée de stockage
3. Suppression de la capture instantanée SAP HANA qui a été créée avant l’exécution de la capture instantanée de stockage

Pour exécuter le script, vous devez l’appeler depuis le dossier exécutable HDB dans lequel il a été copié. 

La période de rétention est administrée avec le nombre de captures instantanées soumis en tant que paramètre lorsque vous exécutez le script. La durée couverte par les captures instantanées de stockage dépend de deux facteurs : la période d’exécution et le nombre de captures instantanées soumises en tant que paramètre au moment de l’exécution du script. Si le nombre de captures instantanées conservées dépasse le nombre spécifié en tant que paramètre dans l’appel du script, la plus ancienne capture instantanée de stockage portant la même étiquette est supprimée avant l’exécution d’une nouvelle capture instantanée. Le nombre que vous indiquez en tant que dernier paramètre de l’appel correspond au nombre que vous pouvez utiliser pour contrôler le nombre de captures instantanées qui sont conservées. Ce nombre vous permet également de contrôler indirectement l’espace disque utilisé pour les captures instantanées. 

> [!NOTE]
>Dès que vous modifiez l’étiquette, le comptage redémarre. Cela signifie que vous devez être précis dans l’étiquetage afin que vos captures instantanées ne soient pas accidentellement supprimées.

### <a name="snapshot-strategies"></a>Stratégies de capture instantanée
La fréquence des captures instantanées pour les différents types varie selon que vous utilisez ou non la fonctionnalité de récupération d’urgence des grandes instances HANA. Étant donné que la fonctionnalité de récupération d’urgence des grandes instances HANA repose sur les captures instantanées de stockage, certaines recommandations spéciales peuvent s’appliquer en termes de fréquence et de périodes d’exécution de ces captures instantanées de stockage. 

Dans les considérations et recommandations ci-après, nous supposons que vous n’utilisez *pas* la fonctionnalité de récupération d’urgence proposée par les grandes instances HANA. À la place, vous utilisez les captures instantanées de stockage comme un moyen d’obtenir des sauvegardes et de pouvoir fournir une récupération jusqu’à une date et heure couvrant les 30 derniers jours. Étant donné les limites liées au nombre de captures instantanées et à l’espace, les clients ont pris en compte les exigences suivantes :

- Temps de récupération pour la récupération jusqu’à une date et heure.
- Espace utilisé.
- L’objectif de point de récupération et l’objectif de délai de récupération pour une récupération d’urgence potentielle.
- Exécution éventuelles de sauvegardes de base de données complètes HANA pour les disques. Chaque fois qu’une sauvegarde de base de données complète est effectuée pour les disques ou pour l’interface **backint**, l’exécution des captures instantanées de stockage échoue. Si vous prévoyez d’exécuter des sauvegardes de base de données complètes reposant sur des captures instantanées de stockage, assurez-vous que l’exécution des captures instantanées de stockage est désactivée pendant cette période.
- Le nombre de captures instantanées par volume est limité à 255.


Pour les clients qui n’utilisent pas la fonctionnalité de récupération d’urgence des grandes instances HANA, la période de capture instantanée est moins fréquente. Dans ces cas, les clients effectuent les captures instantanées combinées sur /hana/data et /hana/shared (/usr/sap compris) toutes les 12 ou 24 heures et qu’ils conservent ces captures instantanées de manière à couvrir un mois complet. Il en va de même pour les captures instantanées du volume de sauvegarde de fichier journal. Toutefois, les sauvegardes de fichier journal SAP HANA sur le volume de sauvegarde de fichier journal sont exécutées toutes les 5 à 15 minutes.

Nous vous encourageons à effectuer des captures instantanées de stockage planifiées à l’aide de cron. Il est également conseillé d’utiliser le même script pour la totalité des sauvegardes et des besoins en matière de récupération d’urgence. Modifiez les entrées du script en fonction des différentes heures de sauvegarde demandées. Ces captures instantanées sont toutes planifiées différemment dans cron selon leur durée d’exécution : toutes les heures, toutes les 12 heures, une fois par jour ou une fois par semaine. 

Voici un exemple d’une planification cron dans /etc/crontab :
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
Dans l’exemple précédent, une capture instantanée horaire combinée couvre les volumes contenant les emplacements /hana/data et /hana/shared (/usr/sap compris). Ce type de capture instantanée doit être utilisé pour une récupération jusqu’à une date et heure plus rapide dans la période des deux derniers jours. En outre, il existe une capture instantanée quotidienne sur ces volumes. Vous avez ainsi deux jours couverts par les captures instantanées horaires, ainsi que quatre semaines couvertes par les captures instantanées quotidiennes. En outre, le volume de sauvegarde de fichier journal est sauvegardé une fois par jour. Ces sauvegardes sont conservées pendant quatre semaines également. Comme le montre la troisième ligne de crontab, la sauvegarde du journal des transactions HANA est planifiée pour être exécutée toutes les cinq minutes. Les minutes de début des différents travaux cron qui exécutent des captures instantanées de stockage sont décalées de façon à ce que ces captures instantanées ne soient pas toutes exécutées en même temps à un moment donné. 

Dans l’exemple suivant, vous effectuez une capture instantanée combinée qui couvre les volumes contenant les emplacements /hana/data et /hana/shared (/usr/sap compris) toutes les heures. Vous conservez ces captures instantanées pendant deux jours. Les captures instantanées des volumes de sauvegarde de fichier journal sont exécutées toutes les cinq minutes et sont conservées pendant quatre heures. Une fois encore, la sauvegarde du fichier journal des transactions HANA est planifiée pour s’exécuter toutes les cinq minutes. La capture instantanée du volume de sauvegarde de fichier journal est effectuée dans un délai de deux minutes après le démarrage de la sauvegarde du fichier journal. Dans des circonstances normales, la sauvegarde du fichier journal SAP HANA doit se terminer dans cet intervalle de deux minutes. Une fois encore, le volume contenant le numéro d’unité logique de démarrage est sauvegardé une fois par jour par une capture instantanée de stockage et est conservé pendant quatre semaines.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Le graphique suivant illustre les séquences de l’exemple précédent, à l’exception du numéro d’unité logique de démarrage :

![Relation entre les sauvegardes et les captures instantanées](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA effectue des écritures régulières sur le volume /hana/log pour documenter les modifications validées dans la base de données. Régulièrement, SAP HANA écrit un point de sauvegarde dans le volume /hana/data. Comme spécifié dans crontab, une sauvegarde de fichier journal SAP HANA est exécutée toutes les cinq minutes. Une capture instantanée SAP HANA est également exécutée toutes les heures suite au déclenchement d’une capture instantanée de stockage combinée sur les volumes /hana/data et /hana/shared. Une fois que la capture instantanée HANA a réussi, la capture instantanée de stockage combinée est exécutée. Comme indiqué dans crontab, la capture instantanée de stockage sur le volume /hana/logbackup est exécutée toutes les cinq minutes, environ deux minutes après la sauvegarde de fichier journal HANA.

> [!NOTE]
>Si vous planifiez des sauvegardes de captures instantanées de stockage sur les deux nœuds d’une configuration de la réplication du système HANA, vous devez vous assurer que l’exécution des sauvegardes de captures instantanées entre les deux nœuds ne se chevauche pas. SAP HANA possède une restriction pour traiter une seule capture instantanée HANA à la fois uniquement. Une capture instantanée HANA étant un composant élémentaire d’une sauvegarde de captures instantanées de stockage, vous devez vous assurer que la capture instantanée de stockage sur le nœud principal et secondaire et un éventuel troisième nœud sont, en temps voulu, éloignés l’un de l’autre.


>[!IMPORTANT]
> L’utilisation de captures instantanées de stockage pour les sauvegardes SAP HANA n’est intéressante que lorsque ces captures sont effectuées conjointement avec des sauvegardes de fichier journal SAP HANA. Ces sauvegardes de fichier journal doivent pouvoir couvrir les périodes qui s’écoulent entre les captures instantanées de stockage. 

Si vous vous êtes engagé envers les utilisateurs à assurer une récupération jusqu’à une date et heure remontant à 30 jours, procédez comme suit :

- Dans des cas extrêmes, vous devez pouvoir accéder à une capture instantanée de stockage combinée sur les volumes /hana/data et /hana/shared remontant à 30 jours.
- Veillez à disposer de sauvegardes de fichier journal contiguës qui couvrent la période entre des captures instantanées de stockage combinées. La capture instantanée la plus ancienne du volume de sauvegarde de fichier journal doit par conséquent remonter à 30 jours, ce qui n’est pas le cas si vous copiez les sauvegardes de fichier journal dans un autre partage NFS qui se trouve sur le stockage Azure. Dans ce cas, vous pourriez extraire les anciennes sauvegardes de fichier journal de ce partage NFS.

Pour pouvoir tirer parti des captures instantanées de stockage et de la réplication de stockage éventuelle des sauvegardes de fichier journal, vous devez changer l’emplacement auquel SAP HANA écrit les sauvegardes de fichier journal. Vous pouvez effectuer ce changement dans HANA Studio. Bien que SAP HANA sauvegarde automatiquement des segments de journal complets, vous devez spécifier un intervalle de sauvegarde de fichier journal de manière à ce qu’il soit déterministe. Cela est particulièrement vrai si vous utilisez l’option de récupération d’urgence, car vous souhaitez généralement exécuter des sauvegardes de fichier journal avec une période déterministe. Dans le cas suivant, 15 minutes sont définies comme intervalle de sauvegarde du fichier journal.

![Planifier les sauvegardes de journaux SAP HANA dans SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Vous pouvez choisir d’effectuer des sauvegardes plus fréquemment que toutes les 15 minutes. Un tel paramètre inférieur est fréquemment utilisé conjointement avec la fonctionnalité de récupération d’urgence des grandes instances HANA. Certains clients effectuent des sauvegardes de fichier journal toutes les cinq minutes.  

Si la base de données n’a jamais été sauvegardée, la dernière étape consiste à effectuer une sauvegarde de base de données basée sur un fichier afin de créer une entrée de sauvegarde unique devant figurer dans le catalogue de sauvegarde. Dans le cas contraire, SAP HANA ne peut pas lancer vos sauvegardes de journaux spécifiées.

![Créer une sauvegarde basée sur un fichier pour créer une entrée de sauvegarde unique](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Après avoir exécuté correctement vos premières captures instantanées de stockage, vous pouvez également supprimer la capture instantanée de test qui a été exécutée à l’étape 6. Pour ce faire, exécutez le script `removeTestStorageSnapshot.pl` :
```
./removeTestStorageSnapshot.pl <hana instance>
```

La sortie du script ressemble à cela :
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Surveillance du nombre et de la taille des captures instantanées sur le volume de disque

Sur un volume de stockage spécifique, vous pouvez surveiller le nombre de captures instantanées et l’espace de stockage consommé par ces captures. La commande `ls` n’affiche pas le répertoire des captures instantanées ni les fichiers. Toutefois, la commande du système d’exploitation Linux `du` donne des détails sur ces captures instantanées de stockage, car elles sont stockées sur les mêmes volumes. Vous pouvez utiliser la commande avec les options suivantes :

- `du –sh .snapshot` : fournit le total des captures instantanées au sein du répertoire de captures instantanées.
- `du –sh --max-depth=1` : répertorie toutes les captures instantanées enregistrées dans le dossier **snapshot** et la taille de chaque capture instantanée.
- `du –hc` : fournit la taille totale utilisée par toutes les captures instantanées.

Utilisez ces commandes pour vous assurer que les captures instantanées créées et stockées ne consomment pas la totalité du stockage sur les volumes.

>[!NOTE]
>Les captures instantanées du numéro d’unité logique de démarrage ne sont pas visibles avec les commandes précédentes.

### <a name="getting-details-of-snapshots"></a>Obtention des détails des captures instantanées
Pour obtenir plus d’informations sur les captures instantanées, vous pouvez également utiliser le script `azure_hana_snapshot_details.pl`. Vous pouvez exécuter ce script dans n’importe quel emplacement à la condition qu’un serveur actif se trouve à l’emplacement de la récupération d’urgence. Le script fournit les informations ventilées suivantes pour chaque volume contenant des captures instantanées : 
   * Taille de toutes les captures instantanées dans un volume
   * Chaque capture instantanée dans ce volume inclut les informations suivantes : 
      - Nom de la capture instantanée 
      - Heure de création 
      - Taille de la capture instantanée
      - Fréquence de la capture instantanée
      - ID de sauvegarde HANA associé à cette capture instantanée, le cas échéant

La syntaxe de l’exécution du script ressemble à ce qui suit :

```
./azure_hana_snapshot_details.pl 
```

Le script doit se connecter à l’instance SAP HANA pour récupérer l’ID de sauvegarde HANA. Pour ce faire, le fichier de configuration HANABackupCustomerDetails.txt doit être correctement défini. Une sortie de deux captures instantanées sur un volume pourrait ressembler à ce qui suit :

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>Restauration au niveau du fichier à partir d’une capture instantanée de stockage
Pour les types de capture instantanée hana et logs, vous pouvez accéder aux captures instantanées directement sur les volumes dans le répertoire **.snapshot**. Il existe un sous-répertoire pour chaque capture instantanée. Vous devez être en mesure de copier chaque fichier qui est couvert par la capture instantanée dans l’état où il se trouvait au moment de celle-ci à partir de ce sous-répertoire dans la structure de répertoire réelle.

>[!NOTE]
>La restauration d’un fichier unique ne fonctionne pas pour les captures instantanées du numéro d’unité logique de démarrage indépendant du type d’unités de grande instance HANA. Le répertoire **.snapshot** n’est pas exposé dans le numéro d’unité logique de démarrage. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Réduction du nombre de captures instantanées sur un serveur

Comme expliqué précédemment, vous pouvez réduire le nombre de captures instantanées stockées portant une certaine étiquette. Les deux derniers paramètres de la commande permettant de lancer une capture instantanée correspondent à l’étiquette et au nombre de captures instantanées à conserver.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

Dans l’exemple précédent, l’étiquette de la capture instantanée est **dailyhana** et le nombre de captures instantanées dotées de cette étiquette qui doivent être conservées est **28**. Afin de faire face au problème de la consommation d’espace disque, vous pouvez vouloir réduire le nombre de captures instantanées stockées. Un moyen simple de réduire le nombre de captures instantanées à 15, par exemple, consiste à exécuter le script en définissant le dernier paramètre sur la valeur **15** :

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Si vous exécutez le script défini avec ce paramètre, le nombre total de captures instantanées, y compris la nouvelle capture instantanée de stockage, est 15. Les 15 captures instantanées les plus récentes sont conservées, tandis que les 15 captures instantanées plus anciennes sont supprimées.

 >[!NOTE]
 > Ce script réduit le nombre de captures instantanées uniquement s’il existe des captures instantanées qui datent de plus d’une heure. Le script ne supprime pas les captures instantanées datant de moins d’une heure. Ces restrictions sont associées à la fonctionnalité de récupération d’urgence facultative offerte.

Si vous ne souhaitez plus conserver un ensemble de captures instantanées portant une étiquette de sauvegarde spécifique **hanadaily** dans les exemples de syntaxe, vous pouvez exécuter le script en définissant **0** comme valeur de rétention Avec ce paramètre de rétention toutes les captures instantanées correspondant à cette étiquette sont supprimées. Toutefois, la suppression de toutes les captures instantanées aura une incidence sur les capacités de la fonctionnalité de récupération d’urgence des grandes instances HANA.

Une deuxième possibilité pour supprimer des captures instantanées spécifiques consiste à utiliser le script `azure_hana_snapshot_delete.pl`. Ce script est conçu pour supprimer une capture instantanée ou un ensemble de captures instantanées à l’aide de l’ID de sauvegarde HANA tel que figurant dans HANA Studio ou du nom de la capture instantanée. À l’heure actuelle, l’ID de sauvegarde est uniquement lié aux captures instantanées créées pour le type de capture instantanée **hana**. Les sauvegardes de captures instantanées de type **logs** et **boot** n’effectuent pas de capture instantanée SAP HANA. Ainsi, il n’existe pas d’ID de sauvegarde pour ces captures instantanées. Si le nom de la capture instantanée est entré, le script recherche sur les différents volumes toutes les captures instantanées qui correspondent au nom entré. Appel du script dont vous avez besoin pour spécifier le SID de l’instance HANA. La syntaxe d’appel du script est la suivante :

```
./azure_hana_snapshot_delete.pl <SID>

```

Exécutez le script en tant qu’utilisateur **racine**.

Si vous sélectionnez une capture instantanée, vous pouvez supprimer chaque capture instantanée individuellement. Vous entrez d’abord le volume qui contient la capture instantanée, puis le nom de la capture instantanée. Si la capture instantanée existe sur ce volume et qu’elle date de plus d’une heure, elle est supprimée. Vous pouvez trouver les noms des volumes et les noms des captures instantanées en exécutant le script `azure_hana_snapshot_details`. 

>[!IMPORTANT]
>Si des données existent uniquement dans la capture instantanée que vous supprimez, la suppression entraînera la perte définitive de ces données.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Récupération jusqu’à la capture instantanée HANA la plus récente

Si vous devez faire face à une interruption de la production, le processus de récupération à partir d’une capture instantanée de stockage peut être lancé sous la forme d’un incident client auprès du support technique de Microsoft Azure. Il s’agit d’un problème de la plus haute urgence si des données sont supprimées d’un système de production et que le seul moyen de les récupérer est de restaurer la base de données de production.

Dans un cas différent, une récupération jusqu’à une date et heure peut ne pas avoir de caractère d’urgence et être planifiée plusieurs jours à l’avance. Vous pouvez planifier cette récupération avec l’équipe de gestion des services SAP HANA sur Azure au lieu de signaler un problème hautement prioritaire. Par exemple, vous pourriez planifier la mise à niveau du logiciel SAP en appliquant un nouveau package d’améliorations, et vouloir ensuite revenir à une capture instantanée correspondant à l’état du système avant sa mise à niveau avec le package d’améliorations.

Avant de soumettre la demande, vous devez effectuer certaines tâches de préparation. L’équipe de gestion des services SAP HANA sur Azure peut alors traiter la demande et fournir les volumes restaurés. Vous pourrez ensuite restaurer la base de données HANA à l’aide des captures instantanées. Voici comment se préparer pour émettre la requête :

>[!NOTE]
>Votre interface utilisateur peut être différente des captures d’écran suivantes, selon la version de SAP HANA que vous utilisez.

1. Déterminez la capture instantanée à restaurer. Sauf indication contraire, seul le volume hana/data est restauré. 

2. Arrêtez l’instance HANA.

 ![Arrêter l’instance HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Démontez les volumes de données sur chaque nœud de base de données HANA. Si les volumes de données sont toujours montés sur le système d’exploitation, la restauration de la capture instantanée échoue.
 ![Démonter les volumes de données sur chaque nœud de base de données HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Ouvrez une demande de support Azure pour demander la restauration d’une capture instantanée spécifique.

 - Pendant la restauration : l’équipe de gestion des services SAP HANA sur Azure peut vous demander de participer à une téléconférence de coordination, vérification et confirmation afin de vous assurer que la capture instantanée de stockage correcte est restaurée. 

 - Après la restauration : l’équipe de gestion des services SAP HANA sur Azure vous informe lorsque la capture instantanée de stockage a été restaurée.

5. Une fois le processus de restauration terminé, remontez tous les volumes de données.

 ![Remonter tous les volumes de données](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Sélectionnez les options de récupération dans SAP HANA Studio, si ces options ne s’affichent pas automatiquement lorsque vous vous reconnectez à la base de données HANA par le biais de SAP HANA Studio. L’exemple ci-après illustre une restauration à partir de la dernière capture instantanée HANA. Une capture instantanée de stockage incorpore une capture instantanée HANA. Si vous effectuez la restauration à partir de la capture instantanée de stockage la plus récente, il doit s’agir de la capture instantanée HANA la plus récente. (Si vous effectuez une restauration à partir d’une capture instantanée de stockage antérieure, vous devez localiser la capture instantanée HANA correspondante en vous basant sur l’heure de création de la capture instantanée de stockage.)

 ![Sélectionner les options de récupération dans SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Sélectionnez l’option **Recover the database to a specific data backup or storage snapshot (Récupérer la base de données jusqu’à une sauvegarde de données ou une capture instantanée de stockage spécifiques)**.

 ![Fenêtre de spécification du type de récupération](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Sélectionnez l’option de **spécification d’une sauvegarde sans catalogue**.

 ![Fenêtre de spécification de l’emplacement de sauvegarde](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Dans la liste **Type de destination**, sélectionnez **Instantané**.

 ![Fenêtre de spécification de la sauvegarde à récupérer](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Sélectionnez **Finish (Terminer)** pour démarrer le processus de récupération.

 ![Sélectionnez « Finish (Terminer) » pour démarrer le processus de récupération.](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. La base de données HANA est restaurée et récupérée à partir de la capture instantanée HANA incluse dans la capture instantanée de stockage.

 ![Base de données HANA restaurée et récupérée dans la capture instantanée de stockage](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Récupération jusqu’à l’état le plus récent

Le processus suivant restaure la capture instantanée HANA incluse dans la capture instantanée de stockage. Il restaure ensuite les sauvegardes des journaux de transactions à l’état le plus récent de la base de données avant de restaurer la capture instantanée de stockage.

>[!IMPORTANT]
>Avant de poursuivre, assurez-vous que vous disposez d’une chaîne complète et contiguë de sauvegardes de fichier journal. Sans ces sauvegardes, vous ne pouvez pas restaurer l’état actuel de la base de données.

1. Réalisez les étapes 1 à 6 de la section [Récupération jusqu’à la capture instantanée HANA la plus récente](#recovering-to-the-most-recent-hana-snapshot).

2. Sélectionnez l’option de **récupération de la base de données jusqu’à son état le plus récent**.

 ![Sélectionner l’option de « récupération de la base de données jusqu’à son état le plus récent »](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Spécifiez l’emplacement des dernières sauvegardes de journaux HANA. L’emplacement doit contenir toutes les sauvegardes de fichier journal HANA entre la capture instantanée HANA et l’état le plus récent.

 ![Spécifier l’emplacement des dernières sauvegardes de journaux HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Sélectionnez une sauvegarde comme base pour la récupération de la base de données. Dans notre exemple, la capture instantanée HANA illustrée dans la capture d’écran est la capture instantanée HANA qui était incluse dans la capture instantanée de stockage. 

 ![Sélectionner une sauvegarde comme base pour la récupération de la base de données](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Désélectionnez l’option **Use Delta Backups (Utiliser les sauvegardes différentielles)** si ces sauvegardes n’existent pas entre le moment de la capture instantanée HANA et l’état le plus récent.

 ![Désélectionner l’option Use Delta Backups (Utiliser les sauvegardes différentielles) si ces sauvegardes n’existent pas](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Sur l’écran récapitulatif, sélectionnez **Finish (Terminer)** pour lancer la procédure de restauration.

 ![Cliquer sur "Finish" (Terminer) sur l’écran récapitulatif](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Récupération à un autre point dans le temps
Pour effectuer une récupération jusqu’à une date et heure situées entre la capture instantanée HANA (incluse dans la capture instantanée de stockage) et une autre date et heure postérieures à la récupération jusqu`à une date et heure de la capture instantanée HANA, procédez comme suit :

1. Vérifiez que vous disposez de toutes les sauvegardes de fichier journal entre la capture instantanée HANA et l’heure souhaitée pour la récupération.
2. Commencez la procédure décrite à la section [Récupération jusqu’à l’état le plus récent](#recovering-to-the-most-recent-state).
3. À l’étape 2 de la procédure, dans la fenêtre de **spécification du type de récupération**, sélectionnez l’option **Recover the database to the following point in time (Récupérer la base de données jusqu’au point dans le temps suivant)**, spécifiez le point dans le temps, puis effectuez les étapes 3 à 6.

### <a name="monitoring-the-execution-of-snapshots"></a>Surveillance de l’exécution de captures instantanées

Quand vous utilisez des captures instantanées de stockage des grandes instances HANA, vous devez également surveiller leur exécution. Le script qui exécute une capture instantanée de stockage écrit la sortie dans un fichier, puis enregistre ce dernier au même emplacement que les scripts Perl. Un fichier distinct est créé pour chaque capture instantanée de stockage. Le résultat de chaque fichier montre clairement les différentes phases que le script de capture instantanée exécute :

1. Recherche des volumes qui doivent créer une capture instantanée
2. Recherche des captures instantanées créées à partir de ces volumes
3. Suppression des captures instantanées existantes en fonction du nombre de captures instantanées que vous avez spécifié
4. Création d’une capture instantanée SAP HANA
5. Création de la capture instantanée de stockage sur les volumes
6. Suppression de la capture instantanée SAP HANA
7. Changement du nom de la capture instantanée la plus récente par l’attribution de la valeur **.0**

La partie la plus importante du fichier cab du script est la suivante :
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Dans cet exemple, vous pouvez voir la façon dont le script enregistre la création de la capture instantanée HANA. Dans le cas d’une montée en puissance parallèle, ce processus est initialisé sur le nœud principal. Le nœud principal lance la création synchrone des captures instantanées SAP HANA sur chacun des nœuds de travail. Puis la capture instantanée de stockage est effectuée. Une fois que l’exécution des captures instantanées de stockage a réussi, la capture instantanée HANA est supprimée. La suppression de la capture instantanée HANA est lancée à partir du nœud principal.


## <a name="disaster-recovery-principles"></a>Principes de la récupération d’urgence
Les grandes instances HANA offrent une fonctionnalité de récupération d’urgence entre les tampons de grande instance HANA dans différentes régions Azure. Par exemple, si vous déployez des unités de grande instance HANA dans la région Ouest des États-Unis d’Azure, vous pouvez utiliser des unités de grande instance HANA dans la région Est des États-Unis en guise d’unités de récupération d’urgence. Comme mentionné précédemment, la récupération d’urgence n’est pas configurée automatiquement, car vous devez payer pour une autre unité de grande instance HANA dans la région de la récupération d’urgence. La configuration de la récupération d’urgence fonctionne pour les configurations avec montée en puissance et avec montée en puissance parallèle. 

Dans les scénarios déployés jusqu’à présent, nos clients se servent de l’unité dans la région de la récupération d’urgence pour exécuter des systèmes de non-production qui utilisent une instance HANA installée. L’unité de grande instance HANA doit avoir la même référence SKU que celle utilisée à des fins de production. La configuration de disque entre l’unité du serveur dans la région Azure de production et la région de la récupération d’urgence ressemble à ceci :

![Configuration de la récupération d’urgence du point de vue du disque](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Comme le montre cette vue d’ensemble, vous devez commander un deuxième ensemble de volumes de disque. Les volumes de disques cibles sont de la même taille que les volumes de production pour l’instance de production dans les unités de récupération d’urgence. Ces volumes de disques sont associés à l’unité du serveur de grande instance HANA sur le site de récupération d’urgence. Les volumes suivants sont répliqués de la région de production sur le site de récupération d’urgence :

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap compris)

Le volume /hana/log n’est pas répliqué, car le journal des transactions SAP HANA n’est pas nécessaire avec le mode de restauration depuis ces volumes qui est employé. 

Fondamentalement, la fonctionnalité de récupération d’urgence proposée est la fonctionnalité de réplication de stockage offerte par l’infrastructure des grandes instances HANA. La fonctionnalité qui est utilisée côté stockage n’est pas un flux constant de modifications répliquées de manière asynchrone à mesure que des changements sont apportés au volume de stockage. Il s’agit plutôt d’un mécanisme qui repose sur le fait que des captures instantanées de ces volumes sont régulièrement créées. La différence entre une capture instantanée déjà répliquée et une nouvelle capture instantanée non encore répliquée est ensuite transférée au site de récupération d’urgence dans des volumes de disque cibles.  Ces captures instantanées sont stockées sur les volumes et doivent, en cas de basculement dans le cadre d’une récupération d’urgence, être restaurées sur ces volumes.  

Le premier transfert de la totalité des données d’un volume doit avoir lieu avant que la quantité de données ne devienne inférieure aux données différentielles entre les captures instantanées. Ainsi, les volumes sur le site de récupération d’urgence contiennent chacun des captures instantanées de volume effectuées sur le site de production. Cela vous permet d’utiliser par la suite ce système de récupération d’urgence pour retrouver un état antérieur afin de récupérer des données perdues sans restaurer le système de production.

En cas de déploiements MCOD avec plusieurs instances SAP HANA indépendantes sur une unité de grande Instance HANA, il est probable que toutes les instances SAP HANA obtiennent un stockage répliqué du côté de la récupération d’urgence.

Si vous utilisez la réplication de système HANA comme fonctionnalité de haute disponibilité dans votre site de production, seuls les volumes de l’instance (ou réplica) de niveau 2 sont répliqués. Cette configuration peut entraîner un délai dans la réplication de stockage vers le site de récupération d’urgence si l’unité du serveur de réplica secondaire (niveau 2) ou l’instance SAP HANA dans cette unité fait l’objet d’une maintenance ou d’un arrêt. 


>[!IMPORTANT]
>Comme dans le cas de la réplication de système HANA multiniveau, un arrêt de l’unité de serveur ou d’instance HANA de niveau 2 bloque la réplication vers le site de récupération d’urgence quand vous utilisez la fonctionnalité de récupération d’urgence des grandes instances HANA.


>[!NOTE]
>La fonctionnalité de réplication de stockage des grandes instances HANA met en miroir et réplique les captures instantanées de stockage. Ainsi, si vous n’effectuez pas de captures instantanées de stockage comme indiqué dans la section de ce document consacrée à la sauvegarde, aucune réplication vers le site de récupération d’urgence ne peut avoir lieu. L’exécution de captures instantanées de stockage est un prérequis pour la réplication de stockage vers le site de récupération d’urgence.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Préparation du scénario de récupération d’urgence
Nous partons du principe que vous disposez d’un système de production exécuté sur de grandes instances HANA dans la région Azure de production. Pour la documentation suivante, supposons que le SID de ce système HANA soit « PRD ». Nous partons également du principe que vous disposez d’un système de non-production exécuté sur de grandes instances HANA exécutées dans la région Azure de récupération d’urgence. Pour la documentation, supposons que son SID est « TST ». La configuration ressemble donc à ceci :

![Première étape de la configuration de la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Si l’instance du serveur n’a pas déjà été commandée avec l’ensemble de volumes de stockage supplémentaire, SAP HANA sur Azure Service Management attache l’ensemble de volumes supplémentaire en tant que cible pour le réplica de production sur l’unité de grande instance HANA sur laquelle vous exécutez l’instance HANA TST. À cette fin, vous devez fournir le SID de votre instance HANA de production. Une fois que l’équipe de gestion des services SAP HANA sur Azure a confirmé que ces volumes ont été attachés, vous devez monter ces derniers sur l’unité de grande instance HANA.

![Étape suivante de la configuration de la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

L’étape suivante consiste à installer la deuxième instance SAP HANA sur l’unité de grande instance HANA dans la région Azure de récupération d’urgence, où vous exécutez l’instance HANA TST. La nouvelle instance SAP HANA installée doit avoir le même SID. Les utilisateurs créés doivent avoir les mêmes UID et ID de groupe que ceux de l’instance de production. Si l’installation a réussi, vous devez :

- Exécuter l’étape 2 de la préparation des captures instantanées de stockage plus avant dans le document
- Créer une clé publique pour l’unité de récupération d’urgence de l’unité de grande instance HANA si vous n’avez pas effectué cette étape avant. La procédure est indiquée à l’étape 3 de la préparation des captures instantanées de stockage plus avant dans le document
- Maintenez **HANABackupCustomerDetails.txt** avec la nouvelle instance HANA et testez si la connectivité au stockage fonctionne correctement.  
- Arrêtez l’instance SAP HANA nouvellement installée sur l’unité de grande instance HANA dans la région Azure de récupération d’urgence.
- Démonter ces volumes PRD et contacter l’équipe de gestion des services SAP HANA sur Azure. Les volumes ne peuvent pas rester montés sur l’unité, car ils ne sont pas accessibles tant qu’ils fonctionnent en tant que cible de réplication de stockage.  

![Étape de configuration de la récupération d’urgence avant d’établir la réplication](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

L’équipe des opérations établira la relation de réplication entre les volumes PRD dans la région Azure de production et les volumes PRD dans la région Azure de récupération d’urgence.

>[!IMPORTANT]
>Le volume /hana/log ne sera pas répliqué, car il n’est pas nécessaire pour restaurer la base de données SAP HANA répliquée à un état cohérent sur le site de récupération d’urgence.

L’étape suivante consiste à définir ou ajuster la planification des sauvegardes de captures instantanées de stockage pour atteindre vos RTO et RPO en cas d’urgence. Pour réduire l’objectif de point de récupération, définissez les intervalles de réplication suivants dans le service de grande instance HANA :
- Les volumes couverts par la capture instantanée combinée (type de capture instantanée = **hana**) sont répliqués toutes les 15 minutes vers les cibles de volume de stockage équivalents sur le site de récupération d’urgence.
- Le volume de sauvegarde de fichier journal (type de capture instantanée = **logs**) est répliqué toutes les trois minutes vers les cibles de volume de stockage équivalents sur le site de récupération d’urgence.

Pour réduire l’objectif de point de récupération, effectuez le paramétrage suivant :
- Effectuez une capture instantanée de stockage de type **hana** (voir « Étape 7 : Effectuer des captures instantanées ») toutes les 30 à 60 minutes.
- Effectuez des sauvegardes de fichier journal SAP HANA toutes les 5 minutes.
- Effectuez une capture instantanée de stockage de type **logs** toutes les 5 à 15 minutes. Avec cette période d’intervalle, vous devriez être en mesure d’atteindre un RPO d’environ 15 à 25 minutes.

Avec cette configuration, la séquence des sauvegardes de fichier journal, des captures instantanées de stockage et de la réplication du volume de sauvegarde de fichier journal HANA et des volumes /hana/data et /hana/shared (/usr/sap compris) pourrait se présenter comme illustré dans le graphique ci-dessous :

 ![Relation entre une capture instantanée de sauvegarde de fichier journal et un miroir de capture instantanée sur un axe temporel](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Pour améliorer davantage le RPO dans le cadre d’une récupération d’urgence, vous pouvez copier les sauvegardes de fichier journal HANA de SAP HANA sur Azure (grandes instances) vers l’autre région Azure. Pour obtenir cette nouvelle réduction du RPO, effectuez grosso modo les étapes suivantes :

1. Sauvegardez le journal des transactions HANA aussi souvent que possible dans /hana/logbackups.
2. Utilisez rsync pour copier les sauvegardes de fichier journal sur les machines virtuelles Azure hébergées sur le partage NFS. Les machines virtuelles se trouvent dans des réseaux virtuels Azure dans la région Azure de production et dans les régions de récupération d’urgence. Vous devez connecter les deux réseaux virtuels Azure au circuit reliant les grandes instances HANA de production à Azure. Reportez-vous au graphique de la section [Considérations sur le réseau pour la récupération d’urgence avec de grandes instances HANA](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances). 
3. Conservez les sauvegardes de fichier journal dans la région sur la machine virtuelle attachée au stockage NFS exporté.
4. Dans le cas d’un basculement d’urgence, complétez les sauvegardes de fichier journal disponibles sur le volume /hana/logbackups avec des sauvegardes de fichier journal plus récentes sur le partage NFS sur le site de récupération d’urgence. 
5. Vous pouvez à présent lancer une restauration de la sauvegarde de fichier journal jusqu’à la dernière sauvegarde enregistrée dans la région de la récupération d’urgence.

Lorsque les opérations de grandes instances HANA confirment la configuration de la relation de réplication et que vous lancez l’exécution des sauvegardes de captures instantanées de stockage, la réplication des données commence.

![Étape de configuration de la récupération d’urgence avant d’établir la réplication](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Durant la réplication, les captures instantanées sur les volumes PRD dans les régions Azure de récupération d’urgence ne sont pas restaurées. Elles sont uniquement stockées. Si les volumes sont montés dans cet état, ils reflètent l’état dans lequel vous les avez démontés après l’installation de l’instance SAP HANA PRD dans l’unité du serveur de la région Azure de récupération d’urgence. Ils reflètent également les sauvegardes de stockage qui ne sont pas encore restaurées.

En cas de basculement, vous pouvez également opter pour effectuer une restauration à partir d’une capture instantanée de stockage plus ancienne au lieu de la dernière capture instantanée de stockage.

## <a name="disaster-recovery-failover-procedure"></a>Procédure de basculement en cas de récupération d’urgence
Il existe deux différents cas à prendre en compte lors du basculement vers le site de récupération d’urgence :

- Vous avez besoin de remettre la base de données SAP HANA à l’état le plus récent des données. Dans ce cas, il existe un script en libre-service qui vous permet d’exécuter le basculement sans devoir contacter Microsoft. Malgré la restauration automatique, vous devez collaborer avec Microsoft.
- Vous voulez restaurer sur une capture instantanée de stockage qui n’est pas la dernière capture instantanée répliquée, vous devez collaborer avec Microsoft. 

>[!NOTE]
>Les étapes du processus ci-dessous doivent être exécutées sur l’unité de grande instance HANA, ce qui représente l’unité de récupération d’urgence. 
 
En cas de restauration sur les dernières captures instantanées de stockage répliquées, les étapes approximatives sont les suivantes : 

1. Étant donné que vous exécutez une instance de non-production de HANA sur l’unité de récupération d’urgence des grandes instances HANA, vous devez arrêter cette instance. Nous partons du principe qu’il existe une instance de production HANA dormante préinstallée.
2. Assurez-vous qu’aucun processus SAP HANA n’est en cours d’exécution. Utilisez la commande suivante pour effectuer cette vérification : `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. La sortie doit indiquer que le processus **hdbdaemon** est arrêté et qu’aucun autre processus HANA n’est en cours d’exécution ou démarré.
3. Sur l’unité de grande instance HANA du site de récupération d’urgence, exécutez le script **azure_hana_dr_failover.pl**. Le script demande la restauration d’un SID SAP HANA. Sur demande du script, entrez un ou le seul SID SAP HANA répliqué et conservé dans le fichier HANABackupCustomerDetails.txt sur l’unité de grande instance HANA dans le site de récupération d’urgence. Si vous souhaitez disposer de plusieurs instances SAP HANA basculées, vous devez exécuter le script plusieurs fois et sur demande entrer le SID SAP HANA que vous souhaitez basculer et restaurer. À la fin, le script affiche une liste de points de montage des volumes ajoutés à l’unité de grande instance HANA. Cette liste inclut également les volumes de récupération d’urgence restaurés
4. Montez les volumes de récupération d’urgence restaurés à l’aide des commandes du système d’exploitation Linux dans l’unité de grande instance HANA sur le site de récupération d’urgence. 
6. Démarrez l’instance de production SAP HANA, qui était dormante jusque-là.
7. Si, en outre, vous avez choisi de copier les journaux de sauvegarde de fichier journal pour réduire le délai RPO, vous devez fusionner ces sauvegardes de fichier journal dans le répertoire de récupération d’urgence /hana/logbackups qui vient d’être monté. Ne remplacez pas les sauvegardes existantes. Copiez simplement les sauvegardes plus récentes qui n’ont pas été répliquées avec la réplication la plus récente d’une capture instantanée de stockage.
8. Vous pouvez également restaurer des fichiers uniques depuis les captures instantanées qui ont été répliquées sur le volume /hana/shared/PRD dans la région Azure de récupération d’urgence. 

Vous pouvez également tester le basculement de récupération d’urgence sans impacter la relation de réplication réelle. Pour effectuer un test de basculement, suivez les étapes 1 et 2 de la procédure ci-dessus. L’étape 3 est cependant différente.

>[!IMPORTANT]
>Vous ne pouvez PAS exécuter toutes les transactions de production sur l’instance que vous avez créée sur le site de récupération d’urgence à travers le processus de **TEST de basculement** avec le script introduit ensuite. La commande introduite ensuite crée un ensemble de volumes sans aucune relation avec le site principal. Par conséquent, une synchronisation de retour au site principal n’est PAS possible. 

L’étape 3 du **test de basculement** doit ressembler à :

Sur l’unité de grande instance HANA du site de récupération d’urgence, exécutez le script **azure_hana_test_dr_failover.pl**. Ce script n’arrête PAS la relation de réplication entre le site principal et le site de récupération d’urgence. Au lieu de cela, ce script clone des volumes de stockage de récupération d’urgence. Une fois le processus de clonage réussi, les volumes clonés sont restaurés à l’état de la capture instantanée la plus récente puis montés sur l’unité de récupération d’urgence. Le script demande la restauration d’un SID SAP HANA. Entrez un ou le seul SID SAP HANA répliqué et conservé dans le fichier HANABackupCustomerDetails.txt sur l’unité de grande instance HANA dans le site de récupération d’urgence. Si vous souhaitez disposer de plusieurs instances SAP HANA basculées, vous devez exécuter le script plusieurs fois et sur demande entrer le SID SAP HANA pour lequel vous souhaitez tester le basculement. À la fin, le script affiche une liste de points de montage des volumes ajoutés à l’unité de grande instance HANA. Cette liste inclut également les volumes de récupération d’urgence clonés.

Puis, passez aux étapes 4 à 8 de la procédure ci-dessus.

Si vous avez besoin de basculer vers le site de récupération d’urgence pour récupérer des données supprimées il y a des heures et dont les volumes de récupération d’urgence doivent être définis à une version de capture instantanée plus récente, cette procédure s’applique. 

1. Étant donné que vous exécutez une instance de non-production de HANA sur l’unité de récupération d’urgence des grandes instances HANA, vous devez arrêter cette instance. Nous partons du principe qu’il existe une instance de production HANA dormante préinstallée.
2. Assurez-vous qu’aucun processus SAP HANA n’est en cours d’exécution. Utilisez la commande suivante pour effectuer cette vérification : `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. La sortie doit indiquer que le processus **hdbdaemon** est arrêté et qu’aucun autre processus HANA n’est en cours d’exécution ou démarré.
3. Déterminez vers quel nom de capture instantanée ou ID de sauvegarde SAP HANA vous souhaitez restaurer le site de récupération d’urgence. Dans les cas réels de récupération d’urgence, cette capture instantanée est généralement la capture instantanée la plus récente. Si vous avez besoin de récupérer des données perdues, sélectionnez une capture instantanée antérieure.
4. Contactez le support Azure par le biais d’une demande de support de haute priorité et demandez la restauration de cette capture instantanée (nom et date de la capture instantanée) ou de l’ID de sauvegarde HANA sur le site de récupération d’urgence. Par défaut, les opérations restaurent uniquement le volume /hana/data. Si vous souhaitez également restaurer les volumes /hana/logbackups, vous devez le préciser. *Il est déconseillé de restaurer le volume /hana/shared.* Sélectionnez plutôt des fichiers spécifiques, tels que global.ini du répertoire **.snapshot** et de ses sous-répertoires, après avoir remonté le volume /hana/shared pour PRD. Du côté des opérations, les étapes suivantes vont se produire : a. La réplication des captures instantanées depuis le volume de production vers les volumes de récupération d’urgence est arrêtée. Cette interruption peut s’être déjà produit si la raison pour laquelle vous avez besoin d’effectuer une récupération d’urgence est une panne sur le site de production.
    b. La capture instantanée de stockage dont vous avez indiqué le nom ou la capture instantanée avec l’ID de sauvegarde que vous avez spécifié est restaurée sur les volumes de récupération d’urgence.
    c. Après la restauration, les volumes de récupération d’urgence peuvent être montés sur les unités de grande instance HANA dans la région de récupération d’urgence.
5. Montez les volumes de récupération d’urgence sur l’unité de grande instance HANA sur le site de récupération d’urgence. 
6. Démarrez l’instance de production SAP HANA, qui était dormante jusque-là.
7. Si, en outre, vous avez choisi de copier les journaux de sauvegarde de fichier journal pour réduire le délai RPO, vous devez fusionner ces sauvegardes de fichier journal dans le répertoire de récupération d’urgence /hana/logbackups qui vient d’être monté. Ne remplacez pas les sauvegardes existantes. Copiez simplement les sauvegardes plus récentes qui n’ont pas été répliquées avec la réplication la plus récente d’une capture instantanée de stockage.
8. Vous pouvez également restaurer des fichiers uniques depuis les captures instantanées qui ont été répliquées sur le volume /hana/shared/PRD dans la région Azure de récupération d’urgence.

La séquence d’étapes suivante implique la récupération de l’instance de production SAP HANA en fonction de la capture instantanée de stockage restaurée et des sauvegardes de fichier journal qui sont disponibles. Les étapes ressemblent à ceci :

1. Modifiez l’emplacement de sauvegarde en le définissant sur **/hana/logbackups** à l’aide de SAP HANA Studio.
   ![Modifier l’emplacement de sauvegarde pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA parcourt les emplacements de fichiers de sauvegarde et suggère une restauration jusqu’à la sauvegarde de fichier journal la plus récente. L’analyse peut prendre quelques minutes jusqu’à ce qu’un écran comme le suivant s’affiche : ![Liste des sauvegardes de fichier journal pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Modifiez certains paramètres par défaut :

      - Désactivez l’option **Use Delta Backups** (Utiliser les sauvegardes différentielles).
      - Sélectionnez l’option **Initialize Log Area** (Initialiser la zone de journalisation).

   ![Définir l’option Initialize Log Area (Initialiser la zone de journalisation)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Sélectionnez **Terminer**.

   ![Terminer la restauration avec récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Une fenêtre de progression, comme celle illustrée ici, doit apparaître. Gardez à l’esprit qu’il s’agit d’un exemple de restauration avec récupération d’urgence d’une configuration de SAP HANA à 3 nœuds avec montée en puissance parallèle.

![Progression de la restauration](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Si la restauration semble bloquée au niveau de l’écran de **fin** et que l’écran de progression n’apparaisse pas, vérifiez que toutes les instances SAP HANA sur les nœuds de travail sont en cours d’exécution. Si nécessaire, démarrez manuellement les instances SAP HANA.


### <a name="failback-from-dr-to-a-production-site"></a>Restauration automatique depuis un site de récupération d’urgence vers un site de production
Vous pouvez effectuer une restauration automatique depuis un site de récupération d’urgence vers un site de production. Supposons que le basculement vers le site de récupération d’urgence a été provoqué par des problèmes affectant la région Azure de production et non par votre besoin de récupérer des données perdues. Vous exécutez votre charge de travail de production SAP depuis un certain temps sur le site de récupération d’urgence. Les problèmes rencontrés sur le site de production étant résolus, vous souhaitez effectuer une restauration automatique sur votre site de production. Étant donné que vous ne pouvez pas perdre de données, le retour au site de production implique plusieurs étapes et une collaboration étroite avec l’équipe des opérations SAP HANA sur Azure. Il vous incombe de donner à l’équipe des opérations le feu vert pour démarrer la synchronisation sur le site de production une fois les problèmes résolus.

La séquence des étapes ressemble à ceci :

1. L’équipe des opérations SAP HANA sur Azure reçoit le feu vert pour synchroniser les volumes de stockage de production à partir des volumes de stockage de récupération d’urgence, qui représentent désormais l’état de production. Dans cet état, l’unité de grande instance HANA sur le site de production est arrêtée.
2. L’équipe des opérations SAP HANA sur Azure surveille la réplication et s’assure qu’un rattrapage est effectué avant de vous informer en tant que client.
3. Vous arrêtez les applications qui utilisent l’instance HANA de production sur le site de récupération d’urgence. Vous effectuez ensuite une sauvegarde de fichier journal HANA, puis arrêtez l’instance HANA en cours d’exécution sur les unités de grande instance HANA sur le site de récupération d’urgence.
4. Une fois arrêtée l’instance HANA en cours d’exécution dans l’unité de grande instance HANA sur le site de récupération d’urgence, l’équipe des opérations resynchronise manuellement les volumes de disque.
5. L’équipe des opérations SAP HANA sur Azure redémarre l’unité de grande instance HANA sur le site de production et vous la repasse. Vérifiez que l’instance SAP HANA est arrêtée au moment du démarrage de l’unité de grande instance HANA.
6. Vous effectuez les mêmes étapes de restauration de base de données que celles que vous avez réalisées pour le basculement vers le site de récupération d’urgence.

### <a name="monitoring-disaster-recovery-replication"></a>Surveillance de la réplication de récupération d’urgence

Vous pouvez surveiller l’état de la progression de la réplication de stockage en exécutant le script `azure_hana_replication_status.pl`. Ce script doit être exécuté depuis une unité en cours d’exécution à l’emplacement de la récupération d’urgence. Dans le cas contraire, il ne fonctionnera pas comme prévu. Le script fonctionne même si la réplication est active. Vous pouvez exécuter le script pour chaque unité de grande instance HANA de votre locataire à l’emplacement de la récupération d’urgence. Vous ne pouvez pas l’utiliser pour obtenir des détails sur le volume de démarrage.

Appelez le script :
```
./replication_status.pl <HANA SID>
```

La sortie indique les sections suivantes pour chaque volume :  

- État du lien
- Activité de réplication en cours
- Dernière capture instantanée répliquée 
- Taille de la capture instantanée la plus récente
- Décalage entre les captures instantanées (la dernière réplication de capture instantanée et maintenant)  

L’état du lien indique **Active**, sauf si le lien entre les emplacements est rompu ou qu’un événement de basculement soit en cours. L’activité de réplication indique si des données sont en cours de réplication, inactives ou si d’autres activités sont en cours sur le lien. La dernière capture instantanée répliquée devrait uniquement apparaître en tant que `snapmirror…`. La taille de la dernière capture instantanée s’affiche ensuite. Enfin, le décalage est indiqué. Ce dernier représente le temps écoulé entre l’heure de réplication planifiée et le moment auquel la réplication se termine. Le décalage peut être supérieur à une heure pour la réplication de données, en particulier pour la réplication initiale, même si la réplication a démarré. Le décalage augmente progressivement jusqu’à ce que la réplication en cours se termine.

Voici un exemple de sortie :

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












