---
title: "Distributions approuvées de Linux | Microsoft Docs"
description: "Découvrez les distributions Linux approuvées sur Azure, notamment des instructions pour Ubuntu, CentOS, Oracle et SUSE."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: 811769443e322af3a2981c58979040a1e33b06e9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux sur les distributions approuvées par Azure
Les partenaires fournissent des images Linux dans la Place de marché Microsoft Azure. Nous collaborons avec différentes communautés Linux pour étoffer la liste des distributions approuvée. Entre-temps, pour les distributions qui ne sont pas disponibles sur la Place de marché, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies dans [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="supported-distributions-and-versions"></a>Distributions et versions prises en charge
Le tableau suivant répertorie les distributions et les version Linux prises en charge sur Microsoft Azure. Reportez-vous à la section [Prise en charge des images Linux dans Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) pour plus d'informations sur la prise en charge de Linux et de la technologie open source dans Azure.

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont des modules noyau que Microsoft distribue directement au noyau Linux en amont.  Certains pilotes LIS sont intégrés dans le noyau de la distribution par défaut. D’anciennes distributions basées sur Red Hat Enterprise (RHEL)/CentOS sont disponibles sous la forme de téléchargements séparés à partir de la page [Linux Integration Services Version 4.1 pour Hyper-V](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Pour plus d’informations sur les pilotes LIS, voir [Conditions requises pour le noyau Linux](create-upload-generic.md#linux-kernel-requirements).

L’agent Linux Azure est pré-installé sur les images disponibles sur la Place de marché Microsoft Azure, et est généralement disponible dans le référentiel du package de la distribution. Le code source est disponible sur [GitHub](https://github.com/azure/walinuxagent).

  
| Distribution | Version | Pilotes | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3 : [téléchargement LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4+ : dans le noyau |Paquet : dans le [référentiel](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Dans le noyau |Code source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |Dans le noyau |Package : dans le référentiel sous « waagent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+ |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pour SAP<br>11 SP4<br>12 SP1+|Dans le noyau |Package :<p> pour 11 dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>pour 12 inclus dans le module « Cloud public » sous « python-azure-agent »<br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |Dans le noyau |Package: dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) sous « python-azure-agent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Pour la prise en charge d'Ubuntu 12.04 sur Azure, consultez la [notice EOL](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partenaires

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

À partir du site web CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et la fiabilité. Au lieu d’installer des packages via yum ou apt, CoreOS utilise des conteneurs Linux pour gérer vos services à un niveau supérieur d’abstraction. Un code de service unique et l’ensemble des dépendances sont déployés au sein d’un conteneur pouvant être exécuté sur une ou plusieurs machines CoreOS.*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ est une société de conseil et de services indépendante, spécialisée dans le développement et l’implémentation de solutions professionnelles à l’aide de logiciels libres. En tant de leader spécialiste de l’Open Source, Credativ a acquis une reconnaissance internationale et de nombreux services informatiques font appel à ses services. En collaboration avec Microsoft, Credativ prépare actuellement des images Debian correspondantes pour Debian 8 (Jessie) et Debian avant 7 (Wheezy). Les deux images sont spécialement conçues pour s’exécuter sur Azure et peuvent être facilement gérées via la plateforme. Credativ prendra également en charge la maintenance et la mise à jour sur le long terme des images Debian pour Azure grâce à ses centres de support Open Source.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La stratégie d’Oracle est d’offrir un vaste portefeuille de solutions pour les clouds privés et publics. La stratégie offre aux clients de choix et la flexibilité leur permettant de décider comment déployer les logiciels Oracle dans des clouds Oracle et autres. À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle.  L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Le leader mondial de solutions open source, Red Hat permet à plus de 90 % des sociétés du classement Fortune 500 de résoudre les défis commerciaux, d’aligner leur stratégies informatique et commerciale, et d’anticiper l’évolution des technologies. Red Hat y parvient en proposant des solutions sécurisées grâce à un modèle commercial ouvert et à un modèle d’abonnement abordable et prévisible.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l’assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

L’ingénierie de Canonical et le mode de gouvernance de la communauté Open Source sont les éléments moteurs de la réussite d’Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. La vision de Canonical d’une plateforme unifiée gratuite disponible dans Ubuntu, offre une gamme d’interfaces cohérentes pour téléphones, tablettes, TV et ordinateurs de bureau. Cette vision fait d’Ubuntu le premier choix pour divers établissements, des fournisseurs de cloud public aux fabricant de produits électroniques de grande consommation, et l’un des choix de prédilection des technologues individuels.

Avec des développeurs et des centres d’ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu destinées tant aux PC, qu’aux serveurs ou aux appareils portables.
