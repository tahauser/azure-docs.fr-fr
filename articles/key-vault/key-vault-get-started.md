---
title: "Prise en main d’Azure Key Vault | Microsoft Docs"
description: "Ce didacticiel va vous aider à démarrer avec Azure Key Vault pour créer un conteneur renforcé dans Azure afin de stocker et gérer des clés de chiffrement et les secrets dans Azure."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/20/2017
ms.author: barclayn
ms.openlocfilehash: 1b70802945b710059e93b54607996ccf74510d1f
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-azure-key-vault"></a>Prise en main du coffre de clés Azure
Cet article vous aide à prendre en main Azure Key Vault avec PowerShell et vous explique en détail :
- Comment créer un conteneur renforcé (un coffre) dans Azure.
- Comment utiliser Key Vault pour stocker et gérer les clés de chiffrement et les secrets dans Azure.
- Comment une application peut utiliser ces clés ou ces secrets.

Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Cet article n’explique pas comment écrire une application Azure. Vous pouvez utiliser l’[exemple d’application Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) pour suivre les procédures décrites ici.

Pour connaître la marche à suivre avec l’interface de ligne de commande interplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Configuration requise
Avant d’aller plus loin, vérifiez que vous disposez des éléments suivants :

- **Un abonnement Azure**. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Azure PowerShell** **version 1.1.0 ou ultérieure**. Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console Azure PowerShell, entrez `(Get-Module azure -ListAvailable).Version`. Si vous utilisez Azure PowerShell version 0.9.1 à 0.9.8, vous pouvez toujours utiliser ce didacticiel en y apportant quelques changements mineurs. Par exemple, vous devez utiliser la commande `Switch-AzureMode AzureResourceManager` ; certaines commandes Azure Key Vault ont également changé. Pour obtenir la liste des applets de commande Azure Key Vault pour les versions 0.9.1 à 0.9.8, consultez la page [Applets de commande Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
- **Une application qui peut être configurée pour utiliser Key Vault**. Un exemple d’application est disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Pour obtenir des instructions, consultez le fichier **Lisez-moi** fourni.

>[!NOTE]
Cet article suppose une connaissance élémentaire de PowerShell et d’Azure. Pour plus d’informations sur PowerShell, consultez [Prise en main de Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande **Get-Help** .

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Par exemple, pour obtenir de l’aide pour l’applet de commande **Login-AzureRmAccount** , tapez :

```PowerShell
Get-Help Login-AzureRmAccount -Detailed
```

Vous pouvez également consulter les articles suivants afin de vous familiariser avec le modèle de déploiement Azure Resource Manager dans Azure PowerShell :

* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Se connecter à vos abonnements
Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

```PowerShell
Login-AzureRmAccount
```

>[!NOTE]
 Si vous utilisez une instance spécifique d’Azure, utilisez le paramètre -Environment. Par exemple : 
 ```powershell
 Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements et souhaitez spécifier un abonnement spécifique à utiliser pour Azure Key Vault, tapez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Ensuite, pour indiquer l’abonnement, tapez ce qui suit :

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Création d’un groupe de ressources
Lorsque vous utilisez Azure Resource Manager, toutes les ressources associées sont créées au sein d’un groupe de ressources. Nous allons créer un groupe de ressources nommé **ContosoResourceGroup** pour ce didacticiel :

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Création d’un coffre de clés
Utilisez l’applet de commande [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) pour créer un coffre de clés. Cette applet de commande a trois paramètres obligatoires : un **nom de groupe de ressources**, un **nom de coffre de clés** et l’**emplacement géographique**.

Par exemple, si vous utilisez :
- Le nom de coffre **ContosoKeyVault**.
- Le nom de groupe de ressources **ContosoResourceGroup**.
- L’emplacement **Est des États-Unis**.

Vous devez taper :

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Sortie après l’exécution de la commande de création du coffre de clés](./media/key-vault-get-started/output-after-creating-keyvault.png)

La sortie de cette applet de commande affiche les propriétés du coffre de clés que vous avez créé. Les deux propriétés les plus importantes sont :

* **Nom du coffre** : dans l’exemple, il s’agit de **ContosoKeyVault**. Vous allez utiliser ce nom pour les autres applets de commande Key Vault.
* **URI du coffre** : dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Votre compte Azure est pour l’instant le seul autorisé à effectuer des opérations sur ce coffre de clés.

> [!NOTE]
> Quand vous essayez de créer votre coffre de clés, il se peut que vous receviez le message d’erreur **L’abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.KeyVault »**. Si ce message s’affiche, exécutez `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Une fois l’inscription effectuée avec succès, vous pouvez exécuter à nouveau la commande New-AzureRmKeyVault. Pour plus d’informations, consultez [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Ajout d’une clé ou d’un secret au coffre de clés
Vous pouvez avoir besoin d’interagir avec Key Vault et les clés ou les secrets de différentes manières.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault génère une clé protégée par un logiciel

Si vous souhaitez qu’Azure Key Vault crée une clé protégée par un logiciel pour vous, utilisez l’applet de commande [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) et tapez :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
Pour afficher l’URI de cette clé, tapez :
```powershell
$key.id
```

Vous pouvez utiliser l’URI d’une clé que vous avez créée ou chargée dans Azure Key Vault pour la référencer. Vous pouvez utiliser **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** pour obtenir la version actuelle et **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** pour obtenir cette version spécifique.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importation d’un fichier PFX existant dans Azure Key Vault

Si vous souhaitez charger un fichier PFX qui contient des clés existantes dans Azure Key Vault, la procédure à suivre est différente. Par exemple :
- Vous disposez d’une clé protégée par un logiciel dans un fichier PFX.
- Le fichier PFC est nommé softkey.pfx. 
- Le fichier est stocké sur le lecteur C.

Vous pouvez taper :

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Tapez ensuite la commande suivante pour importer la clé à partir du fichier PFX, ce qui protège la clé par logiciel dans le service Key Vault :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Pour afficher l’URI de cette clé, tapez :

```powershell
$Key.id
```
Pour afficher votre clé, tapez : 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Si vous souhaitez afficher les propriétés du fichier PFX dans le portail, vous verrez quelque chose de similaire à l’image ci-dessous.

![Apparence d’un certificat dans le portail](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Pour ajouter un secret à Azure Key Vault

Pour ajouter un secret au coffre, à savoir un mot de passe nommé SQLPassword avec la valeur Pa$$w0rd dans Azure Key Vault, commencez par convertir la valeur Pa$$w0rd en chaîne sécurisée en tapant :

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Ensuite, tapez :

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Vous pouvez maintenant référencer ce mot de passe que vous avez ajouté dans Azure Key Vault à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/SQLPassword** pour toujours obtenir la version actuelle, et **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** pour obtenir cette version spécifique.

Pour afficher l’URI de ce secret, tapez :

```powershell
$secret.Id
```
Pour afficher votre secret, tapez : `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. Vous pouvez également le secret dans le portail.

![secret](./media/key-vault-get-started/secret-value.png)

Pour afficher sous forme de texte brut la valeur contenue dans le secret :
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
À présent, votre coffre de clés et la clé/le secret sont prêts à être utilisés par les applications qui doivent recevoir les autorisations adéquates.  

## <a id="register"></a>Inscription d’une application auprès d’Azure Active Directory
Cette étape est généralement effectuée par un développeur et sur un ordinateur distinct. Elle n’est pas propre à Azure Key Vault. Pour obtenir des instructions détaillées sur l’inscription d’une application auprès d’Azure Active Directory, consultez l’article [Intégration d’applications dans Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) ou [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../azure-resource-manager/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Pour suivre le didacticiel, le compte, le coffre et l’application que vous inscrivez dans cette étape doivent tous se trouver dans le même répertoire Azure.


Les applications qui utilisent un coffre de clés doivent s’authentifier à l’aide d’un jeton à partir d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans Azure Active Directory. À la fin de l’inscription, le propriétaire de l’application obtient les valeurs suivantes :

- Un **ID d’application**. 
- Une **clé d’authentification** (également appelée secret partagé). 

L’application doit présenter ces deux valeurs à Azure Active Directory afin d’obtenir un jeton. La manière dont l’application est configurée pour cela dépend de l’application en question. Pour l’[exemple d’application Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), le propriétaire de l’application définit ces valeurs dans le fichier app.config.


Pour inscrire votre application auprès d’Azure Active Directory :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche de l’écran, cliquez sur **Inscriptions d’applications**. Si vous ne trouvez pas l’option Inscriptions d’applications, cliquez sur **Autres services** pour l’afficher.  
>[!NOTE]
Vous devez sélectionner le répertoire qui contient l’abonnement Azure avec lequel vous avez créé votre coffre de clés. 
3. Cliquez sur **Nouvelle inscription d’application**.
4. Dans le panneau **Créez**, renseignez un nom pour votre application, sélectionnez **APPLICATION WEB ET/OU API WEB** (option par défaut), puis spécifiez l’**URL DE CONNEXION** de votre application web. Si vous ne disposez pas de cette information, vous pouvez utiliser une URL factice pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com). Peu importe si ces sites existent. 

    ![Nouvelle inscription d’application](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Assurez-vous que vous avez sélectionné **APPLICATION WEB ET/OU API WEB**. Si ce n’est pas le cas, l’option **Clés** n’apparaîtra pas dans les paramètres.

5. Cliquez sur le bouton **Créer** .
6. Une fois l’inscription de l’application terminée, la liste des applications inscrites s’affiche. Recherchez l’application que vous venez d’inscrire et cliquez dessus.
7. Cliquez sur le panneau **Application inscrite** et copiez l’**ID de l’application**
8. Cliquez sur **Tous les paramètres**.
9. Dans le panneau **Paramètres**, cliquez sur **Clés**.
9. Saisissez une description dans la zone **Description de la clé**, sélectionnez une durée, puis cliquez sur **Enregistrer**. La page est actualisée et affiche à présent une valeur de clé. 
10. Vous utiliserez les informations **ID de l’application** et **Clé** à l’étape suivante pour définir les autorisations de votre coffre.

## <a id="authorize"></a>Autorisation de l’application à utiliser la clé ou le secret
Pour autoriser l’application à accéder à la clé ou au secret dans le coffre, utilisez l’applet de commande [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Par exemple, si le nom de votre coffre est **ContosoKeyVault** , que l'application que vous souhaitez autoriser a l'ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed et que vous souhaitez autoriser l'application à déchiffrer et à signer avec des clés dans le coffre, exécutez la commande suivante :

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Si vous souhaitez autoriser cette même application à lire les éléments secrets de votre coffre, exécutez la commande suivante :

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```

## <a id="HSM"></a>Utilisation d’un module de sécurité matériel (HSM)
Pour une meilleure garantie, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Les modules HSM bénéficient d’une validation FIPS 140-2 de niveau 2. Si cette exigence ne s’applique pas à vous, ignorez cette section et accédez à [Supprimer le coffre de clés et les clés et secrets associés](#delete).

Pour créer les clés protégées par HSM, vous devez utiliser le [niveau de service Azure Key Vault Premium qui prend en charge les clés protégées par HSM](https://azure.microsoft.com/pricing/free-trial/). En outre, notez que cette fonctionnalité n’est pas disponible pour Azure en Chine.

Lorsque vous créez le coffre de clés, ajoutez le paramètre **-SKU** :

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


Vous pouvez ajouter des clés protégées par logiciel (comme indiqué plus haut) et des clés protégées par HSM dans ce coffre de clés. Pour créer une clé protégée par HSM, définissez le paramètre **-Destination** sur « HSM » :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un fichier PFX sur votre ordinateur. Cette commande importe la clé dans les modules de sécurité matériels dans le service Key Vault :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

La commande suivante importe un package BYOK (Bring Your Own Key, Apporter votre propre clé). Ce scénario vous permet de générer votre clé dans votre module de sécurité matériel local et de la transférer vers les modules de sécurité matériels du service Key Vault, sans que la clé quitte la limite HSM :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Pour plus d'instructions sur la génération de ce package BYOK, consultez [Génération et transfert de clés protégées par HSM pour Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Suppression du coffre de clés et des clés/secrets associés
Si vous n’avez plus besoin du coffre de clés ni de la clé ou du secret qu’il contient, vous pouvez supprimer le coffre de clés à l’aide de l’applet de commande [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault) :

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Vous pouvez également supprimer un groupe de ressources Azure, qui inclut le coffre de clés et les autres ressources incluses dans ce groupe :

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Autres applets de commande Azure PowerShell
Autres commandes pouvant être utiles pour la gestion du coffre de clés Azure.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des clés et propriétés sélectionnées.
- `$Keys[0]`: cette commande affiche la liste complète des propriétés pour la clé spécifiée
- `Get-AzureKeyVaultSecret`: cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des secrets et des propriétés sélectionnés.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'` : exemple montrant comment supprimer une clé spécifique.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'` : exemple montrant comment supprimer un secret spécifique.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)
- Pour voir comment votre coffre de clés est utilisé, consultez l’article [Journalisation d’Azure Key Vault](key-vault-logging.md).
- Pour assurer le suivi d'un didacticiel sur l'utilisation d'Azure Key Vault dans une application web, consultez la page [Utilisation d'Azure Key Vault à partir d'une application web](key-vault-use-from-web-application.md).
- Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).
- Pour obtenir la liste des dernières applets de commande Azure PowerShell pour le coffre de clés Azure, consultez la page relative aux [applets de commande Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).