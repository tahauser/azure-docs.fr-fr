---
title: Installation de SAP HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Comment installer SAP HANA sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef85c098058c97e5ec6d758fcf1dab5b1a87786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure

Voici quelques définitions importantes à connaître avant de lire ce guide. Dans [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), nous avons présenté deux classes différentes d’unités de grande instance HANA avec :

- S72, S72m, S144, S144m, S192 et S192m, que nous appelons la « Classe Type I » des références SKU ;
- S384, S384m, S384xm, S576, S768 et S960, que nous appelons la « Classe Type II » des références SKU.

Le spécificateur de classe est utilisé dans la documentation relative à la grande instance HANA pour désigner différentes fonctionnalités et exigences en fonction des références SKU de grande instance HANA.

Nous utilisons fréquemment les autres définitions suivantes :
- **Tampon de grande instance :** une pile d’infrastructure matérielle certifiée SAP HANA TDI et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA TDI certifié qui est déployé dans les tampons de grande instance dans différentes régions Azure. Le terme associé **Grande instance HANA** est la forme abrégée de SAP HANA sur Azure (grandes instances) largement utilisée dans ce guide de déploiement technique.


L’installation de SAP HANA relève de votre responsabilité et vous pouvez démarrer l’activité après la remise d’un nouveau SAP HANA sur un serveur Azure (grandes instances). Et après que la connexion entre vos réseaux virtuels Azure et les unités de Grande instance HANA est établie. 

> [!Note]
> En vertu de la stratégie de SAP, l’installation de SAP HANA doit être effectuée par une personne certifiée pour l’effectuer, à savoir une personne ayant passé l’examen Certified SAP Technology Associate et l’examen de certification SAP HANA Installation, ou par un intégrateur de systèmes (SI) certifié SAP.

