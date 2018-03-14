---
title: "Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure avec Azure Migrate | Microsoft Docs"
description: "Décrit comment découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure, à l’aide du service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bbd08637894c43c543aeb8236f515e5ed9c5fc19
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Découvrir et évaluer des machines virtuelles VMware locales pour la migration vers Azure.

Le service [Azure Migrate](migrate-overview.md) évalue les charges de travail locales pour la migration vers Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte utilisé par Azure Migrate pour découvrir les machines virtuelles locales
> * Créer un projet Azure Migrate.
> * Configurer une machine virtuelle collector locale, pour découvrir les machines virtuelles VMware locales en vue de les évaluer.
> * Regrouper les machines virtuelles et créer une évaluation.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- **VMware** : les machines virtuelles à migrer doivent être gérées par un vCenter Server exécutant la version 5.5, 6.0 ou 6.5. De plus, vous avez besoin d'un hôte ESXi exécutant la version 5.0 ou ultérieure pour déployer la machine virtuelle du collecteur. 
 
> [!NOTE]
> La prise en charge pour Hyper-V fait partie de la feuille de route et sera activée prochainement. 

- **Compte de serveur vCenter** : vous avez besoin d'un compte en lecture seule pour accéder au serveur vCenter. Azure Migrate utilise ce compte pour découvrir les machines virtuelles sur site.
- **Autorisations** : sur le serveur vCenter, vous devez disposer des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA. 
- **Paramètres de statistiques** : vous devez définir les paramètres de statistiques pour le serveur vCenter sur le niveau 3 avant de commencer le déploiement. Si le niveau appliqué est inférieur à 3, l’évaluation fonctionne, mais les données de performances pour le stockage et le réseau ne sont pas collectées. Dans ce cas, les recommandations de taille seront effectuées selon les données de performances pour le processeur et la mémoire, et selon les données de configuration pour les adaptateurs de disque et réseau. 

## <a name="create-an-account-for-vm-discovery"></a>Créer un compte pour la découverte de machine virtuelle

Azure Migrate doit accéder à des serveurs VMware pour découvrir automatiquement les machines virtuelles à évaluer. Créez un compte VMware avec les propriétés suivantes. Vous devez spécifier ce compte pendant la configuration d’Azure Migrate.

- Type d’utilisateur : au moins un utilisateur en lecture seule
- Autorisations : objet de centre de données > Propager vers l’objet enfant, rôle = lecture seule
- Détails : l’utilisateur est affecté au niveau du centre de données et a accès à tous les objets du centre de données.
- Pour restreindre l’accès, attribuez le rôle Aucun accès avec l’autorisation Propager vers l’objet enfant aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Création d’un projet

1. Dans le portail Azure, cliquez sur **Créer une ressource**.
2. Recherchez **Azure Migrate**, puis sélectionnez le service **Azure Migrate** dans les résultats de la recherche. Cliquez ensuite sur **Créer**.
3. Spécifiez un nom de projet et l’abonnement Azure pour le projet.
4. Créez un groupe de ressources.
5. Spécifiez l’emplacement dans lequel créer le projet, puis cliquez sur **Créer**. Vous ne pouvez créer un projet Azure Migrate que dans la région Centre-Ouest ou Est des États-Unis. Toutefois, vous pouvez toujours planifier votre migration pour n'importe quel emplacement Azure cible. L’emplacement spécifié pour le projet est utilisé uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales. 

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

    Pour OVA version 1.0.9.5

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  
    
    Pour OVA version 1.0.9.2

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  
    
    Pour OVA version 1.0.8.59

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    Pour OVA version 1.0.8.49
    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Pour OVA version 1.0.8.40 :

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
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

### <a name="assessment-details"></a>Détails de l’évaluation

Une évaluation inclut des informations complémentaires sur la compatibilité des machines virtuelles locales pour Azure, la taille idéale de machine virtuelle pour l’exécution de la machine virtuelle dans Azure, et l’estimation des coûts Azure mensuels. 

