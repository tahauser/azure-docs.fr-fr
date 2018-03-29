---
title: Créer un runtime d’intégration autohébergé dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer un runtime d’intégration autohébergé dans Azure Data Factory, permettant aux fabriques de données d’accéder aux magasins de données dans un réseau privé.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b18fa2879056600cfc9c6a85afc599e2695af711
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Guide pratique pour créer et configurer le runtime d’intégration autohébergé
Le runtime d’intégration (IR) représente l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Pour plus d’informations sur le runtime d’intégration (IR), consultez [Vue d’ensemble du runtime d’intégration](concepts-integration-runtime.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Documentation de Data Factory version 1](v1/data-factory-introduction.md).

Un runtime d’intégration autohébergé est capable d’exécuter des activités de copie entre des magasins de données cloud et un magasin de données dans un réseau privé et de répartir des activités de transformation par rapport à des ressources de calcul dans un site local ou un réseau virtuel Azure. Installez les éléments nécessaires au runtime d’intégration autohébergé sur un ordinateur local ou sur une machine virtuelle à l’intérieur d’un réseau privé.  

Ce document présente la façon dont vous pouvez créer et configurer le runtime d’intégration autohébergé.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Procédure générale pour installer un runtime d’intégration autohébergé
1.  Créez un runtime d’intégration autohébergé. Voici un exemple PowerShell :

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Téléchargez et installez un runtime d’intégration autohébergé (sur l’ordinateur local).
3.  Récupérez la clé d’authentification et inscrivez le runtime d’intégration autohébergé à l’aide de la clé. Voici un exemple PowerShell :

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Flux de commandes et flux de données
Lorsque vous déplacez les données entre des machines locales et cloud, l’activité utilise un runtime d’intégration autohébergé pour transférer les données à partir de la source de données locale vers le cloud et vice versa.

Voici un flux de données global et un résumé des étapes pour la copie à l’aide d’un runtime d’intégration autohébergé :

