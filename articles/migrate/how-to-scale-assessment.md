---
title: "Mise à l'échelle de la découverte et de l’évaluation avec Azure Migrate | Microsoft Docs"
description: "Décrit comment évaluer un grand nombre de machines sur site avec le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: e28a2144dd102fcd2ec05531432cac0df250ae01
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Découvrir et évaluer un environnement VMware de grande taille

Cet article décrit comment évaluer un grand nombre de machines sur site avec [Azure Migrate](migrate-overview.md). Azure Migrate évalue les machines pour vérifier si elles sont adaptées à la migration vers Azure, et fournit des estimations de dimensionnement et de coût d’exécution de chaque machine dans Azure.

## <a name="prerequisites"></a>Composants requis

- **VMware** : vous avez besoin d’au moins une machine virtuelle VMware sur un cluster ou hôte ESXi exécutant la version 5.0 ou supérieure. L’hôte ou le cluster doit être géré par un serveur vCenter exécutant la version 5.5 ou 6.0.
- **Compte de vCenter** : vous avez besoin d’un compte en lecture seule doté d’informations d’identification d’administrateur pour le serveur vCenter. Azure Migrate utilise ce compte pour découvrir les machines virtuelles.
- **Autorisations** : sur le serveur vCenter, vous devez disposer des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA.
- **Paramètres de statistiques** : vous devez définir les paramètres de statistiques pour le serveur vCenter sur le niveau 2 ou supérieur, avant de commencer le déploiement.

## <a name="plan-azure-migrate-projects"></a>Planification de projets Azure Migrate

Un projet Azure Migrate peut évaluer jusqu'à 1 500 machines. Une seule découverte dans un projet peut découvrir jusqu'à 1 000 machines.

- Si vous avez moins de 1 000 machines à découvrir, vous avez besoin d'un seul projet avec une seule découverte.
- Si vous avez entre 1 000 et 1 500 machines, vous avez besoin d'un seul projet avec deux découvertes.
- Si vous avez plus de 1 500 machines, vous devez créer plusieurs projets et effectuer plusieurs découvertes, selon vos besoins. Par exemple :
    - Si vous avez 3 000 machines, vous pouvez configurer deux projets avec deux découvertes, ou trois projets avec une seule découverte.
    - Si vous avez 5 000 machines, vous pouvez créer quatre projets. Deux avec une découverte de 1 500 machines, et un avec une découverte de 500 machines. Vous pouvez également configurer cinq projets avec une seule découverte dans chacun d'eux. 
- Lorsque vous effectuez une découverte dans Azure Migrate, vous pouvez définir la portée de la découverte sur un centre de données, un cluster ou un dossier VMware.
- Pour effectuer plusieurs recherches, vérifiez dans vCenter que les machines virtuelles que vous souhaitez détecter se trouvent dans des dossiers, des centres de données ou des clusters prenant en charge la limitation de 1 000 machines.
- À des fins d'évaluation, nous vous recommandons de conserver les machines ayant des interdépendances au sein d'un même projet et d'une même évaluation. Dans vCenter, assurez-vous que les machines dépendantes se trouvent dans le même dossier, centre de données ou cluster, à des fins d'évaluation.


## <a name="create-a-project"></a>Création d’un projet

Créez un projet Azure Migrate en fonction de vos besoins.

1. Dans le Portail Azure, cliquez sur **Créer une ressource**.
2. Recherchez **Azure Migrate**, puis sélectionnez le service **Azure Migrate (préversion)** dans les résultats de la recherche. Cliquez ensuite sur **Créer**.
3. Spécifiez un nom de projet et l’abonnement Azure pour le projet.
4. Créez un groupe de ressources.
5. Spécifiez la région dans laquelle créer le projet, puis cliquez sur **Créer**. Les métadonnées collectées à partir des machines virtuelles sur site sont stockées dans cette région.

## <a name="set-up-the-collector-appliance"></a>Configuration de l’appliance collecteur

Azure Migrate crée une machine virtuelle sur site connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware sur site et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, vous téléchargez un fichier .OVA, puis vous l’importez dans le serveur vCenter sur site pour créer la machine virtuelle.

### <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Si vous avez plusieurs projets, vous n'avez besoin de télécharger l'appliance de collecte qu'une seule fois sur le serveur vCenter. Une fois l'appliance téléchargée et configurée, vous l'exécutez pour chaque projet et spécifiez l'ID et la clé uniques du projet.

1. Dans le projet Azure Migrate, cliquez sur **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Découvrir des machines**, cliquez sur **Télécharger** pour télécharger le fichier .OVA.
3. Dans **Copier les informations d’identification du projet**, copiez l’ID et la clé du projet. Vous en aurez besoin pour la configuration du collecteur.

   
### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Vérifiez que le fichier .OVA est sécurisé, avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Le code de hachage généré doit correspondre aux paramètres ci-après.

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Créer la machine virtuelle collector

