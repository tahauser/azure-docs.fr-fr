---
title: "Présentation et architecture de SAP HANA sur Azure (grandes instances) | Microsoft Docs"
description: "Présentation de l’architecture du déploiement de SAP HANA sur Azure (grandes instances)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2461e5fbf620fa2651792b47d41e9835d4d6ef8c
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Qu’est-ce que SAP HANA sur Azure (grandes instances) ?

SAP HANA sur Azure (grandes instances) est une solution Azure unique. En plus de fournir des machines virtuelles Azure à des fins de déploiement et d’exécution de SAP HANA, Azure vous offre la possibilité d’exécuter et de déployer SAP HANA sur des serveurs nus qui vous sont dédiés en tant que client. La solution SAP HANA sur Azure (grandes instances) s’appuie sur un matériel nu de type serveur/hôte non partagé, qui vous est assigné en tant que client. Le matériel de serveur est incorporé dans des tampons plus volumineux qui contiennent l’infrastructure de calcul/serveur, de réseau et de stockage. Cette combinaison est certifiée HANA TDI. L’offre de service SAP HANA sur Azure (grandes instances) propose plusieurs références SKU ou tailles de serveur, d’unités dotées de 72 processeurs et 768 Go de mémoire à des unités dotées de 960 processeurs et 20 To de mémoire.

L’isolation du client dans le tampon de l’infrastructure s’effectue dans les locataires de la manière suivante :

- Mise en réseau : isolation des clients au sein de la pile d’infrastructure au travers de réseaux virtuels par locataire assigné à un client. Un locataire est assigné à un seul client. Un client peut avoir plusieurs locataires. L’isolation réseau des locataires interdit la communication réseau entre les locataires au niveau du tampon d’infrastructure. Et ce, même si les locataires appartiennent au même client.
- Composants de stockage : isolation par le biais de machines virtuelles auxquelles des volumes de stockage sont assignés. Les volumes de stockage ne peuvent être assignés qu’à une seule machine virtuelle de stockage. Une machine virtuelle de stockage est assignée exclusivement à un seul locataire dans la pile d’infrastructure certifiée SAP HANA TDI. Par conséquent, les volumes de stockage assignés à une machine virtuelle de stockage sont accessibles dans un seul locataire associé. Et ils ne sont pas visibles entre les différents locataires déployés.
- Serveur ou hôte : unité de serveur ou hôte qui n’est pas partagée entre les clients ou les locataires. Un serveur ou un hôte déployé pour un client constitue une unité de calcul nue atomique qui est assignée à un seul locataire. **Aucun** partitionnement matériel ou logiciel utilisé ne peut vous conduire, en tant que client, à partager un hôte ou un serveur avec un autre client. Les volumes de stockage qui sont assignés à la machine virtuelle de stockage du locataire spécifique sont montés sur ce type de serveur. Une ou plusieurs unités de serveur de différentes références SKU peuvent être exclusivement assignées à un seul locataire.
- Au sein d’une solution SAP HANA sur le tampon d’infrastructure Azure (grandes instances), plusieurs locataires sont déployés et isolés les uns par rapport aux autres, au travers des concepts de locataire sur les niveaux de mise en réseau, de stockage et de calcul. 


Ces unités de serveur nues exécutent uniquement SAP HANA. La couche Application SAP ou la couche intermédiaire de la charge de travail est en cours d’exécution dans les Machines virtuelles Microsoft Azure. Les tampons d’infrastructure exécutant SAP HANA sur des unités Azure (grandes instances) sont connectés aux dorsales principales de réseau Azure : une connectivité de faible latence entre SAP HANA sur des unités Azure (grandes instances) et les machines virtuelles Azure est donc fournie.

Ce document constitue l’un des cinq documents qui traitent de SAP HANA sur Azure (grandes instances). Dans ce document, nous présentons brièvement l’architecture de base, les responsabilités et les services fournis, puis nous détaillons les fonctionnalités de la solution. Les quatre autres documents apportent plus de détails sur la plupart des autres points, comme la mise en réseau et la connectivité. La documentation de SAP HANA sur Azure (grandes instances) n’aborde pas les aspects de l’installation de SAP NetWeaver ni les déploiements de SAP NetWeaver dans les machines virtuelles Azure. Cette rubrique est couverte dans la documentation spécifique disponible dans le même conteneur de documentation. 


Les cinq parties de ce guide traitent des thèmes suivants :