Effectuez une nouvelle vérification, en particulier si vous vous préparez à installer HANA 2.0 ([Note de support #2235581 relative à SAP HANA : systèmes d’exploitation pris en charge](https://launchpad.support.sap.com/#/notes/2235581/E)) afin de vous assurer que le système d’exploitation utilisé est pris en charge par la dernière version de SAP HANA que souhaitez installer. Vous comprendrez alors que le nombre de systèmes d’exploitation pris en charge par HANA 2.0 est plus limité que celui des systèmes pris en charge par HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Premières étapes à suivre après la réception des unités de grandes instances HANA

Après avoir reçu l’unité de grandes instances HANA, puis établi l’accès et la connectivité aux instances, la **première étape** consiste à enregistrer le système d’exploitation de l’instance auprès de votre fournisseur de système d’exploitation. Cela inclut l’inscription de votre système d’exploitation SUSE Linux dans une instance de SUSE SMT que vous devez avoir déployée sur une machine virtuelle dans Azure. L’unité de grande instance HANA peut se connecter à cette instance SMT (voir plus loin dans cette documentation). Ou bien l’enregistrement de votre système d’exploitation Red Hat dans le Gestionnaire d’abonnements Red Hat auquel vous devez vous connecter. Consultez également la section Remarques de ce [document](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette étape est également nécessaire si vous souhaitez pouvoir appliquer des correctifs au système d’exploitation. Tâche qui incombe au client. Pour SUSE, la documentation permettant d’installer et de configurer SMT se trouve [ici](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

La **deuxième étape** consiste à rechercher les nouveaux correctifs applicables à la version spécifique du système d’exploitation. Vérifiez que le niveau de correctif logiciel des grandes instances HANA est à jour. En fonction de la date de publication des correctifs/versions du système d’exploitation et des modifications de l’image que Microsoft peut déployer, il peut arriver que les correctifs les plus récents ne soient pas inclus. Par conséquent, cette étape est obligatoire après réception d’une unité de grande instance HANA afin de vérifier si des correctifs indispensables pour la sécurité, le fonctionnement, la disponibilité et les performances ont été publiés entretemps par le fournisseur Linux concerné, et s’ils doivent être appliqués.

La **troisième étape** consiste à consulter les notes de support SAP relatives à l’installation et à la configuration de SAP HANA sur la version spécifique du système d’exploitation. En raison des recommandations variables ou des modifications apportées aux notes de support SAP ou des configurations qui dépendent de scénarios d’installation uniques, Microsoft ne sera pas toujours en mesure de fournir une unité de grandes instances HANA configurée à la perfection. Par conséquent, vous êtes tenu, en tant que client, de lire les Notes de SAP liées à SAP HANA sur votre version de Linux. Vérifiez également les configurations de la publication/version de système d’exploitation nécessaires, et appliquez les paramètres de configuration si ce n’est déjà fait.

Vérifiez en particulier les paramètres ci-dessous et effectuez les ajustements éventuels nécessaires :

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Sous SLES12 SP1 et RHEL 7.2, ces paramètres doivent être définis dans un fichier de configuration dans le répertoire /etc/sysctl.d. Il faut par exemple créer un fichier de configuration nommé 91-NetApp-HANA.conf. Pour les versions plus anciennes de SLES et RHEL, ces paramètres doivent être définis dans /etc/sysctl.conf.

Pour toutes les versions de RHEL et à partir de SLES12, le paramètre 
- sunrpc.tcp_slot_table_entries = 128

doit être défini dans /etc/modprobe.d/sunrpc-local.conf. Si le fichier n’existe pas, il doit tout d’abord être créé en ajoutant l’entrée suivante : 
- options sunrpc tcp_max_slot_table_entries=128

La **quatrième étape** consiste à vérifier l’heure système de votre unité de grandes instances HANA. Les instances sont déployées sur la base d’un fuseau horaire système qui représente l’emplacement de la région Azure dans laquelle se trouve le tampon de grande instance HANA. Vous êtes libre de modifier l’heure système ou le fuseau horaire des instances que vous possédez. Dans ce cas, si vous commandez d’autres instances dans votre locataire, vous devrez probablement modifier le fuseau horaire des nouvelles instances. Microsoft Operations n’a aucune visibilité sur le fuseau horaire système défini pour vos instances après leur réception. C’est pourquoi les instances nouvellement déployées peuvent ne pas être définies sur le même fuseau horaire que celui choisi précédemment. Il vous incombe donc, en tant que client, de vérifier le fuseau horaire des instances remises et de les modifier si nécessaire. 

La **cinquième étape** consiste à vérifier le fichier etc/hosts. À la réception des unités, des adresses IP spécifiques sont affectées à différentes fonctions (voir la section suivante). Vérifiez le fichiers etc/hosts. Si vous ajoutez des unités à un locataire existant, le fichier etc/hosts des systèmes nouvellement déployés ne contiendra pas les adresses IP des systèmes reçus précédemment. Il vous incombe donc, en tant que client, de définir les paramètres appropriés pour que l’instance nouvellement déployée puisse interagir et résoudre les noms des unités précédemment déployées dans votre locataire. 

## <a name="networking"></a>Mise en réseau
Nous partons du principe que vous avez suivi les recommandations données dans les documents ci-après pour concevoir vos réseaux virtuels Azure et connecter ceux-ci aux grandes instances HANA :

- [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Certains détails concernant la mise en réseau d’unités individuelles valent la peine d’être soulignés. Chaque unité de grandes instances HANA est fournie avec deux ou trois adresses IP affectées à deux ou trois de ses ports de carte réseau. Trois adresses IP sont utilisées dans les configurations de montée en charge HANA et dans le scénario de réplication système HANA. Une des adresses IP affectées à la carte réseau de l’unité se situe hors du pool d’adresses IP du serveur décrit à la page [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Pour les unités avec deux adresses IP affectées, voici à quoi devrait ressembler la distribution :

- L’adresse IP affectée à eth0.xx doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Cette adresse IP doit être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.
- L’adresse IP affectée à eth1.xx doit être utilisée pour la communication avec le stockage NFS. Par conséquent, ces adresses **N’ONT PAS BESOIN** d’être mises à jour dans le fichier etc/hosts pour autoriser un trafic interinstance au sein du locataire.

Une configuration à deux adresses IP ne convient pas aux déploiements de réplication système HANA ou aux montées en charge HANA. Si seules deux adresses IP sont affectées et si vous souhaitez déployer une telle configuration, contactez SAP HANA sur Azure Service Management pour obtenir une troisième adresse IP dans un troisième VLAN. Pour les unités de grande instance HANA avec trois adresses IP affectées à trois ports de carte réseau, les règles d’utilisation suivantes s’appliquent :

- L’adresse IP affectée à eth0.xx doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Par conséquent, cette adresse IP ne doit pas être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.
- L’adresse IP affectée à eth1.xx doit être utilisée pour la communication avec le stockage NFS. Ce type d’adresse ne doit donc pas être mis à jour dans le fichier etc/hosts.
- L’adresse IP du port eth2.xx doit être mise à jour dans le fichier etc/hosts pour la communication entre les différentes instances exclusivement. Ces adresses doivent également être mises à jour dans les configurations HANA de montée en charge : ce sont les adresses IP que HANA utilise pour la communication entre les nœuds.



## <a name="storage"></a>Stockage

La disposition du stockage de SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur Azure Service Management en suivant les directives recommandées par SAP dans le livre blanc [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Conditions de stockage SAP HANA). La taille approximative des différents volumes avec les différentes références SKU de grandes instances HANA est documentée dans [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les conventions d’affectation de noms des volumes de stockage sont répertoriées dans le tableau suivant :

| Utilisation du stockage | Nom du montage | Nom du volume | 
| --- | --- | ---|
| Données HANA | /hana/data/SID/mnt0000<m> | Adresse IP du stockage:/hana_data_SID_mnt00001_tenant_vol |
| Journal HANA | /Hana/log/SID/mnt0000<m> | Adresse IP du stockage:/hana_log_SID_mnt00001_tenant_vol |
| Sauvegarde de fichier journal HANA | HANA/log/backups | Adresse IP du stockage:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA partagé | /Hana/Shared/SID | Adresse IP du stockage:/hana_shared_SID_mnt00001_tenant_vol/shared |
| /usr/sap | /usr/SAP/SID | Adresse IP du stockage:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Où SID est l’ID système de l’instance HANA 

Et tenant est une énumération interne des opérations lors du déploiement d’un client.

Comme vous pouvez le voir, HANA partagé et usr/sap partagent le même volume. La nomenclature des points de montage n’inclut ni l’ID système des instances HANA, ni le nombre de montages. Dans les déploiements avec montée en puissance parallèle, il n’existe qu’un seul montage, par exemple mnt00001. Dans un déploiement avec montée en puissance parallèle vous devriez voir un nombre de montages équivalent au nombre de nœuds worker et principaux. Pour l’environnement avec montée en puissance parallèle, les données, les journaux et les volumes de sauvegarde de fichier journal sont partagés et associés à chaque nœud dans la configuration de la montée en puissance parallèle. Pour les configurations exécutant plusieurs instances SAP, un autre jeu de volumes est créé et attaché à l’unité de grande instance HANA.

À la lecture de ce document, et en regardant une unité de plus près, vous pouvez vous rendre compte de la générosité du volume disque des unités de grande instance HANA attribué à HANA/data, et remarquer la présence d’un volume HANA/log/backup. Si HANA/data est aussi volumineux, c’est parce que les captures instantanées du stockage que nous vous proposons l’exigent. Cela signifie que, plus vous prenez d’instantanés de stockage, plus les instantanés occupent d’espace dans les volumes de stockage qui vous sont assignés. Le volume HANA/log/backup n’est pas conçu pour être le volume sur lequel placer des sauvegardes de base de données. Il est dimensionné pour être utilisé en tant que volume de sauvegarde pour les sauvegardes des journaux de transactions HANA. Dans les futures versions de l’instantané de stockage libre service, nous ciblerons ce volume spécifique pour obtenir des instantanés plus fréquents. Et avec ces réplications plus fréquentes sur le site de récupération d’urgence, vous pouvez opter pour la fonctionnalité de récupération d’urgence fournie par l’infrastructure de grande instance HANA. Pour plus de détails, voir [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En plus de la capacité de stockage fournie, les clients peuvent acheter de l’espace de stockage supplémentaire par incréments de 1 To. Cet espace supplémentaire peut être ajouté en tant que nouveaux volumes à des grandes instances HANA.

Pendant l’intégration avec SAP HANA sur Azure Service Management, le client spécifie un ID d’utilisateur et un ID de groupe pour l’utilisateur sidadm et le groupe sapsys (p. ex. : 1000,500). Ces mêmes valeurs doivent être utilisées lors de l’installation du système SAP HANA. Comme vous souhaitez déployer plusieurs instances HANA sur une unité, vous obtenez plusieurs ensembles de volumes (un ensemble pour chaque instance). Par conséquent, au moment du déploiement, vous devez définir :

- Le SID des différentes instances HANA (sidadm en est dérivé).
- Tailles de mémoire des différentes instances HANA. En effet, la taille de mémoire par instance définit la taille des volumes dans chaque jeu de volumes.

Selon les recommandations du fournisseur de stockage, les options de montage suivantes sont configurées pour tous les volumes montés (exclut le LUN de démarrage) :

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Ces points de montage sont configurés dans/etc/fstab, comme indiqué dans les graphiques suivants :

![fstab des volumes montés dans l’unité de grande instance HANA](./media/hana-installation/image1_fstab.PNG)

La sortie de la commande df -h sur une unité de grande instance HANA S72m ressemblerait à :

![fstab des volumes montés dans l’unité de grande instance HANA](./media/hana-installation/image2_df_output.PNG)


Le contrôleur de stockage et les nœuds dans les tampons de grandes instances sont synchronisés avec les serveurs NTP. Dans la mesure où vous synchronisez les unités SAP HANA sur Azure (grandes instances) et les machines virtuelles Azure avec un serveur NTP, vous ne devriez pas rencontrer d’écart de temps entre l’infrastructure et les unités de calcul dans Azure ou les tampons de grandes instances.

Pour optimiser SAP HANA pour le stockage sous-jacent, définissez également les paramètres de configuration SAP HANA suivants :

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Pour les versions de SAP HANA 1.0 jusqu’à SPS12, ces paramètres peuvent être définis au cours de l’installation de la base de données SAP HANA, comme décrit dans la [Note de support SAP #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Configuration de la base de données SAP HANA)

Vous pouvez également configurer les paramètres après l’installation de la base de données SAP HANA via le framework hdbparam. 

L’utilisation du framework hdbparam est déconseillée avec SAP HANA 2.0. Il faut donc définir les paramètres à l’aide de commandes SQL. Pour plus d’informations, consultez la [Note de support SAP #2399079 : Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Suppression de hbdparam dans HANA 2).


## <a name="operating-system"></a>Système d’exploitation

La taille de l’espace d’échange de l’image du système d’exploitation livré est définie sur 2 Go conformément à la [note de support #1999997 relative à la FAQ : mémoire SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Tout autre paramètre souhaité doit être défini par vous-même, en tant que client.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) est la distribution de Linux installée pour SAP HANA sur Azure (grandes instances). Cette distribution spéciale fournit des fonctionnalités spécifiques à SAP &quot;prêtes à l’emploi&quot; (y compris des paramètres prédéfinis pour une exécution efficace de SAP sur SLES).

Consultez [la bibliothèque de ressources/les livres blancs](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sur le site Web SUSE et [SAP sur SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) sur SCN (SAP Community Network) pour bénéficier de ressources utiles relatives au déploiement de SAP HANA sur SLES (notamment sur la configuration de la haute disponibilité, le renforcement de la sécurité spécifique aux opérations SAP, etc.).

Autres liens utilies concernant SAP sur SUSE :

- [SAP HANA sur le site SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Meilleure pratique pour SAP : Réplication de la file d’attente – SAP NetWeaver sur Linux Enterprise 12).
- [ClamSAP - SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP – Protection antivirus SLES pour SAP, y compris SLES 12 for les applications SAP).

Notes de support SAP applicables à l’implémentation de SAP HANA sur SLES 12 :

- [Note de support SAP #1944799 relative aux instructions SAP HANA pour l’installation du système d’exploitation SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Note de support SAP #2205917 relative aux paramètres de système d’exploitation SAP HANA DB recommandés pour SLES 12 ainsi que les application SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Note de support SAP #1984787 relative à SUSE Linux Enterprise Server 12 : notes d’installation](https://launchpad.support.sap.com/#/notes/1984787).
- [Note de support SAP #171356 relative aux logiciels SAP sur Linux : informations générales](https://launchpad.support.sap.com/#/notes/1984787).
- [Note de support SAP #1391070 relative aux solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux pour SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) est une autre offre permettant l’exécution de SAP HANA sur les grandes instances HANA. RHEL 6.7 et 7.2 sont disponibles. Veuillez noter que contrairement aux machines virtuelles Azure natives où seuls RHEL 7.2 et les versions plus récentes sont pris en charge, les grandes instances HANA prennent également en charge RHEL 6.7. Toutefois, nous vous recommandons d’utiliser une version 7.x de RHEL.

Autres liens SAP utiles relatifs à Red Hat :
- [Site de SAP HANA sur Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notes de support SAP applicables à l’implémentation de SAP HANA sur Red Hat :

- [Note de support SAP #2009879 relative aux instructions SAP HANA pour les systèmes d’exploitation Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Note de support SAP #2292690 relative à SAP HANA DB : paramètres de système d’exploitation recommandés pour RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Note de support SAP #2247020 relative à SAP HANA DB : paramètres de système d’exploitation recommandés pour RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Note de support SAP #1391070 relative aux solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
- [Note de support SAP #2228351 relative à Linux : SAP HANA Database SPS 11 révision 110 (ou plus) sur RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Note de support SAP #2397039 relative à la FAQ : SAP sur RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Note de support SAP #1496410 relative à Red Hat Enterprise Linux 6.x : installation et mise à niveau](https://launchpad.support.sap.com/#/notes/1496410).
- [Note de support SAP #2002167 relative à Red Hat Enterprise Linux 7.x : installation et mise à niveau](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Synchronisation temporelle

Les applications SAP basées sur l’architecture de SAP NetWeaver sont sensibles aux différences d’heure des différents composants qui constituent le système SAP. Vous connaissez certainement les vidages courts SAP ABAP avec le titre d’erreur ZDATE\_LARGE\_TIME\_DIFF, dans la mesure où ces ceux-ci surviennent lorsque l’heure système des différents serveurs ou machines virtuelles s’écarte trop.

Pour SAP HANA sur Azure (grandes instances), la synchronisation de l’heure effectuée dans Azure ne s’applique pas aux unités de calcul dans les tampons de grandes instances. Cette synchronisation ne concerne pas les applications SAP qui s’exécutent sur des machines virtuelles Azure natives, car Azure garantit la bonne synchronisation de l’heure des systèmes. Par conséquent, un serveur temporel distinct doit être configuré pour être utilisé par les serveurs d’applications SAP s’exécutant sur des machines virtuelles Azure et les instances de base de données SAP HANA s’exécutant sur des grandes instances HANA. L’heure de l’infrastructure de stockage dans les tampons de grandes instances est synchronisée avec les serveurs NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Configuration de serveur SMT pour SUSE Linux
Les grandes instances SAP HANA n’ont pas de connexion directe à Internet. Par conséquent, le processus incluant l’inscription d’une telle unité auprès du fournisseur du système d’exploitation ainsi que le téléchargement et l’application des correctifs n’est pas un simple. Dans le cas de SUSE Linux, une solution peut être de configurer un serveur SMT sur une machine virtuelle Azure. En revanche, la machine virtuelle Azure doit être hébergée sur un réseau virtuel Azure connecté à la grande instance HANA. Avec un test serveur SMT, l’unité de grande instance HANA peut inscrire et télécharger des correctifs. 

SUSE fournit un guide plus étoffé sur son [outil de gestion des abonnement pour SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Comme condition préalable à l’installation d’un serveur SMT accomplissant la tâche pour une grande instance HANA, vous devez disposer des éléments suivants :

- un réseau virtuel Azure connecté au circuit ER de la grande instance HANA ;
- un compte SUSE associé à une organisation, tandis que l’organisation aurait besoin d’un abonnement SUSE valide.

### <a name="installation-of-smt-server-on-azure-vm"></a>Installation du serveur SMT sur une machine virtuelle Azure

Dans le cadre de cette étape, vous installez le serveur SMT sur une machine virtuelle Azure. La première mesure consiste à se connecter au [Centre de clients SUSE](https://scc.suse.com/).

Une fois connecté, accédez à Organisation--> Informations d’identification de l’organisation. Dans cette section, vous devez rechercher les informations d’identification nécessaires pour configurer le serveur SMT.

La troisième étape consiste à installer une machine virtuelle SUSE Linux dans le réseau virtuel Azure. Pour déployer la machine virtuelle, prenez une image de la galerie SLES 12 SP2 d’Azure. Dans le processus de déploiement, ne définissez pas de nom DNS et n’utilisez pas d’adresses IP statiques comme indiqué dans cette capture d’écran.

![Déploiement de machine virtuelle pour le serveur SMT](./media/hana-installation/image3_vm_deployment.png)

La machine virtuelle déployée était plus petite et a obtenu l’adresse IP 10.34.1.4 dans le réseau virtuel Azure. Le nom de la machine virtuelle était smtserver. Après l’installation, la connectivité aux unités de grande instance HANA a été activée. Selon la façon dont vous avez organisé la résolution de noms, il se peut que vous deviez configurer la résolution des unités de grande instance HANA dans le fichier etc/hosts de la machine virtuelle Azure. Ajoutez un disque à la machine virtuelle qui va accueillir les correctifs. Le disque de démarrage lui-même pourrait être trop petit. Dans le cas présenté, le disque a été monté sur /srv/www/htdocs comme indiqué dans la capture d’écran suivante. Un disque de 100 Go devrait suffire.

![Déploiement de machine virtuelle pour le serveur SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Connectez-vous aux unités de grande instance HANA, conservez le fichier /etc/hosts et vérifiez si vous pouvez atteindre la machine virtuelle Azure supposée exécuter le serveur SMT sur le réseau.

Une cette vérification réussie, vous devez vous connecter à la machine virtuelle Azure supposée exécuter le serveur SMT. Si vous utilisez PuTTY pour vous connecter à la machine virtuelle, vous devez exécuter cette séquence de commandes dans la fenêtre de interpréteur de commandes :

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Après avoir exécuté ces commandes, redémarrez votre interpréteur de commandes pour activer les paramètres. Ensuite, démarrez YaST.

Dans YaST, accédez à Software Maintenance (Maintenance logicielle), puis recherchez smt. Sélectionnez smt, qui bascule automatiquement vers yast2-smt, comme illustré ci-dessous.

![SMT dans YaST](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptez la sélection pour l’installation sur smtserver. Une fois l’installation terminée, accédez à la configuration du serveur SMT, puis entrez les informations d'identification de votre organisation que vous avez récupérées précédemment à partir du Centre de clients SUSE. Entrez également le nom d’hôte de votre machine virtuelle Azure en tant qu’URL du serveur SMT. Dans cette démonstration, il s’agit de https://smtserver, comme affiché dans le graphique suivant.

![Configuration du serveur SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

À l’étape suivante, vous allez vérifier si la connexion au Centre de clients SUSE fonctionne. Comme le montrent les graphiques suivants, dans le cas de la démonstration, cela n’a pas fonctionné.

![Test de connexion au Centre de clients SUSE](./media/hana-installation/image7_test_connect.png)

Lors que le programme d’installation de SMT démarre, vous devez fournir un mot de passe de base de données. Comme il s’agit d’une nouvelle installation, vous devez définir celui-ci comme indiqué dans le graphique suivant.

![Définir le mot de passe pour la base de données](./media/hana-installation/image8_define_db_passwd.PNG)

L’interaction suivante intervient lors de la création d’un certificat. Parcourez la boîte de dialogue comme dans l’illustration suivante pour achever l’étape.

![Créer un certificat pour le serveur SMT](./media/hana-installation/image9_certificate_creation.PNG)

L’étape « Run synchronization check » (Exécuter le contrôle de synchronisation) à la fin de la configuration peut prendre quelques minutes. Une fois l’installation et la configuration du serveur SMT terminées, vous devez rechercher le référentiel de répertoire sous le point de montage /srv/www/htdocs/, ainsi que certains sous-répertoires dans le référentiel. 

Redémarrez le serveur SMT et ses services associés avec ces commandes.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Télécharger des packages sur le serveur SMT

Une fois que tous les services ont redémarré, sélectionnez les packages appropriés dans SMT Management (Gestion de SMT) à l’aide de YaST. La sélection du package dépend de l’image de système d'exploitation du serveur de grande instance HANA, et non de la publication ou de la version SLES de la machine virtuelle exécutant le serveur SMT. Vous trouverez ci-dessous un exemple de l’écran de sélection.

![Sélectionner des packages](./media/hana-installation/image10_select_packages.PNG)

Une fois les packages sélectionnés, vous devez démarrer leur copie initiale sur le serveur SMT que vous configurés. Cette copie est déclenchée dans l’interpréteur de commandes à l’aide de la commande smt-mirror, comme illustré ci-dessous :


![Télécharger des packages sur le serveur SMT](./media/hana-installation/image11_download_packages.PNG)

Comme ci-dessus, les packages doivent être copiés dans les répertoires créés sous le montage point /srv/www/htdocs. Ce processus peut prendre un certain temps. Selon le nombre de packages que vous sélectionnez, il peut prendre jusqu’à une heure, voire davantage.
Lorsque ce processus s’achève, vous devez passer à la configuration du client SMT. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurer le client SMT sur des unités de grande instance HANA

Dans ce cas, les clients sont les unités de grande instance HANA. Le programme d’installation du serveur SMT a copié le script clientSetup4SMT.sh sur la machine virtuelle Azure. Copiez ce script sur l’unité de grande instance HANA que vous souhaitez connecter à votre serveur SMT. Démarrez le script avec l’option -h, puis donnez-lui en guise de paramètre le nom de votre serveur SMT. Dans ce exemple, il s’agit de smtserver.

![Configurer un client SMT](./media/hana-installation/image12_configure_client.PNG)

Il peut y avoir un scénario où le chargement du certificat à partir du serveur par le client réussit, mais où l’inscription échoue, comme illustré ci-dessous.

![L’inscription du client échoue](./media/hana-installation/image13_registration_failed.PNG)

Si l’inscription échoue, lisez ce [document de support SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024), puis exécutez les étapes qu’il décrit.

> [!IMPORTANT] 
> En tant que nom de serveur, vous devez fournir le nom de la machine virtuelle, en l’occurrence smtserver, sans le nom de domaine complet. Le nom de la machine virtuelle suffit. 

Une fois ces étapes effectuées, vous devez exécuter la commande suivante sur l’unité de grande instance HANA.

```
SUSEConnect –cleanup
```

> [!Note] 
> Dans nos tests, nous avons toujours dû attendre quelques minutes après cette étape. L’exécution immédiate de clientSetup4SMT.sh, après les mesures de correction décrites dans l’article sur SUSE, s’est terminée avec des messages indiquant que le certificat n’était pas encore valide. Une attente de 5 à 10 minutes, puis l’exécution de clientSetup4SMT.sh a abouti à une configuration réussie du client.

Si vous avez rencontré le problème et avez du le résoudre en suivant les étapes de l’article sur SUSE, vous devez redémarrer clientSetup4SMT.sh sur l’unité de grande instance HANA. L’opération devrait à présent aboutir correctement, comme indiqué ci-dessous.

![Inscription du client réussie](./media/hana-installation/image14_finish_client_config.PNG)

Dans le cadre de cette étape, vous avez configuré le client SMT de l’unité de grande instance HANA pour qu’il se connecte au serveur SMT que vous avez installé sur la machine virtuelle Azure. Vous pouvez à présent utiliser « zypper up » ou « zypper in » pour installer des correctifs logiciels de système d’exploitation sur des grandes instances HANA ou installer des packages supplémentaires. Il est entendu que vous pouvez obtenir uniquement les correctifs que vous avez téléchargés auparavant sur le serveur SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exemple d’installation de SAP HANA sur des grandes instances HANA
Cette section illustre la procédure d’installation de SAP HANA sur une unité de grande instance HANA. L’état de démarrage que nous avons ressemble à ceci :

- Vous avez fourni à Microsoft toutes les données pour déployer pour vous une grande instance SAP HANA.
- Vous avez reçu la grande instance SAP HANA de Microsoft.
- Vous avez créé un réseau virtuel Azure qui est connecté à votre réseau local.
- Vous connectez le circuit ExpressRoute pour les grandes instances HANA au même réseau virtuel Azure.
- Vous avez installé une machine virtuelle Azure que vous utilisez comme serveur intermédiaire pour les grandes instances HANA.
- Vérifiez que vous pouvez vous connecter à partir du serveur intermédiaire à l’unité de grande instance HANA et inversement.
- Vous avez vérifié si tous les packages et correctifs requis sont installés.
- Vous avez lu les notes et la documentation de SAP concernant l’installation de HANA sur le système d’exploitation que vous utilisez, et vérifié que la version de HANA choisie est prise en charge sur la version du système d'exploitation.

Les séquences suivantes présentent le téléchargement des packages d’installation de HANA sur la machine virtuelle faisant office de serveur intermédiaire qui s’exécute en l’occurrence sur un système d’exploitation Windows, la copie des packages vers l’unité de grande instance HANA et la séquence de l’installation.

### <a name="download-of-the-sap-hana-installation-bits"></a>Télécharger les bits d’installation de SAP HANA
Étant donné que les unités de grande instance HANA n’ont pas de connexion directe à Internet, vous ne pouvez pas télécharger les packages d’installation directement de SAP sur la machine virtuelle de grande instance HANA. Pour surmonter le problème d’absence de connexion Internet directe, vous avez besoin du serveur intermédiaire. Vous téléchargez les packages sur la machine virtuelle faisant office de serveur intermédiaire.

Pour télécharger les packages d’installation de HANA, vous avez besoin d’un utilisateur S ou d’un autre utilisateur SAP, qui vous permet d’accéder au Marketplace SAP. Une fois connecté, parcourez cette séquence d’écrans :

Accédez au [Marketplace du Service SAP](https://support.sap.com/en/index.html) > Cliquez sur Download Software (Télécharger un logiciel) > Installations and Upgrade (Installations et mise à niveau) > By Alphabetical Index (par index alphabétique) > Sous H – SAP HANA Platform Edition (Édition de plateforme SAP HANA) > SAP HANA Platform Edition 2.0 (Plateforme SAP HANA Édition 2.0) > Installation > Download the following files (Télécharger les fichiers suivants)

![Télécharger les packages d’installation de HANA](./media/hana-installation/image16_download_hana.PNG)

Dans le cas de la démonstration, nous avons téléchargé les packages d’installation SAP HANA 2.0. Sur la machine virtuelle Azure faisant office de serveur intermédiaire, vous développez les archives à extraction automatique dans le répertoire, comme illustré ci-dessous.

![Extraire les packages d’installation de HANA](./media/hana-installation/image17_extract_hana.PNG)

Les archives étant extraites, copiez le répertoire créé par l’extraction, dans le cas ci-dessus 51052030, vers l’unité de grande instance HANA dans le volume /hana/shared figurant dans un répertoire que vous avez créé.

> [!Important]
> Ne copiez pas les packages d’installation dans la racine ou le LUN de démarrage, car l’espace est limité et doit être utilisé également par d’autres processus.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installer SAP HANA sur l’unité de grande instance HANA
Pour installer SAP HANA, vous devez vous connecter en tant qu’utilisateur racine. Seule la racine dispose d’autorisations suffisantes pour installer SAP HANA.
La première chose à faire consiste à définir des autorisations sur le répertoire que vous avez recopié dans /hana/shared. Les autorisations doivent être définies comme suit :

```
chmod –R 744 <Installation bits folder>
```

Si vous souhaitez installer SAP HANA à l’aide du programme d’installation graphique, le package gtk2 doit être installé sur les grandes instances HANA. Vérifiez si l’installation a réussi avec la commande suivante :

```
rpm –qa | grep gtk2
```

Les étapes suivantes montrent l’installation de SAP HANA avec l’interface utilisateur graphique. En guide d’étape suivante, accédez au répertoire d’installation, puis naviguez dans le sous-répertoire HDB_LCM_LINUX_X86_64. Démarrez

```
./hdblcmgui 
```
en dehors de ce répertoire. Vous êtes à présent guidé dans une séquence d’écrans dans laquelle vous devez fournir les données nécessaires pour l’installation. Dans le cas présenté, nous installons le serveur de base de données SAP HANA et les composants du client SAP HANA. Par conséquent, notre sélection est « SAP HANA Database » (Base de données SAP HANA), comme illustré ci-dessous.

![Sélectionner HANA dans l’installation](./media/hana-installation/image18_hana_selection.PNG)

Dans l’écran suivant, vous choisissez l’option « Install New System » (Installer une nouveau système).

![Sélectionner une nouvelle installation de HANA](./media/hana-installation/image19_select_new.PNG)

Après cette étape, vous devez opérer une sélection parmi plusieurs composants supplémentaires qui peuvent être installés par ailleurs sur le serveur de base de données SAP HANA.

![Sélectionner des composants supplémentaires HANA](./media/hana-installation/image20_select_components.PNG)

Dans cette documentation, nous avons choisi le SAP HANA Client et SAP HANA Studio. Nous avons également installé une instance de montée en puissance parallèle. Par conséquent, dans l’écran suivant, vous devez choisir « Single-Host System » (Système à hôte unique). 

![Sélectionner un installation avec montée en puissance parallèle](./media/hana-installation/image21_single_host.PNG)

Dans l’écran suivant, vous devez fournir des données.

![Fournir le SID de SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> En tant qu’ID système (SID) HANA, vous devez fournir le même SID que celui fourni à Microsoft lors de la commande du déploiement de la grande instance HANA. Le choix d’un SID différent fait échouer l’installation en raison de problèmes d’autorisation d’accès sur les différents volumes.

En tant que répertoire d’installation, vous utilisez /hana/shared. À l’étape suivante, vous devez fournir les emplacements des fichiers de données et des fichiers journaux de HANA.


![Fournir l’emplacement du journal de HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Vous devez définir en tant que données et fichiers journaux les volumes qui accompagnaient déjà les points de montage contenant le SID que vous avez choisi dans la sélection de l’écran précédant cet écran. Si le SID ne correspond pas à celui que vous avez tapé dans l’écran précédent, revenez en arrière et ajustez le SID sur la valeur définie sur les points de montage.

À l’étape suivante, révisez le nom d’hôte et corrigez-le éventuellement. 

![Réviser le nom d’hôte](./media/hana-installation/image24_review_host_name.PNG)

À l’étape suivante, vous devez également récupérer les données que vous avez communiquées à Microsoft lors de la commande du déploiement de la grande instance HANA. 

![Fournir l’UID et le GID de l’utilisateur système](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Vous devez fournir les mêmes ID d’utilisateur système et ID de groupe d’utilisateurs que ceux fournis à Microsoft lors de la commande du déploiement de l’unité. Si vous ne parvenez pas à donner les mêmes ID, l’installation de SAP HANA sur l’unité de grande instance HANA échoue.

Dans les deux écrans suivants, non illustrés dans cette documentation, vous devez fournir le mot de passe de l’utilisateur SYSTÈME de la base de données SAP HANA, et le mot de passe de l’utilisateur sapadm utilisé pour l’agent hôte SAP qui est installé dans le cadre de l’instance de base de données SAP HANA.

Une fois le mot de passe défini, un écran de confirmation s’affiche. Vérifiez toutes les données affichées, puis poursuivez l’installation. Vous accédez à un écran montrant la progression de l’installation, comme celui ci-dessous.

![Vérifier la progression de l’installation](./media/hana-installation/image27_show_progress.PNG)

Lorsque l’installation se termine, vous devez voir une image telle que la suivante.

![L’installation est terminée](./media/hana-installation/image28_install_finished.PNG)

À ce stade, l’instance SAP HANA doit être opérationnelle et prête à l’emploi. Vous devez être en mesure de vous y connecter à partir de SAP HANA Studio. Veillez également à rechercher les derniers correctifs de SAP HANA et à les appliquer.
























































 







 




