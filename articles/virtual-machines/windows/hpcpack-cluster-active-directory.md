---
title: "Cluster HPC Pack avec Azure Active Directory | Microsoft Docs"
description: "Apprenez à intégrer un cluster Microsoft HPC Pack 2016 dans Azure avec Azure Active Directory"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Gérer un cluster HPC Pack dans Azure avec Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) prend en charge l’intégration avec [Azure Active Directory](../../active-directory/index.md) (Azure AD) pour les administrateurs qui déploient un cluster HPC Pack dans Azure.



Effectuez la procédure décrite dans cet article pour les tâches de haut niveau suivantes : 
* Intégrer manuellement votre cluster HPC Pack avec votre locataire Azure AD
* Gérer et planifier des travaux dans votre cluster HPC Pack dans Azure 

L’intégration d’une solution de cluster HPC Pack avec Azure AD suit la procédure standard permettant d’intégrer d’autres applications et services. Cet article suppose que vous êtes familiarisé avec la gestion des utilisateurs de base dans Azure AD. Pour plus d’informations et du contexte, voir la [documentation Azure Active Directory](../../active-directory/index.md) et les sections suivantes.

## <a name="benefits-of-integration"></a>Avantages de l’intégration


Azure Active Directory (Azure AD) est le service de gestion des annuaires et des identités basé sur le cloud mutualisé qui fournit un accès via l’authentification unique (SSO) aux solutions cloud.

L’intégration d’un cluster HPC Pack avec Azure AD peut vous aider à atteindre les objectifs suivants :