![Rapport d’évaluation](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Préparé pour Azure

La vue Préparé pour Azure de l’évaluation indique l’état de préparation de chaque machine virtuelle. En fonction des propriétés de la machine virtuelle, chaque machine virtuelle peut être marquée comme suit :
- Préparé pour Azure
- Préparé pour Azure sous condition
- Non préparé pour Azure
- Préparation inconnue 

Pour les machines virtuelles qui sont prêtes, Azure Migrate recommande une taille de machine virtuelle dans Azure. La recommandation de taille effectuée par Azure Migrate varie selon le critère de dimensionnement spécifié dans les propriétés de l’évaluation. Si le critère de dimensionnement est le dimensionnement basé sur les performances, la recommandation de taille tient compte de l’historique des performances des machines virtuelles. Si le critère de dimensionnement est « comme en local », la recommandation est effectuée en fonction de la taille de la machine virtuelle locale (dimensionnement en l’état). Les données d’utilisation ne sont pas prises en compte dans ce cas. [En savoir plus](concepts-assessment-calculation.md) sur le déroulement du dimensionnement dans Azure Migrate. 

Pour les machines virtuelles qui ne sont pas préparées ou qui sont préparées sous condition pour Azure, Azure Migrate explique les problèmes de préparation et fournit la procédure de correction. 

Les machines virtuelles pour lesquelles Azure Migrate ne peut pas identifier la préparation pour Azure (en raison de l’indisponibilité des données) sont marquées de la valeur Préparation inconnue.

En plus de la préparation Azure et du dimensionnement, Azure Migrate suggère des outils que vous pouvez utiliser pour migrer la machine virtuelle. Cela requiert une découverte plus approfondie que dans l’environnement local. [En savoir plus](how-to-get-migration-tool.md) sur la procédure à suivre pour effectuer une découverte plus approfondie en installant des agents sur les machines locales. Si les agents ne sont pas installés sur les machines locales, la migration lift-and-shift est suggérée à l’aide [d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Si les agents sont installés sur la machine locale, Azure Migrate examine les processus exécutés à l’intérieur de la machine et indique si celle-ci est une machine de base de données ou non. Si la machine est une machine de base de données, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) est suggéré ; autrement Azure Site Recovery est suggéré en tant qu’outil de migration.

  ![Préparation de l’évaluation](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimation des coûts mensuels

Cette vue affiche le coût total du calcul et du stockage de l'exécution des machines virtuelles dans Azure avec les détails pour chaque machine. Les estimations des coûts sont calculées en tenant compte des recommandations de taille d’Azure Migrate pour une machine, ses disques et les propriétés de l’évaluation. 

> [!NOTE]
> L'estimation des coûts fournie par Azure Migrate s'applique à l'exécution des machines virtuelles sur site en tant que machines virtuelles Azure Infrastructure as a service (IaaS). Azure Migrate ne tient pas compte des coûts Platform as a service (PaaS) ou Software as a service (SaaS). 

L’estimation des coûts mensuels de calcul et de stockage est agrégée pour toutes les machines virtuelles dans le groupe. 

![Évaluation des coûts pour les machines virtuelles](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

#### <a name="confidence-rating"></a>Niveau de confiance

Chaque évaluation dans Azure Migrate est associée à un niveau de confiance allant de 1 étoile à 5 étoiles (1 étoile constituant la valeur la plus faible, et 5 étoiles la valeur la plus élevée). Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation. Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate. 

Le niveau de confiance est utile quand vous effectuez un *dimensionnement basé sur les performances*, car Azure Migrate peut ne pas disposer de suffisamment de points de données pour effectuer un dimensionnement basé sur l’utilisation. Pour le *dimensionnement local*, le niveau de confiance s’élève toujours à 5 étoiles, car Azure Migrate dispose de tous les points de données nécessaires au dimensionnement de la machine virtuelle. 

Pour le dimensionnement basé sur les performances de la machine virtuelle, Azure Migrate a besoin des données d’utilisation relatives à l’UC et à la mémoire. Par ailleurs, pour chaque disque attaché à la machine virtuelle, les E/S par seconde en lecture/écriture et le débit sont des données nécessaires. De même, pour chaque carte réseau jointe à la machine virtuelle, Azure Migrate doit permettre au réseau entrant/sortant de respecter un dimensionnement basé sur les performances. Si aucun des chiffres d’utilisation ci-dessus n’est disponible dans vCenter Server, la recommandation de dimensionnement effectuée par Azure Migrate peut ne pas être fiable. Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

Tous les points de données d’une évaluation peuvent ne pas être disponibles pour l’une des raisons suivantes :
- Le paramètre des statistiques dans vCenter Server n’est pas défini sur le niveau 3 et l’évaluation présente un dimensionnement basé sur les performances en tant que critère de dimensionnement. Si le paramètre des statistiques dans vCenter Server est inférieur au niveau 3, les données de performances pour le disque et le réseau ne sont pas collectées à partir de vCenter Server. Dans ce cas, la recommandation fournie par Azure Migrate pour le disque et le réseau n’est pas basée sur l’utilisation. Pour le stockage, Azure Migrate recommande des disques standard sans tenir compte des E/S par seconde/du débit du disque. Azure Migrate ne peut pas déterminer si le disque a besoin d’un disque premium dans Azure.
- Le paramètre des statistiques dans vCenter Server a été défini sur le niveau 3 pour une durée plus courte, avant le lancement de la découverte. Par exemple, intéressons-nous au scénario dans lequel vous faites passer le paramètre de statistiques au niveau 3 aujourd’hui et lancez la découverte en utilisant l’appliance de collecteur demain (après 24 heures). Si vous créez une évaluation pour une journée, vous disposez de tous les points de données, et le niveau de confiance de l’évaluation est de 5 étoiles. Mais si vous modifiez la durée des performances dans les propriétés d’évaluation pour la définir à un mois, le niveau de confiance diminue, car les données de performances du disque et du réseau pour mois dernier ne sont pas disponibles. Si vous souhaitez prendre en compte les données de performances du dernier mois, il est recommandé de conserver le paramètre des statistiques vCenter Server au niveau 3 pendant un mois avant d’exécuter la découverte. 
- Plusieurs machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles ont été mises hors tension pendant un certain temps, vCenter Server ne disposera pas des données de performances pour cette période. 
- Quelques machines virtuelles ont été créées pendant la période de calcul de l’évaluation. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques machines virtuelles dans l’environnement ne remonte qu’à une semaine. Dans ce cas, l’historique des performances des nouvelles machines virtuelles ne sera pas disponible pour toute la durée définie.

> [!NOTE]
> Si le niveau de confiance d’une évaluation est inférieur à 4 étoiles, nous vous recommandons de faire passer les paramètres de statistiques vCenter Server au niveau 3, de patienter pendant toute la période que vous souhaitez prendre en compte pour l’évaluation (1 jour/1 semaine/1 mois), puis de procéder à la découverte et à l’évaluation. Si l’exemple précédent ne peut pas être effectué, le dimensionnement basé sur les performances est susceptible de manquer de fiabilité et il est recommandé de basculer vers le *dimensionnement Localement* en changeant les propriétés de l’évaluation.
 
## <a name="next-steps"></a>étapes suivantes

- [Découvrez comment détecter et évaluer](how-to-scale-assessment.md) un environnement VMware de grande taille.
- Découvrez comment créer des groupes d’évaluation à fiabilité élevée à l’aide d’un [mappage de dépendances des machines](how-to-create-group-machine-dependencies.md).
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
