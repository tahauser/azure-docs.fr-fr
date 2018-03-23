---
title: Protéger les données personnelles au repos dans Azure avec le chiffrement | Microsoft Docs
description: Cet article peut être utilisé pour se conformer aux réglementations industrielles ou gouvernementales, telles que le Règlement général de Protection des données (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 37da3d91df4be871d6647b6290768ea4999a5010
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>technologies de chiffrement Azure : Protéger les données personnelles au repos avec un chiffrement

Cet article vous aide à comprendre et à utiliser des technologies de chiffrement Azure pour sécuriser les données au repos.

Le chiffrement des données au repos est une bonne pratique essentielle pour protéger les données sensibles ou personnelles et pour satisfaire aux exigences de confidentialité des données et de conformité. Le chiffrement au repos est conçu pour empêcher l’attaquant d’accéder aux données non chiffrées en garantissant que les données sont chiffrées quand elles sont sur le disque. Les informations contenues dans cet article peuvent être utilisées pour garantir la conformité avec le Règlement général sur la protection des données (RGPD).

## <a name="scenario"></a>Scénario 

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni.

La compagnie utilise Microsoft Azure pour stocker les données d’entreprise dans le cloud. Ces dernières peuvent inclure des informations sur les clients et/ou les employés comme les suivantes :

- Adresses
- Numéros de téléphone
- Numéros d’identification fiscale
- Informations de carte de crédit

La compagnie doit protéger la confidentialité des données des clients et des employés tout en les rendant accessibles aux services qui en ont besoin. (Notamment, les services de paie et de réservation.)

La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense qui inclut des informations personnelles pour effectuer le suivi des relations avec les clients actuels et anciens.

### <a name="problem-statement"></a>Définition du problème

La compagnie doit protéger la confidentialité des données personnelles des clients et des employés tout en les rendant accessibles aux services qui en ont besoin (notamment, les services de paie et de réservation). Ces données personnelles sont stockées en dehors du centre de données de l’entreprise et ne sont pas sous le contrôle physique de l’entreprise.

### <a name="company-goal"></a>Objectif de l’entreprise

Dans le cadre d’une stratégie de sécurité de défense en profondeur multicouche, un objectif de l’entreprise vise à garantir le chiffrement de toutes les sources de données qui contiennent des données personnelles, notamment celles qui résident dans le stockage cloud. Si des personnes non autorisées accèdent aux données personnelles, celles-ci doivent être sous forme illisible. L’application du chiffrement doit être simple (ou transparente) pour les utilisateurs et les administrateurs.

## <a name="solutions"></a>solutions

Les services Azure proposent plusieurs outils et technologies pour vous aider à protéger les données personnelles au repos en les chiffrant.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) assure un stockage sécurisé des clés utilisées pour chiffrer les données au repos dans les services Azure et correspond à la solution recommandée de stockage et de gestion des clés. La gestion des clés de chiffrement est essentielle à la sécurisation des données stockées.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Comment utiliser Azure Key Vault pour protéger les clés de chiffrement des données personnelles ?

Pour utiliser Azure Key Vault, vous avez besoin d’un abonnement à un compte Azure. Vous avez également besoin d’installer Azure PowerShell. Les étapes nécessaires impliquent d’utiliser des applets de commande PowerShell pour effectuer les opérations suivantes :

1. Connexion à vos abonnements

2. Création d’un coffre de clés

3. Ajout d’une clé ou d’un secret au coffre de clés

4. Inscrire les applications qui utiliseront le coffre de clés avec Azure Active Directory

5. Autoriser les applications à utiliser la clé ou le secret

Pour créer un coffre de clés, utilisez l’applet de commande PowerShell New-AzureRmKeyVault. Vous devez attribuer un nom au coffre, un nom au groupe de ressources et un emplacement géographique. Vous utiliserez le nom du coffre pour gérer des clés par le biais d’autres applets de commande. Les applications qui utilisent le coffre par le biais de l’API REST utilisent l’URI du coffre.

Azure Key Vault peut vous fournir une clé protégée par un logiciel ou vous pouvez importer une clé existante dans un fichier .PFX. Vous pouvez également stocker des secrets (mots de passe) dans le coffre.

Vous pouvez également générer une clé dans votre module de sécurité matériel local et la transférer vers les modules de sécurité matériels du service Key Vault, sans que la clé ne quitte la limite de ce module de sécurité matériel.

