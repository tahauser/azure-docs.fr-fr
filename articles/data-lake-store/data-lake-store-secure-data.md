---
title: Sécurisation des données stockées dans Azure Data Lake Store | Microsoft Docs
description: Apprenez à sécuriser les données dans Azure Data Lake Store à l'aide de groupes et de listes de contrôle d'accès
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4d926ee08da593e590aa77a2ca09d8d1e1f6bb46
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Sécurisation des données stockées dans Azure Data Lake Store
La sécurisation des données dans Azure Data Lake Store se fait en trois étapes.  Le contrôle d’accès en fonction du rôle (RBAC) et les listes de contrôle d’accès (ACL, access control list) doivent être définis pour offrir aux utilisateurs et aux groupes de sécurité un accès total aux données.

1. Commencez par créer des groupes de sécurité dans Azure Active Directory (AAD). Ces groupes de sécurité sont utilisés pour implémenter le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).
2. Affectez les groupes de sécurité AAD au compte Azure Data Lake Store. Ceci contrôle l'accès au compte Data Lake Store à partir du portail et les opérations de gestion à partir du portail ou des API.
3. Affectez les groupes de sécurité AAD comme listes de contrôle d'accès (ACL) sur le système de fichiers Data Lake Store.
4. En outre, vous pouvez définir une plage d’adresses IP pour les clients pouvant accéder aux données de Data Lake Store.

