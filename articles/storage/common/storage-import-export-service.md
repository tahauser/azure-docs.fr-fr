---
title: "Utilisation du service Azure Import/Export pour transférer des données vers et à partir de Stockage Azure | Microsoft Docs"
description: "Découvrez comment créer des tâches d’importation et d’exportation dans le portail Azure pour transférer des données vers et à partir de Stockage Azure."
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/28/2018
ms.author: muralikk
ms.openlocfilehash: e9fce2530bc4e654304b946cea1715ac8e2ce6fa
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Transférer des données vers Stockage Azure à l’aide du service Microsoft Azure Import/Export
Cet article fournit des instructions pas à pas sur l’utilisation du service Azure Import/Export pour transférer en toute sécurité des volumes importants de données vers Stockage Blob Azure et Azure Files en expédiant des lecteurs de disques vers un centre de données Azure. Vous pouvez également utiliser ce service pour transférer des données de Stockage Blob Azure vers des lecteurs de disques durs et les expédier vers vos sites locaux. Les données d’un seul lecteur de disque SATA interne peuvent être importées dans Stockage Blob Azure ou Azure Files. 

> [!IMPORTANT] 
> Ce service accepte uniquement les HDD ou SSD SATA internes. Aucun autre périphérique n’est pris en charge. N’envoyez pas de périphériques NAS ni HDD externes, entre autres, car ils seront renvoyés si possible ou rejetés.
>
>

Suivez les étapes ci-dessous si les données sur le disque doivent être importées dans Stockage Azure.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Étape 1 : Préparer les lecteurs à l’aide de l’outil WAImportExport et générer les fichiers journaux.

