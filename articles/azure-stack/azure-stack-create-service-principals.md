---
title: "Créer un principal de service pour Azure Stack | Microsoft Docs"
description: "Explique comment créer un principal de service utilisable avec le contrôle d’accès en fonction du rôle dans Azure Resource Manager pour gérer l’accès aux ressources."
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2018
ms.author: mabrigg
ms.openlocfilehash: 64c424ee7045ae20b3fba6433166039580387d76
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="provide-applications-access-to-azure-stack"></a>Fournir l’accès des applications à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Lorsqu’une application a besoin d’accès pour déployer ou configurer des ressources par le biais d’Azure Resource Manager dans Azure Stack, créez un principal de service qui constitue l’information d’identification de votre application.  Vous pouvez ensuite lui déléguer uniquement les autorisations nécessaires.  

Par exemple, vous avez peut-être un outil de gestion de la configuration qui utilise Azure Resource Manager pour inventorier les ressources Azure.  Dans ce scénario, vous pouvez créer un principal de service, lui accorder le rôle de lecteur et limiter l’outil de gestion de la configuration à un accès en lecture seule. 

De préférence, utilisez des principaux de service plutôt que d’exécuter l’application avec vos propres informations d’identification, car :

* Vous pouvez attribuer au principal de service des autorisations différentes des vôtres. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire.
* Il est inutile de modifier les informations d’identification de l’application si vos responsabilités évoluent.
* Vous pouvez utiliser un certificat pour automatiser l’authentification lors de l’exécution d’un script sans assistance.  

## <a name="getting-started"></a>Prise en main

Selon la façon dont vous avez déployé Azure Stack, commencez par créer un principal de service.  Ce document vous guide tout au long de la création d’un principal de service à la fois pour [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) et pour [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Une fois que vous avez créé le principal de service, une série d’étapes communes à AD FS et à Azure Active Directory permet de [déléguer des autorisations](azure-stack-create-service-principals.md#assign-role-to-service-principal) à ce rôle.     

## <a name="create-service-principal-for-azure-ad"></a>Créer un principal de service pour Azure AD

Si vous avez déployé Azure Stack avec Azure AD comme magasin d’identités, vous pouvez créer des principaux de service de la même façon que pour Azure.  Cette rubrique explique comment suivre ces étapes sur le portail.  Vérifiez que vous disposez des [autorisations Azure AD requises](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) avant de commencer.

### <a name="create-service-principal"></a>Créer un principal du service
Dans cette section, vous créez une application (principal de service) dans Azure AD qui représente votre application.

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** > **Enregistrement d’applications** > **Ajouter**.   
3. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web / API** ou **Native** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **Créer**.

Vous avez créé un principal de service pour votre application.

### <a name="get-credentials"></a>Récupérer les informations d’identification
Si vous vous connectez par programmation, utilisez l’ID de votre application et une clé d’authentification. Pour obtenir ces valeurs, procédez comme suit :

1. Dans **Inscriptions d’applications** dans Active Directory, sélectionnez votre application.

2. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Les applications de la section [Exemples d’applications](#sample-applications) font référence à cette valeur en tant qu’ID de client.

     ![ID CLIENT](./media/azure-stack-create-service-principal/image12.png)
3. Pour générer une clé d’authentification, sélectionnez **Clés**.

4. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

![clé enregistrée](./media/azure-stack-create-service-principal/image15.png)


Lorsque c’est fait, [attribuez un rôle à votre application](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Créer un principal de service pour AD FS
Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour créer un principal de service, attribuer un rôle pour l’accès et vous connecter à partir de PowerShell avec cette identité.

Le script est exécuté depuis le point de terminaison privilégié sur une machine virtuelle ERCS.


Requirements:
- Un certificat est nécessaire.

**Paramètres**

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :


|Paramètre|Description|exemples|
|---------|---------|---------|
|NOM|Nom du compte SPN|MyAPP|
|ClientCertificates|Tableau d’objets de certificat|Certificat X509|
|ClientRedirectUris<br>(facultatif)|URI de redirection de l’application|         |

**Exemple**

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les commandes suivantes :

   > [!NOTE]
   > Cet exemple crée un certificat auto-signé. Quand vous exécutez ces commandes dans un déploiement de production, utilisez Get-Certificate pour récupérer l’objet de certificat pour le certificat que vous voulez utiliser.

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. Quand Automation a terminé, il affiche les informations nécessaires pour utiliser le SPN. 

   Par exemple : 

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>Attribuer un rôle
Une fois le principal de service créé, vous devez [lui attribuer un rôle](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Se connecter avec PowerShell
Une fois que vous avez attribué un rôle, vous pouvez vous connecter à Azure Stack à l’aide du principal de service avec la commande suivante :

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Attribuer un rôle au principal de service
Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Sur le portail Azure Stack, accédez au niveau d’étendue que vous souhaitez attribuer à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**. Vous pouvez également sélectionner un groupe de ressources ou une ressource.

2. Sélectionnez l’abonnement (groupe de ressources ou ressource) auquel l’application doit être affectée.

     ![sélectionner l’abonnement pour l’affectation](./media/azure-stack-create-service-principal/image16.png)

3. Sélectionnez **Contrôle d’accès (IAM)**.

     ![sélectionner l’accès](./media/azure-stack-create-service-principal/image17.png)

4. Sélectionnez **Ajouter**.

5. Sélectionnez le rôle que vous souhaitez affecter à l’application.

6. Recherchez votre application et sélectionnez-la.

7. Sélectionnez **OK** pour finaliser l’affectation du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

Maintenant que vous avez créé un principal de service et que vous lui avez attribué un rôle, vous pouvez commencer à l’utiliser au sein de votre application pour accéder aux ressources Azure Stack.  

## <a name="next-steps"></a>étapes suivantes

[Ajouter des utilisateurs pour ADFS](azure-stack-add-users-adfs.md)
[Gérer les autorisations des utilisateurs](azure-stack-manage-permissions.md)