* Supprimer le contrôleur de domaine Active Directory traditionnel du cluster HPC Pack. Cela peut aider à réduire les coûts de maintenance du cluster si ce n’est pas nécessaire pour votre entreprise, ainsi qu’à accélérer le déploiement.
* Exploiter les avantages suivants fournis par Azure AD :
    *   Authentification unique 
    *   Utilisation d’une identité AD locale pour le cluster HPC Pack dans Azure 

    ![Environnement Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Composants requis
* **Cluster HPC Pack 2016 déployé dans des machines virtuelles** : pour connaître les étapes, consultez [Déployer un cluster HPC Pack 2016 dans Azure](hpcpack-2016-cluster.md). Vous avez besoin du nom DNS du nœud principal et des informations d’identification d’un administrateur de cluster pour effectuer les étapes décrites dans cet article.

  > [!NOTE]
  > L’intégration d’Azure Active Directory n’est pas prise en charge dans les versions de HPC Pack antérieures au HPC Pack 2016.



* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows ou Windows Server pour exécuter des utilitaires clients HPC Pack. Si vous souhaitez uniquement utiliser le portail web de HPC Pack ou l’API REST pour envoyer des travaux, vous pouvez utiliser l’ordinateur client de votre choix.

* **Utilitaires clients HPC Pack** : installez-les sur l’ordinateur client à l’aide du package d’installation gratuit disponible dans le Centre de téléchargement Microsoft.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Étape 1 : Inscrire le serveur de cluster HPC avec votre locataire Azure AD
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Si votre compte vous permet d’accéder à plus d’un client Azure AD, cliquez sur votre compte dans le coin supérieur droit. Définissez votre session de portail sur le client souhaité. Vous devez être autorisé à accéder aux ressources du répertoire. 
3. Cliquez sur **Azure Active Directory** dans le volet de navigation Services à gauche, cliquez sur **Utilisateurs et groupes**, et vérifiez qu’il existe déjà des comptes d’utilisateur créés ou configurés.
4. Dans **Azure Active Directory**, cliquez sur **Inscriptions d’application** > **Nouvelle inscription d’application**. Entrez les informations suivantes :
    * **Nom** : HPCPackClusterServer.
    * **Type d’application** : Sélectionnez **Application web/API**
    * **URL de connexion** : entrez l’URL de base de l’exemple, qui est par défaut `https://hpcserver`.
    * Cliquez sur **Créer**.
5. Une fois l’application ajoutée, sélectionnez-la dans la liste **Inscriptions d’application**. Puis cliquez sur **Paramètres** > **Propriétés**. Entrez les informations suivantes :
    * Sélectionnez **Oui** pour **Mutualisé**.
    * Modifiez **URI ID d’application** sur `https://<Directory_name>/<application_name>`. Remplacez `<Directory_name`> par le nom complet de votre locataire Azure AD, par exemple `hpclocal.onmicrosoft.com`, puis remplacez `<application_name>` par le nom que vous avez précédemment choisi.
6. Cliquez sur **Enregistrer**. Une fois l’enregistrement terminé, dans la page de l’application, cliquez sur **Manifeste**. Modifiez le manifeste en recherchant le paramètre `appRoles` et en ajoutant le rôle d’application suivant, puis en cliquant sur **Enregistrer** :

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. Dans **Azure Active Directory**, cliquez sur **Applications d’entreprise** > **Toutes les applications**. Sélectionnez **HPCPackClusterServer** dans la liste.
8. Cliquez sur **Propriétés** et modifiez **Affectation d’utilisateurs requise** sur **Oui**. Cliquez sur **Enregistrer**.
9. Cliquez sur **Utilisateurs et groupes** > **Ajouter un utilisateur**. Sélectionnez un utilisateur et sélectionnez un rôle, puis cliquez sur **Affecter**. Affectez l’un des rôles disponibles (HpcUsers ou HpcAdminMirror) à l’utilisateur. Répétez cette étape avec des utilisateurs supplémentaires dans le répertoire. Pour plus d’informations générales sur les utilisateurs de cluster, consultez [Gestion des utilisateurs de clusters](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Étape 2 : Inscrire le client de cluster HPC avec votre locataire Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Si votre compte vous permet d’accéder à plus d’un client Azure AD, cliquez sur votre compte dans le coin supérieur droit. Définissez votre session de portail sur le client souhaité. Vous devez être autorisé à accéder aux ressources du répertoire. 
3. Dans **Azure Active Directory**, cliquez sur **Inscriptions d’application** > **Nouvelle inscription d’application**. Entrez les informations suivantes :

    * **Nom** : HPCPackClusterClient.    
    * **Type d’application** : Sélectionnez **Native**
    * **URI de redirection** - `http://hpcclient`
    * Cliquez sur **Créer**

4. Une fois l’application ajoutée, sélectionnez-la dans la liste **Inscriptions d’application**. Copiez la valeur **ID d’application** et enregistrez-la. Vous en aurez besoin plus tard lors de la configuration de votre application.

5. Cliquez sur **Paramètres** > **Autorisations requises** > **Ajouter** > **Sélectionner une API**. Recherchez et sélectionnez l’application HpcPackClusterServer (créée à l’étape 1).

6. Sur la page **Activer l’accès**, sélectionnez **Accéder à HpcClusterServer**. Cliquez ensuite sur **Terminé**.


## <a name="step-3-configure-the-hpc-cluster"></a>Étape 3 : Configurer le cluster HPC

1. Connectez-vous au nœud principal HPC Pack 2016 dans Azure.

2. Démarrez PowerShell HPC.

3. Exécutez la commande suivante :

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    où

    * `AADTenant` spécifie le nom de locataire Azure AD, tel que `hpclocal.onmicrosoft.com`.
    * `AADClientAppId` spécifie l’ID de l’application créée à l’étape 2.

4. Effectuez une des actions suivantes, selon la configuration du nœud principal :

    * Dans un cluster HPC Pack avec un seul nœud principal, redémarrez le service HpcScheduler.

    * Dans un cluster HPC Pack avec plusieurs nœuds principaux, exécutez les commandes PowerShell suivantes sur le nœud principal pour redémarrer le service HpcSchedulerStateful :

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Étape 4 : Gérer et envoyer des travaux à partir du client

Pour installer les utilitaires clients HPC Pack sur votre ordinateur, téléchargez les fichiers d’installation (installation complète) de HPC Pack 2016 à partir du Centre de téléchargement Microsoft. Au début de l’installation, choisissez l’option d’installation des **utilitaires du client HPC Pack**.

Pour préparer l’ordinateur client, installez le certificat utilisé au cours de l’[installation du cluster HPC](hpcpack-2016-cluster.md) sur l’ordinateur client. Utilisez les procédures de gestion de certificats Windows standard pour installer le certificat public dans le magasin **Certificats - Utilisateur actuel** > **Autorités de certification racines de confiance**. 

Vous pouvez maintenant exécuter les commandes HPC Pack ou utiliser l’interface utilisateur graphique du Gestionnaire de travaux HPC Pack pour soumettre et gérer des travaux de cluster en utilisant le compte Azure AD. Pour les options d’envoi de travaux, consultez [Envoyer des travaux HPC vers un cluster HPC Pack déployé dans Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Lorsque vous essayez de vous connecter au cluster HPC Pack dans Azure pour la première fois, une fenêtre contextuelle s’affiche. Entrez vos informations d’identification Azure AD pour vous connecter. Le jeton est ensuite mis en cache. Les connexions ultérieures au cluster dans Azure utilisent le jeton en cache, sauf en cas de modification de l’authentification ou d’effacement du cache.
>
  
Par exemple, après avoir effectué les étapes précédentes, vous pouvez interroger les travaux à partir d’un client local comme suit :

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Applets de commande utiles pour l’envoi de travaux avec l’intégration Azure AD 

### <a name="manage-the-local-token-cache"></a>Gérer le cache local de jetons

HPC Pack 2016 fournit les applets de commande HPC PowerShell suivantes pour gérer le cache local de jetons. Ces applets de commande sont utiles pour envoyer des travaux en mode non interactif. Voir l’exemple suivant :

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Définir les informations d’identification pour envoyer des travaux à l’aide du compte Azure AD 

Parfois, vous pouvez être amené à exécuter le travail sous l’utilisateur du cluster HPC (pour un cluster HPC joint à un domaine, exécuter en tant qu’utilisateur d’un domaine ; pour un cluster HPC non joint à un domaine, exécuter en tant qu’utilisateur local sur le nœud principal).

1. Exécutez les commandes suivantes pour définir les informations d’identification :

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Envoyez ensuite le travail comme suit. Le travail et la tâche s’exécutent sous $localUser sur les nœuds de calcul.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Si `–Credential` n’est pas spécifié avec `Submit-HpcJob`, le travail ou la tâche s’exécute sous un utilisateur local mappé en tant que compte Azure AD. Le cluster HPC crée un utilisateur local avec le même nom que le compte Azure AD pour exécuter la tâche.
    
3. Définissez les données étendues pour le compte Azure AD. Ceci est utile lors de l’exécution d’un travail MPI sur des nœuds Linux à l’aide du compte Azure AD.

   * Définir les données étendues pour le compte Azure AD

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Définir les données étendues et exécuter en tant qu’utilisateur du cluster HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

