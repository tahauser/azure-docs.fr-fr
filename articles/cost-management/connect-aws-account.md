---
title: Connecter un compte Amazon Web Services à Azure Cost Management | Microsoft Docs
description: Connectez un compte Amazon Web Services pour afficher les données de coût et d’utilisation dans les référentiels Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Connecter un compte Amazon Web Services

Vous avez deux possibilités pour connecter votre compte Amazon Web Services (AWS) à Azure Cost Management. Vous pouvez le connecter avec un rôle IAM ou avec un compte utilisateur IAM en lecture seule. Le rôle IAM est recommandé, car il vous permet de déléguer l’accès avec des autorisations définies pour les entités approuvées. Le rôle IAM ne vous oblige pas à partager les clés d’accès sur le long terme. Une fois le compte AWS connecté à Cost Management, les données de coût et d’utilisation sont disponibles dans les rapports de Cost Management. Ce document vous présente les deux options disponibles.

Pour plus d’informations sur les identités AWS IAM, consultez [Identités (utilisateurs, groupes et rôles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>Accès en fonction du rôle AWS

Les sections suivantes vous guident lors de la création d’un rôle IAM en lecture seule pour fournir l’accès à Cost Management.

### <a name="get-your-cost-management-account-external-id"></a>Obtention de l’ID externe de votre compte Cost Management

La première étape consiste à obtenir la phrase secrète de connexion unique à partir du portail Azure Cost Management. Il est utilisé dans AWS comme **ID externe**.

1. Ouvrez le portail Cloudyn à partir du portail Azure, ou accédez à la page [https://azure.cloudyn.com](https://azure.cloudyn.com) et connectez-vous.
2. Cliquez sur **Paramètres** (icône de roue dentée), puis sélectionnez **Comptes cloud**.
3. Dans Gestion de comptes, sélectionnez l’onglet **Comptes AWS**, puis cliquez sur **Ajouter un nouveau +**.
4. Dans la boîte de dialogue **Ajouter un compte AWS**, copiez l’**ID externe** et enregistrez cette valeur pour les étapes de création du rôle AWS de la section suivante. Dans l’image suivante, l’exemple d’ID est _Cloudyn_. Votre ID est différent.  
    ![ID externe](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Ajouter un accès en lecture seule en fonction du rôle AWS

1. Connectez-vous à la console AWS à l’adresse https://console.aws.amazon.com/iam/home et sélectionnez **Rôles**.
2. Cliquez sur **Créer un rôle**, puis sélectionnez **Autre compte AWS**.
3. Collez l’identité `432263259397` dans le champ **ID de compte**. Cet ID de compte est le compte de collecteur de données Cost Management que vous devez utiliser.
4. En regard d’**Options**, sélectionnez **Nécessite un ID externe**, puis collez la valeur copiée précédemment dans le champ **ID externe** et cliquez sur **Suivant : autorisations**.  
    ![Créer un rôle](./media/connect-aws-account/create-role01.png)
5. Sous **Joindre des stratégies d’autorisation**, dans la recherche de filtre **Type de stratégie**, tapez `ReadOnlyAccess`, sélectionnez **ReadOnlyAccess**, puis cliquez sur **Suivant : révision**.  
    ![Accès en lecture seule](./media/connect-aws-account/readonlyaccess.png)
6. Sur la page Révision, vérifiez que vos sélections sont correctes, puis tapez un **nom de rôle**. Par exemple, *Azure-Cost-Mgt*. Entrez une **description du rôle**. Par exemple, _Attribution de rôle pour Azure Cost Management_, puis cliquez sur **Créer un rôle**.
7. Dans la liste **Rôles**, cliquez sur le rôle que vous avez créé et copiez la valeur du champ **Role ARN** à partir de la page de résumé. Utilisez la valeur du champ Role ARN ultérieurement lorsque vous enregistrerez votre configuration dans Azure Cost Management.  
    ![Role ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Configurer l’accès en fonction du rôle AWS IAM dans Cost Management

1. Ouvrez le portail Cloudyn à partir du portail Azure, ou accédez à la page https://azure.cloudyn.com/ et connectez-vous.
2. Cliquez sur **Paramètres** (icône de roue dentée), puis sélectionnez **Comptes cloud**.
3. Dans Gestion de comptes, sélectionnez l’onglet **Comptes AWS**, puis cliquez sur **Ajouter un nouveau +**.
4. Dans **Nom de compte**, saisissez un nom pour le compte.
5. En regard de **Type d’accès**, sélectionnez **Rôle IAM**.
6. Dans le champ **Role ARN**, collez la valeur que vous avez copiée précédemment, puis cliquez sur **Enregistrer**.  
    ![État du compte AWS](./media/connect-aws-account/aws-account-status01.png)

Votre compte AWS apparaît dans la liste des comptes. L’**ID de propriétaire** répertorié correspond à la valeur du champ Role ARN. L’**état de votre compte** doit être accompagné d’une coche verte.

Cost Management commence la collecte des données et le remplissage des rapports. Toutefois, certains rapports d’optimisation peuvent nécessiter la collecte de données pendant quelques jours avant que des recommandations précises ne soient générées.

## <a name="aws-user-based-access"></a>Accès en fonction de l’utilisateur AWS

Les sections suivantes vous guident lors de la création d’un utilisateur en lecture seule pour fournir l’accès à Cost Management.

### <a name="add-aws-read-only-user-based-access"></a>Ajouter un accès en lecture seule en fonction de l’utilisateur AWS

1. Connectez-vous à la console AWS à l’adresse https://console.aws.amazon.com/iam/home et sélectionnez **Utilisateurs**.
2. Cliquez sur **Add User**.
3. Dans le champ **Nom d’utilisateur**, tapez un nom d’utilisateur.
4. Sous **Type d’accès**, sélectionnez **Accès par programme** et cliquez sur **Suivant : autorisations**.  
    ![Ajouter un utilisateur](./media/connect-aws-account/add-user01.png)
5. Pour les autorisations, sélectionnez **Attacher directement les stratégies existantes**.
6. Sous **Joindre des stratégies d’autorisation**, dans la recherche de filtre **Type de stratégie**, tapez `ReadOnlyAccess`, sélectionnez **ReadOnlyAccess**, puis cliquez sur **Suivant : révision**.  
    ![Sélectionner les autorisations pour l’utilisateur](./media/connect-aws-account/set-permission-for-user.png)
7. Sur la page Révision, vérifiez que vos sélections sont correctes, puis cliquez sur **Créer un utilisateur**.
8. Sur la page Terminé, votre ID de clé d’accès et la clé d’accès secrète sont affichés. Ces informations vous permettent de configurer l’inscription dans Cost Management.
9. Cliquez sur **Télécharger .csv** et enregistrez le fichier credentials.csv dans un emplacement sécurisé.  
    ![Télécharger les informations d’identification](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Configurer l’accès en fonction de l’utilisateur AWS IAM dans Cost Management

1. Ouvrez le portail Cloudyn à partir du Portail Azure, ou accédez à la page https://azure.cloudyn.com/ et connectez-vous.
2. Cliquez sur **Paramètres** (icône de roue dentée), puis sélectionnez **Comptes cloud**.
3. Dans Gestion de comptes, sélectionnez l’onglet **Comptes AWS**, puis cliquez sur **Ajouter un nouveau +**.
4. Sous **Nom de compte**, tapez un nom de compte.
5. En regard de **Type d’accès**, sélectionnez **Utilisateur IAM**.
6. Sous **Clé d’accès**, collez la valeur du champ **Access key ID** à partir du fichier credentials.csv.
7. Sous **Clé secrète**, collez la valeur du champ **Secret access key** à partir du fichier credentials.csv, puis cliquez sur **Enregistrer**.  
    ![État du compte utilisateur AWS](./media/connect-aws-account/aws-user-account-status.png)

Votre compte AWS apparaît dans la liste des comptes. L’**état de votre compte** doit être accompagné d’une coche verte.

Cost Management commence la collecte des données et le remplissage des rapports. Toutefois, certains rapports d’optimisation peuvent nécessiter la collecte de données pendant quelques jours avant que des recommandations précises ne soient générées.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Azure Cost Management, poursuivez avec le didacticiel [Examen de l’utilisation et des coûts](tutorial-review-usage.md) pour Cost Management.