1.  Identifiez les données à importer dans Stockage Azure. Vous pouvez importer des répertoires et des fichiers autonomes sur un serveur local ou sur un partage réseau.
2.  En fonction de la taille totale des données, procurez-vous le nombre requis de SSD de 2,5 pouces ou HDD SATA II ou III de 2,5 ou 3,5 pouces.
3.  Attachez les disques durs directement à l’aide de SATA ou avec des adaptateurs USB externes à un ordinateur Windows.
1.  Créez un seul volume NTFS sur chaque disque dur et affectez-lui une lettre de lecteur. Pas de points de montage.
2.  Pour activer le chiffrement sur l’ordinateur Windows, activez le chiffrement BitLocker sur le volume NTFS. Suivez les instructions à l’adresse https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx.
3.  Copiez entièrement les données vers ces volumes NTFS uniques chiffrés sur les disques à l’aide des fonctions Copier/Coller ou Glisser-déplacer, de Robocopy ou de n’importe quel outil de ce type.
7.  Téléchargez WAImportExport V1 à partir de https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Effectuez la décompression dans le dossier par défaut waimportexportv1. Par exemple, C:\WaImportExportV1  
9.  Sélectionnez l’option Exécuter en tant qu’administrateur et ouvrez PowerShell ou une ligne de commande, puis remplacez le répertoire par le dossier décompressé. Par exemple, cd C:\WaImportExportV1
10. Copiez la ligne de commande suivante dans un éditeur de texte et modifiez-la pour en créer une autre :

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ 
    ```
    
    Les options de ligne de commande sont décrites dans le tableau suivant :

    |Option  |Description  |
    |---------|---------|
    |/j:     |Nom du fichier journal, avec l’extension .jrn. Un fichier journal est généré par lecteur. Il est recommandé d’utiliser le numéro de série du disque comme nom de fichier journal.         |
    |/sk:     |Clé de compte de stockage Azure.         |
    |/t:     |Lettre de lecteur du disque à expédier. Exemple : lecteur `D`.         |
    |/bk:     |Clé BitLocker du lecteur.         |
    |/srcdir:     |Lettre de lecteur du disque à expédier suivie de `:\`. Par exemple : `D:\`.         |
    |/dstdir:     |Nom du conteneur de destination dans Stockage Azure.         |

1. Répétez l’étape 10 pour chaque disque qui doit être expédié.
2. Un fichier journal avec le nom fourni avec le paramètre /j: est créé pour chaque exécution de la ligne de commande.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Étape 2 : Créer une tâche d’importation dans le portail Azure.

1. Ouvrez une session sur https://portal.azure.com/ et sous Autres services-> STOCKAGE -> Tâches d’importation/exportation, cliquez sur **Créer une tâche d’importation/exportation**.

2. Dans la section Fonctions de base, sélectionnez « Importer dans Azure », entrez une chaîne pour le nom de la tâche, sélectionnez un abonnement, entrez ou sélectionnez un groupe de ressources. Indiquez un nom décrivant le travail d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom choisi servira à suivre les tâches en cours et terminées.

3. Dans la section Détails de la tâche, chargez les fichiers journaux du lecteur que vous avez obtenus lors de l’étape de préparation du lecteur. Si la version 1 de waimportexport.exe a été utilisée, vous devrez charger un fichier par lecteur préparé. Sélectionnez le compte de stockage dans lequel les données sont importées dans la section « Destination de l’importation » du compte de stockage. L’emplacement de remise est automatiquement rempli en fonction de la région du compte de stockage sélectionné.
   
   ![Créer une tâche d'importation - Étape 3](./media/storage-import-export-service/import-job-03.png)
4. Dans la section Informations de retour, sélectionnez le transporteur dans la liste déroulante et entrez un numéro de compte de transporteur valide créé avec ce transporteur. Microsoft utilise ce compte pour renvoyer les lecteurs une fois la tâche d’importation terminée. Indiquez le nom d’une personne, un numéro de téléphone, un e-mail, le nom de la rue, la ville, le code postal, l’état/la province et le payx/la région et assurez-vous que ces informations sont complètes et valides.
   
5. Dans la section Résumé, l’adresse de livraison du centre de données Azure est fournie afin d’y envoyer vos disques. Assurez-vous que le nom du travail et l’adresse complète sont mentionnés sur l’étiquette d’expédition. 

6. Sur la page Résumé, cliquez sur OK pour terminer la création du travail d’importation.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Étape 3 : Expédier les lecteurs à l’adresse de livraison du centre de données Azure fournie à l’étape 2.
Il est possible d’utiliser FedEx, UPS ou DHL pour envoyer le colis au centre de données Azure.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Étape 4 : Mettre à jour la tâche créée à l’étape 2 avec le numéro de suivi d’expédition.
Après l’envoi des disques, revenez dans la page **Importer/Exporter** du portail Azure pour mettre à jour le numéro de suivi en effectuant les opérations suivantes : a) Accédez à la tâche d’importation et cliquez dessus. b) Cliquez sur **Mettez à jour l’état de la tâche et les informations de suivi une fois les lecteurs expédiés**. c) Cochez la case « Marquer comme expédié ». d) Indiquez le transporteur et le numéro de suivi.
Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera. Vous pouvez suivre l’état d’avancement de la tâche sur le tableau de bord du portail. Pour connaître la signification de chaque état de travail dans la section précédente, consultez [Affichage de l’état de votre travail](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>À quel moment dois-je utiliser le service Azure Import/Export ?
Envisagez d’utiliser le service Azure Import/Export lorsque le chargement ou le téléchargement de données sur le réseau est trop lent ou lorsque l’obtention d’une bande passante réseau supplémentaire est coûteuse.

Vous pouvez utiliser ce service dans des scénarios tels que :

* Migration de données vers le cloud : déplacez de grandes quantités de données vers Azure rapidement et à moindre coût.
* Distribution de contenu : envoyez rapidement des données aux sites de vos clients.
* Sauvegarde : sauvegardez vos données locales dans Stockage Azure.
* Récupération des données : récupérez les grandes quantités de données stockées pour les transférer vers votre site local.

## <a name="prerequisites"></a>Prérequis
Cette section décrit les prérequis de ce service. Lisez-les soigneusement avant d’expédier vos lecteurs.

### <a name="storage-account"></a>Compte de stockage
Vous devez disposer d’un abonnement Azure et d’un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Azure Import/Export prend uniquement en charge les comptes classiques, de stockage Blob et les comptes de stockage v1 à usage général. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une même tâche d’importation/exportation ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Le service Azure Import/Export ne prend pas en charge les comptes de stockage où la fonctionnalité [Points de terminaison de service sur réseaux virtuels](../../virtual-network/virtual-network-service-endpoints-overview.md) a été activée. 
> 
> 

### <a name="data-types"></a>Types de données
Vous pouvez utiliser le service Azure Import/Export pour copier des données dans des objets blob de **blocs**, des objets blob de **pages** ou des **fichiers**. En revanche, vous ne pouvez qu’exporter les objets blob de **bloc**, de **page** ou **d’ajout** depuis le stockage Azure. Le service prend en charge uniquement l’importation d’Azure Files vers Stockage Azure. L’exportation d’Azure Files n’est pas prise en charge actuellement.

### <a name="job"></a>Travail
Pour démarrer le processus d’importation ou d’exportation vers ou à partir d’un stockage, commencez par créer une tâche. Il peut s'agir d'une tâche d'importation ou d'une tâche d'exportation :

* Créez une tâche d’importation quand vous souhaitez transférer des données locales vers votre compte de stockage Azure.
* Créez une tâche d’exportation quand vous souhaitez transférer des données stockées dans votre compte de stockage vers des disques durs envoyés à Microsoft. Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure.

* Dans le cas d’un travail d’importation, vous expédiez des disques durs contenant vos données.
* Dans le cas d’un travail d’exportation, vous expédiez des disques durs vides.
* Vous pouvez expédier jusqu’à 10 disques durs par travail.

Vous pouvez créer une tâche d’importation ou d’exportation à l’aide du portail Azure ou de [l’API REST Import/Export du stockage Azure](/rest/api/storageimportexport).

> [!Note]
> Les API RDFE ne seront plus prises en charge à partir du 28 février 2018. Pour continuer à utiliser le service, migrez vers les [API REST Import/Export ARM](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>Outil WAImportExport
Pour créer une tâche **d’importation**, la première étape consiste à préparer les disques qui seront utilisés pour l’importation. Pour ce faire, vous devez les connecter à un serveur local et exécuter l’outil WAImportExport sur le serveur local. Cet outil facilite la copie de vos données sur le disque, leur chiffrement avec BitLocker et la génération des fichiers journaux du disque.

Ces fichiers journaux stockent des informations sur votre travail et sur le disque, par exemple le numéro de série du disque et le nom du compte de stockage. Ce fichier journal n’est pas stocké sur le disque. Il est utilisé lors de la création de travaux d’importation. Une procédure détaillée de la création de ces travaux est fournie plus loin dans cet article.

L’outil WAImportExport est compatible uniquement avec le système d’exploitation Windows 64 bits. Pour connaître les versions de système d’exploitation prises en charge, consultez la section [Système d’exploitation](#operating-system) .

Téléchargez la dernière version de l’[outil WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Pour plus d’informations sur l’utilisation de l’outil WAImportExport, consultez la page [Using the WAImportExport Tool](storage-import-export-tool-how-to.md) (Utilisation de l’outil WAImportExport).

>[!NOTE]
>**Version précédente :** vous pouvez [télécharger la version v1 de l’outil WAImportExpot](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) et consulter le [guide d’utilisation de WAImportExport v1](storage-import-export-tool-how-to-v1.md). La version v1 de l’outil WAImportExpot ne prend pas en charge **la préparation des disques lorsque des données sont préalablement écrites sur ces derniers**. Si la seule clé disponible est une clé SAP, vous devrez utiliser l’outil WAImportExpot v1.

>

### <a name="hard-disk-drives"></a>Disques durs
Le service Import/Export ne prend en charge que les disques durs internes SSD de 2,5 pouces ou SATA II ou III de 2,5 ou 3,5 pouces. Une même tâche d’importation/exportation peut avoir un maximum de 10 disques durs/SSD (quelle que soit la taille de chaque disque). Il est possible de répartir un grand nombre de disques entre plusieurs tâches, et il n’existe aucune limite quant au nombre de tâches pouvant être créées. 

Dans le cas des tâches d’importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS.

> [!IMPORTANT]
> Les disques durs externes équipés d’un adaptateur USB intégré ne sont pas pris en charge par ce service. En outre, le disque se trouvant à l’intérieur du boîtier d’un HDD externe est inutilisable ; n’envoyez pas de HDD externes.
> 
> 

Voici la liste des adaptateurs USB externes utilisés pour copier des données vers des HDD internes. Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (6628 Series) Thermaltake BlacX Hot-Swap SATA External Hard Drive Docking Station (USB 2.0 & eSATA)

### <a name="encryption"></a>Chiffrement
Les données contenues dans le disque dur doivent être chiffrées à l’aide du chiffrement de lecteur BitLocker. Ce chiffrement permet de protéger vos données en transit.

Pour les travaux d’importation, le chiffrement s’effectue de deux manières différentes. La première consiste à spécifier l’option lors de l’utilisation du fichier CSV du jeu de données pendant l’exécution de l’outil WAImportExport pour préparer le disque. La seconde consiste à activer manuellement le chiffrement BitLocker sur le disque et à spécifier la clé de chiffrement dans le fichier CSV du jeu de disques lors de l’exécution de la ligne de commande de l’outil WAImportExport pendant la préparation du disque.

Pour les travaux d’exportation, une fois vos données copiées sur le disque, le service chiffre le disque à l’aide de BitLocker avant de vous le renvoyer. La clé de chiffrement vous est fournie par le biais du Portail Azure.  

### <a name="operating-system"></a>Système d’exploitation
Vous pouvez utiliser l’un des systèmes d’exploitation 64 bits suivants pour préparer le disque dur à l’aide de l’outil WAImportExport avant de l’envoyer à Azure :

Windows 7 Entreprise, Windows 7 Édition intégrale, Windows 8 Professionnel, Windows 8 Entreprise, Windows 8.1 Professionnel, Windows 8.1 Entreprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tous ces systèmes d’exploitation prennent en charge le chiffrement de lecteur BitLocker.

### <a name="locations"></a>Emplacements
Le service Azure Import/Export prend en charge la copie des données vers et depuis tous les comptes de stockage publics Azure. Vous pouvez envoyer les lecteurs de disques durs aux endroits suivants. Si votre compte de stockage se trouve dans un emplacement Azure public non spécifié ici, un autre emplacement d’expédition sera indiqué lorsque vous créerez la tâche à l’aide du portail Azure ou de l’API REST Import/Export.

Emplacements d’expédition pris en charge :

* Est des États-Unis
* États-Unis de l’Ouest
* Est des États-Unis 2
* Ouest des États-Unis 2
* Centre des États-Unis
* Centre-Nord des États-Unis
* États-Unis - partie centrale méridionale
* Centre-Ouest des États-Unis
* Europe du Nord
* Europe de l'Ouest
* Est de l'Asie
* Asie du Sud-Est
* Est de l’Australie
* Sud-est de l’Australie
* Ouest du Japon
* Est du Japon
* Inde centrale
* Inde du Sud
* Inde occidentale
* Centre du Canada
* Est du Canada
* Sud du Brésil
* Centre de la Corée
* Gouvernement américain - Virginie
* US Gov Iowa
* Est des États-Unis – US DoD
* Centre des États-Unis – US DoD
* Chine orientale
* Chine du Nord
* Sud du Royaume-Uni
* Centre de l’Allemagne
* Nord-Est de l’Allemagne

### <a name="shipping"></a>Expédition
**Expédition de disques au centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous recevez l’adresse de l’un des emplacements auquel expédier vos disques. L’adresse d’expédition fournie dépend de l’emplacement du compte de stockage, mais elle n’est pas nécessairement identique.

Il est possible d’utiliser FedEx, UPS ou DHL pour envoyer vos lecteurs à l’adresse d’expédition.

**Expédition de disques depuis le centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous devez indiquer une adresse de retour que Microsoft utilisera pour vous remettre les disques une fois le travail terminé. Veillez à indiquer une adresse d’expéditeur valide pour éviter tout retard de traitement.

Le transporteur doit proposer un système de suivi approprié afin d’assurer la traçabilité du disque. Vous devez indiquer un numéro de compte de transporteur FedEx, UPS ou DHL valide pour permettre à Microsoft de renvoyer les lecteurs. Un numéro de compte FedEx, UPS ou DHL est requis pour les retours de lecteurs en provenance des États-Unis et d’Europe. Un numéro de compte DHL est requis pour les retours de disque à partir d’Asie et d’Australie. Si vous n’en avez pas, vous pouvez créer un compte de transporteur [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et l’Europe) ou [DHL](http://www.dhl.com/) (pour l’Asie et l’Australie). Si vous avez déjà un compte de transporteur, vérifiez qu’il est valide.

Lorsque vous expédiez vos colis, vous devez respecter les [conditions d'utilisation des services Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Notez que le support physique que vous expédiez devra peut-être franchir des frontières internationales. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d’expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d’atteindre Microsoft dans les délais. Par exemple, tout colis qui franchit des frontières internationales (sauf au sein de l’Union européenne) doit être accompagné d’une facture commerciale. Vous pouvez imprimer une copie de la facture commerciale à partir du site web de support du transporteur. Exemples de factures commerciales : [facture commerciale DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) et [facture commerciale FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assurez-vous que Microsoft n’a pas été indiqué comme l’exportateur.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Fonctionnement du service Azure Import/Export
Vous pouvez transférer des données entre votre site local et Stockage Azure à l’aide du service Azure Import/Export en créant des travaux et en expédiant des disques durs à un centre de données Azure. Chaque disque dur expédié est associé à un seul travail. Chaque travail est associé à un seul compte de stockage. Consultez la [section Prérequis](#pre-requisites) pour découvrir les particularités de ce service, comme les types de disque, les localisations, les expéditions et les types de données pris en charge.

Cette section décrit la procédure générale permettant d’importer et d’exporter des tâches. Ensuite, la section [Démarrage rapide](#quick-start) expliquera comment créer une tâche d’importation et d’exportation.

### <a name="inside-an-import-job"></a>Dans un travail d’importation
Globalement, un travail d’importation comprend les opérations suivantes :

* Déterminez les données à importer et le nombre de lecteurs dont vous avez besoin.
* Identifiez l’emplacement des fichiers ou des objets blob de destination de vos données dans Stockage Blob.
* Utilisez l’outil WAImportExport pour copier vos données sur un ou plusieurs disques durs et chiffrez-les à l’aide de BitLocker.
* Créez une tâche d’importation dans votre compte de stockage cible à l’aide du portail Azure ou de l’API REST Import/Export. Si vous utilisez le portail Azure, téléchargez les fichiers journaux des disques.
* Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
* Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
* Mettez à jour le numéro de suivi et envoyez le travail d’importation.
* Les disques sont réceptionnés et traités dans le centre de données Azure.
* Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.
  
    ![Figure 1 : flux d’importation de travail](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dans un travail d’exportation
> [!IMPORTANT]
> Le service prend uniquement en charge l’exportation de blobs Azure et ne prend pas en charge l’exportation de fichiers Azure.
> 
>

Globalement, un travail d’exportation comprend les opérations suivantes :

* Déterminez les données à exporter et le nombre de lecteurs dont vous avez besoin.
* Identifiez les objets blob source ou les chemins d’accès au conteneur de vos données dans Stockage Blob.
* Créez une tâche d’exportation dans votre compte de stockage source à l’aide du portail Azure ou de l’API REST Import/Export.
* Indiquez les objets blob source ou les chemins d’accès au conteneur de vos données dans le travail d’exportation.
* Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
* Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
* Mettez à jour le numéro de suivi et envoyez le travail d’exportation.
* Les disques sont réceptionnés et traités dans le centre de données Azure.
* Les disques sont chiffrés à l’aide de BitLocker et les clés sont disponibles via le portail Azure.  
* Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.
  
    ![Figure 2 : flux d’exportation de travail](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Affichage de l’état de vos tâches et de vos disques
Vous pouvez suivre l’état de vos tâches d’importation ou d’exportation dans le portail Azure. Cliquez sur l’onglet **Import/Export**. La liste de vos tâches s’affiche sur la page.

![Afficher l’état des tâches](./media/storage-import-export-service/jobstate.png)

Selon la phase de traitement de votre lecteur, vous obtiendrez l’un des statuts suivants :

| Statut de tâche | Description |
|:--- |:--- |
| Creating | Après qu’une tâche a été créée, son état est défini sur Création. Pendant que la tâche se trouve dans l’état Création, le service Import/Export suppose que les disques n’ont pas été expédiés au centre de données. Une tâche peut rester dans l’état Création pendant deux semaines maximum. Passé ce délai, elle est automatiquement supprimée par le service. |
| Expédition | Après avoir expédié votre colis, vous devez mettre à jour les informations de suivi dans le portail Azure.  La tâche passera alors à l’état Expédition. La tâche restera dans l’état Expédition pendant deux semaines maximum. 
| Reçu | Lorsque tous les disques ont été reçus dans le centre de données, l’état de la tâche est défini sur Reçu. |
| Transferring | Une fois que le traitement a commencé pour au moins un disque, l’état de la tâche est défini sur Transfert. Pour plus d’informations, consultez la section État du disque ci-dessous. |
| Packaging | Une fois le traitement terminé pour tous les disques, la tâche est définie sur l’état Emballage jusqu’à ce que les disques vous aient été renvoyés. |
| Completed | Une fois que tous les disques ont été renvoyés au client, si la tâche s’est terminée sans erreur, la tâche est définie sur l’état Terminé. La tâche est automatiquement supprimée au bout de 90 jours dans l’état Terminé. |
| Fermés | Une fois que tous les disques ont été renvoyés au client, si des erreurs sont survenues pendant le traitement de la tâche, la tâche est définie sur l’état Clôturé(e). La tâche est automatiquement supprimée au bout de 90 jours dans l’état Clôturé(e). |

Le tableau ci-dessous décrit le cycle de vie d’un disque individuel tout au long d’une tâche d’importation ou d’exportation. L’état actuel de chaque disque d’une tâche est désormais visible dans le portail Azure.
Le tableau suivant décrit chacun des états par lesquels le disque d’une tâche peut passer.

| État du disque | Description |
|:--- |:--- |
| Spécifié | Pour une tâche d’importation, lorsque la tâche est créée à partir du portail Azure, l’état initial d’un disque est l’état Spécifié. Pour une tâche d’exportation, comme aucun disque n’est spécifié lors de la création de la tâche, l’état initial du disque est l’état Reçu. |
| Reçu | Le disque passe à l’état Reçu une fois que l’opérateur du service Import/Export a traité les disques reçus de l’entreprise de transport pour une tâche d’importation. Pour une tâche d’exportation, l’état initial du disque est l’état Reçu. |
| NeverReceived (Jamais reçu) | Le disque passe à l’état NeverReceived (Jamais reçu) lorsque le colis associé à une tâche est arrivé, mais ne contient pas le disque. Un disque peut également passer à cet état si le colis n’est toujours pas arrivé au centre de données deux semaines après réception des informations d’expédition par le service. |
| Transferring | Un disque passe à l’état Transfert lorsque le service commence à transférer les données du disque au stockage Microsoft Azure. |
| Completed | Un disque passe à l’état Terminé lorsque le service a transféré toutes les données sans erreurs.
| CompletedMoreInfo (Terminé avec des informations) | Un disque passe à l’état CompletedMoreInfo (Terminé avec des informations) lorsque le service a rencontré des erreurs pendant la copie des données à partir du disque ou sur celui-ci. Les informations peuvent inclure des erreurs, des avertissements ou des messages d’informations sur le remplacement des objets blob.
| ShippedBack (Renvoyé) | Le disque passe à l’état ShippedBack (Renvoyé) lorsqu’il a été renvoyé du centre de données à l’adresse de retour. |

Cette image à partir du portail Azure affiche l’état du lecteur d’un exemple de travail :

![Afficher l’état des disques](./media/storage-import-export-service/drivestate.png)

Le tableau suivant décrit les états associés aux défaillances de disque et les mesures mises en œuvre pour chacun de ces états.

| État du disque | Événement | Résolution / Étape suivante |
|:--- |:--- |:--- |
| NeverReceived (Jamais reçu) | Un disque qui a été marqué comme NeverReceived (Jamais reçu) car le colis associé à la tâche ne le contenait pas arrive dans un autre colis. | L’équipe responsable des opérations fait passer le disque à l’état Reçu. |
| N/A | Un disque qui n’est associé à aucune tâche arrive au centre de données dans le cadre d’une autre tâche. | Le disque est marqué en tant que disque supplémentaire et est retourné au client une fois la tâche associée au colis d’origine terminée. |

### <a name="time-to-process-job"></a>Temps de traitement du travail 
Le temps de traitement d’un travail d’importation/exportation varie en fonction de différents facteurs, tels que le délai de livraison, le type de travail, le type et la taille des données copiées, ou encore la taille des disques fournis. Le service Import/Export n’a pas de contrat de niveau de service mais, après réception des disques le service, il s’efforce d’effectuer la copie en 7 à 10 jours. Vous pouvez utiliser l’API REST pour assurer le suivi détaillé de la progression du travail. Dans l’option d’affichage des travaux, un pourcentage d’achèvement terminé indique l’état d’avancement de la copie. Contactez-nous si vous avez besoin d’estimer la durée nécessaire à un travail d’importation/exportation.

### <a name="pricing"></a>Tarifs
**Frais de manipulation de disque**

Des frais de manipulation sont appliqués pour chaque disque traité dans le cadre de votre travail d’importation/exportation. Pour plus d’informations, consultez [Tarification d’Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frais d’expédition**

Lorsque vous envoyez des disques à Azure, vous payez le coût d’expédition au transporteur. Lorsque Microsoft vous renvoie les disques, les frais d’expédition sont facturés au compte de transporteur que vous avez spécifié lors de la création du travail.

**Frais de transaction**

L’importation de données dans le stockage Azure n’entraîne pas de frais de transaction supplémentaires par rapport aux frais de transaction du stockage standard. Des frais de sortie standard s’appliquent quand les données sont exportées à partir de Stockage Blob. Pour plus d’informations sur les frais de transaction, consultez [Tarification - Transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Comment faire pour importer des données dans le Stockage Fichier Azure à l’aide de HDD et SSD SATA internes ?
Suivez les étapes ci-après si les données sur le disque doivent être importées dans le Stockage Fichier Azure.
Lors de l’importation des données à l’aide du service Azure Import/Export, la première étape consiste à préparer vos disques à l’aide de l’outil WAImportExport. Suivez la procédure ci-dessous pour préparer vos disques :

1. Identifiez les données à importer dans le Stockage Fichier Azure. Il peut s’agir de répertoires et de fichiers autonomes sur le serveur local ou sur un partage de réseau.  
2. Déterminez le nombre de disques nécessaires en fonction de la taille totale des données. Procurez-vous le nombre requis de disques durs SSD de 2,5 pouces ou SATA II ou III de 2,5 ou 3,5 pouces.
4. Déterminez les répertoires et/ou les fichiers à copier sur chaque disque dur.
5. Créez les fichiers CSV du jeu de données et du jeu de disques.
    
  Voici un exemple de fichier CSV de jeu de données pour importer des données en tant que fichiers Azure :
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   Dans l’exemple ci-dessus, le fichier 100M_1.csv.txt sera copié à la racine de « fileshare ». Si « fileshare » n’existe pas, il sera créé. Tous les fichiers et dossiers présents sous 50M_original seront copiés de manière récursive dans fileshare. La structure des dossiers sera conservée.

    Vous trouverez plus d’informations sur la [préparation du fichier CSV du jeu de données ici](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Fichier CSV du jeu de disques**

    La valeur de l’indicateur DriveSet est un fichier CSV qui contient la liste des disques auxquels les lettres de lecteur sont mappées afin que l’outil sélectionne correctement les disques à préparer. 

    Voici un exemple de fichier CSV de jeu de disques :
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    L’exemple ci-dessus part du principe que deux disques sont attachés et que des volumes NTFS de base présentant les lettres G:\ et H:\ ont été créés. L’outil formatera et chiffrera uniquement le disque contenant le volume H:\, le disque contenant le volume G:\ étant déjà formaté et chiffré.

    Vous trouverez plus d’informations sur la [préparation du fichier CSV du jeu de disques ici](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Utilisez l’outil [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) pour copier vos données sur un ou plusieurs disques durs.
7.  Vous pouvez spécifier « Encrypt » pour le champ Encryption dans le fichier CSV du jeu de disques afin d’activer le chiffrement BitLocker sur le disque dur. Vous pouvez également activer le chiffrement BitLocker manuellement sur le disque dur, spécifier « AlreadyEncrypted » pour ce même champ et fournir la clé dans le fichier CSV du jeu de disques lors de l’exécution de l’outil.

8. Ne modifiez ni les données ni le fichier journal après la préparation du disque.

> [!IMPORTANT]
> Un fichier journal est créé pour chaque disque dur que vous préparez. Lorsque vous créez la tâche d’importation à l’aide du portail Azure, vous devez télécharger tous les fichiers journaux des disques qui font partie de cette tâche. Les disques sans fichier journal ne sont pas traités.
> 
>

Voici les commandes et des exemples utiles pour préparer le disque dur à l’aide de l’outil WAImportExport.

Lors de la première session de copie, la commande PrepImport de l’outil WAImportExport permet de copier les répertoires ou les fichiers dans le cadre d’une nouvelle session de copie :

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Exemple d’importation 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Pour **ajouter des disques**, vous pouvez créer un nouveau fichier de jeu de disques et exécuter la commande indiquée ci-dessous. Pour les sessions de copie suivantes sur les disques non spécifiés dans le fichier CSV du paramètre InitialDriveSet, spécifiez un nouveau fichier CSV de jeu de disques et définissez-le en tant que valeur pour le paramètre « AdditionalDriveSet ». Utilisez le **même nom de fichier journal** et fournissez un **nouvel ID de session**. Le format du fichier CSV du paramètre AdditionalDriveSet est identique au format de celui du paramètre InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Exemple d’importation 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Afin d’ajouter d’autres données au même jeu de disques, la commande PrepImport de l’outil WAImportExport peut être appelée dans le cadre des sessions de copie suivantes pour copier les fichiers/répertoires supplémentaires : pour les sessions de copie suivantes sur les disques durs spécifiés dans le fichier CSV du paramètre InitialDriveSet, utilisez le **même nom de fichier journal** et fournissez un **nouvel ID de session** ; il est inutile de fournir la clé du compte de stockage.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Exemple d’importation 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Pour en savoir plus sur l’utilisation de l’outil WAImportExport, consultez [Préparation des disques durs pour une tâche d’importation](storage-import-export-tool-preparing-hard-drives-import.md).

Pour une description étape par étape, consultez [Exemple de flux de travail pour préparer des disques durs à un travail d’importation](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md).  



## <a name="create-an-export-job"></a>Création d’une tâche d’exportation
Créez une tâche d’exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs disques vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les disques, qui vous seront ensuite renvoyés.

### <a name="prepare-your-drives"></a>Préparation des lecteurs
Les vérifications préalables suivantes sont recommandées pour préparer vos disques en vue d’un travail d’exportation :

1. Vérifiez le nombre de disques requis à l’aide de la commande PreviewExport de l’outil WAImportExport. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser.
2. Vérifiez que le disque dur expédié en vue de la tâche d’exportation est accessible en lecture/écriture.

### <a name="create-the-export-job"></a>Création du travail d’importation
1. Pour créer un travail d’exportation, dans le portail Azure, accédez à Plus de services -> STOCKAGE -> « Tâches d’importation/exportation ». Cliquez sur **Créer une tâche d’importation/exportation**.
2. À l’étape 1 Fonctions de base, sélectionnez « Exporter à partir d’Azure », entrez une chaîne pour le nom du travail, sélectionnez un abonnement, entrez ou sélectionnez un groupe de ressources. Indiquez un nom décrivant le travail d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que vous choisissez vous servira à suivre vos travaux pendant et après son exécution. Fournissez les coordonnées de la personne responsable de ce travail d’exportation. 

3. À l’étape 2 Détails du travail, sélectionnez le compte de stockage duquel les données sont exportées dans la section « Compte de stockage ». L’emplacement de dépôt est automatiquement renseigné en fonction de la région du compte de stockage sélectionné. Indiquez les données de blob que vous souhaitez exporter depuis votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d’exporter toutes les données d’objets blob contenues dans le compte de stockage ou indiquer les objets blob ou ensembles d’objets blob à exporter.
   
   Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To** , puis indiquez le chemin d’accès relatif de l’objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
   
   Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With** , puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.
   
   Le tableau suivant présente des exemples de chemins d’accès d’objet blob valides :
   
   | Sélecteur | Chemin d'accès d'objet blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporte tous les objets blob présents dans le compte de stockage. |
   | Starts With |/$root/ |Exporte tous les objets blob présents dans le conteneur racine. |
   | Starts With |/book |Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book** |
   | Starts With |/music/ |Exporte tous les objets blob présents dans le conteneur **music** |
   | Starts With |/music/love |Exporte tous les objets blob présents dans le conteneur **music** qui commencent par le préfixe **love**. |
   | Equal To |$root/logo.bmp |Exporte l'objet blob **logo.bmp** présent dans le conteneur racine. |
   | Equal To |videos/story.mp4 |Exporte l’objet blob **story.mp4** présent dans le conteneur **videos**. |
   
   Vous devez indiquer les chemins d’accès aux objets blob dans des formats valides pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran.
   
   ![Créer une tâche d'exportation - Étape 3](./media/storage-import-export-service/export-job-03.png)

4. À l’étape 3 Informations de retour, sélectionnez le transporteur dans la liste déroulante et entrez un numéro de compte de transporteur valide créé avec ce transporteur. Microsoft utilise ce compte pour réexpédier les lecteurs une fois la tâche d’importation terminée. Indiquez le nom d’une personne, un numéro de téléphone, un e-mail, le nom de la rue, la ville, le code postal, l’état/la province et le payx/la région et assurez-vous que ces informations sont complètes et valides.
   
 5. Sur la page Résumé, l’adresse du centre de données Azure est fournie afin d’y envoyer vos disques. Assurez-vous que le nom du travail et l’adresse complète sont mentionnés sur l’étiquette d’expédition. 

6. Sur la page Résumé, cliquez sur OK pour terminer la création du travail d’exportation.

7. Après l’envoi des disques, revenez sur la page **Importer/Exporter** du portail Azure : a) Accédez au travail d’importation et cliquez dessus. b) Cliquez sur **Mettez à jour l’état de la tâche et les informations de suivi une fois les lecteurs expédiés**. 
     c) Cochez la case « Marquer comme expédié ». d) Indiquez le transporteur et le numéro de suivi.
    
   Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.
   
8. Vous pouvez suivre l’état d’avancement de votre travail sur le tableau de bord du portail. Pour connaître la signification de chaque état de travail dans la section précédente, consultez [Affichage de l’état de votre travail](#viewing-your-job-status).

   > [!NOTE]
   > Si l’objet blob à exporter est en cours d’utilisation au moment de la copie sur le disque dur, le service d’importation/exportation Azure prend un instantané de l’objet blob et copie la capture instantanée.
   > 
   > 
 
9. Après avoir réceptionné les disques avec vos données exportées, vous pouvez afficher et copier les clés BitLocker générées par le service pour votre disque. Accédez au travail d’exportation dans le portail Azure et cliquez sur l’onglet Import/Export. Sélectionnez votre travail d’exportation dans la liste, puis cliquez sur l’option Clés BitLocker. Les clés BitLocker s’affichent comme ci-dessous :
   
   ![Afficher les clés BitLocker pour une tâche d'exportation](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Accédez à la section FAQ ci-dessous, car elle aborde les questions les plus courantes concernant l’utilisation de ce service.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Puis-je copier à partir de Stockage Fichier Azure à l’aide du service Azure Import/Export ?**

Oui, le service Azure Import/Export prend en charge l’importation dans le Stockage Fichier Azure. L’exportation de fichiers Azure n’est pas prise en charge pour l’instant.

**Le service Azure Import/Export est-il disponible pour les abonnements de fournisseur de services de chiffrement ?**

Le service Azure Import/Export prend en charge les abonnements de fournisseur de services de chiffrement.

**Puis-je ignorer l’étape de préparation de disque pour un travail d’importation ou préparer un disque sans effectuer la copie ?**

Tout disque que vous souhaitez expédier en vue d’importer des données doit être préparé à l’aide de l’outil Azure WAImportExport. Vous devez utiliser cet outil pour copier les données sur le disque.

**Ai-je besoin d’effectuer la préparation du disque lors de la création d'une tâche d'exportation ?**

Non, mais certaines vérifications préalables sont recommandées. Vérifiez le nombre de disques requis à l’aide de la commande PreviewExport de l’outil WAImportExport. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser. Vérifiez également que le disque dur qui sera utilisé pour le travail d’exportation est accessible en lecture et en écriture.

**Que se passe-t-il si j’envoie par accident un disque dur non conforme au type pris en charge ?**

Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

**Puis-je annuler une tâche ?**

Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps l’état des tâches terminées est-il consultable dans le portail Azure ?**

L’état des travaux terminés est visible pendant 90 jours. Au-delà de ce délai, les travaux terminés sont supprimés.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

Le service Import/Export limite chaque tâche d’importation ou d’exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches. Les disques associés au même travail doivent être expédiés ensemble dans le même colis.
Microsoft propose des conseils et une assistance quand la capacité de données s’étend sur plusieurs travaux d’importation de disques. Pour plus d’informations, contactez bulkimport@microsoft.com.

**Le service formate-t-il les disques avant de les renvoyer ?**

Non. Tous les disques sont chiffrés avec BitLocker.

**Est-il possible d’acheter des lecteurs auprès de Microsoft pour des tâches d’importation/exportation ?**

Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

** Comment accéder aux données importées par ce service**

Les données de votre compte Stockage Azure sont accessibles via le portail Azure ou à l’aide d’un outil autonome nommé Explorateur de stockage. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Une fois la tâche d’importation terminée, à quoi mes données ressembleront-elles dans le compte de stockage ? Mon arborescence sera-t-elle préservée ?**

Quand vous préparez un disque dur pour une tâche d’importation, la destination est spécifiée par le champ DstBlobPathOrPrefix du fichier CSV du jeu de données. Il s’agit du conteneur cible dans le compte de stockage dans lequel les données du disque dur sont copiées. Dans ce conteneur cible, des répertoires virtuels sont créés pour les dossiers du disque dur et des objets blob sont créés pour les fichiers. 

**Si le disque contient des fichiers qui existent déjà dans mon compte de stockage, le service va-t-il remplacer les fichiers ou les objets blob dans mon compte de stockage ?**

Lors de la préparation du disque, vous pouvez indiquer si les fichiers cibles doivent être remplacés ou ignorés à l’aide du champ Disposition:< rename|no-overwrite|overwrite> du fichier CSV du jeu de données. Par défaut, le service ne remplace pas les objets blob ou les fichiers, mais renomme les nouveaux fichiers.

**L’outil WAImportExport est-il compatible avec un système d’exploitation Windows 32 bits ?**
Non. L’outil WAImportExport est compatible uniquement avec les systèmes d’exploitation Windows 64 bits. Pour obtenir la liste complète des versions de système d’exploitation pris en charge, consultez la section [Conditions préalables](#pre-requisites) .

**Dois-je ajouter quelque chose en plus du disque dur dans mon colis ?**

Incluez uniquement vos disques durs. N’incluez pas d’accessoires tels que des câbles d’alimentation ou USB.

**Dois-je expédier mes disques à l’aide de FedEx ou DHL ?**

Vous pouvez expédier des disques au centre de données à l’aide d’un transporteur connu comme FedEx, DHL, UPS ou La Poste. Toutefois, pour que les disques du centre de données puissent vous être retournés, vous devez fournir un numéro de compte FedEx aux États-Unis et dans l’UE, ou un numéro de compte DHL en Asie et en Australie.

**Existe-t-il des restrictions à l’expédition de mon disque dur hors des frontières ?**

Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d'expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais.

**Lorsque vous créez une tâche, l’adresse d’expédition désigne un emplacement différent de celui de mon compte de stockage. Que dois-je faire ?**

Certains emplacements de compte de stockage sont associés à d’autres emplacements d’expédition. Les emplacements d’expédition auparavant disponibles peuvent être temporairement associés à d’autres emplacements. Vérifiez toujours l’adresse d’expédition fournie lors de la création du travail avant d’expédier vos disques.

**Lors de l’expédition de mon disque, le transporteur me demande l’adresse et le numéro de téléphone du centre de données. Que dois-je indiquer ?**

Le numéro de téléphone et l’adresse du centre de données sont fournis dans le cadre de la création de la tâche.

**Puis-je utiliser le service Azure Import/Export pour copier des boîtes aux lettres PST et des données SharePoint vers O365 ?**

Consultez la rubrique [Importer des fichiers PST ou des données SharePoint dans Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Puis-je utiliser le service Azure Import/Export pour copier mes sauvegardes en mode hors connexion sur le service Sauvegarde Azure ?**

Consultez la rubrique [Flux de travail de la sauvegarde hors connexion dans Sauvegarde Azure](../../backup/backup-azure-backup-import-export.md).

**Quel est le nombre maximal de HDD par expédition ?**

Une livraison peut contenir n’importe quel nombre de disques durs et, si les disques appartiennent à plusieurs tâches, il est recommandé : a) d’indiquer les noms de tâche correspondants sur les disques. b) de mettre à jour les tâches avec un numéro de suivi se terminant par -1, -2, etc.
  
**Quelle est la taille maximale des objets blob de blocs et objets blob de pages prise en charge pour chaque importation/exportation de disque ?**

La taille maximale des objets blob de blocs est d’environ 4,768 To ou 5 000 000 Mo.
La taille maximale des objets blob de pages est de 1 To.

**L’importation/exportation de disque prennent-elles en charge le chiffrement AES 256 ?**

Par défaut, le service Azure Import/Export chiffre avec le chiffrement BitLocker AES 128, mais vous pouvez aller jusqu’à un chiffrement à AES 256 en chiffrant manuellement avec bitlocker avant la copie des données. 

Si vous utilisez [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), voici un exemple de commande.
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Si vous utilisez [WAImportExport Tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip), spécifiez « AlreadyEncrypted », puis fournissez la clé dans le volume partagé de cluster driveset.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>étapes suivantes

* [Configuration de l’outil WAImportExport](storage-import-export-tool-how-to.md)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Exemple d’API REST Azure Import Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