Pour obtenir des instructions détaillées sur l’utilisation d’Azure Key Vault, suivez les étapes indiquées dans [Prise en main d’Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

Pour obtenir la liste des applets de commande PowerShell utilisées avec Azure Key Vault, consultez [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Azure Disk Encryption pour Windows

[Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) protège les données au repos sur les machines virtuelles Azure et s’intègre à Azure Key Vault. Azure Disk Encryption utilise [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) dans Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) dans Linux pour chiffrer à la fois le système d’exploitation et les disques de données. Azure Disk Encryption est pris en charge sur Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 et sur les clients Windows 8 et Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Comment utiliser Azure Disk Encryption pour protéger des données personnelles ?

Pour utiliser Azure Disk Encryption, vous avez besoin d’un abonnement à un compte Azure. Pour activer Azure Disk Encryption pour les machines virtuelles Windows et Linux, procédez comme suit :

1. Utilisez le modèle Azure Disk Encryption Resource Manager, PowerShell ou l’interface de ligne de commande (CLI) pour activer le chiffrement des disques et spécifier la configuration du chiffrement. 

2. Octroyez l’accès à la plateforme Azure pour lire les documents de chiffrement à partir de votre coffre de clés.

3. Fournissez une identité d’application Azure Active Directory (Azure AD) pour écrire les documents de clé de chiffrement dans votre coffre de clés.

Azure met à jour la machine virtuelle et la configuration du coffre de clés, puis installe votre machine virtuelle chiffrée.

Quand vous configurez votre coffre de clés pour prendre en charge Azure Disk Encryption, vous pouvez ajouter une clé de chiffrement de clé (KEK) pour une sécurité accrue et pour prendre en charge la sauvegarde des machines virtuelles chiffrées.

![](media/protect-personal-data-at-rest/create-key.png)

Des instructions détaillées propres à des scénarios de déploiement et des expériences utilisateur spécifiques sont incluses dans [Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux.](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Chiffrement du service de stockage Azure

Le [Chiffrement du service de stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/storage-service-encryption) permet de protéger vos données pour garantir le respect des engagements de votre organisation en matière de sécurité et de conformité. Le stockage Azure chiffre automatiquement vos données à l’aide d’un chiffrement AES 256 bits avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. Ce service est disponible pour les fichiers et objets blob Azure.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Comment utiliser le Chiffrement du service de stockage pour protéger les données personnelles ?

Pour activer le Chiffrement du service de stockage, procédez comme suit :

1. Connectez-vous au portail Azure.

2. Sélectionnez un compte de stockage.

3. Dans Paramètres, sous la section Service BLOB, sélectionnez Chiffrement.

4. Dans la section Service de fichiers, sélectionnez Chiffrement.

Lorsque vous avez cliqué sur le paramètre Chiffrement, vous pouvez activer ou désactiver Storage Service Encryption.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Les nouvelles données sont chiffrées. Les données incluses dans les fichiers existants de ce compte de stockage restent non chiffrées.

Après avoir activé le chiffrement, copiez les données dans le compte de stockage à l’aide de l’une des méthodes suivantes :

1. Copiez les objets blob ou les fichiers à l’aide de l’[utilitaire de ligne de commande AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy).

2. [Montez un partage de fichiers à l’aide de SMB](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows) afin de pouvoir utiliser un utilitaire tel que Robocopy pour copier des fichiers.

3. Copiez les données d’objet blob ou de fichier vers et depuis le stockage blob ou entre des comptes de stockage à l’aide de [bibliothèques clientes de stockage telles que .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Utilisez un [explorateur de stockage](https://docs.microsoft.com/azure/storage/storage-explorers) pour télécharger des objets blob dans votre compte de stockage avec le chiffrement activé.

### <a name="transparent-data-encryption"></a>Chiffrement transparent des données

Transparent Data Encryption (TDE) est une fonctionnalité dans SQL Azure qui permet de chiffrer les données aux niveaux de la base de données et du serveur. TDE est désormais activée par défaut sur toutes les nouvelles bases de données récemment créées. TDE effectue le chiffrement et le déchiffrement d’E/S en temps réel des données et des fichiers journaux.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Comment utiliser TDE pour protéger les données personnelles ?

Vous pouvez configurer TDE dans le portail Azure à l’aide de l’API REST ou de PowerShell. Pour activer TDE sur une base de données existante à l’aide du portail Azure, procédez comme suit :

1. Visitez le portail Azure à l’adresse <https://portal.azure.com> et connectez-vous avec votre compte administrateur ou contributeur Azure.

2. Dans la bannière de gauche, cliquez sur PARCOURIR, puis cliquez sur Bases de données SQL.

3. Les bases de données SQL étant sélectionnées dans le volet gauche, cliquez sur votre base de données utilisateur.

4. Dans le panneau de la base de données, cliquez sur Tous les paramètres.

5. Dans le panneau Paramètres, cliquez sur la partie Transparent Data Encryption pour ouvrir le panneau éponyme.

6. Dans le panneau Chiffrement des données, déplacez le bouton Chiffrement des données sur Activé, puis cliquez sur Enregistrer (en haut de la page) pour appliquer le paramètre. L’état de chiffrement indique approximativement la progression du chiffrement transparent des données.

![Activation du chiffrement des données](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Des instructions sur l’activation de TDE et des informations sur le déchiffrement des bases de données protégées par chiffrement TDE sont entre autres disponibles dans l’article [Transparent Data Encryption avec Azure SQL Database.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Résumé

La compagnie peut atteindre son objectif de chiffrement des données personnelles stockées dans le cloud Azure. Pour cela, elle peut utiliser Azure Disk Encryption pour protéger des volumes entiers. Ceux-ci peuvent inclure à la fois les fichiers de système d’exploitation et les fichiers de données qui contiennent des informations d’identification personnelle et d’autres données sensibles. Le Chiffrement du service de stockage Azure peut servir à protéger les données personnelles qui sont stockées dans des objets blob et des fichiers. Pour les données stockées dans des bases de données SQL Azure, Transparent Data Encryption fournit une protection contre l’exposition non autorisée des informations personnelles.

Pour protéger les clés utilisées pour chiffrer les données dans Azure, la compagnie peut utiliser Azure Key Vault. Ce dernier rationalise le processus de gestion des clés et permet à la compagnie de garder le contrôle des clés qui accèdent aux données personnelles et les chiffrent.

## <a name="next-steps"></a>Étapes suivantes

- [Guide de dépannage Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Chiffrement d’une machine virtuelle Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Chiffrement des données dans Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Chiffrement de base de données Azure Cosmos DB au repos](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