![Vue d’ensemble globale](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Le développeur des données crée un runtime d’intégration autohébergé dans une fabrique de données Azure à l’aide d’une applet de commande PowerShell. Actuellement, le portail Azure ne prend pas en charge cette fonctionnalité.
2. Le développeur des données crée un service lié pour un magasin de données sur site en spécifiant l’instance de runtime d’intégration autohébergé qu’il doit utiliser pour se connecter à des magasins de données. Dans le cadre de la configuration du service lié, le développeur des données utilise le Gestionnaire d’informations d’identification (non pris en charge actuellement) pour spécifier les types d’authentification et les informations d’identification. La boîte de dialogue du Gestionnaire d’informations d’identification communique avec le magasin de données pour tester la connexion et le runtime d’intégration autohébergé pour enregistrer les informations d’identification.
4.  Le nœud du runtime d’intégration autohébergé chiffre les informations d’identification à l’aide de l’interface DPAPI (Windows Data Protection Application Programming Interface) et l’enregistre localement. Si plusieurs nœuds sont définis pour une haute disponibilité, les informations d’identification sont synchronisées sur les autres nœuds. Chaque nœud chiffre ces informations avec DPAPI et les stocke localement. La synchronisation des informations d’identification est une opération transparente pour le développeur des données, et elle est gérée par le runtime d’intégration autohébergé.    
5.  Le service Data Factory communique avec le runtime d’intégration autohébergé pour la planification et la gestion des tâches via un **canal de contrôle** qui utilise une file d’attente Azure Service Bus partagée. Lorsqu’une tâche de l’activité doit être lancée, Data Factory place en file d’attente la requête ainsi que les informations d’identification (au cas où les informations d’identification ne sont pas déjà stockées sur le runtime d’intégration autohébergé). Le runtime d’intégration autohébergé lance la tâche après interrogation de la file d’attente.
6.  Le runtime d’intégration autohébergé copie les données d’un magasin sur site vers un stockage cloud, ou vice versa selon la configuration de l'activité de copie dans le pipeline de données. Pour cette étape, le runtime d’intégration autohébergé communique directement avec le service de stockage basé sur le cloud comme Stockage Blob Azure via un canal sécurisé (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Considérations relatives à l’utilisation du runtime d’intégration autohébergé

- Un même runtime d’intégration autohébergé peut servir pour plusieurs sources de données sur site. Toutefois, **un runtime d’intégration autohébergé n’est lié qu’à une seule instance d’Azure Data Factory** et ne peut pas être partagé avec une autre instance d’Azure Data Factory.
- Vous ne pouvez installer qu’**une seule instance du runtime d’intégration autohébergé** sur un même ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer un runtime d’intégration autohébergé sur deux ordinateurs locaux. En d’autres termes, un runtime d’intégration autohébergé est lié à une fabrique de données spécifique
- Le **runtime d’intégration autohébergé n’a pas besoin d’être sur la même machine que la source de données**. Toutefois, le fait d’avoir un runtime d’intégration autohébergé plus proche de la source de données réduit le temps de connexion du runtime d’intégration autohébergé à la source de données. Nous vous recommandons d’installer le runtime d’intégration autohébergé sur un ordinateur différent de celui qui héberge la source de données locale. Lorsque le runtime d’intégration autohébergé et la source de données se trouvent sur des machines différentes, le runtime d’intégration autohébergé ne demande pas de ressources de la source de données.
- Vous pouvez avoir **plusieurs runtimes d’intégration autohébergés sur différents ordinateurs connectés à la même source de données locale**. Par exemple, vous pouvez avoir deux runtimes d’intégration autohébergés desservant deux fabriques de données, alors que la même source de données locale est enregistrée auprès des deux fabriques de données.
- Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez un **runtime d’intégration autohébergé distinct pour Azure Data Factory** sur un autre ordinateur.
- Un runtime d’intégration autohébergé doit être utilisé pour prendre en charge l’intégration des données au sein du réseau virtuel Azure.
- Considérez votre source de données comme une source de données locale (derrière un pare-feu), même lorsque vous utilisez **ExpressRoute**. Utilisez le runtime d’intégration autohébergé pour établir la connectivité entre le service et la source de données.
- Vous devez utiliser le runtime d’intégration autohébergé même si le magasin de données se trouve dans le cloud sur une **machine virtuelle Azure IaaS**.
- Les tâches risquent d’échouer dans un runtime d’intégration auto-hébergé installé sur un serveur Windows sur lequel le chiffrement compatible FIPS est activé. Pour contourner ce problème, désactivez le chiffrement compatible FIPS sur le serveur. Pour désactiver le chiffrement compatible FIPS, modifiez la valeur de registre suivante de 1 (activé) à 0 (désactivé) : `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Prérequis


- Les versions de **système d’exploitation** prises en charge sont Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. L’installation du runtime d’intégration autohébergé sur un **contrôleur de domaine n’est pas prise en charge**.
- **.NET Framework 4.6.1 ou version ultérieure** est requis. Si vous installez un runtime d’intégration autohébergé sur un ordinateur Windows 7, installez .NET Framework 4.6.1 ou une version ultérieure. Consultez [Configuration système requise pour .NET Framework](/dotnet/framework/get-started/system-requirements) pour plus d’informations.
- La **configuration** recommandée pour l’ordinateur du runtime d’intégration autohébergé est la suivante : au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go d’espace disque.
- Si l’ordinateur hôte est en veille prolongée, le runtime d’intégration autohébergé ne répond pas à la demande de données. Vous devez donc configurer un plan de gestion de l’alimentation approprié sur l’ordinateur avant d’installer le runtime d’intégration autohébergé. L’installation du runtime d’intégration autohébergé ouvre un message si l’ordinateur est configuré pour la mise en veille prolongée.
- Vous devez être administrateur sur la machine pour installer et configurer le runtime d’intégration autohébergé avec succès.
- Étant donné que l’activité de copie s’exécute selon une fréquence spécifique, l’utilisation des ressources (processeur, mémoire) sur l’ordinateur suit également le même modèle avec des pics et des baisses d’inactivité. L'utilisation des ressources dépend également en grande partie de la quantité de données déplacées. Lorsque plusieurs travaux sont en cours, vous constaterez une augmentation des ressources utilisées pendant les heures de pointe.

## <a name="installation-best-practices"></a>Bonnes pratiques d’installation
Le runtime d’intégration autohébergé peut être installé en téléchargeant un package d’installation MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Pour des instructions pas à pas, consultez l’article [Déplacement de données entre des sources locales et le cloud](tutorial-hybrid-copy-powershell.md).

- Définissez un plan d'alimentation sur l'ordinateur hôte du runtime d’intégration autohébergé afin d’empêcher la mise en veille prolongée. Si l’ordinateur hôte est en veille prolongée, le runtime d’intégration autohébergé se déconnecte.
- Sauvegardez régulièrement les informations d’identification associées au runtime d’intégration autohébergé.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Installer et enregistrer le runtime d’intégration autohébergé à partir du centre de téléchargement

1. Accédez à la [page de téléchargement du runtime d’intégration Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Cliquez sur **Télécharger**, sélectionnez la version appropriée (**32 bits** ou **64 bits**), puis cliquez sur **Suivant**.
3. Exécutez le **MSI** directement ou enregistrez-le sur votre disque dur avant de l’exécuter.
4. Dans la page **Bienvenue**, sélectionnez une **langue** et cliquez sur **Suivant**.
5. **Acceptez** le Contrat de Licence Utilisateur Final et cliquez sur **Suivant**.
6. Sélectionnez **dossier** pour installer le runtime d’intégration autohébergé, puis cliquez sur **Suivant**.
7. Dans la page **Prêt pour l’installation**, cliquez sur **Installer**.
8. Cliquez sur **Terminer** pour terminer l’installation.
9. Obtenez la clé d’authentification à l’aide d’Azure PowerShell. Exemple PowerShell pour récupérer la clé d’authentification :

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Sur la page **Inscrire le Runtime d'intégration (autohébergé)** du Gestionnaire de configuration du Runtime d'intégration Microsoft en cours d’exécution sur votre ordinateur, procédez comme suit :
    1. Collez la **clé d’authentification** dans la zone de texte.
    2. Éventuellement, cliquez sur **Afficher la clé d’authentification** pour afficher le texte de la clé.
    3. Cliquez sur **S'inscrire**.


## <a name="high-availability-and-scalability"></a>Haute disponibilité et extensibilité
Un runtime d’intégration auto-hébergé peut être associé à plusieurs machines locales. Ces ordinateurs sont appelés nœuds. Vous pouvez associer jusqu’à quatre nœuds à un runtime d’intégration autohébergé. Avoir plusieurs nœuds (ordinateurs locaux avec une passerelle installée) procure les avantages suivants à une passerelle logique :
1. La haute disponibilité du runtime d’intégration autohébergé supprime le point de défaillance dans votre solution Big Data ou dans l’intégration de vos données cloud avec Azure Data Factory, ce qui garantit la continuité des activités sur un maximum de 4 nœuds.
2. Les performances et le débit lors du déplacement des données entre les magasins de données locaux et dans le cloud ont été améliorés. Plus d’informations sur les [comparaisons des performances](copy-activity-performance.md).

Vous pouvez associer plusieurs nœuds en installant simplement le logiciel du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=39717) et en l’inscrivant à l’aide des clés d’authentification obtenues via l’applet de commande New-AzureRmDataFactoryV2IntegrationRuntimeKey, comme décrit dans le [didacticiel](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Vous n’avez pas besoin de créer un runtime d’intégration autohébergé pour associer chaque nœud. Vous pouvez installer le runtime d’intégration auto-hébergé sur une autre machine et l’inscrire à l’aide de la même clé d’authentification. 

> [!NOTE]
> Avant d’ajouter un autre nœud de **haute disponibilité et extensibilité**, vérifiez que l’option **Accès à distance à partir de l'intranet** est **activée** sur le premier nœud (Gestionnaire de configuration Microsoft Integration Runtime -> Paramètres -> Accès à distance à partir de l'intranet). 

### <a name="tlsssl-certificate-requirements"></a>Configuration requise des certificats TLS/SSL
Voici la configuration requise pour le certificat TLS/SSL utilisé pour sécuriser les communications entre les nœuds de runtime d’intégration :

- Le certificat doit être un certificat X509 v3 approuvé publiquement. Nous vous recommandons d’utiliser des certificats émis par une autorité de certification (tierce) publique.
- Chaque nœud de runtime d’intégration doit approuver ce certificat.
- Les certificats utilisant des caractères génériques sont pris en charge. Si votre nom de domaine complet est **node1.domain.contoso.com**, vous pouvez utiliser ***.domain.contoso.com** comme nom du sujet du certificat.
- Les certificats SAN ne sont pas recommandés, car seul le dernier élément des Autres noms de l’objet sera utilisé et tous les autres seront ignorés en raison d’une limitation actuelle. Par exemple, si vous avez un certificat SAN dont les noms SAN sont **node1.domain.contoso.com** et **node2.domain.contoso.com**, vous ne pouvez utiliser ce certificat que sur l’ordinateur dont le FQDN est **node2.domain.contoso.com**.
- Prise en charge de toutes les tailles de clé prises en charge par Windows Server 2012 R2 pour les certificats SSL.
- Les certificat utilisant des clés CNG ne sont pas pris en charge. Non-prise en charge des certificats qui utilisent des clés CNG.

## <a name="system-tray-icons-notifications"></a>Icônes de la barre d’état système/notifications
Si vous déplacez le curseur sur les icônes/messages de notification de la barre d’état système, vous obtenez des informations supplémentaires sur l’état du runtime d’intégration autohébergé.

![Notifications de la barre d’état système](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Ports et pare-feu
Vous devez porter votre attention sur deux pare-feu : le **pare-feu d’entreprise**, exécuté sur le routeur central de l’organisation et le **Pare-feu Windows**, configuré en tant que démon sur l’ordinateur local sur lequel le runtime d’intégration autohébergé est installé.

![Pare-feu](media\create-self-hosted-integration-runtime\firewall.png)

Au niveau du **pare-feu d’entreprise**, vous devez configurer les domaines et ports de sortie suivants :

Noms de domaine | Ports | Description
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Utilisé pour la communication avec le serveur principal du service Déplacement des données
*.core.windows.net | 443 | Utilisé pour une copie intermédiaire à l’aide d’objets Blob Azure (si configuré)
*.frontend.clouddatahub.net | 443 | Utilisé pour la communication avec le serveur principal du service Déplacement des données

Au niveau du **pare-feu Windows** (niveau de la machine), ces ports de sortie sont normalement activés. Sinon, vous pouvez configurer en conséquence les domaines et les ports sur l’ordinateur du runtime d’intégration autohébergé.

> [!NOTE]
> Selon votre source/vos récepteurs, vous devrez peut-être ajouter des domaines et des ports de sortie supplémentaires à la liste verte de votre pare-feu d’entreprise/Windows.
>
> Pour certaines bases de données cloud (par exemple : Azure SQL Database, Azure Data Lake, etc.), vous devrez peut-être ajouter l’adresse IP de l’ordinateur du runtime d’intégration autohébergé à la liste verte dans la configuration du pare-feu.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copier des données d’une source vers un récepteur
Assurez-vous que les règles de pare-feu sont correctement activées sur le pare-feu d’entreprise, sur le pare-feu Windows de l’ordinateur du runtime d’intégration autohébergé, ainsi que sur le magasin de données lui-même. Activer ces règles permet au runtime d’intégration autohébergé de se connecter correctement à la source et au récepteur. Activez les règles pour chaque magasin de données impliqué dans l’opération de copie.

Par exemple, pour copier à partir d’une **banque de données locale vers un récepteur Azure SQL Database ou un récepteur Azure SQL Data Warehouse**, effectuez les opérations suivantes :

- Autorisez le trafic **TCP** sortant sur le port **1433** pour le pare-feu Windows et le pare-feu d’entreprise.
- Configurez les paramètres de pare-feu du serveur SQL Azure pour ajouter l’adresse IP de l’ordinateur du runtime d’intégration autohébergé à la liste d’adresses IP autorisées.

> [!NOTE]
> Si votre pare-feu n’autorise pas le port de sortie 1433, le runtime d’intégration autohébergé ne peut pas accéder directement à Azure SQL. Dans ce cas, vous pouvez utiliser la [copie intermédiaire](copy-activity-performance.md) sur SQL Azure Database/SQL Azure DW. Dans ce scénario, vous auriez uniquement besoin du protocole HTTPS (port 443) pour le déplacement des données.


## <a name="proxy-server-considerations"></a>Considérations relatives aux serveurs proxy
Si votre environnement de réseau d’entreprise utilise un serveur proxy pour accéder à Internet, configurez le runtime d’intégration autohébergé pour utiliser les bons paramètres de proxy. Vous pouvez définir le proxy lors de la phase initiale de l’enregistrement.

![Spécifier le proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Le runtime d’intégration autohébergé utilise le serveur proxy pour se connecter au service cloud. Cliquez sur le lien Modifier pendant l’installation initiale. La boîte de dialogue Paramètre proxy s’affiche.

![Définir le proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Il existe trois options de configuration :

- **Ne pas utiliser de proxy**: le runtime d’intégration autohébergé n’utilise pas explicitement de proxy pour se connecter aux services cloud.
- **Utiliser le proxy système** : le runtime d’intégration autohébergé utilise le paramètre de proxy configuré dans diahost.exe.config et diawp.exe.config. Si aucun proxy n’est configuré dans diahost.exe.config et diawp.exe.config, le runtime d’intégration autohébergé se connecte au service cloud directement sans passer par le proxy.
- **Utiliser un proxy personnalisé** : configurez les paramètres du proxy HTTP à utiliser pour le runtime d’intégration autohébergé, au lieu d’utiliser les configurations dans diahost.exe.config et diawp.exe.config. L’adresse et le port sont requis. Le nom d’utilisateur et le mot de passe sont facultatifs selon le paramètre d’authentification de votre proxy. Tous les paramètres sont chiffrés avec Windows DPAPI sur le runtime d’intégration autohébergé et stockés localement sur l’ordinateur.

Le service hôte du runtime d’intégration autohébergé redémarre automatiquement après avoir enregistré les paramètres de proxy mis à jour.

Une fois le runtime d’intégration autohébergé enregistré avec succès, si vous souhaitez afficher ou mettre à jour les paramètres de proxy, utilisez le Gestionnaire de configuration du Runtime d'intégration.

1.  Lancez le **Gestionnaire de configuration du Runtime d'intégration Microsoft**.
2.  Basculez vers l’onglet **Paramètres** .
3.  Cliquez sur le lien **Modifier** dans la section **Serveur proxy HTTP** pour lancer la boîte de dialogue **Définir le proxy HTTP**.
4.  Après avoir cliqué sur le bouton **Suivant**, vous verrez une boîte de dialogue d’avertissement demandant l’autorisation d’enregistrer les paramètres de proxy et de redémarrer le service hôte du runtime d’intégration.

Vous pouvez afficher et mettre à jour le proxy HTTP à l’aide de l’outil Gestionnaire de Configuration.

![Afficher le proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Si vous configurez un serveur proxy avec l’authentification NTLM, le service hôte du runtime d’intégration s’exécute sous le compte du domaine. Si vous modifiez le mot de passe du compte du domaine ultérieurement, veillez à mettre à jour les paramètres de configuration pour le service et à redémarrer ce dernier en conséquence. En raison de cette exigence, nous vous conseillons d’utiliser un compte de domaine dédié qui ne nécessite pas de mettre à jour le mot de passe fréquemment pour accéder au serveur proxy.

### <a name="configure-proxy-server-settings"></a>Configurer les paramètres du serveur proxy

Si vous sélectionnez le paramètre **Utiliser le proxy système** pour le proxy HTTP, le runtime d’intégration autohébergé utilise le paramètre du proxy dans diahost.exe.config et diawp.exe.config. Si aucun proxy n’est spécifié dans diahost.exe.config et diawp.exe.config, le runtime d’intégration autohébergé se connecte au service cloud directement sans passer par le proxy. La procédure suivante fournit des instructions pour mettre à jour le fichier de configuration diahost.exe.config.

1. Dans l’Explorateur de fichiers, effectuez une copie de sauvegarde de C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config pour sauvegarder le fichier d’origine.
2. Lancez Notepad.exe en tant qu’administrateur, puis ouvrez le fichier texte C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. La balise par défaut pour system.net apparaît dans le code suivant :

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Vous pouvez ensuite ajouter les détails du serveur proxy comme illustré dans l’exemple suivant :

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Vous pouvez ajouter des propriétés supplémentaires à l’intérieur de la balise de proxy pour spécifier les paramètres requis comme scriptLocation. Reportez-vous à la page [proxy, élément (paramètres réseau)](https://msdn.microsoft.com/library/sa91de1e.aspx) pour connaître la syntaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Enregistrez le fichier de configuration à l’emplacement d’origine, puis redémarrez le service hôte du runtime d’intégration autohébergé, qui relève les modifications. Pour redémarrer le service, utilisez l’applet de services du panneau de configuration, ou allez dans le **Gestionnaire de configuration du Runtime d'intégration**, cliquez sur le bouton **Arrêter le service**, puis sur **Démarrer le service**. Si le service ne démarre pas, il est probable qu’une syntaxe de balise XML incorrecte ait été ajoutée dans le fichier de configuration d’application que vous avez modifié.

> [!IMPORTANT]
> N’oubliez pas de mettre à jour diahost.exe.config et diawp.exe.config.

Outre ces points, vous devez également vous assurer que Microsoft Azure figure dans la liste d’autorisation de votre entreprise. Vous pouvez télécharger la liste des adresses IP Microsoft Azure valides à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Symptômes possibles des erreurs liées au pare-feu et au serveur proxy
Si vous rencontrez l’une des erreurs suivantes, cela signifie que vous avez probablement mal configuré le serveur proxy ou le pare-feu, et que le runtime d'intégration autohébergé ne peut pas se connecter à Data Factory pour s’authentifier. Reportez-vous à la section précédente pour vous assurer que votre pare-feu et votre serveur proxy sont correctement configurés.

1.  Lorsque vous tentez d’inscrire le runtime d’intégration autohébergé, vous recevez le message d’erreur suivant : Échec d'inscription de ce nœud Runtime d'intégration ! Vérifiez que la clé d’authentification est valide et que le service hôte d’intégration est en cours d’exécution sur cet ordinateur. "
2.  Lorsque vous ouvrez le Gestionnaire de configuration du Runtime d'intégration, l’état indiqué est « **Déconnecté** » ou « **En cours de connexion** ». Lorsque vous affichez les journaux des événements Windows, sous « Observateur d’événements » > « Journaux des applications et services » > « Runtime d’intégration Microsoft », des messages d’erreur tels que le suivant s’affichent :

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Activer l'accès à distance à partir de l'intranet  
Si vous utilisez l’application **PowerShell** ou **Gestionnaire d'informations d'identification** pour chiffrer les informations d’identification à partir d’une machine (dans le réseau) autre que celle où est installé le runtime d’intégration auto-hébergé, l’option **Accès à distance à partir de l’Intranet** doit être activée. Si vous exécutez l’application **PowerShell** ou **Gestionnaire d’informations d’identification** pour chiffrer les informations d’identification sur la machine où est installé le runtime d’intégration auto-hébergé, il n’est pas nécessaire d’activer l’option **Accès à distance à partir de l’Intranet**.

L’accès à distance à partir de l’Intranet doit être **activé** avant d’ajouter un autre nœud de **haute disponibilité et extensibilité**.  

Pendant l’installation du runtime d’intégration auto-hébergé (v 3.3.xxxx.x et versions ultérieures), par défaut, l’installation du runtime d’intégration auto-hébergé désactive l’option  **Accès à distance à partir de l’Intranet** sur la machine du runtime d’intégration auto-hébergé.

Si vous utilisez un pare-feu tiers, vous pouvez ouvrir manuellement le port 8060 (ou le port configuré par l’utilisateur). Si vous rencontrez des problèmes de pare-feu lors de l’installation du runtime intégration autohébergé, vous pouvez essayer d’utiliser la commande suivante pour installer le runtime intégration autohébergé sans configurer le pare-feu.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> L’**application Gestionnaire d’informations d’identification** n’est pas encore disponible pour le chiffrement des informations d’identification dans ADFv2. Cette prise en charge sera ajoutée ultérieurement.  

Si vous préférez ne pas ouvrir le port 8060 sur l’ordinateur du runtime intégration autohébergé, utilisez d’autres mécanismes que l’application **Définition des informations d’identification-- pour configurer les informations d’identification de la banque de données. Vous pouvez par exemple utiliser l’applet de commande PowerShell AzureRmDataFactoryV2LinkedServiceEncryptCredential. Consultez la section Configuration des informations d’identification et de la sécurité pour savoir comment configurer les informations d’identification de la banque de données.


## <a name="next-steps"></a>Étapes suivantes
Consultez le didacticiel suivant pour obtenir des instructions pas à pas : [didacticiel : copier des données locales dans le cloud](tutorial-hybrid-copy-powershell.md).
