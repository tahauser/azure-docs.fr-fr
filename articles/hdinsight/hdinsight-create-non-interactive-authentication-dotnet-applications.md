---
title: "Créer des applications .NET d’authentification non interactives dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des applications .NET d’authentification non interactives dans Azure HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: b2b24747ce4ea8499c999c693f00fb09178d52b0
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Créer une application .NET HDInsight d’authentification non interactive
Vous pouvez exécuter votre application Microsoft .NET Azure HDInsight sous l’identité de l’application (non interactive) ou sous l’identité de l’utilisateur connecté à l’application (interactive). Cet article vous présente la création d’une application .NET d’authentification non interactive pour se connecter à Azure et gérer HDInsight. Pour voir un exemple d’application interactive, consultez [Se connecter à Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

À partir de votre application .NET non interactive, vous avez besoin des éléments suivants :

* Votre ID de locataire d’abonnement Azure (également appelée *ID répertoire*). Voir [Obtenir l’ID de locataire](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* L’ID client d’application Azure Active Directory (Azure AD). Voir [Créer une application Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) et la rubrique [Obtenir un ID d’application](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* La clé secrète de l’application Azure AD. Voir la rubrique sur [l’obtention de la clé d’authentification de l’application](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Prérequis
* Un cluster HDInsight. Voir le [didacticiel de prise en main](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Affecter un rôle à l’application Azure AD
Affectez à votre application Azure AD un [rôle](../active-directory/role-based-access-built-in-roles.md), pour lui accorder l’autorisation d’effectuer des actions. Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue. (Par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie que l’application peut lire le groupe de ressources et toutes les ressources qu’il contient). Dans ce didacticiel, vous définissez la portée au niveau du groupe de ressources. Pour en savoir plus, consultez [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).

**Pour ajouter le rôle propriétaire à l’application Azure AD**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Groupe de ressources**.
3. Sélectionnez le groupe de ressources qui contient le cluster HDInsight sur lequel vous exécuterez votre requête Hive plus tard dans ce didacticiel. Si vous avez un grand nombre de groupes de ressources, vous pouvez utiliser le filtre pour trouver celui qui que vous intéresse.
4. Dans le menu du groupe de ressources, sélectionnez **Contrôle d’accès (IAM)**.
5. Sous **Utilisateurs**, sélectionnez **Ajouter**.
6. Suivez les instructions pour ajouter le rôle Propriétaire à votre application Azure AD. Après avoir ajouté avec succès le rôle, l’application est répertoriée sous **Utilisateurs**, avec le rôle Propriétaire. 

## <a name="develop-an-hdinsight-client-application"></a>Développement d’une application cliente HDInsight

1. Création d’une application console C#.
2. Ajoutez les packages NuGet suivants :

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Exécutez le code suivant :

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Étapes suivantes
* [Créez une application Azure Active Directory et un principal du service dans le portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Découvrez comment [authentifier un principal du service à l’aide d’Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* En savoir plus sur le [Contrôle d’accès en fonction du rôle dans Azure (RBAC)](../active-directory/role-based-access-control-configure.md).