Cet article explique comment utiliser le portail Azure pour effectuer les tâches ci-dessus. Pour obtenir des informations détaillées sur la manière dont le Data Lake Store implémente la sécurité au niveau du compte et des données, consultez [Sécurité dans Azure Data Lake Store](data-lake-store-security-overview.md). Pour des informations détaillées sur la façon dont les listes de contrôle d’accès sont implémentées dans Azure Data Lake Store, consultez [Overview of Access Control in Data Lake Store](data-lake-store-access-control.md) (Vue d’ensemble du contrôle d’accès dans Data Lake Store).

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Créer des groupes de sécurité dans Azure Active Directory
Pour obtenir des instructions sur la création de groupes de sécurité AAD et l'ajout d'utilisateurs au groupe, consultez [Gestion des groupes de sécurité dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Vous pouvez ajouter tant des utilisateurs que d’autres groupes à un groupe dans Azure AD à l’aide du portail Azure. Toutefois, pour ajouter un principal de service à un groupe, utilisez le [module PowerShell d’Azure AD](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Affecter les utilisateurs ou les groupes de sécurité aux comptes Azure Data Lake Store
Lorsque vous affectez des utilisateurs ou des groupes de sécurité aux comptes Azure Data Lake Store, vous contrôlez l'accès aux opérations de gestion sur le compte à l'aide du portail Azure et des API Azure Resource Manager. 

1. Ouvrez un compte Azure Data Lake Store. Dans le volet gauche, cliquez sur **Toutes les ressources**, puis, dans le volet Toutes les ressources, cliquez sur le nom du compte auquel vous souhaitez affecter un utilisateur ou un groupe de sécurité.

2. Dans le panneau de votre compte Data Lake Store, cliquez sur **Contrôle d’accès (IAM)**. Le panneau par défaut répertorie les propriétaires de l’abonnement en tant que propriétaires.
   
    ![Affecter un groupe de sécurité à un compte Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter un groupe de sécurité à un compte Azure Data Lake Store")

3. Dans le panneau **Contrôle d’accès (IAM)**, cliquez sur **Ajouter** pour ouvrir le panneau **Ajouter des autorisations**. Dans le panneau **Ajouter des autorisations**, sélectionnez un **rôle** pour l’utilisateur ou le groupe. Recherchez le groupe de sécurité créé précédemment dans Azure Active Directory et sélectionnez-le. Si la liste d’utilisateurs et de groupes est longue, utilisez la zone de texte **Sélectionner** pour filtrer sur le nom du groupe. 
   
    ![Ajouter un rôle pour l’utilisateur](./media/data-lake-store-secure-data/adl.add.user.1.png "Ajouter un rôle pour l’utilisateur")
   
    Les rôles **Propriétaire** et **Collaborateur** fournissent l’accès à différentes fonctions d’administration sur le compte Data Lake. Pour les utilisateurs qui interagiront avec les données du Data Lake, mais qui auront toujours besoin d’afficher les informations de gestion de compte, vous pouvez les ajouter au rôle **Lecteur**. L'étendue de ces rôles est limitée aux opérations de gestion associées au compte Azure Data Lake Store.
   
    Pour les opérations sur les données, des autorisations pour chaque système de fichiers définissent ce que les utilisateurs peuvent faire. Par conséquent, un utilisateur ayant un rôle de Lecteur peut afficher uniquement les paramètres d'administration associés au compte mais peut potentiellement lire et écrire des données en fonction des autorisations du système de fichiers qui lui sont affectées. Les autorisations de système de fichiers Data Lake Store sont décrites dans [Affecter un groupe de sécurité comme ACL au système de fichiers Azure Data Lake Store](#filepermissions).

    > [!IMPORTANT]
    > Seul le rôle **Propriétaire** active automatiquement l’accès au système de fichiers. Les rôles **Contributeur**, **Lecteur** et autres requièrent des ACL pour activer n’importe quel niveau d’accès aux dossiers et aux fichiers.  Le rôle **Propriétaire** fournit des autorisations d’accès aux dossiers et aux fichiers de niveau super utilisateur qui ne peuvent pas être remplacées via des ACL. Pour plus d’informations sur la correspondance entre les stratégies RBAC et l’accès aux données, consultez [RBAC pour la gestion des comptes](data-lake-store-security-overview.md#rbac-for-account-management).

4. Pour ajouter un utilisateur ou un groupe qui n’est pas répertorié dans le panneau **Ajouter des autorisations**, vous pouvez l’inviter en tapant son adresse e-mail dans la zone de texte **Sélectionner**, puis en le sélectionnant dans la liste.
   
    ![Ajouter un groupe de sécurité](./media/data-lake-store-secure-data/adl.add.user.2.png "Ajouter un groupe de sécurité")
   
5. Cliquez sur **Enregistrer**. Vous devez voir le groupe de sécurité ajouté comme indiqué ci-dessous.
   
    ![Groupe de sécurité ajouté](./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité ajouté")

6. Votre groupe de sécurité ou utilisateur a désormais accès au compte Azure Data Lake Store. Si vous souhaitez fournir un accès à des utilisateurs en particulier, vous pouvez les ajouter au groupe de sécurité. De même, si vous souhaitez révoquer l'accès d'un utilisateur, vous pouvez le supprimer du groupe de sécurité. Vous pouvez également affecter plusieurs groupes de sécurité à un compte. 

## <a name="filepermissions"></a>Affecter des utilisateurs ou des groupes de sécurité en tant qu’ACL au système de fichiers Azure Data Lake Store
En affectant des groupes de sécurité ou des utilisateurs au système de fichiers Azure Data Lake, vous définissez un contrôle d'accès aux données stockées dans Azure Data Lake Store.

1. Dans le panneau de votre compte Data Lake Store, cliquez sur **Explorateur de données**.
   
    ![Afficher les données via l’Explorateur de données](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Afficher les données via l’Explorateur de données")
2. Dans le panneau **Explorateur de données**, cliquez sur le dossier pour lequel vous souhaitez configurer l’ACL, puis cliquez sur **Accès**. Pour affecter des ACL à un fichier, vous devez tout d’abord cliquer sur le fichier pour en afficher un aperçu, puis cliquer sur **Accès** dans le panneau **Aperçu du fichier**.
   
    ![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")
3. Le panneau **Accès** répertorie les propriétaires et les autorisations déjà affectés à la racine. Cliquez sur l’icône **Ajouter** pour ajouter des ACL d’accès supplémentaires.
    > [!IMPORTANT]
    > La définition d’autorisations d’accès pour un fichier unique ne donne pas nécessairement accès à ce fichier à un utilisateur ou un groupe. L’emplacement du fichier doit être accessible à l’utilisateur ou au groupe attribué. Pour obtenir plus d’informations et des exemples, consultez [Scénarios courants liés aux autorisations](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lister les accès standard et personnalisés](./media/data-lake-store-secure-data/adl.acl.2.png "Lister les accès standard et personnalisés")
   
   * Les sections **Propriétaires** et **Tous les autres** permettent d’attribuer un accès de type UNIX, où vous spécifiez la lecture, l’écriture et l’exécution (rwx) pour trois classes d’utilisateurs distinctes : propriétaire, groupe et autres.
   * **Autorisations attribuées** correspond aux ACL POSIX, qui vous permettent de définir des autorisations pour des utilisateurs ou des groupes nommés spécifiques ne se limitant pas au propriétaire ou au groupe du fichier. 
     
     Pour plus d'informations, consultez la page [ACL HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Pour plus d’informations sur l’implémentation des ACL dans Data Lake Store, consultez [Contrôle d’accès dans Data Lake Store](data-lake-store-access-control.md).
4. Cliquez sur l’icône **Ajouter** pour ouvrir le panneau **Affecter des autorisations**. Dans ce panneau, cliquez sur **Sélectionner un utilisateur ou un groupe**, puis, dans le panneau **Sélectionner un utilisateur ou un groupe**, recherchez le groupe de sécurité créé précédemment dans Azure Active Directory. Si la liste de groupes est longue, utilisez la zone de texte en haut pour filtrer par nom de groupe. Cliquez sur le groupe que vous souhaitez ajouter, puis cliquez sur **Sélectionner**.
   
    ![Ajouter un groupe](./media/data-lake-store-secure-data/adl.acl.3.png "Ajouter un groupe")
5. Cliquez sur **Sélectionner des autorisations**, sélectionnez les autorisations, puis indiquez si celles-ci doivent être appliquées de manière récursive et affectées en tant qu’ACL d’accès, ACL par défaut ou les deux. Cliquez sur **OK**.
   
    ![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.acl.4.png "Affecter des autorisations à un groupe")
   
    Pour plus d’informations sur les autorisations dans Data Lake Store et sur les ACL par défaut ou d’accès, consultez [Contrôle d’accès dans Azure Data Lake Store](data-lake-store-access-control.md).
6. Une fois que vous avez cliqué sur **OK** dans le panneau **Sélectionner des autorisations**, le groupe qui vient d’être ajouté et les autorisations associées sont répertoriés dans le panneau **Accès**.
   
    ![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.acl.5.png "Affecter des autorisations à un groupe")
   
   > [!IMPORTANT]
   > Dans la version actuelle, vous pouvez avoir jusqu’à 28 entrées sous **Autorisations attribuées**. Pour ajouter plus de 28 utilisateurs, vous devez créer des groupes de sécurité, ajouter les utilisateurs aux groupes de sécurité et fournir à ces groupes de sécurité un accès au compte Data Lake Store.
   > 
   > 
7. Si nécessaire, vous pouvez également modifier les autorisations d'accès après avoir ajouté le groupe. Cochez ou décochez la case de chaque type d'autorisation (lecture, écriture, exécution) selon que vous souhaitez retirer ou affecter cette autorisation au groupe de sécurité. Cliquez sur **Enregistrer** pour enregistrer les modifications, ou sur **Ignorer** pour annuler les modifications.

## <a name="set-ip-address-range-for-data-access"></a>Définir la plage d’adresses IP pour accéder aux données
Azure Data Lake Store vous permet de mieux verrouiller l’accès à votre magasin de données au niveau du réseau. Vous pouvez activer le pare-feu, spécifier une adresse IP ou définir une plage d’adresses IP pour vos clients approuvés. Une fois qu’il est activé, seuls les clients qui possédant des adresses IP dans la plage définie peuvent se connecter au magasin.

![Paramètres de pare-feu et accès IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Paramètres de pare-feu et accès IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Supprimer les groupes de sécurité d'un compte Azure Data Lake Store
Lorsque vous supprimez des groupes de sécurité de comptes Azure Data Lake Store, vous ne modifiez que l’accès aux opérations de gestion sur le compte à l’aide du portail Azure et des API Azure Resource Manager.  

L’accès aux données n’est pas modifié et est toujours géré par les ACL d’accès,  exception faite des utilisateurs/groupes du rôle Propriétaires.  Les utilisateurs/groupes supprimés du rôle Propriétaires ne sont plus super utilisateurs et leur accès bascule sur les paramètres d’ACL d’accès. 

1. Dans le panneau de votre compte Data Lake Store, cliquez sur **Contrôle d’accès (IAM)**. 
   
    ![Affecter un groupe de sécurité à un compte Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter un groupe de sécurité à un compte Azure Data Lake")
2. Dans le panneau **Contrôle d’accès (IAM)**, cliquez sur les groupes de sécurité à supprimer. Cliquez sur **Supprimer**.
   
    ![Groupe de sécurité supprimé](./media/data-lake-store-secure-data/adl.remove.group.png "Groupe de sécurité supprimé")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Supprimer les ACL d'un groupe de sécurité du système de fichiers Azure Data Lake Store
Quand vous supprimez les ACL d’un groupe de sécurité du système de fichiers Azure Data Lake Store, vous modifiez l’accès aux données du Data Lake Store.

1. Dans le panneau de votre compte Data Lake Store, cliquez sur **Explorateur de données**.
   
    ![Créer des répertoires dans un compte Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer des répertoires dans un compte Data Lake")
2. Dans le panneau **Explorateur de données**, cliquez sur le dossier pour lequel vous souhaitez supprimer l’ACL, puis cliquez sur **Accès**. Pour supprimer des ACL pour un fichier, vous devez tout d’abord cliquer sur le fichier pour en afficher un aperçu, puis cliquer sur **Accès** dans le panneau **Aperçu du fichier**. 
   
    ![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")
3. Dans le panneau **Accès**, cliquez sur le groupe de sécurité à supprimer. Dans le panneau **Détails de l’accès**, cliquez sur **Supprimer**.
   
    ![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.remove.acl.png "Affecter des autorisations à un groupe")

## <a name="see-also"></a>Voir aussi
* [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
* [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Prise en main de Data Lake Store avec PowerShell](data-lake-store-get-started-powershell.md)
* [Prise en main de Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Accéder aux journaux de diagnostic de Data Lake Store](data-lake-store-diagnostic-logs.md)

