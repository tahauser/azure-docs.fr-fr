---
title: Gérer les données personnelles dans Microsoft Azure | Microsoft Docs
description: comment corriger, mettre à jour, supprimer et exporter des données personnelles pour vous aider à vous conformer au Règlement général sur la protection des données (RGPD) dans Azure Active Directory et dans Azure SQL Database
services: security
documentationcenter: na
author: barclayn
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
ms.openlocfilehash: 41c0cc4eb3697aa79abeabddc98a84598ce4ea50
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Gérer les données personnelles dans Microsoft Azure

Cet article donne des conseils sur la manière de corriger, de mettre à jour, de supprimer et d’exporter des données personnelles pour vous aider à vous conformer au Règlement général sur la protection des données (RGPD) dans Azure Active Directory et dans Azure SQL Database.

## <a name="scenario"></a>Scénario

Une entreprise basée à Dublin propose à sa clientèle nationale et internationale des services clé en main d’organisation de mariages haut de gamme en Irlande et dans le monde entier. Elle possède des bureaux, des clients, des employés et des prestataires dans le monde entier pour assurer des services complets sur place.

Parmi les différents services qu’elle propose, l’entreprise assure le suivi des réponses et prend en compte les allergies et préférences alimentaires des invités. Les personnes invitées à un mariage peuvent s’inscrire à diverses activités, comme l’équitation, le surf, les promenades en bateau, etc. et même échanger entre eux sur une page web centrale dans les mois qui précèdent l’événement. L’entreprise collecte des informations personnelles sur les employés, les prestataires, les clients et les invités du mariage. Compte tenu de la dimension internationale de l’activité, l’entreprise doit se conformer à plusieurs niveaux de réglementation.

## <a name="problem-statement"></a>Définition du problème

- Les administrateurs de données doivent pouvoir corriger les informations personnelles inexactes et mettre à jour celles qui sont incomplètes ou qui ont changé.

- Les administrateurs de données doivent pouvoir supprimer les informations personnelles à la demande de la personne concernée.

- Les administrateurs de données doivent pouvoir exporter les données et les fournir dans un format courant et structuré à la personne concernée qui en fait la demande.

## <a name="company-goals"></a>Objectifs de la société

- Les informations inexactes ou incomplètes sur les clients, les invités du mariage, les employés et les fournisseurs doivent être corrigées ou mises à jour dans Azure Active Directory et Azure SQL Database.

- Les informations personnelles doivent pouvoir être supprimées dans Azure Active Directory et Azure SQL Database à la demande de la personne concernée.

- Les données personnelles doivent pouvoir être exportées dans un format commun et structuré à la demande de la personne concernée.

## <a name="solutions"></a>solutions

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory : rectifier/corriger des données personnelles inexactes ou incomplètes et effacer/supprimer des données personnelles/profils utilisateur

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion d’annuaires et d’identités multilocataire basé sur le cloud.
Vous pouvez corriger, mettre à jour ou supprimer les profils utilisateur de clients et d’employés et les informations professionnelles utilisateur qui contiennent des données personnelles, telles que le nom d’utilisateur, la fonction, l’adresse ou le numéro de téléphone dans votre environnement [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) via le [portail Azure](https://portal.azure.com/).

Vous devez vous connecter avec un compte d’administrateur général de l’annuaire.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Comment corriger ou mettre à jour des informations professionnelles et de profil utilisateur dans Azure Active Directory ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Tous les services**, entrez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Utilisateurs**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Dans le panneau **Utilisateurs et groupes - Utilisateurs**, sélectionnez un utilisateur dans la liste puis, dans le panneau de l’utilisateur sélectionné, sélectionnez **Profil** pour afficher les informations de profil utilisateur qu’il convient de corriger ou de mettre à jour.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Corrigez ou mettez à jour les informations puis, dans la barre de commandes, sélectionnez **Enregistrer**.

6.  Dans le panneau de l’utilisateur sélectionné, sélectionnez **Informations professionnelles** pour afficher les informations professionnelles utilisateur qu’il convient de corriger ou de mettre à jour.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Corrigez ou mettez à jour les informations professionnelles utilisateur puis, dans la barre de commandes, sélectionnez **Enregistrer**.

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Comment supprimer un profil utilisateur dans Azure Active Directory ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Tous les services**, entrez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

    ![](media/manage-personal-data-azure/image001.png)

3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Utilisateurs**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Sur le panneau **Utilisateurs et groupes - Utilisateurs -** , sélectionnez un utilisateur dans la liste.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. Dans le panneau de l’utilisateur sélectionné, sélectionnez **Vue d’ensemble**, puis, dans la barre de commandes, sélectionnez **Supprimer**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database : rectifier/corriger des données personnelles inexactes ou incomplètes ; effacer/supprimer des données personnelles ; exporter des données personnelles 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) est une base de données cloud qui permet aux développeurs de créer et tenir à jour des applications.

Les données personnelles peuvent être mises à jour dans [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) à l’aide de requêtes SQL standard et peuvent aussi être supprimées. Par ailleurs, les données personnelles peuvent être exportées à partir de SQL Database selon différentes méthodes, notamment à l’aide de l’Assistant Importation et Exportation Azure SQL Server, et dans divers formats, notamment sous forme de fichier BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Comment corriger, mettre à jour ou supprimer des données personnelles dans SQL Database ?

Pour savoir comment corriger ou mettre à jour des données personnelles dans SQL Database, consultez la documentation [Update (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [Update Text](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [Update with Common Table Expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) ou [Update Write Text](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql).

Pour savoir comment supprimer des données personnelles dans SQL Database, consultez la documentation [Delete (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Comment exporter des données personnelles dans un fichier BACPAC dans SQL Database ?

Un fichier BACPAC comporte des données et des métadonnées SQL Database. Il s’agit d’un fichier compressé doté d’une extension BACPAC. Cette opération peut se faire via le [portail Azure](https://portal.azure.com/), l’utilitaire en ligne de commande SQLPackage, SQL Server Management Studio (SSMS) ou PowerShell.

Pour savoir comment exporter des données dans un fichier BACPAC, consultez la page [Exporter une base de données SQL Azure dans un fichier BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export). Vous y trouverez des instructions détaillées pour chaque méthode mentionnée ci-dessus.

Comment exporter des données personnelles à partir de SQL Database avec l’Assistant Importation et Exportation SQL Server ?

Cet Assistant vous permet de copier des données d’une source vers une destination. Pour obtenir une présentation de l’Assistant, notamment des informations d’autorisation, et pour savoir comment vous procurer l’outil et obtenir de l’aide sur celui-ci, consultez la page web [Importer et exporter des données avec l’Assistant Importation et Exportation SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard).

Pour obtenir une vue d’ensemble des étapes de l’Assistant, consultez la page web [Étapes de l’Assistant Importation et Exportation SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Étapes suivantes :

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

