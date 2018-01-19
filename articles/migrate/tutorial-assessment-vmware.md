---
title: "Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure avec Azure Migrate | Microsoft Docs"
description: "Décrit comment découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure, à l’aide du service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: a5019d3f729f2efbd01fca021b0089c7f99b0014
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure.

Le service [Azure Migrate](migrate-overview.md) évalue les charges de travail locales pour la migration vers Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un projet Azure Migrate.
> * Configurer une machine virtuelle collector locale, pour découvrir les machines virtuelles VMware locales en vue de les évaluer.
> * Regrouper les machines virtuelles et créer une évaluation.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Conditions préalables

- **VMware** : les machines virtuelles à migrer doivent être gérées par un serveur vCenter exécutant la version 5.5, 6.0 ou 6.5. De plus, vous avez besoin d'un hôte ESXi exécutant la version 5.0 ou ultérieure pour déployer la machine virtuelle du collecteur. 
 
> [!NOTE]
> La prise en charge pour Hyper-V fait partie de la feuille de route et sera activée prochainement. 

- **Compte de serveur vCenter** : vous avez besoin d'un compte en lecture seule pour accéder au serveur vCenter. Azure Migrate utilise ce compte pour découvrir les machines virtuelles sur site.
- **Autorisations** : sur le serveur vCenter, vous devez disposer des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA. 
- **Paramètres de statistiques** : vous devez définir les paramètres de statistiques pour le serveur vCenter sur le niveau 3 avant de commencer le déploiement. Si le niveau appliqué est inférieur à 3, l’évaluation fonctionne, mais les données de performances pour le stockage et le réseau ne sont pas collectées. Dans ce cas, les recommandations de taille seront effectuées selon les données de performances pour le processeur et la mémoire, et selon les données de configuration pour les adaptateurs de disque et réseau. 

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Création d’un projet

1. Dans le portail Azure, cliquez sur **Créer une ressource**.
2. Recherchez **Azure Migrate**, puis sélectionnez le service **Azure Migrate (préversion)** dans les résultats de la recherche. Cliquez ensuite sur **Créer**.
3. Spécifiez un nom de projet et l’abonnement Azure pour le projet.
4. Créez un groupe de ressources.
5. Spécifiez l’emplacement dans lequel créer le projet, puis cliquez sur **Créer**. Vous ne pouvez créer un projet Azure Migrate que dans la région Centre des États-Unis pour cette préversion. Toutefois, vous pouvez toujours planifier votre migration pour n'importe quel emplacement Azure cible. L’emplacement spécifié pour le projet est utilisé uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Azure Migrate crée une machine virtuelle locale connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware sur site et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, vous téléchargez un fichier .OVA, puis vous l’importez dans le serveur vCenter local pour créer la machine virtuelle.

1. Dans le projet Azure Migrate, cliquez sur **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Découvrir des machines**, cliquez sur **Télécharger** pour télécharger le fichier .OVA.
3. Dans **Copier les informations d’identification du projet**, copiez l’ID de projet et la clé. Vous en aurez besoin pour la configuration du collecteur.

    ![Télécharger le fichier .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Vérifiez que le fichier .OVA est sécurisé, avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Le code de hachage généré doit correspondre aux paramètres ci-après.
    
    Pour OVA version 1.0.8.49
    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 8779eea842a1ac465942295c988ac0c7 
    SHA1 | c136c52a0f785e1fd98865e16479dd103704887d
    SHA256 | 5143b1144836f01dd4eaf84ff94bc1d2c53f51ad04b1ca43ade0d14a527ac3f9

    Pour OVA version 1.0.8.40 :

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Créer la machine virtuelle collector

Importez le fichier téléchargé sur le serveur vCenter.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .ova.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle collector et l’objet d’inventaire destiné à héberger la machine virtuelle.
5. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle collector.
7. Dans Storage (Stockage), spécifiez la destination de stockage pour la machine virtuelle collector.
8. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
9. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle collector. Le réseau requiert une connexion à Internet pour envoyer des métadonnées vers Azure. 
10. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).

## <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Indiquez les préférences de langue, de fuseau horaire et de mot de passe de l’appliance.
3. Sur le bureau, cliquez sur le raccourci **Run collector** (Exécuter le collecteur).
4. Dans Azure Migrate Collector, ouvrez **Set Up Prerequisites** (Configurer les prérequis).
    - Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - Le collecteur vérifie que la machine virtuelle a accès à Internet.
    - Si la machine virtuelle accède à internet via un proxy, cliquez sur **Proxy settings** (Paramètres du proxy) et spécifiez l’adresse du proxy et le port d’écoute. Spécifiez les informations d’identification si le proxy nécessite une authentification.

    > [!NOTE]
    > L’adresse proxy doit être entrée au format http://ProxyAdresseIP ou http://ProxyFQDN. Seuls les proxys HTTP sont pris en charge.

    - Le collecteur vérifie que le service Collector est en cours d’exécution. Le service est installé par défaut sur la machine virtuelle collector.
    - Téléchargez et installez VMware PowerCLI.

