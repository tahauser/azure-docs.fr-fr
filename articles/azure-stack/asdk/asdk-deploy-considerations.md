---
title: Prérequis du déploiement du kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Passez en revue la configuration requise de l’environnement et du matériel du kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f4b55bb3287f67792b3257c3f62256437f5625ca
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-deployment-planning-considerations"></a>Considérations liées à la planification du déploiement d’Azure Stack
Avant de déployer le kit de développement Azure Stack (ASDK), vérifiez que l’ordinateur hôte du kit de développement répond à la configuration requise décrite dans cet article.


## <a name="hardware"></a>Matériel
| Composant | Minimale | Recommandé |
| --- | --- | --- |
| Lecteurs de disque : système d’exploitation |1 disque de système d’exploitation avec un minimum de 200 Go disponibles pour la partition système (SSD ou HDD) |1 disque de système d’exploitation avec un minimum de 200 Go disponibles pour la partition système (SSD ou HDD) |
| Lecteurs de disque : données générales du kit de développement<sup>*</sup>  |4 disques. Chaque disque doit avoir une capacité d’au moins 140 Go (SSD ou HDD). Tous les disques disponibles sont utilisés. |4 disques. Chaque disque doit avoir une capacité d’au moins 250 Go (SSD ou HDD). Tous les disques disponibles sont utilisés. |
| Calcul : UC |Double socket : 12 cœurs physiques (total) |Double socket : 16 cœurs physiques (total) |
| Calcul : mémoire |96 Go de RAM |128 Go de RAM (minimum nécessaire pour la prise en charge des fournisseurs de ressources PaaS)|
| Calcul : BIOS |Compatible Hyper-V (avec prise en charge de SLAT) |Compatible Hyper-V (avec prise en charge de SLAT) |
| Réseau : Carte réseau |Certification Windows Server 2012 R2 nécessaire pour la carte réseau ; pas de fonctionnalités spécialisées requises |Certification Windows Server 2012 R2 nécessaire pour la carte réseau ; pas de fonctionnalités spécialisées requises |
| Logo de certification du matériel |[Certifié pour Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifié pour Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Vous aurez besoin d’une plus grande capacité que celle recommandée si vous prévoyez d’ajouter de nombreux [éléments de la Place de marché](asdk-marketplace-item.md) Azure.

**Configuration des lecteurs de disque de données :** tous les lecteurs de données doivent être de même type (SAS, SATA ou NVMe) et avoir la même capacité. Si vous utilisez des lecteurs de disque SAS, vous devez les joindre par le biais d’un chemin d’accès unique (aucune prise en charge de MPIO ou des chemins d’accès multiples n’est fournie).

**Options de configuration HBA**

* (Recommandé) HBA simple
* HBA RAID : la carte doit être configurée en mode Pass Through
* HBA RAID : les disques doivent être configurés en tant que disque unique, RAID-0

**Combinaisons bus/type de support prises en charge**

* DISQUE DUR SATA
* DISQUE DUR SAS
* DISQUE DUR RAID
* SSD RAID (si le type de support n’est pas spécifié ou connu<sup>*</sup>)
* DISQUE SSD SATA + DISQUE DUR SATA
* DISQUE SSD SAS + DISQUE DUR SAS
* NVMe

<sup>*</sup> Les contrôleurs RAID sans fonctionnalité Pass Through ne peuvent pas reconnaître le type de support. Ces contrôleurs marquent les disques durs et les disques SSD comme étant « Non spécifiés ». Dans ce cas, le disque SSD sert de stockage persistant et non de dispositif de mise en cache. Vous pouvez alors déployer le Kit de développement sur ces disques SSD.

**Exemples d’HBA**: LSI 9207-8i, LSI-9300-8i ou LSI-9265-8i en mode pass-through

Des exemples de configurations OEM sont disponibles.

## <a name="operating-system"></a>Système d’exploitation
|  | **Configuration requise** |
| --- | --- |
| **Version du SE** |Windows Server 2012 R2 ou version ultérieure. La version du système d’exploitation n’est pas critique avant le démarrage du déploiement, car vous allez démarrer l’ordinateur hôte sur le disque VHD qui est fourni dans l’installation Azure Stack. Le système d’exploitation et tous les correctifs nécessaires sont déjà intégrés dans l’image. N’utilisez pas de clés pour activer les instances Windows Server utilisées dans le Kit de développement. |

> [!TIP]
> Après avoir installé le système d’exploitation, vous pouvez utiliser le [vérificateur de déploiement Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) pour vérifier que votre matériel a la configuration requise.

## <a name="account-requirements"></a>Exigences pour les comptes
En général, vous déployez le Kit de développement avec une connexion Internet pour pouvoir vous connecter à Microsoft Azure. Dans ce cas, configurez un compte Azure Active Directory (Azure AD) pour déployer le Kit de développement.

Si votre environnement n’est pas connecté à Internet, ou si vous ne souhaitez pas utiliser Azure AD, vous pouvez déployer Azure Stack à l’aide des services de fédération Active Directory (AD FS). Le Kit de développement inclut ses propres instances AD FS et AD DS (Active Directory Domain Services). Si vous choisissez cette option de déploiement, vous n’avez pas besoin de configurer des comptes au préalable.

>[!NOTE]
Si vous effectuez le déploiement avec l’option AD FS, vous devez redéployer Azure Stack pour utiliser Azure AD à la place.

### <a name="azure-active-directory-accounts"></a>Comptes Azure Active Directory
Pour déployer Azure Stack en utilisant un compte Azure AD, vous devez préparer ce compte avant d’exécuter le script de déploiement PowerShell. Ce compte devient administrateur général pour le locataire Azure AD. Il est utilisé pour provisionner et déléguer des applications et des principaux de service pour tous les services Azure Stack qui interagissent avec Azure Active Directory et l’API Graph. Il est également propriétaire de l’abonnement du fournisseur par défaut (vous pouvez changer ce paramètre ultérieurement). Vous pouvez utiliser ce compte pour vous connecter au portail de l’administrateur de votre système Azure Stack.

1. Créez un compte Azure AD qui est administrateur d’au moins un annuaire Azure AD. Si vous en avez déjà un, vous pouvez l’utiliser. Sinon, vous pouvez en créer un gratuitement à l’adresse [https://azure.microsoft.com/free/](http://azure.microsoft.com/pricing/free/) (pour la Chine, rendez-vous sur le site <http://go.microsoft.com/fwlink/?LinkID=717821>). Si vous prévoyez [d’inscrire Azure Stack auprès d’Azure](asdk-register.md) ultérieurement, vous devez également avoir un abonnement avec ce nouveau compte.
   
    Enregistrez ces informations d’identification afin de les utiliser en tant qu’administrateur du service. Vous pouvez utiliser ce compte pour configurer et gérer les clouds de ressources, les comptes d’utilisateur, les plans de locataire, les quotas et les tarifs. Dans le portail, il peut créer des clouds de sites web, des clouds privés de machine virtuelle, des plans et gérer les abonnements des utilisateurs.
1. Créez au moins un compte d’utilisateur de test dans Azure AD avec lequel vous pouvez vous connecter au kit de développement en tant que locataire.
   
   | **Compte Active Directory Azure** | **Pris en charge ?** |
   | --- | --- |
   | Compte professionnel ou scolaire avec un abonnement Azure public valide |OUI |
   | Compte Microsoft avec abonnement Azure public valide |OUI |
   | Compte professionnel ou scolaire avec un abonnement Azure en Chine valide |OUI |
   | Compte professionnel ou scolaire avec un abonnement Azure pour le gouvernement américain valide |OUI |

## <a name="network"></a>Réseau
### <a name="switch"></a>Switch
Un port disponible sur un commutateur de la machine du Kit de développement.  

La machine du Kit de développement prend en charge la connexion à un port d’accès de commutateur ou de jonction. Aucune fonctionnalité spéciale n’est requise pour le commutateur. Si vous utilisez un port de jonction ou si vous devez configurer un ID de réseau local virtuel, vous devez fournir cet ID de réseau local virtuel comme paramètre de déploiement.

### <a name="subnet"></a>Sous-réseau
Ne connectez pas la machine du Kit de développement aux sous-réseaux suivants :

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Ces sous-réseaux sont réservés aux réseaux internes au sein de l’environnement du Kit de développement.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Seul le protocole IPv4 est pris en charge. Il est impossible de créer des réseaux IPv6.

### <a name="dhcp"></a>DHCP
Assurez-vous que le serveur DHCP est disponible sur le réseau auquel la carte réseau se connecte. Si DHCP n’est pas disponible, vous devez préparer un réseau IPv4 statique supplémentaire en plus de celui que l’hôte utilise. Vous devez fournir cette adresse IP et la passerelle comme paramètre de déploiement.

### <a name="internet-access"></a>Accès à Internet
Azure Stack nécessite un accès à Internet, directement ou via un proxy transparent. Azure Stack ne prend pas en charge la configuration d’un proxy web pour l’accès à Internet. L’adresse IP de l’hôte et la nouvelle adresse IP assignée à MAS-BGPNAT01 (par DHCP ou IP statique) doivent pouvoir accéder à Internet. Les ports 80 et 443 sont utilisés sous les domaines graph.windows.net et login.microsoftonline.com.


## <a name="next-steps"></a>Étapes suivantes
[Télécharger le package de déploiement de l’ASDK](asdk-download.md)
