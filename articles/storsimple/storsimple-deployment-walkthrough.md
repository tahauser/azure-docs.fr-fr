---
title: "Déploiement d’un appareil StorSimple local | Microsoft Docs"
description: "Décrit les procédures et meilleures pratiques de déploiement du service et de l’appareil StorSimple. (S'applique à Microsoft Azure StorSimple version .3 et versions antérieures.)"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b27f87a2-1363-4e0d-90f7-37b5dd1f21c9
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 5a056bf1ef6b13a2989922e135b99d31d84637c7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="deploy-your-on-premises-storsimple-device"></a>Déploiement de votre appareil StorSimple local
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

> [!div class="op_single_selector"]
> * [Update 2](storsimple-deployment-walkthrough-u2.md)
> * [Update 1](storsimple-deployment-walkthrough-u1.md)
> * [Version Mise à la disposition générale](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Bienvenue dans cette série de didacticiels consacrée au déploiement d’appareils Microsoft Azure StorSimple. Ces didacticiels de déploiement s’appliquent à StorSimple série 8000 Release Version, StorSimple série 8000 Update 0.1, StorSimple série 8000 Update 0.2 et StorSimple série 8000 Update 0.3. Ils expliquent comment configurer votre appareil StorSimple et proposent une liste de contrôle de la configuration, ainsi que la configuration requise et des étapes de configuration détaillées.

Les informations contenues dans ces didacticiels supposent que vous avez passé en revue les précautions de sécurité, et décompacté, monté en rack et câblé votre appareil StorSimple. Si vous devez encore effectuer ces tâches, commencez par lire les [précautions de sécurité](storsimple-safety.md). Selon le modèle de votre appareil, vous pouvez ensuite décompacter, monter en rack et câbler avec les instructions suivantes :

* [Décompacter, monter en rack et câbler votre appareil 8100](storsimple-8100-hardware-installation.md)
* [Décompacter, monter en rack et câbler votre appareil 8600](storsimple-8600-hardware-installation.md)

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de contrôle de configuration avant de commencer. Le processus de déploiement et de configuration peut prendre du temps.

> [!NOTE]
> Les informations de déploiement StorSimple publiées sur le site web Microsoft Azure s’appliquent aux appareils StorSimple de la série 8000 uniquement. Pour obtenir des informations complètes sur les appareils des séries 5000 et 7000, consultez la page [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour plus d’informations sur le déploiement des séries 5000 et 7000, consultez le [Guide de démarrage rapide du système StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).
> 
> 

## <a name="deployment-steps"></a>Étapes du déploiement
Suivez ces étapes requises pour configurer votre appareil StorSimple et le connecter à votre service StorSimple Manager : Outre les étapes requises, il existe des étapes et des procédures facultatives pendant le déploiement. Les instructions de déploiement étape par étape indiquent le moment où vous devez effectuer chacune de ces étapes facultatives.

| Étape | Description |
| --- | --- |
| **CONFIGURATION REQUISE** |Ces étapes doivent être effectuées en préparation du déploiement à venir. |
| Liste de contrôle de la configuration du déploiement. |Utilisez cette liste de contrôle pour rassembler et enregistrer des informations avant et pendant le déploiement. |
| Conditions préalables au déploiement. |Ces conditions permettent de s’assurer que l’environnement est prêt pour le déploiement. |
|  | |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE** |Ces étapes sont nécessaires pour déployer votre appareil StorSimple en production. |
| Étape 1 : Création d'un service. |Configurez le stockage et la gestion de cloud pour votre appareil StorSimple. Ignorez cette étape si vous avez un service existant pour d'autres appareils StorSimple. |
| Étape 2 : Obtention de la clé d'inscription. |Utilisez cette clé pour inscrire et connecter votre appareil StorSimple auprès du service de gestion. |
| Étape 3 : Configuration et inscription de l'appareil via Windows PowerShell pour StorSimple. |Connectez l’appareil à votre réseau et enregistrez-le avec Azure pour terminer l’installation à l’aide du service de gestion. |
| Étape 4 : Fin de l'installation minimale de l'appareil</br>Facultatif : mise à jour de votre appareil StorSimple. |Utilisez le service de gestion pour terminer la configuration de l'appareil et lui permettre d'assurer le stockage. |
| Étape 5 : Création d'un conteneur de volumes. |Créez un conteneur pour mettre en service les volumes. Un conteneur de volumes dispose de paramètres de compte de stockage, de bande passante et de chiffrement pour tous les volumes qu’il contient. |
| Étape 6 : Création d'un volume. |Mettez en service le(s) volume(s) de stockage sur l'appareil StorSimple pour vos serveurs. |
| Étape 7 : Montage, initialisation et formatage d'un volume.</br>Facultatif : Configuration de solution MPIO |Connectez vos serveurs au stockage iSCSI fourni par l'appareil. Vous avez la possibilité de configurer la solution MPIO pour vous assurer que vos serveurs peuvent tolérer une panne de liaison, de réseau et d’interface. |
| Étape 8 : Sauvegarde. |Configurez votre stratégie de sauvegarde pour protéger vos données. |
|  | |
| **AUTRES PROCÉDURES** |Vous devrez peut-être faire référence à ces procédures lors du déploiement de votre solution. |
| Configuration d'un nouveau compte de stockage pour le service. | |
| Utilisation de PuTTY pour se connecter à la console série de l'appareil. | |
| Obtention du nom qualifié d'un hôte Windows Server. | |
| Création d'une sauvegarde manuelle. | |

## <a name="deployment-configuration-checklist"></a>Liste de contrôle de la configuration du déploiement
La liste de contrôle de configuration du déploiement suivante présente les informations dont vous avez besoin avant et pendant la configuration du logiciel de votre appareil StorSimple. L'étude préalable de ces informations simplifie le déploiement de l'appareil StorSimple dans votre environnement. Utilisez cette liste de contrôle pour noter également les détails de configuration lors du déploiement de votre appareil.

| Étape | Paramètre | Détails | Valeurs |
| --- | --- | --- | --- |
| **Câblage de l'appareil** |Accès série |Configuration initiale de l’appareil |Oui/Non |
|  | | | |
| **Configuration et inscription de l'appareil** |Paramètres réseau Data 0 |Adresse IP Data 0 :</br>Masque de sous-réseau :</br>Passerelle :</br>Serveur DNS principal :</br>Serveur NTP principal :</br>Serveur de proxy web IP/nom de domaine complet (facultatif) :</br>Port proxy Web : | |
| &nbsp; |Mot de passe d'administrateur de l'appareil |Le mot de passe doit contenir entre 8 et 15 caractères en minuscules, en majuscules, numériques et spéciaux. | |
| &nbsp; |Mot de passe de gestionnaire d’instantanés StorSimple |Le mot de passe doit contenir 14 ou 15 caractères en minuscules, en majuscules, numériques et spéciaux. | |
| &nbsp; |Clé d'inscription du service |Cette clé est générée à partir du Portail Azure Classic. | |
| &nbsp; |Clé de chiffrement des données du service |Cette clé est créée lorsque l'appareil est inscrit auprès du service de gestion par le biais de Windows PowerShell pour StorSimple. Copiez-la et enregistrez-la en lieu sûr. | |
|  | | | |
| **Mener à bien l'installation minimale de l’appareil** |Nom convivial pour votre appareil |Il s'agit d'un nom descriptif pour l'appareil. | |
| &nbsp; |Fuseau horaire |Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées. | |
| &nbsp; |Serveur DNS secondaire |Il s'agit d'une configuration requise. | |
| &nbsp; |Interface réseau : Adresses IP fixes de contrôleur Data 0 |Ces adresses IP doivent être routables vers Internet.</br>Adresse IP fixe de contrôleur 0 :</br>Adresse IP fixe de contrôleur 1 : | |
|  | | | |
| **Paramètres d'interface réseau supplémentaires** |Interface réseau : Data 1</br>Si iSCSI est activé, ne configurez pas la passerelle. |Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle : | |
| &nbsp; |Interface réseau : Data 2</br>Si iSCSI est activé, ne configurez pas la passerelle. |Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle : | |
| &nbsp; |Interface réseau : Data 3</br>Si iSCSI est activé, ne configurez pas la passerelle. |Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle : | |
| &nbsp; |Interface réseau : Data 4</br>Si iSCSI est activé, ne configurez pas la passerelle. |Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle : | |
| &nbsp; |Interface réseau : Data 5</br>Si iSCSI est activé, ne configurez pas la passerelle. |Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle : | |
|  | | | |
| **Création d’un conteneur de volumes** |Nom du conteneur de volumes : |nom du conteneur | |
| &nbsp; |Compte Azure Storage : |nom et clé d'accès du compte de stockage à associer à ce conteneur de volume | |
| &nbsp; |Clé de chiffrement de stockage cloud : |clé de chiffrement pour le stockage dans chaque conteneur | |
|  | | | |
| **Création d’un volume** |Détails de chaque volume |Nom du volume : | |
| &nbsp; |&nbsp; |Taille : | |
| &nbsp; |&nbsp; |Type d'utilisation : | |
| &nbsp; |&nbsp; |Nom ACR : | |
| &nbsp; |&nbsp; |Stratégie de sauvegarde par défaut : | |
|  | | | |
| **Monter, initialiser et formater un volume** |Détails pour chaque serveur hôte se connectant au stockage |Nom du serveur Windows : | |
| &nbsp; |&nbsp; |IQN du serveur Windows : | |
| &nbsp; |&nbsp; |Nom de volume du serveur Windows : | |
| &nbsp; |&nbsp; |Point de montage/lettre de lecteur NTFS : | |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement
Les sections suivantes décrivent les conditions préalables à la configuration de votre service StorSimple Manager, de votre appareil StorSimple et du réseau dans votre centre de données.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager
Avant de commencer, assurez-vous que :

* Vous disposez d’un compte Microsoft doté d’informations d’identification d’accès.
* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.
* Le service StorSimple Manager est activé dans votre abonnement Microsoft Azure. Votre abonnement doit être acheté dans la page [Accord Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Vous avez accès à un logiciel d’émulation de terminal tel que PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Pour l’appareil dans le centre de données
Avant de configurer l’appareil, assurez-vous que :

* Votre appareil est correctement décompacté, monté en rack et câblé à l’alimentation, au réseau et au port série comme cela est indiqué dans les articles suivants :
  
  * [Décompacter, monter en rack et câbler votre appareil 8100](storsimple-8100-hardware-installation.md)
  * [Décompacter, monter en rack et câbler votre appareil 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données
Avant de commencer, assurez-vous que :

* Les ports du pare-feu de votre centre de données sont ouverts pour autoriser le trafic iSCSI et du cloud, comme décrit dans la section [Configuration réseau requise pour un appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* L'appareil dans votre centre de données peut se connecter au réseau externe. Exécutez les applets de commande [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) (sous forme de tableau ci-dessous) pour valider la connectivité au réseau externe. Effectuez cette validation sur un ordinateur (dans le réseau du centre de données) qui dispose d'une connectivité à Azure et où vous déploierez votre appareil StorSimple.  

| Pour ce paramètre... | Pour vérifier la validité... | Exécutez ces commandes/applets de commande. |
| --- | --- | --- |
| **IP**</br>**Sous-réseau**</br>**Passerelle** |Est-ce une adresse IPv4 ou IPv6 valide ?</br>S’agit-il d’un sous-réseau valide ?</br>S’agit-il d’une passerelle valide ?</br>Est-ce une adresse IP dupliquée sur le réseau ? |`ping ip`</br>`arp -a`</br>Les commandes `ping` et `arp` doivent échouer, indiquant qu’il n’existe aucun appareil sur le réseau du centre de données qui utilise cette adresse IP. |
|  | | |
| **DNS** |Est-ce un nom DNS valide qui peut résoudre des URL Azure ? |`Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Une autre commande qui peut être utilisée est la suivante :</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com` |
| &nbsp; |Vérifiez si le port 53 est ouvert. Ceci s'applique uniquement si vous utilisez un DNS externe pour votre appareil. Le DNS interne doit résoudre automatiquement les URL externes. |`Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Plus d'informations sur cette applet de commande](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/) |
|  | | |
| **NTP** |Nous déclenchons une synchronisation horaire dès que le serveur NTP est en entrée. Vérifiez que le port UDP 123 est ouvert lorsque vous entrez `time.windows.com` ou des serveurs horaires publics. |[Télécharger et utiliser ce script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca). |
|  | | |
| **Proxy (facultatif)** |S'agit-il d'un URI et d'un port de proxy valides ? </br> Le mode d'authentification est-il correct ? |<code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Cette commande doit être exécutée immédiatement après la configuration du proxy Web. Si un code d’état de 200 est retourné, cela indique que la connexion est établie. |
| &nbsp; |Le trafic est-il acheminé via le proxy ? |Exécutez la validation DNS, la vérification NTP ou la vérification HTTP une fois après la configuration du proxy sur votre appareil. Ceci vous indique clairement si le trafic est bloqué au niveau du proxy ou ailleurs. |
|  | | |
| **Inscription** |Vérifiez si les ports TCP sortants 443, 80, 9354 sont ouverts. |`Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Plus d'informations sur l'applet de commande Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx) |

## <a name="step-by-step-deployment"></a>DÉPLOIEMENT ÉTAPE PAR ÉTAPE
Utilisez la procédure détaillée ci-dessous pour déployer votre appareil StorSimple dans le centre de données.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Création d’un nouveau service
Un service StorSimple Manager peut gérer plusieurs appareils StorSimple. Pour le déploiement de votre premier appareil StorSimple, vous devrez créer un service StorSimple Manager.

> [!IMPORTANT]
> Ignorez cette étape si vous avez un service StorSimple Manager existant et que vous envisagez de déployer votre appareil StorSimple avec ce service.
> 
> 

Procédez comme suit pour créer une instance du service StorSimple Manager.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> Si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service, vous devez créer au moins un compte de stockage après avoir créé un service. Ce compte de stockage est utilisé lorsque vous créez un conteneur de volumes.
> 
> Si vous n’avez pas créé de compte de stockage automatiquement, accédez à la page [Configuration d’un compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> Si vous avez activé la création automatique d’un compte de stockage, passez à l’ [étape 2 : Obtention de la clé d’inscription](#step-2:-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>étape 2 : Obtention de la clé d’inscription
Une fois le service StorSimple Manager opérationnel, vous devez obtenir la clé d’inscription. Cette clé est utilisée pour inscrire et connecter votre appareil StorSimple auprès du service.

Procédez comme suit dans le Portail Azure Classic.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Étape 3 : Configuration et inscription de l’appareil via Windows PowerShell pour StorSimple
> [!IMPORTANT]
> Avant d'effectuer cette configuration, débranchez toutes les interfaces réseau autres que DATA 0 sur les deux contrôleurs (actif et passif).
> 
> 

Utilisez Windows PowerShell pour StorSimple pour terminer l’installation initiale de votre appareil StorSimple, comme expliqué dans la procédure suivante. Vous devez utiliser un logiciel d’émulation de terminal pour effectuer cette étape. Pour plus d’informations, consultez la rubrique [Utilisation de PuTTY pour se connecter à la console série de l’appareil](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Étape 4 : Fin de l'installation minimale de l'appareil
Pour pouvoir mener à bien la configuration minimale de votre appareil StorSimple, vous devez :

* configurer le serveur DNS secondaire ;
* activer la norme iSCSI sur au moins une interface réseau ;
* affecter des adresses IP fixes aux deux contrôleurs.

Pour mener à bien la configuration minimale requise, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Une fois la configuration de l'appareil terminée, vous devez rechercher les mises à jour et les installer. Les mises à jour peuvent prendre plusieurs heures. Suivez les instructions sous [Recherche et application des mises à jour](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Étape 5 : Création d’un conteneur de volumes
Un conteneur de volumes dispose de paramètres de compte de stockage, de bande passante et de chiffrement pour tous les volumes qu’il contient. Vous devez créer un conteneur de volumes avant de commencer la configuration des volumes sur votre appareil StorSimple.

Pour créer un conteneur de volumes, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Étape 6 : Création d’un volume
Après avoir créé un conteneur de volumes, vous pouvez configurer un volume de stockage sur l’appareil StorSimple pour vos serveurs. Pour créer un volume, procédez comme suit dans le portail Azure Classic.

> [!IMPORTANT]
> StorSimple Manager peut créer uniquement des volumes alloués dynamiquement.  Vous ne pouvez pas créer des volumes alloués entièrement ou partiellement.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Étape 7 : Montage, initialisation et formatage d’un volume
> [!IMPORTANT]
> * Pour une haute disponibilité de votre solution StorSimple, nous vous recommandons de configurer MPIO sur votre hôte Windows Server (facultatif) avant de configurer iSCSI sur votre hôte Windows Server. La configuration de MPIO sur les serveurs hôtes permet de s'assurer que les serveurs peuvent tolérer une panne de liaison, de réseau ou d'interface.
> * Pour obtenir les instructions d'installation et de configuration de la solution MPIO et d'iSCSI, consultez [Configuration de la solution MPIO pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md). Ces instructions incluent également les étapes pour monter, initialiser et formater des volumes StorSimple.
> 
> 

Si vous décidez de ne pas configurer MPIO, procédez comme suit pour monter, initialiser et formater vos volumes StorSimple.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Étape 8 : Sauvegarde
Les sauvegardes fournissent une protection jusqu’à une date et une heure des volumes et optimisent la récupération tout en réduisant les délais de restauration. Vous pouvez effectuer deux types de sauvegarde sur votre appareil StorSimple : les instantanés locaux et les instantanés cloud. Chacun de ces types de sauvegarde peut être **Planifié** ou **Manuel**.

Pour créer une sauvegarde planifiée, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez à tout moment effectuer une sauvegarde manuelle. Pour connaître les procédures à suivre, consultez la rubrique [Création d’une sauvegarde manuelle](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configuration d’un compte de stockage pour le service
Il s’agit d’une étape facultative que vous devez exécuter uniquement si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service. Un compte de stockage Microsoft Azure est requis pour créer un conteneur de volumes StorSimple.

Si vous devez créer un compte de stockage Azure dans une autre région, consultez la page [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md) pour obtenir des instructions détaillées.

Procédez comme suit dans le Portail Azure Classic, sur la page **Service StorSimple Manager** .

[!INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilisation de PuTTY pour se connecter à la console série de l’appareil
Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser un logiciel d’émulation de terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’appareil directement via la console série ou en ouvrant une session telnet à partir d’un ordinateur distant.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Recherche et application des mises à jour
La mise à jour de votre appareil peut prendre entre 1 et 4 heures. Procédez comme suit pour rechercher et appliquer des mises à jour sur votre appareil.

> [!NOTE]
> Si vous avez une passerelle configurée sur une interface réseau différente de Data 0, vous devrez désactiver les interfaces réseau Data 2 et Data 3 avant d'installer la mise à jour. Accédez à **Périphériques > Configurer** et désactivez les interfaces Data 2 et Data 3. Vous devrez réactiver ces interfaces après la mise à jour de l'appareil.
> 
> 

#### <a name="to-update-your-device"></a>Mise à jour de votre appareil
1. Sur la page **Démarrage rapide** de l’appareil, cliquez sur **Appareils**. Sélectionnez l’appareil physique, cliquez sur **Maintenance** puis sur **Rechercher les mises à jour**.  
2. La tâche créée recherche les mises à jour disponibles. Si des mises à jour sont disponibles, l’option **Rechercher les mises à jour** devient **Installer les mises à jour**. Cliquez sur **Installer les mises à jour**. Vous serez peut-être invité à désactiver Data 2 et Data 3 avant d'installer les mises à jour. Vous devez désactiver ces interfaces réseau ou les mises à jour peuvent échouer.
3. Une tâche de mise à jour est créée. Surveillez l'état de la mise à jour en accédant à **Tâches**.
   
   > [!NOTE]
   > Lorsque la tâche de mise à jour démarre, elle affiche immédiatement l'état de 50 %. L'état passe ensuite à 100 % uniquement lorsque la tâche de mise à jour est terminée. Il n'existe aucun état en temps réel du processus de mise à jour.
   > 
   > 
4. Lorsque l'appareil est correctement mis à jour, activez les interfaces réseau Data 2 et Data 3 si elles ont été désactivées.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtention du nom qualifié d’un hôte Windows Server
Procédez comme suit pour obtenir le nom qualifié iSCSI (IQN) d'un hôte Windows exécutant Windows Server 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Création d’une sauvegarde manuelle
Pour créer une sauvegarde manuelle à la demande pour un seul volume sur votre appareil StorSimple, procédez comme suit dans le portail Azure Classic.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="next-steps"></a>Étapes suivantes
* Configuration d’un [appareil virtuel](storsimple-virtual-device-u2.md).
* Utilisez le [service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer votre appareil StorSimple.