5. Dans **Specify vCenter Server details** (Spécifier les informations vCenter Server), procédez aux étapes suivantes :
    - Spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter.
    - Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte en lecture seule que le collecteur utilisera pour découvrir les machines virtuelles sur le serveur vCenter.
    - Dans **Étendue de la collecte**, sélectionnez une étendue pour la découverte des machines virtuelles. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. Elle ne doit pas contenir plus de 1 000 machines virtuelles. 

6. Dans **Specify migration project** (Spécifier un projet de migration), spécifiez l’ID et la clé du projet Azure Migrate que vous avez copiés à partir du portail. Si vous ne les avez pas copiés, ouvrez le portail Azure à partir de la machine virtuelle collector. Dans la page **Vue d’ensemble** du projet, cliquez sur **Découvrir des machines**, puis copiez les valeurs.  
7. Dans **View collection progress** (Afficher la progression de la collecte), surveillez le processus de détection et vérifiez que les métadonnées collectées à partir des machines virtuelles appartiennent à l’étendue spécifiée. Le collecteur fournit une durée approximative de la découverte.

> [!NOTE]
> Le collecteur prend uniquement en charge « Anglais (États-Unis) » comme langue du système d’exploitation et langue de l’interface du collecteur. La prise en charge de langues supplémentaires sera bientôt disponible.


### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

La durée de la découverte varie selon le nombre de machines virtuelles découvertes. En règle générale, pour 100 machines virtuelles, une fois que l’exécution du collecteur prend fin, la découverte se termine en une heure environ. 

1. Dans le projet Azure Migrate, cliquez sur **Gérer** > **Machines**.
2. Vérifiez que les machines virtuelles que vous souhaitez découvrir apparaissent dans le portail.


## <a name="create-and-view-an-assessment"></a>Créer et afficher une évaluation

Une fois les machines virtuelles découvertes, vous les regroupez pour créer une évaluation. 

1. Dans la page **Vue d’ensemble** du projet, cliquez sur **Créer une évaluation**.
2. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.
3. Créez le groupe et spécifiez un nom de groupe.
4. Sélectionnez les machines que vous souhaitez ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe et l’évaluation.
6. Une fois l’évaluation créée, affichez-la dans **Vue d’ensemble** > **Tableau de bord**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.

### <a name="sample-assessment"></a>Exemple d’évaluation

Voici un exemple de rapport d’évaluation. Il indique le degré de compatibilité des machines virtuelles avec Azure et fournit une estimation des coûts mensuels. 

![Rapport d’évaluation](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Préparé pour Azure

Cet affichage indique l’état de préparation pour chaque machine.

- Pour les machines virtuelles qui sont prêtes, Azure Migrate recommande une taille de machine virtuelle dans Azure.
- Pour les machines virtuelles qui ne sont pas prêtes, Azure Migrate explique pourquoi et indique des étapes de correction.
- Azure Migrate suggère des outils que vous pouvez utiliser pour la migration. Si la machine est adaptée à la migration lift-and-shift, le service Azure Site Recovery est recommandé. Dans le cas d’un ordinateur de base de données, Azure Database Migration Service est recommandé.

  ![Préparation de l’évaluation](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimation des coûts mensuels

Cette vue affiche le coût total du calcul et du stockage de l'exécution des machines virtuelles dans Azure avec les détails pour chaque machine. Les estimations des coûts sont calculées à partir des recommandations de taille pour une machine et ses disques suivant leurs performances et des propriétés de l’évaluation. 

> [!NOTE]
> L'estimation des coûts fournie par Azure Migrate s'applique à l'exécution des machines virtuelles sur site en tant que machines virtuelles Azure Infrastructure as a service (IaaS). Azure Migrate ne tient pas compte des coûts Platform as a service (PaaS) ou Software as a service (SaaS). 

L’estimation des coûts mensuels de calcul et de stockage est agrégée pour toutes les machines virtuelles dans le groupe. 

![Évaluation des coûts pour les machines virtuelles](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Vous pouvez explorer les détails d'une machine spécifique.

![Évaluation des coûts pour les machines virtuelles](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>étapes suivantes

- [Découvrez comment détecter et évaluer](how-to-scale-assessment.md) un environnement VMware de grande taille.
- Découvrez comment créer des groupes d’évaluation à fiabilité élevée à l’aide d’un [mappage de dépendances des machines](how-to-create-group-machine-dependencies.md).
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