- [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Guide pratique de résolution des problèmes et de surveillance de SAP HANA (grandes instances) sur Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Définitions

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS :** Infrastructure as a Service
- **PaaS :** Platform as a Service
- **SaaS :** Software as a Service
- **Composant SAP :** une application SAP individuelle telle que ECC, BW, Solution Manager ou EP. Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP :** un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
- **Paysage SAP :** fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
- **Système SAP :** ensemble couche SGBD/couche Application d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM, etc. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites et Azure. Cela signifie qu’un système SAP doit être déployé en local ou dans Azure. Toutefois, vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Pour SAP HANA sur Azure (grandes instances), vous devez héberger la couche Application SAP des systèmes SAP dans des machines virtuelles Azure et l’instance SAP HANA liée sur une unité dans le tampon de grande instance HANA.
- **Tampon de grande instance :** une pile d’infrastructure matérielle certifiée SAP HANA TDI et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA TDI certifié qui est déployé dans les tampons de grande instance dans différentes régions Azure. Le terme associé **Grande instance HANA** est la forme abrégée de SAP HANA sur Azure (grandes instances) largement utilisée dans ce guide de déploiement technique.
- **Intersite :** décrit un scénario dans lequel les machines virtuelles sont déployées vers un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, le répertoire Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure du ou des abonnements Azure. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Consultez les guides de [planification et conception de la passerelle VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et de [création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations.
- **Locataire :** un client déployé dans le tampon de grande instance HANA est isolé dans un « locataire ». Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Par conséquent, les unités de stockage et de calcul assignées aux différents locataires ne peuvent pas se voir les unes les autres ni communiquer sur le niveau de tampon de grande instance HANA. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires sur le niveau de tampon de grande instance HANA.
- **Catégorie de référence SKU :** pour les grandes instances HANA, les deux catégories suivantes de références SKU sont proposées.
    - **Classe de type I :** S72, S72m, S144, S144m, S192 et S192m
    - **Classe de type II :** S384, S384m, S384xm, S576, S768 et S960


Il existe de nombreuses ressources supplémentaires qui ont été publiées sur le sujet du déploiement de la charge de travail SAP sur un cloud public Microsoft Azure. Il est vivement recommandé que toute planification et toute exécution d’un déploiement de SAP HANA dans Azure soit effectuée par une personne expérimentée, connaissant les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Les ressources suivantes fournissent davantage d’informations et doivent être consultées avant de continuer :


- [Utilisation de solutions SAP sur des machines virtuelles Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certification

Outre la certification NetWeaver, SAP requiert une certification spéciale pour SAP HANA, afin de prendre en charge SAP HANA sur certaines infrastructures, telles que Azure IaaS.

La Note SAP principale sur NetWeaver, et dans une certaine mesure sur la certification SAP HANA, est [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533).

La Note SAP [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances) (Note SAP n° 2316233 - SAP HANA sur Microsoft Azure (grandes instances))](https://launchpad.support.sap.com/#/notes/2316233/E) est également importante. Elle aborde la solution décrite dans ce guide. En outre, vous pouvez exécuter SAP HANA dans le type de machine virtuelle GS5 d’Azure. [Les informations relatives à cette situation sont disponibles sur le site Web SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solution SAP HANA sur Azure (grandes instances) à laquelle il est fait référence dans la Note SAP n° 2316233 fournit aux clients SAP et Microsoft la possibilité de déployer les ressources volumineuses SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA ou d’autres charges de travail SAP HANA dans Azure. La solution est basée sur le tampon matériel dédié et certifié SAP-HANA ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)). Grâce à l’exécution en tant que solution configurée SAP HANA TDI, vous avez l’assurance que toutes les applications SAP HANA (y compris SAP Business Suite sur SAP HANA, SAP Business Warehouse (BW) sur SAP HANA, S4/HANA et BW4/HANA) fonctionneront sur l’infrastructure matérielle.

En comparaison avec l’exécution de SAP HANA dans les machines virtuelles Azure, cette solution présente l’avantage de fournir des volumes de mémoire beaucoup plus importants. Si vous souhaitez activer cette solution, vous devez avoir connaissance de certains aspects importants :

- La couche Application SAP et les applications autres que SAP s’exécutent dans des machines virtuelles Azure hébergées dans les tampons matériels Azure habituels.
- Les déploiements d’applications, de centres de données et d’infrastructures locaux du client sont connectés à la plateforme cloud Microsoft Azure via Azure ExpressRoute (recommandé) ou un réseau privé virtuel (VPN). Le répertoire Active Directory (AD) et le DNS sont également étendus dans Azure.
- L’instance de base de données SAP HANA pour les charges de travail HANA s’exécute sur SAP HANA sur Azure (grandes instances). Le tampon de grande instance est connecté au réseau Azure, afin que les logiciels s’exécutant dans des machines virtuelles Azure puissent interagir avec l’instance HANA s’exécutant dans les grandes instances HANA.
- Le matériel de SAP HANA sur Azure (grandes instances) est un matériel dédié fourni dans une Infrastructure as a Service (IaaS) avec SUSE Linux Enterprise Server ou Red Hat Enterprise Linux préinstallé. Comme pour les machines virtuelles Azure, les mises à jour et la maintenance supplémentaires du système d’exploitation relèvent de votre responsabilité.
- L’installation de HANA ou de tous les autres composants nécessaires à l’exécution de SAP HANA sur des unités de grandes instances HANA relève de votre responsabilité, tout comme l’ensemble des administrations et opérations respectives en cours de SAP HANA sur Azure.
- Outre les solutions décrites ici, vous pouvez installer d’autres composants dans votre abonnement Azure qui se connecte à SAP HANA sur Azure (grandes instances).  Par exemple, les composants qui permettent la communication avec et directement avec la base de données SAP HANA (serveurs jump, serveurs RDP, SAP HANA Studio, scénarios SAP Data Services for SAP BI ou des solutions de surveillance réseau).
- Comme dans Azure, les grandes instances HANA prennent en charge des fonctionnalités de haute disponibilité et de récupération d’urgence.

## <a name="architecture"></a>Architecture

À un niveau élevé, la couche Application SAP de la solution SAP HANA sur Azure (grandes instances) réside sur des machines virtuelles Azure, tandis que la couche Base de données réside sur un matériel configuré SAP TDI situé dans un tampon de grande instance dans la même région Azure qui est connectée à Azure IaaS.

> [!NOTE]
> Vous devez déployer la couche Application SAP dans la même région Azure que la couche SGBD SAP. Cette règle est bien documentée dans les informations publiées sur la charge de travail SAP sur Azure. 

L’architecture globale de SAP HANA sur Azure (grandes instances) fournit une configuration matérielle certifiée SAP TDI (serveur non virtualisé, nu et hautes performances pour la base de données SAP HANA), ainsi que la capacité et la flexibilité d’Azure pour mettre à l’échelle les ressources de la couche Application SAP, afin de répondre à vos besoins.

![Présentation de l’architecture de SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image1-architecture.png)

L’architecture présentée est divisée en trois sections :

- **À droite :** une infrastructure locale exécutant différentes applications dans des centres de données avec les utilisateurs finaux accédant aux applications métier (telles que SAP). Dans l’idéal, cette infrastructure locale est ensuite connectée à Azure avec Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Au centre :** montre Azure IaaS et, dans ce cas, l’utilisation des machines virtuelles Azure pour héberger SAP ou d’autres applications qui utilisent SAP HANA en tant que système SGBD. Les instances HANA plus petites qui fonctionnent avec la mémoire que les machines virtuelles Azure fournissent sont déployées dans les machines virtuelles Azure avec leur couche Application. En savoir plus sur les [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).
<br />La mise en réseau Azure permet de regrouper les systèmes SAP ainsi que d’autres applications dans les réseaux virtuels Azure. Ces réseaux virtuels se connectent à des systèmes locaux et à SAP HANA sur Azure (grandes instances).
<br />Pour les bases de données et les applications SAP NetWeaver qui sont prises en charge pour être exécutées dans Microsoft Azure, consultez [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note de prise en charge SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533). Pour la documentation sur le déploiement des solutions SAP sur Azure, veuillez consulter :

  -  [Utilisation de SAP sur des machines virtuelles Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utilisation de solutions SAP sur des machines virtuelles Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À gauche :** montre le matériel certifié SAP HANA TDI dans le tampon de grande instance Azure. Les unités de grande instance HANA sont connectées aux réseaux virtuels Azure de votre abonnement à l’aide de la même technologie que la connectivité du système local à Azure.

Le tampon de grande instance Azure combine les composants suivants :

- **Calcul :** serveurs basés sur des processeurs Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 qui fournissent les capacités de calcul nécessaires et sont certifiés SAP HANA.
- **Réseau :** une structure réseau haut débit unifiée qui relie le calcul, le stockage et les composants de réseau local.
- **Stockage :** une infrastructure de stockage qui est accessible via une structure réseau unifiée. La capacité de stockage spécifique est fournie selon la configuration SAP HANA sur Azure particulière (grandes instances) en cours de déploiement (plus de capacité de stockage disponible pour un coût mensuel supplémentaire).

Dans l’infrastructure multilocataire du tampon de grande instance, les clients sont déployés en tant que locataires isolés. Au moment du déploiement du locataire, vous devez nommer un abonnement Azure au sein de votre inscription Azure. Il s’agira de l’abonnement Azure sur lequel la ou les instances de grande taille HANA seront facturées. Ces locataires ont une relation 1:1 avec l’abonnement Azure. En termes de réseau, il est possible d’accéder à une unité de grande instance HANA déployée sur un locataire dans une région Azure à partir de différents réseaux virtuels Azure, qui appartiennent à différents abonnements Azure. Cependant, ces abonnements Azure doivent appartenir à la même inscription Azure. 

Comme avec les machines virtuelles Azure, SAP HANA sur Azure (grandes instances) est proposé dans plusieurs régions Azure. Pour bénéficier de fonctionnalités de récupération d’urgence, vous pouvez choisir de vous y abonner. Les différents tampons de grande instance d’une même région géopolitique sont connectés entre eux. Par exemple, les tampons de grande instance HANA de la région ouest et de la région est des États-Unis sont connectés via une liaison réseau dédiée à des fins de réplication de la récupération d’urgence. 

Tout comme vous avez le choix entre différents types de machines virtuelles avec Azure Virtual Machines, vous pouvez choisir parmi différentes références de grandes instances HANA qui sont adaptées aux divers types de charges de travail de SAP HANA. SAP applique la mémoire aux rapports de socket de processeur pour différentes charges de travail en fonction des générations de processeurs Intel. Le tableau qui suit affiche les types de références SKU proposées.

À compter de juillet 2017, SAP HANA sur Azure (grandes instances) est disponible dans plusieurs configurations dans les régions Azure de l’ouest et de l’est des États-Unis, de l’est et du sud-est de l’Australie, et enfin de l’ouest et du nord de l’Europe :

| Solution SAP | UC | Mémoire | Storage | Disponibilité |
| --- | --- | --- | --- | --- |
| Optimisée pour OLAP : SAP BW, BW/4HANA<br /> ou SAP HANA pour les charges de travail OLAP génériques | SAP HANA sur Azure S72<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  768 Go |  3 To | Disponible |
| --- | SAP HANA sur Azure S144<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  1,5 To |  6 To | Plus proposé |
| --- | SAP HANA sur Azure S192<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC |  2 TO |  8 To | Disponible |
| --- | SAP HANA sur Azure S384<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  4 TO |  16 TO | Prêt à commander |
| Optimisée pour OLTP : SAP Business Suite<br /> sur SAP HANA ou S/4HANA (OLTP),<br /> OLTP générique | SAP HANA sur Azure S72m<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  1,5 To |  6 To | Disponible |
|---| SAP HANA sur Azure S144m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  3 TO |  12 To | Plus proposé |
|---| SAP HANA sur Azure S192m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC  |  4 TO |  16 TO | Disponible |
|---| SAP HANA sur Azure S384m<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  6,0 To |  18 To | Prêt à commander |
|---| SAP HANA sur Azure S384xm<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  8,0 To |  22 To |  Prêt à commander |
|---| SAP HANA sur Azure S576<br /> - 12 x processeurs Intel® Xeon® E7-8890 v4<br /> 288 cœurs et 576 threads d’UC |  12,0 To |  28 To | Prêt à commander |
|---| SAP HANA sur Azure S768<br /> - 16 x processeurs Intel® Xeon® E7-8890 v4<br /> 384 cœurs et 768 threads d’UC |  16,0 To |  36 To | Prêt à commander |
|---| SAP HANA sur Azure S960<br /> - 20 x processeurs Intel® Xeon® E7-8890 v4<br /> 480 cœurs et 960 threads d’UC |  20,0 To |  46 To | Prêt à commander |

- Cœurs d’unité centrale = somme des cœurs d’UC non multithreads dérivée de la somme des processeurs de l’unité de serveur.
- Threads d’UC = somme des threads de calcul fournis par les cœurs d’UC multithreads dérivée de la somme des processeurs de l’unité de serveur. Toutes les unités sont configurées par défaut pour utiliser la technologie Hyper-Threading.


Les différentes configurations ci-dessus, qui sont « Disponible » ou « Plus proposé », sont référencées dans la [Note de prise en charge SAP #2316233 – SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (SAP HANA sur Microsoft Azure (grandes instances)). Les configurations qui sont marquées comme « Prêt pour la commande » seront bientôt intégrées à la Note SAP. Cependant, ces références (SKU) d’instance peuvent déjà être commandées pour les six régions Azure dans lesquelles le service de grande instance HANA est disponible.

Les configurations spécifiques choisies dépendent de la charge de travail, des ressources d’UC et de la mémoire souhaitée. La charge de travail OLTP est en mesure d’utiliser des références optimisées pour les charges de travail OLAP. 

La base matérielle pour toutes les offres est certifiée SAP HANA TDI. Toutefois, nous faisons la distinction entre deux classes de matériel, qui séparent les références SKU comme suit :

- S72, S72m, S144, S144m, S192 et S192m, que nous appelons la « Classe Type I » des références SKU ;
- S384, S384m, S384xm, S576, S768 et S960, que nous appelons la « Classe Type II » des références SKU.

Il est important de noter qu’un tampon de grande instance HANA complet n’est pas exclusivement alloué à une utilisation pour un seul client. Cela s’applique également aux racks de ressources de calcul et de stockage connectés par le biais d’une structure réseau déployée dans Azure. L’infrastructure de grandes instances HANA, par exemple Azure, déploie plusieurs &quot;locataires&quot; client qui sont séparés les uns des autres sur trois niveaux :

- Réseau : isolation par le biais des réseaux virtuels dans le tampon de grande instance HANA.
- Stockage : isolation par le biais de machines virtuelles auxquelles des volumes de stockage sont assignés et qui isolent les volumes de stockage entre les locataires.
- Calcul : attribution des unités de serveur à un seul locataire. Aucun partitionnement matériel ou logiciel des unités de serveur. Aucun partage d’une unité de serveur ou hôte unique entre les locataires. 

Par conséquent, les déploiements d’unités de grandes instances HANA entre les différents locataires ne sont pas visibles entre eux. Et les unités de grandes instances HANA déployées dans différents locataires ne peuvent pas communiquer directement entre elles au niveau du tampon de grande instance HANA. Seules les unités de grandes instances HANA d’un locataire unique peuvent communiquer entre elles au niveau du tampon de grande instance HANA.
Un locataire déployé dans le tampon de grande instance est attribué à un abonnement Azure en termes de facturation. Cependant, en termes de réseau, il est accessible à partir des réseaux virtuels Azure d’autres abonnements Azure dans la même inscription Azure. Si vous effectuez un déploiement avec un autre abonnement Azure dans la même région Azure, vous pouvez également demander à obtenir un locataire de grande instance HANA distinct.

Il existe des différences importantes entre l’exécution de SAP HANA sur les grandes instances HANA et l’exécution de SAP HANA sur les machines virtuelles Azure déployées dans Azure :

- Il n’existe aucune couche Virtualisation pour SAP HANA sur Azure (grandes instances). Vous bénéficiez des performances du matériel nu sous-jacent.
- Contrairement à Azure, le serveur SAP HANA sur Azure (grandes instances) est dédié à un client spécifique. Il est impossible qu’une unité de serveur ou hôte fasse l’objet d’un partitionnement matériel ou logiciel. Par conséquent, une unité de grande instance HANA est assignée dans son intégralité à un locataire, et donc à vous en tant que client. Un redémarrage ou un arrêt du serveur n’entraîne pas automatiquement le déploiement du système d’exploitation et de SAP HANA sur un autre serveur. (Pour les références SKU de classe Type I, la seule exception est lorsqu’un serveur rencontre des problèmes et qu’un redéploiement doit être effectué sur un autre serveur.)
- Contrairement à Azure, où les types de processeurs hôtes sont sélectionnés en fonction du meilleur rapport prix/performances, les types de processeurs choisis pour SAP HANA sur Azure (grandes instances) sont les plus performants de la gamme de processeurs Intel E7v3 et E7v4.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Exécution de plusieurs instances SAP HANA sur une unité de grande instance HANA
Il est possible d’héberger plusieurs instances SAP HANA actives sur les unités de grande instance HANA. Afin de continuer à fournir les fonctionnalités de captures instantanées du stockage et de récupération d’urgence, une telle configuration requiert un volume défini par instance. À partir de maintenant, les unités de grande instance HANA peuvent être classées comme suit :

- S72, S72m, S144, S192 : par incréments de 256 Go avec l’unité de départ la plus petite qui fait 256 Go. Des incréments différents, comme 256 Go, 512 Go, etc., peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- S144m et S192m : par incréments de 256 Go avec la plus petite unité qui fait 512 Go. Des incréments différents, comme 512 Go, 768 Go, etc., peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- Classe Type II : par incréments de 512 Go avec l’unité de départ la plus petite qui fait 2 To. Des incréments différents, comme 512 Go, 1 To, 1,5 To, etc., peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.

Voici quelques exemples de ce à quoi pourrait ressembler l’exécution de plusieurs instances SAP HANA :

| SKU | Taille de la mémoire | Taille de stockage | Tailles avec plusieurs bases de données |
| --- | --- | --- | --- |
| S72 | 768 Go | 3 To | 1 x instance HANA de 768 Go<br /> ou 1 x instance de 512 Go + 1 x instance de 256 Go<br /> ou 3 x instances de 256 Go | 
| S72m | 1,5 To | 6 To | 3 x instances HANA de 512 Go<br />ou 1 x instance de 512 Go + 1 x instance de 1 To<br />ou 6 x instances de 256 Go<br />ou 1 x instance de 1,5 To | 
| S192m | 4 To | 16 TO | 8 x instances de 512 Go<br />ou 4 x instances de 1 To<br />ou 4 x instances de 512 Go + 2 x instances de 1 To<br />ou 4 x instances de 768 Go + 2 x instances de 512 Go<br />ou 1 x instance de 4 To |
| S384xm | 8 To | 22 To | 4 x instances de 2 To<br />ou 2 x instances de 4 To<br />ou 2 x instances de 3 To + 1 x instance de 2 To<br />ou 2 x instances de 2,5 To + 1 x instance de 3 To<br />ou 1 x instance de 8 To |


Vous avez compris l’idée. Il existe certainement d’autres variantes. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>Utilisation des nœuds d’extension et de la hiérarchisation des données SAP HANA
SAP prend en charge un modèle de hiérarchisation des données pour SAP BW de différentes versions SAP NetWeaver et pour SAP BW/4HANA. Des informations détaillées concernant ce modèle de hiérarchisation des données sont à votre disposition dans ce document, ainsi que dans le blog référencé dans ce document par SAP : [SAP BW/4HANA AND SAP BW ON HANA WITH SAP HANA EXTENSION NODES](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Avec les grandes instances HANA, vous pouvez utiliser la configuration de l’option 1 des nœuds d’extension SAP HANA, comme détaillé dans ces Questions fréquentes (FAQ) et documents de blog SAP. Les configurations de l’option 2 peuvent être définies avec les références SKU de grandes instances HANA suivantes : S72m, S192, S192m, S384 et S384m.  
L’avantage de telles configurations n’est pas forcément visible immédiatement lorsque l’on parcourt la documentation. Par contre, en examinant les instructions de dimensionnement de SAP, vous pouvez trouver un avantage à utiliser les nœuds d’extension SAP HANA avec l’option 1 et l’option 2. Voici un exemple :

- Les instructions de dimensionnement de SAP HANA exigent généralement de doubler la quantité du volume de données en mémoire. Ainsi, lorsque vous exécutez votre instance SAP HANA avec les données à chaud, vous n’avez que 50 % de la mémoire maximum qui sont remplis par les données. Ce qui reste de la mémoire est en principe conservé pour permettre à SAP HANA de faire son travail.
- Autrement dit, dans une unité S192 de grande instance HANA, dotée de 2 To de mémoire et exécutant une base de données SAP BW, vous ne disposez que de 1 To en volume de données.
- Si vous utilisez un nœud d’extension supplémentaire SAP HANA de l’option 1, ainsi qu’une référence SKU S192 de grande instance HANA, vous obtenez une capacité supplémentaire de 2 To pour le volume de données. Dans la configuration de l’option 2, comptez même sur 4 To supplémentaires pour le volume de données tièdes. Par rapport au nœud chaud, la capacité de mémoire totale du nœud d’extension « tiède » peut servir pour le stockage de données de l’option 1 tandis que le double de mémoire est utilisable pour le volume de données dans la configuration du nœud d’extension SAP HANA de l’option 2.
- En conséquence, vous vous retrouvez avec une capacité de 3 To pour vos données et un rapport chaud à tiède de 1:2 pour l’option 1, et avec une capacité de 5 To de données et un rapport de 1:4 dans la configuration du nœud d’extension pour l’option 2.

Cela dit, plus le volume de données est important par rapport à la mémoire, plus la probabilité est forte que les données tièdes que vous demandez soient stockées dans le stockage sur disque.


## <a name="operations-model-and-responsibilities"></a>Responsabilités et modèle opérationnel

Le service fourni avec SAP HANA sur Azure (grandes instances) est aligné avec les services Azure IaaS. Vous bénéficiez d’une instance de type grandes instances HANA avec un système d’exploitation installé qui est optimisé pour SAP HANA. Comme avec les machines virtuelles Azure IaaS, la plupart des tâches de renforcement du SE, d’installation des logiciels supplémentaires dont vous avez besoin, le fonctionnement du SE et de HANA et la mise à jour du SE et de HANA relèvent de votre responsabilité. Microsoft ne vous impose pas les mises à jour du SE, ni de HANA.

![Responsabilités relatives à SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image2-responsibilities.png)

Comme vous pouvez le voir dans le schéma ci-dessus, SAP HANA sur Azure (grandes instances) est une offre Infrastructure as a Service multilocataire. Et par conséquent, la répartition des responsabilités se trouve à la limite entre le SE et l’infrastructure, la plupart du temps. Microsoft est responsable de tous les aspects du service en dessous de la ligne correspondant au système d’exploitation, et vous êtes donc responsable de tout ce qui se trouve au-dessus de la ligne, y compris du système d’exploitation. Ainsi, les méthodes locales les plus récentes que vous utilisez peut-être pour la conformité, la sécurité, la gestion des applications, la base et la gestion du système d’exploitation peuvent toujours être utilisées. Les systèmes sont considérés comme faisant partie de votre réseau, à tous les égards.

Toutefois, ce service étant optimisé pour SAP HANA, vous devez collaborer avec Microsoft dans certains domaines pour utiliser les capacités de l’infrastructure sous-jacente et obtenir de meilleurs résultats.

La liste suivante fournit plus de détails sur chacune des couches et vos responsabilités :

**Mise en réseau :** tous les réseaux internes pour le tampon de grande instance exécutant SAP HANA, son accès au stockage, la connectivité entre les instances (pour la montée en charge et d’autres fonctions), la connectivité avec le paysage et la connectivité à Azure, à l’endroit où se trouve la couche Application SAP résidant dans les machines virtuelles Azure. Il inclut également la connectivité WAN entre les centres de données Azure pour la réplication à des fins de récupération d’urgence. Tous les réseaux sont partitionnés par le locataire et la qualité de service est appliquée.

**Stockage :** le stockage partitionné virtualisé pour tous les volumes requis par les serveurs SAP HANA, ainsi que pour les captures instantanées. 

**Serveurs :** les serveurs physiques dédiés à l’exécution des bases de données SAP HANA attribuées aux locataires. Les serveurs de classe Type I des références SKU font abstraction du matériel. Avec ces types de serveurs, la configuration du serveur est collectée et gérée dans des profils, qui peuvent être déplacés d’un matériel physique à un autre. Ce déplacement (manuel) d’un profil par opération peut être comparé à la réparation de service Azure. Les serveurs des références SKU de classe Type II n’offrent pas ce type de fonction.

**SDDC :** logiciel de gestion utilisé pour gérer les centres de données comme des entités logicielles définies. Il permet à Microsoft de regrouper des ressources à des fins de mise à l’échelle, de disponibilité et de performances.

**Système d’exploitation :** le SE que vous choisissez (SUSE Linux ou Red Hat Linux) et qui s’exécute sur les serveurs. Les images de systèmes d’exploitation que vous obtenez sont les images fournies par chaque fournisseur Linux à Microsoft pour les besoins de l’exécution de SAP HANA. Vous devez être abonné au fournisseur Linux de l’image optimisée pour SAP HANA spécifique. Vos responsabilités incluent l’enregistrement des images auprès du fournisseur du SE. À partir du point de remise de Microsoft, vous êtes également responsable de l’application des autres correctifs du système d’exploitation Linux. Cette mise à jour inclut également des packages supplémentaires qui peuvent être nécessaires pour réussir l’installation de SAP HANA (consultez la documentation d’installation et les notes relatives à SAP HANA) et qui ne sont pas inclus par le fournisseur Linux spécifique dans ses images de SE optimisé pour SAP HANA. Le client est également responsable de l’application des correctifs du SE associés à un dysfonctionnement et une optimisation du SE et de ses pilotes relatifs au matériel de serveur spécifique. Ou de toute application de correctif de fonctionnalité ou de sécurité du SE. Le client est également responsable de la surveillance et de la planification de capacité des éléments suivants :

- Consommation des ressources d’UC
- Consommation de mémoire
- Volumes de disques liés à l’espace libre, aux entrées et sorties par seconde et à la latence
- Trafic en volume du réseau entre la grande instance HANA et la couche Application SAP

L’infrastructure sous-jacente des grandes instances HANA fournit des fonctionnalités de sauvegarde et de restauration du volume de SE. L’utilisation de cette fonctionnalité relève également de votre responsabilité.

**Intergiciel :** l’instance SAP HANA principalement. L’administration, les opérations et la surveillance relèvent de votre responsabilité. Parmi les fonctionnalités fournies, certaines vous permettent d’utiliser des captures instantanées de stockage à des fins de récupération d’urgence et de sauvegarde/restauration. Ces fonctionnalités sont fournies par l’infrastructure. Toutefois, vos responsabilités incluent également la conception d’une architecture haute disponibilité ou de récupération d’urgence avec ces fonctionnalités, leur utilisation et la surveillance de la bonne exécution des captures instantanées de stockage.

**Données :** vos données gérées par SAP HANA et d’autres données telles que les fichiers de sauvegarde situés sur des volumes ou des partages de fichiers. Vos responsabilités incluent la surveillance de l’espace disque disponible et la gestion du contenu sur les volumes, ainsi que la surveillance de l’exécution des sauvegardes sur les volumes de disque et les captures instantanées de stockage. Toutefois, l’exécution réussie de la réplication de données pour les sites de récupération d’urgence est la responsabilité de Microsoft.

**Applications :** les instances d’application SAP ou, dans le cas d’applications autres que SAP, la couche Application de ces applications. Vos responsabilités incluent le déploiement, l’administration, les opérations et la surveillance de ces applications liées à la planification de la capacité de la consommation des ressources d’UC, la consommation de mémoire, la consommation de stockage Azure et la consommation de bande passante réseau au sein des réseaux virtuels Azure et des réseaux virtuels Azure vers SAP HANA sur Azure (grandes instances).

**WAN :** les connexions que vous établissez en local vers des déploiements Azure pour les charges de travail. Tous nos clients disposant de grandes instances HANA utilisent Azure ExpressRoute pour la connectivité. Cette connexion ne faisant pas partie de la solution SAP HANA sur Azure (grandes instances), vous êtes responsable de sa configuration.

**Archive :** vous préférerez peut-être archiver des copies de données à l’aide de vos propres méthodes dans les comptes de stockage. L’archivage inclut de la gestion, de la conformité, des coûts et des opérations. Vous êtes chargé de générer des copies de l’archive et des sauvegardes sur Azure et de les stocker d’une manière conforme.

Consultez le [SLA pour SAP HANA sur des grandes instances Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionnement

Le dimensionnement des grandes instances HANA n’est pas différent du dimensionnement HANA en général. Pour les systèmes déployés et existants, si vous souhaitez migrer d’autres SGBDR vers HANA, SAP fournit plusieurs rapports qui s’exécutent sur vos systèmes SAP existants. Si la base de données est migrée vers HANA, ces rapports vérifient les données et calculent les besoins en mémoire de l’instance HANA. Lisez les Notes SAP suivantes pour obtenir plus d’informations sur l’exécution de ces rapports et savoir comment obtenir leurs correctifs/versions les plus récents :

- [Note de support SAP #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Dimensionnement de SAP Suite sur HANA)
- [Note de support SAP #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Rapport de dimensionnement de Suite sur HANA et S/4 HANA)
- [Note de support SAP #2121330 - FAQ: SAP BW on HANA Sizing Report](https://launchpad.support.sap.com/#/notes/2121330) (FAQ : Rapport de dimensionnement SAP BW sur HANA)
- [Note de support SAP #1736976 - Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Rapport de dimensionnement de BW sur HANA)
- [Note de support SAP #2296290 - New Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nouveau rapport de dimensionnement pour BW sur HANA)

Pour les nouvelles implémentations, SAP Quick Sizer permet de calculer les besoins en mémoire de l’implémentation du logiciel SAP sur HANA.

La mémoire requise pour HANA augmentant en même temps que le volume de données, vous souhaiterez connaître la consommation de mémoire actuelle et être en mesure de prédire celle à venir. Selon les besoins en mémoire, vous pouvez ensuite mapper votre demande à l’une des références de grandes instances HANA.

## <a name="requirements"></a>Configuration requise

Cette liste indique la configuration requise pour l’exécution de SAP HANA sur Azure (plus grandes instances).

**Microsoft Azure :**

- Un abonnement Azure qui peut être lié à SAP HANA sur Azure (grandes instances).
- Contrat de Support Premier Microsoft. Consultez [SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites (Note de prise en charge SAP n° 2015553 - SAP sur Microsoft Azure : conditions préalables pour le support)](https://launchpad.support.sap.com/#/notes/2015553) pour des informations spécifiques liées à l’exécution de SAP dans Azure. Avec des unités de grande instance HANA incluant 384 processeurs et plus, vous devez également étendre le contrat de support Premier pour inclure Azure Rapid Response (ARR).
- Connaître les références des grandes instances HANA dont vous avez besoin après avoir effectué un exercice de dimensionnement avec SAP.

**Connectivité réseau :**

- Azure ExpressRoute entre le système local et Azure : pour connecter votre centre de données local à Azure, assurez-vous de demander au moins une connexion de 1 Gbit/s à votre FAI. 

**Système d’exploitation :**

- Licences pour SUSE Linux Enterprise Server 12 pour les applications SAP.

> [!NOTE] 
> Le système d’exploitation fourni par Microsoft n’est pas inscrit auprès de SUSE, ni connecté à une instance SMT.

- L’outil de gestion des abonnements SUSE Linux déployé dans Azure sur une machine virtuelle Azure. Cela offre la possibilité d’enregistrer SAP HANA sur Azure (grandes instances) et de le mettre à jour par SUSE (comme il n’existe aucun accès internet au sein du centre de données de grandes instances HANA). 
- Licences pour Red Hat Enterprise Linux 6.7 ou 7.2 pour SAP HANA.

> [!NOTE]
> Le système d’exploitation fourni par Microsoft n’est pas inscrit auprès de Red Hat, ni connecté à une instance Red Hat Subscription Manager.

- Gestionnaire d’abonnements Red Hat déployé dans Azure sur une machine virtuelle Azure. Le gestionnaire d’abonnement Red Hat offre la possibilité d’enregistrer SAP HANA sur Azure (grandes instances) et de le mettre à jour par Red Hat (comme il n’existe aucun accès internet direct depuis le locataire déployé sur le tampon de grande instance Azure).
- Le système SAP requiert également un contrat de support avec votre fournisseur Linux. Ce contrat est également requis avec les grandes instances HANA ou si vous exécutez Linux dans Azure. Contrairement à certaines des images de la galerie Linux Azure, les frais de service ne sont PAS inclus dans l’offre de solution HANA (grandes instances). Il vous incombe de respecter les exigences de SAP en matière de contrat de support avec le distributeur Linux.   
   - Pour connaître les exigences en matière de contrat de support avec SUSE Linux, consultez la [Note de support SAP #1984787 - SUSE LINUX Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (SUSE LINUX Enterprise Server 12 : notes d’installation) et la [Note de support SAP #1056161 - SUSE Priority Support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Support prioritaire SUSE pour les applications SAP).
   - Avec Red Hat Linux, vous devez disposer d’un niveau d’abonnement prenant en charge le support et la maintenance (mise à jour des systèmes d’exploitation des grandes instances HANA). Red Hat recommande de disposer d’un abonnement « RHEL for [SAP Solutions](https://access.redhat.com/solutions/3082481) ». 

Pour consulter la matrice de prise en charge des différentes versions de SAP HANA avec les différentes versions de Linux, reportez-vous à la [note SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Base de données :**

- Licences et composants d’installation logiciels pour SAP HANA (édition Enterprise ou Platform).

**Applications :**

- Licences et composants d’installation logiciels pour toutes les applications SAP se connectant à SAP HANA et associées aux contrats de support SAP.
- Licences et composants d’installation logiciels pour toutes les applications autres que SAP utilisées dans le cadre de l’environnement SAP HANA sur Azure (grandes instances) et associées aux contrats de support.

**Compétences :**

- Expérience et connaissances relatives à Azure IaaS et ses composants.
- Expérience et connaissances relatives au déploiement de charges de travail SAP dans Azure.
- Personnel certifié pour l’installation de SAP HANA.
- Compétences en architecture SAP pour créer la haute disponibilité et la récupération d’urgence autour de SAP HANA.

**SAP :**

- Vous devez être un client SAP et disposer d’un contrat de support auprès de SAP
- En particulier pour les implémentations de références SKU de grande instance HANA de classe Type II, il est vivement recommandé de consulter SAP pour connaître les versions de SAP HANA et les configurations éventuelles sur du matériel mis à l’échelle de grande taille.


## <a name="storage"></a>Storage

La disposition du stockage de SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur Azure Service Management en suivant les directives recommandées par SAP dans le livre blanc [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Conditions de stockage SAP HANA).

Les grandes instances HANA de classe Type I présentent 4 fois plus de capacité mémoire que de volume de stockage. Pour les unités de grande instance HANA de classe Type II, le stockage n’est pas 4 fois plus élevé. Le volume fourni dans les unités est prévu pour le stockage des sauvegardes de fichiers journaux HANA. Pour plus d’informations, consultez le [Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consultez le tableau suivant pour connaître l’allocation de stockage. Le tableau indique à peu près la capacité des différents volumes fournis avec les diverses unités de grande instance HANA.

| Référence SKU de grande instance HANA | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 Go | 512 Go | 768 Go | 512 Go |
| S72m | 3 328 Go | 768 Go |1280 Go | 768 Go |
| S192 | 4 608 Go | 1024 Go | 1536 Go | 1024 Go |
| S192m | 11 520 Go | 1536 Go | 1 792 Go | 1536 Go |
| S384 | 11 520 Go | 1536 Go | 1 792 Go | 1536 Go |
| S384m | 12 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S384xm | 16 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S576 | 20 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S768 | 28 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S960 | 36 000 Go | 4 100 Go | 2 050 Go | 4 100 Go |


Les volumes réellement déployés peuvent varier légèrement en fonction du déploiement et de l’outil utilisés pour afficher les tailles de volume.

Si vous décomposez une référence SKU de grande instance HANA, voici quelques exemples de division possible :

| Partition de la mémoire en Go | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 Go | 160 Go | 304 Go | 160 Go |
| 512 | 768 Go | 384 Go | 512 Go | 384 Go |
| 768 | 1280 Go | 512 Go | 768 Go | 512 Go |
| 1 024 | 1 792 Go | 640 Go | 1024 Go | 640 Go |
| 1536 | 3 328 Go | 768 Go | 1280 Go | 768 Go |


Ces tailles sont des volumes approximatifs qui peuvent varier légèrement en fonction du déploiement et des outils utilisés pour afficher les volumes. On peut également imaginer d’autres tailles de partition, comme 2,5 To. Ces tailles de stockage sont calculées selon une formule similaire à celle utilisée pour les partitions ci-dessus. Le terme de « partitions » n’indique pas que les ressources du système d’exploitation, de la mémoire ou du processeur sont partitionnées de quelque manière que ce soit. Il indique simplement les partitions de stockage pour les différentes instances HANA que vous voudrez peut-être déployer sur une seule unité de grande instance HANA. 

En tant que client, vous pouvez avoir besoin de davantage de stockage. Dans ce cas, vous avez la possibilité d’acheter du stockage supplémentaire en unités de 1 To. Ce stockage supplémentaire peut être ajouté en tant que volume supplémentaire ou peut être utilisé pour étendre un ou plusieurs volumes existants. Il n’est pas possible de réduire la taille des volumes tels qu’ils ont été déployés à l’origine et documentés principalement dans le ou les tableaux ci-dessus. Il est également impossible de modifier les noms des volumes ou les noms des montages. Les volumes de stockage, comme décrit ci-dessus, sont joints aux unités de grande instance HANA en tant que volumes NFS4.

En tant que client, vous pouvez utiliser les captures instantanées de stockage à des fins de sauvegarde/restauration et de récupération d’urgence. Vous trouverez plus de détails à ce sujet à la page [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos
Le stockage utilisé pour les grandes instances HANA permet un chiffrement transparent des données lorsqu’elles sont stockées sur les disques. Vous pouvez activer ce type de chiffrement au moment du déploiement d’une unité de grande instance HANA. Vous pouvez également modifier les volumes chiffrés après le déploiement. Le passage d’un volume non chiffré à un volume chiffré est transparent et n’entraîne aucun temps d’arrêt. 

Avec les références SKU de classe Type I, le volume sur lequel le numéro d’unité logique de démarrage est stocké est chiffré. Dans le cas de références SKU de grande instance HANA de classe Type II, vous devez chiffrer le numéro d’unité logique de démarrage avec les méthodes du système d’exploitation. Pour plus d’informations, contactez l’équipe de gestion des services Microsoft.


## <a name="networking"></a>Mise en réseau

L’architecture de mise en réseau Azure est un composant clé pour la réussite du déploiement des applications SAP sur les grandes instances HANA. En règle générale, les déploiements SAP HANA sur Azure (grandes instances) ont un plus grand paysage SAP avec plusieurs solutions SAP distinctes et tailles de bases de données variées, une consommation des ressources d’UC et une utilisation de la mémoire différentes. Il est probable que tous les systèmes SAP ne soient pas basés sur SAP HANA, et votre paysage SAP est certainement un hybride qui utilise :

- Des systèmes SAP déployés en local. En raison de leur taille, ces systèmes ne peuvent actuellement pas être hébergés dans Azure ; un exemple classique est un système de production SAP ERP s’exécutant sur Microsoft SQL Server (comme la base de données) et qui nécessite plus de ressources de processeur ou de mémoire que les machines virtuelles Azure ne peuvent fournir.
- Des systèmes SAP basés sur SAP HANA déployés en local.
- Des systèmes SAP déployés dans des machines virtuelles Azure. Ces systèmes peuvent être des instances de développement, de test, de bac à sable ou de production pour toutes les applications basées sur SAP NetWeaver qui peuvent être déployées correctement dans Azure (sur des machines virtuelles), en fonction de la demande de mémoire et de la consommation de ressources. Ces systèmes peuvent également être basés sur des bases de données comme SQL Server (consultez [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note de prise en charge SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533/E)) ou SAP HANA (consultez [SAP HANA Certified IaaS Platforms (Plateformes IaaS certifiées SAP HANA)](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Des serveurs d’applications SAP déployés dans Azure (sur des machines virtuelles) qui utilisent SAP HANA sur Azure (grandes instances) dans les tampons de grande instance Azure.

Tandis qu’un environnement SAP hybride (avec quatre scénarios de déploiement différents ou plus) est typique, il existe de nombreux cas client ayant un paysage SAP s’exécutant intégralement dans Azure. Les machines virtuelles Microsoft Azure devenant de plus en plus puissantes, le nombre de clients qui migrent toutes leurs solutions SAP vers Azure augmente.

La mise en réseau Azure dans le contexte des systèmes SAP déployés dans Azure n’est pas difficile. Elle est basée sur les principes suivants :

- Les réseaux virtuels Azure doivent être connectés au circuit Azure ExpressRoute qui se connecte au réseau local.
- Un circuit ExpressRoute se connectant en local à Azure doit généralement avoir une bande passante de 1 Gbit/s ou plus. Cette bande passante minimale permet de bénéficier d’une bande passante suffisante pour transférer des données entre les systèmes locaux et les systèmes s’exécutant sur des machines virtuelles Azure (ainsi que pour la connexion aux systèmes Azure depuis les systèmes locaux des utilisateurs finaux).
- Tous les systèmes SAP dans Azure doivent être configurés dans les réseaux virtuels Azure pour communiquer entre eux.
- Le répertoire Active Directory et le DNS hébergés en local sont étendus à Azure via ExpressRoute, en local.


> [!NOTE] 
> En matière de facturation, un seul abonnement Azure peut être associé à un seul locataire dans un tampon de grande instance d’une région Azure spécifique, et inversement, un seul locataire de tampon de grande instance peut être associé à un seul abonnement Azure. Cela n’est pas différent des autres objets facturables dans Azure.

Le déploiement de SAP HANA sur Azure (grandes instances) dans plusieurs régions Azure différentes entraîne le déploiement d’un locataire distinct dans le tampon de grande instance. Toutefois, vous pouvez exécuter ces deux locataires dans le même abonnement Azure tant que ces instances font partie du même paysage SAP. 

> [!IMPORTANT] 
> Seul le déploiement Azure Resource Manager est pris en charge avec SAP HANA sur Azure (grandes instances).

 

### <a name="additional-azure-vnet-information"></a>Informations supplémentaires sur les réseaux virtuels Azure

Pour connecter un réseau virtuel Azure à ExpressRoute, une passerelle Azure doit être créée, (consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Une passerelle Azure peut être utilisée avec ExpressRoute pour rediriger vers une infrastructure en dehors d’Azure (ou un tampon de grande instance Azure), ou pour la connexion entre les réseaux virtuels Azure (consultez [Configuration d’une connexion de réseau virtuel à réseau virtuel pour Resource Manager à l’aide de PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Vous pouvez connecter la passerelle Azure à quatre connexions ExpressRoute différentes maximum, tant que celles-ci proviennent de routeurs MS Enterprise Edges (MSEE) distincts.  Pour plus d’informations, consultez la page [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Le débit fourni par une passerelle Azure est différent pour les deux cas d’utilisation (consultez [À propos de la passerelle VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Le débit maximal pouvant être atteint avec une passerelle de réseau virtuel est de 10 Gbits/s, à l’aide d’une connexion ExpressRoute. Gardez à l’esprit que la copie de fichiers entre une machine virtuelle Azure résidant dans un réseau virtuel Azure et un système local (en tant que flux de copie unique) ne bénéficie pas du débit total des différentes références de passerelles. Pour tirer parti de l’intégralité de la bande passante de la passerelle de réseau virtuel, vous devez utiliser plusieurs flux ou copier différents fichiers dans des flux parallèles d’un seul fichier.


### <a name="networking-architecture-for-hana-large-instances"></a>Architecture de mise en réseau pour les grandes instances HANA
L’architecture de mise en réseau pour les grandes instances HANA comme indiqué ci-dessous, peut être divisée en quatre parties :

- Mise en réseau locale et connexion ExpressRoute à Azure. Cette partie est le domaine des clients et est connectée à Azure via ExpressRoute. Il s’agit de la partie dans le coin inférieur droit du graphique ci-dessous.
- Mise en réseau Azure brièvement décrite ci-dessus avec des réseaux virtuels Azure, qui ont aussi des passerelles. Il s’agit d’une zone où vous devez trouver les modèles appropriés pour la configuration requise de vos applications, la sécurité et les exigences de conformité. L’utilisation de grandes instances HANA est un autre point à prendre en compte en termes de nombre de réseaux virtuels et de références SKU de passerelle Azure (SKU). Il s’agit de la partie dans le coin supérieur droit du graphique.
- Connectivité des grandes instances HANA grâce à la technologie ExpressRoute dans Azure. Cette partie est déployée et gérée par Microsoft. En tant que client, vous devez simplement fournir certaines plages d’adresses IP et, après le déploiement de vos ressources dans les grandes instances HANA, connecter le circuit ExpressRoute au(x) réseau(x) virtuel(s) Azure (consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Mise en réseau dans les grandes instances HANA, qui est essentiellement transparente pour vous en tant que client.

![Réseau virtuel Azure connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Le fait que vous utilisiez des grandes instances HANA ne vous dispense pas de connecter vos ressources locales à Azure via ExpressRoute. Cela ne modifie pas non plus la nécessité d’utiliser un ou plusieurs réseaux virtuels qui exécutent les machines virtuelles Azure qui hébergent la couche Application qui se connecte aux instances HANA hébergées dans les unités de grande instance HANA. 

La différence avec les déploiements SAP uniquement dans Azure est liée aux points suivants :

- Les unités de grande instance HANA de votre locataire client sont connectées via un autre circuit ExpressRoute à votre ou vos réseaux virtuels Azure. Afin de séparer les conditions de charge, les liens ExpressRoute entre les machines locales et les réseaux virtuels Azure et les liens entre les réseaux virtuels Azure et les grandes instances HANA ne partagent pas les mêmes routeurs.
- Le profil de charge de travail entre la couche Application SAP et l’instance HANA est différent des nombreuses petites requêtes et agit comme des transferts de données (jeux de résultats) à partir de SAP HANA dans la couche Application.
- L’architecture d’application SAP est plus sensible à la latence du réseau que les scénarios classiques dans lesquels les données sont échangées entre les machines locales et Azure.
- La passerelle de réseau virtuel a au moins deux connexions ExpressRoute et ces deux connexions partagent la bande passante maximale de la passerelle de réseau virtuel pour les données entrantes.

La latence du réseau rencontrée entre les machines virtuelles Azure et les unités de grande instance HANA peut être supérieure à la latence standard sur un réseau de machine virtuelle à machine virtuelle. En fonction de la région Azure, les valeurs mesurées peuvent dépasser 0,7 ms, ce qui est classé en dessous de la moyenne dans la [Note SAP 1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (FAQ : performances du réseau). Néanmoins, les clients ont réussi à déployer des applications SAP de production basées sur SAP HANA avec succès sur des grandes instances SAP HANA. Les clients ayant effectué ces déploiements ont signalé d’importantes améliorations en exécutant leurs applications SAP sur SAP HANA à l’aide d’unités de grande instance HANA. Toutefois, nous vous conseillons de tester minutieusement vos processus métier dans les grandes instances HANA Azure.
 
Afin d’offrir une latence réseau déterministe entre les machines virtuelles Azure et une grande instance HANA, le choix de la référence SKU de passerelle de réseau virtuel Azure est essentiel. Contrairement aux modèles de trafic entre machines locales et machines virtuelles Azure, le modèle de trafic entre les machines virtuelles Azure et les grandes instances HANA peut créer de petites décharges de requêtes exigeantes et des volumes de données à transmettre. Pour bien gérer ces situations, nous vous recommandons fortement d’utiliser la référence SKU de passerelle UltraPerformance. Pour les références SKU de grande instance HANA de classe Type II, l’utilisation de la référence SKU de la passerelle UltraPerformance en tant que passerelle de réseau virtuel Azure est obligatoire.  

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Applications SAP, seules les références de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances). Pour les références SKU de grande instance HANA de Type II, seule la référence SKU de la passerelle UltraPerformance est prise en charge en tant que passerelle de réseau virtuel Azure.



### <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée ci-dessus est connectée via ExpressRoute à Azure, et le circuit ExpressRoute se connecte à un routeur Microsoft Enterprise Edge Router (MSEE) (consultez [Présentation technique d’ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Une fois mise en place, cette route se connecte à la dorsale principale de Microsoft Azure, ainsi qu’à toutes les régions Azure accessibles.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le MSEE le plus proche de la région Azure dans le paysage SAP. Les tampons de grande instance Azure sont connectés via les appareils MSEE dédiés pour réduire la latence réseau entre les machines virtuelles Azure dans Azure IaaS et les tampons de grande instance.

La passerelle de réseau virtuel pour les machines virtuelles Azure, hébergeant les instances d’application SAP, est connectée à ce circuit ExpressRoute, et ce même réseau virtuel est connecté à un routeur MSEE Router distinct, dédié à la connexion de tampons de grande instance.

Il s’agit d’un exemple simple d’un système SAP unique, où la couche Application SAP est hébergée dans Azure et où la base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). Nous supposons que le fait que la bande passante de la passerelle de réseau virtuel soit de 2 Gbits/s ou de 10 Gbit/s ne représente pas un goulot d’étranglement.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si plusieurs systèmes SAP ou si de grands systèmes SAP sont déployés en se connectant à SAP HANA sur Azure (grandes instances), il est raisonnable de supposer que le débit de la passerelle de réseau virtuel puisse devenir un goulot d’étranglement. Dans ce cas, vous devez diviser les couches Application entre plusieurs réseaux virtuels Azure. Il pourrait également être utile de créer des réseaux virtuels spéciaux se connectant aux grandes instances HANA dans les cas suivants :

- Exécution de sauvegardes directement à partir des grandes instances HANA vers une machine virtuelle Azure hébergeant des partages NFS.
- Copie de sauvegardes volumineuses ou d’autres fichiers des unités de grandes instances HANA vers l’espace disque géré dans Azure.

L’utilisation de réseaux virtuels distincts, qui hébergent des machines virtuelles gérant le stockage, élimine l’impact des transferts de données ou de fichiers volumineux depuis les grandes instances HANA vers Azure sur la passerelle de réseau virtuel associée aux machines virtuelles exécutant la couche Application SAP. 

Pour une architecture réseau plus évolutive :

- Utilisez plusieurs réseaux virtuels Azure pour une couche Application SAP unique, plus grande.
- Déployez un réseau virtuel Azure distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP dans des sous-réseaux séparés dans le même réseau virtuel.

 Une architecture réseau plus évolutive pour SAP HANA sur Azure (grandes instances) :

![Déploiement de la couche Application SAP sur plusieurs réseaux virtuels Azure](./media/hana-overview-architecture/image4-networking-architecture.png)

Le déploiement de la couche Application SAP, ou des composants, sur plusieurs réseaux virtuels Azure comme présenté ci-dessus a introduit une augmentation inévitable de la latence qui s’est produite lorsque les applications hébergées dans ces réseaux virtuels Azure communiquent entre elles. Par défaut, le trafic réseau entre les machines virtuelles Azure situées dans différents réseaux virtuels passe par les routeurs MSEE dans cette configuration. Toutefois, depuis septembre 2016, ce routage peut être optimisé. La méthode d’optimisation et de réduction de la latence de communication entre les deux réseaux virtuels consiste à homologuer vos réseaux virtuels Azure dans la même région. Cela est valable même si ces réseaux virtuels appartiennent à des abonnements différents. Grâce à l’homologation des réseaux virtuels Azure, des machines virtuelles de deux réseaux virtuels Azure peuvent utiliser le réseau Azure principal pour communiquer directement entre elles. La latence est donc la même que si les machines virtuelles se trouvaient dans le même réseau virtuel. Tandis que le trafic d’adressage des plages d’adresses IP connectées via la passerelle du réseau virtuel Azure est acheminé via la passerelle de réseau virtuel individuel du réseau virtuel. Pour obtenir plus d’informations sur l’homologation du réseau virtuel Azure, consultez l’article [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routage dans Azure

Deux points importants sont à prendre en compte en matière de routage pour SAP HANA sur Azure (grandes instances) :

1. L’accès à SAP HANA sur Azure (grandes instances) se fait uniquement via les machines virtuelles Azure dans la connexion ExpressRoute dédiée, et non directement via le système local. Certains clients d’administration et toutes les applications nécessitant un accès direct, telles que SAP Solution Manager s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA.

2. Une adresse IP de la plage du pool d’adresses IP de serveur que vous avez soumis est affectée aux unités SAP HANA sur Azure (grandes instances). (Consultez la page [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour en savoir plus).  Cette adresse IP est accessible via les abonnements Azure et le circuit ExpressRoute qui connecte les réseaux virtuels Azure à HANA sur Azure (grandes instances). L’adresse IP affectée hors de la plage de ce pool d’adresses IP de serveur est directement attribuée à l’unité matérielle et n’est PLUS traduite, comme cela était le cas dans les premiers déploiements de cette solution. 

> [!NOTE] 
> Si vous avez besoin de vous connecter à SAP HANA sur Azure (grandes instances) dans un scénario _d’entrepôt de données_, où les applications et/ou les utilisateurs finaux doivent se connecter à la base de données SAP HANA (exécution directe), un autre composant réseau doit être utilisé : un proxy inverse pour router les données dans les deux sens. Par exemple, F5 BIG-IP, NGINX avec Traffic Manager déployé dans Azure en tant que solution de routage de trafic/pare-feu virtuelle.

### <a name="internet-connectivity-of-hana-large-instances"></a>Connectivité internet des grandes instances HANA
Les grandes instances HANA ne bénéficient PAS d’une connectivité directe à Internet. Cela limite par exemple la possibilité d’enregistrer l’image du système d’exploitation directement auprès du fournisseur du système d’exploitation. Par conséquent, vous devrez utiliser le serveur local SLES SMT ou le gestionnaire d’abonnements RHEL.

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Chiffrement des données entre les machines virtuelles Azure et les grandes instances HANA
Les données transférées entre les grandes instances HANA et les machines virtuelles Azure ne sont pas chiffrées. En revanche, vous pouvez activer le chiffrement du trafic pour l’échange entre le DBMS HANA et les applications basées sur JDBC/ODBC. Reportez-vous à [cette documentation SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Utilisation d’unités de grande instance HANA dans plusieurs régions

Vous pouvez avoir d’autres raisons de déployer SAP HANA sur Azure (grandes instances) dans plusieurs régions Azure, en plus de la récupération d’urgence. Vous voulez peut-être accéder aux grandes instances HANA à partir de chaque machine virtuelle déployée dans les différents réseaux virtuels dans les régions. Puisque les adresses IP affectées aux différentes unités de grandes instances HANA ne sont pas propagées au-delà des réseaux virtuels Azure (qui sont directement connectés via leur passerelle aux instances), il existe une légère modification de la conception du réseau virtuel présenté ci-dessus : une passerelle de réseau virtuel Azure peut gérer quatre circuits ExpressRoute différents de divers MSEE, et chaque réseau virtuel qui est connecté à l’un des tampons de grande instance peut être connecté au tampon de grande instance dans une autre région Azure.

![Réseaux virtuels Azure connectés aux tampons de grande instance Azure dans différentes régions Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figure ci-dessus illustre comment les différents réseaux virtuels Azure dans les deux régions sont connectés à deux circuits ExpressRoute distincts qui sont utilisés pour se connecter à SAP HANA sur Azure (grandes instances) dans les deux régions Azure. Les connexions nouvellement introduites sont représentées par les lignes rouges rectangulaires. Avec ces connexions, en dehors des réseaux virtuels Azure, les machines virtuelles s’exécutant dans l’un de ces réseaux virtuels peuvent accéder à chacune des différentes unités de grande instance HANA déployées dans les deux régions. Comme vous voyez dans le schéma ci-dessus, il est supposé que vous disposez de deux connexions ExpressRoute du système local aux deux régions Azure, qui sont recommandées pour des raisons de récupération d’urgence.

> [!IMPORTANT] 
> Si plusieurs circuits ExpressRoute sont utilisés, les paramètres de BGP Local Preference et du préfixe AS Path doivent être utilisés pour garantir le routage correct du trafic.