Importez le fichier téléchargé sur le serveur vCenter.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .ova.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle collector et l’objet d’inventaire destiné à héberger la machine virtuelle.
5. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle collector.
7. Dans Storage (Stockage), spécifiez la destination de stockage pour la machine virtuelle collector.
8. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
9. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle collector. Le réseau requiert une connexion à Internet pour envoyer des métadonnées vers Azure. 
10. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).

## <a name="identify-the-key-and-id-for-each-project"></a>Identification de la clé et de l'identifiant pour chaque projet

Si vous avez plusieurs projets, assurez-vous d'identifier l'ID et la clé pour chacun d'eux. Vous avez besoin de la clé lorsque vous exécutez le collecteur pour découvrir les machines virtuelles.

1. Dans le projet, cliquez sur **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Copier les informations d’identification du projet**, copiez l’ID et la clé du projet. 
    ![ID du projet](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>Niveau de statistiques vCenter pour collecter les compteurs de performances
Voici la liste des compteurs collectés lors de la découverte. Par défaut, les compteurs sont disponibles à différents niveaux sur le serveur vCenter. Nous vous recommandons de définir le niveau commun le plus élevé (niveau 3) pour le niveau de statistiques afin que tous les compteurs soient correctement collectés. Si vCenter est défini à un niveau inférieur, seuls quelques compteurs risquent d'être collectés complètement, le reste étant défini sur 0. L'évaluation peut donc afficher des données incomplètes. Le tableau ci-dessous répertorie également les résultats d'évaluation qui seront impactés si un compteur particulier n'est pas collecté.

|Compteur                                  |Level    |Par niveau d'appareil  |Évaluation de l'impact                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |N/D                |Taille de machine virtuelle recommandée et coût                    |
|mem.usage.average                        | 1       |N/D                |Taille de machine virtuelle recommandée et coût                    |
|virtualDisk.read.average                 | 2       |2                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.write.average                | 2       |2                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Taille du disque, coût de stockage et taille de la machine virtuelle         |
|net.received.average                     | 2       |3                 |Taille de la machine virtuelle et coût du réseau                        |
|net.transmitted.average                  | 2       |3                 |Taille de la machine virtuelle et coût du réseau                        |

> [!WARNING]
> Si vous venez de définir un niveau de statistiques plus élevé, la génération des compteurs de performances prendra jusqu'à une journée. Par conséquent, il est recommandé d'exécuter la découverte un jour plus tard.

## <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

Pour chaque découverte que vous devez effectuer, vous exécutez le collecteur pour afin de découvrir les machines virtuelles dans la portée requise. Exécutez les découvertes les unes après les autres. Les découvertes simultanées ne sont pas prises en charge, et chaque découverte doit avoir une portée différente.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Indiquez les préférences de langue, de fuseau horaire et de mot de passe de l’appliance.
3. Sur le bureau, cliquez sur le raccourci **Run collector** (Exécuter le collecteur).
4. Dans Azure Migrate Collector, ouvrez **Set Up Prerequisites** (Configurer les prérequis).
    - Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - Le collecteur vérifie que la machine virtuelle a accès à Internet.
    - Si la machine virtuelle accède à internet via un proxy, cliquez sur **Proxy settings** (Paramètres du proxy) et spécifiez l’adresse du proxy et le port d’écoute. Spécifiez les informations d’identification si le proxy nécessite une authentification.
    - Le collecteur vérifie que le service de profil Windows est en cours d’exécution. Le service est installé par défaut sur la machine virtuelle collector.
    - Téléchargez et installez VMware PowerCLI.
. Dans **Découvrir des machines**, effectuez les étapes suivantes :
    - Spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter.
    - Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte en lecture seule que le collecteur utilisera pour découvrir les machines virtuelles sur le serveur vCenter.
    - Dans **Étendue de la collecte**, sélectionnez une étendue pour la découverte des machines virtuelles. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. Elle ne doit pas contenir plus de 1 000 machines virtuelles. 
    - n **Identifier une catégorie pour le regroupement**, sélectionnez **Aucune**.

        ![Sélectionner la portée](./media/how-to-scale-assessment/select-scope.png)

1. Dans **Sélectionner un projet**, spécifiez l'ID et la clé du projet. Si vous ne les avez pas copiés, ouvrez le portail Azure à partir de la machine virtuelle collector. Dans la page **Vue d’ensemble** du projet, cliquez sur **Découvrir des machines**, puis copiez les valeurs.  
Dans **Effectuer la découverte**, surveillez le processus de découverte et vérifiez que les métadonnées collectées à partir des machines virtuelles appartiennent à l’étendue. Le collecteur fournit une durée approximative de la découverte.


### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

La durée de la découverte varie selon le nombre de machines virtuelles découvertes. En règle générale, pour 100 machines virtuelles, une fois que l’exécution du collecteur prend fin, la découverte se termine en une heure environ. 

1. Dans le projet Azure Migrate, cliquez sur **Gérer** > **Machines**.
2. Vérifiez que les machines virtuelles que vous souhaitez découvrir apparaissent dans le portail.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un groupe](how-to-create-a-group.md) pour l'évaluation.
- [Découvrez plus en détails](concepts-assessment-calculation.md) le mode de calcul des évaluations.