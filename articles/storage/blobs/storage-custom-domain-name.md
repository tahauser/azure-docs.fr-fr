---
title: "Configurer un nom de domaine personnalisé pour un point de terminaison de stockage Blob Azure | Microsoft Docs"
description: Utilisez le portail Azure pour mapper votre propre nom canonique (CNAME) au point de terminaison de stockage Blob dans un compte de stockage Azure.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 2b776e8f40f6972a60f933b0104312b119439f38
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurer un nom de domaine personnalisé pour un point de terminaison de stockage Blob

Vous pouvez configurer un domaine personnalisé pour accéder à des données d’objets blob dans votre compte de stockage Azure. Le point de terminaison par défaut de stockage Blob est `<storage-account-name>.blob.core.windows.net`. Si vous mappez un domaine personnalisé et un sous-domaine comme **www.contoso.com** au point de terminaison d’objet blob de votre compte de stockage, vos utilisateurs peuvent alors accéder aux données de l’objet blob de votre compte de stockage à l’aide de ce domaine.

> [!IMPORTANT]
> Stockage Azure ne prend pas encore en charge HTTPS nativement avec des domaines personnalisés. Vous pouvez [utiliser Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md).
>

Le tableau suivant contient des exemples d’URL pour les données de l’objet blob situées dans un compte de stockage nommé **mystorageaccount**. Le domaine personnalisé enregistré pour le compte de stockage est **www.contoso.com**:

| Type de ressource | URL par défaut | URL du domaine personnalisé |
| --- | --- | --- |
| Compte de stockage | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Conteneur racine | http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Mappage de domaine direct ou intermédiaire

Il existe deux méthodes pour mapper votre domaine personnalisé au point de terminaison d’objet blob pour votre compte de stockage : le mappage CNAME direct et l’utilisation du sous-domaine intermédiaire *asverify*.

### <a name="direct-cname-mapping"></a>Mappage CNAME direct

La première méthode, qui est aussi la plus simple, consiste à créer un enregistrement de nom canonique (CNAME) qui mappe votre domaine personnalisé et un sous-domaine directement au point de terminaison d’objet blob. Un enregistrement CNAME est une fonctionnalité DNS (Domain Name System) qui mappe un domaine source à un domaine de destination. Dans ce cas, le domaine source est votre propre domaine personnalisé et un sous-domaine, par exemple *www.contoso.com*. Le domaine de destination est votre point de terminaison de service BLOB, par exemple *mystorageaccount.blob.core.windows.net*.

La méthode directe est couverte dans [Inscrire un domaine personnalisé](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mappage intermédiaire avec *asverify*

La seconde méthode utilise également des enregistrements CNAME, mais emploie tout d’abord un sous-domaine spécial reconnu par Azure pour éviter les temps d’arrêt : **asverify**.

Le processus consistant à mapper votre domaine personnalisé à un point de terminaison d’objet blob peut entraîner un problème d’indisponibilité de courte durée du domaine au moment où vous l’inscrivez dans le [portail Azure](https://portal.azure.com). Si votre domaine personnalisé prend en charge une application visée par un contrat de niveau de service (SLA) interdisant toute interruption de service, vous pouvez utiliser le sous-domaine Azure *asverify* en tant qu’étape d’inscription intermédiaire. Cette étape intermédiaire permet de garantir que les utilisateurs peuvent accéder à votre domaine pendant que vous procédez au mappage DNS.

La méthode intermédiaire est couverte dans [Inscrire un domaine personnalisé à l’aide du sous-domaine *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Inscrire un domaine personnalisé
Cette procédure permet d’inscrire un domaine personnalisé si vous n’êtes pas inquiet à l’idée que les utilisateurs ne puissent pas accéder au domaine pendant un court instant, ou si votre domaine personnalisé n’héberge actuellement aucune application. Avec Azure DNS, vous pouvez configurer un nom DNS personnalisé pour votre magasin d’objets blob. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

Si votre domaine personnalisé prend en charge une application qui ne peut supporter une interruption de service, suivez la procédure décrite à la rubrique [Inscrire un domaine personnalisé à l’aide du sous-domaine *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

Pour configurer un nom de domaine personnalisé, vous devez créer un enregistrement CNAME dans DNS. L’enregistrement CNAME spécifie un alias pour un nom de domaine. Dans ce cas, il mappe l’adresse de votre domaine personnalisé au point de terminaison de stockage Blob de votre compte de stockage.

En règle générale, vous pouvez gérer les paramètres DNS de votre domaine sur le site web du Registre de celui-ci. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire. Certaines formules de base pour l’inscription de domaine n’offrent pas la configuration DNS. Avant de pouvoir créer l’enregistrement CNAME, vous devrez peut-être mettre à niveau votre formule d’inscription de domaine.

1. Accédez à votre compte de stockage dans le [Portail Azure](https://portal.azure.com).
1. Sous **SERVICE BLOB** dans le panneau des menus, sélectionnez **Domaine personnalisé** pour ouvrir le panneau *Domaine personnalisé*.
1. Connectez-vous au site web du Registre de votre domaine et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.
1. Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias** ou **Sous-domaines**.
1. Créez un enregistrement CNAME et indiquez un alias de sous-domaine tel que **www** ou **photos**. Indiquez ensuite un nom d’hôte, à savoir votre point de terminaison de service BLOB, au format **mystorageaccount.blob.core.windows.net** (où *mystorageaccount* correspond au nom de votre compte de stockage). Le nom d’hôte à utiliser s’affiche dans l’élément #1 du panneau *Domaine personnalisé* dans le [portail Azure](https://portal.azure.com).
1. Dans la zone de texte sur le panneau *Domaine personnalisé* dans le [portail Azure](https://portal.azure.com), entrez le nom de votre domaine personnalisé, avec le sous-domaine. Par exemple, si votre domaine est **contoso.com** et votre alias de sous-domaine est **www**, entrez **www.contoso.com**. Si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Le sous-domaine est *obligatoire*.
1. Sélectionnez **Enregistrer** sur le panneau *Domaine personnalisé* pour inscrire votre domaine personnalisé. Si l’inscription réussit, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour.

Une fois votre nouvel enregistrement CNAME propagé via DNS, vos utilisateurs peuvent afficher les données de l’objet blob à l’aide de votre domaine personnalisé, tant qu’ils ont les autorisations appropriées.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Inscrire un domaine personnalisé à l’aide du sous-domaine *asverify*
Cette procédure permet d’enregistrer votre domaine personnalisé si ce dernier prend en charge une application visée par un contrat SLA qui interdit toute interruption de service. En créant un enregistrement CNAME qui pointe de `asverify.<subdomain>.<customdomain>` vers `asverify.<storageaccount>.blob.core.windows.net`, vous pouvez préinscrire votre domaine auprès d’Azure. Vous pouvez ensuite créer un second enregistrement CNAME qui pointe de `<subdomain>.<customdomain>` vers `<storageaccount>.blob.core.windows.net` afin de diriger le trafic allant vers votre domaine personnalisé vers votre point de terminaison d’objet blob.

Le sous-domaine **asverify** est un sous-domaine spécial reconnu par Azure. En ajoutant `asverify` à votre propre sous-domaine, vous permettez à Azure de reconnaître votre domaine personnalisé sans modifier l’enregistrement DNS du domaine. Quand l’enregistrement DNS pour le domaine est modifié, il est mappé au point de terminaison d’objet blob sans aucune interruption de service.

1. Accédez à votre compte de stockage dans le [Portail Azure](https://portal.azure.com).
1. Sous **SERVICE BLOB** dans le panneau des menus, sélectionnez **Domaine personnalisé** pour ouvrir le panneau *Domaine personnalisé*.
1. Connectez-vous au site web du fournisseur DNS et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.
1. Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias** ou **Sous-domaines**.
1. Créez un enregistrement CNAME et indiquez un alias de sous-domaine qui inclut le sous-domaine *asverify*. Par exemple, **asverify.www** ou **asverify.photos**. Indiquez ensuite un nom d’hôte, à savoir votre point de terminaison de service BLOB, au format **asverify.mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Le nom d’hôte à utiliser s’affiche dans l’élément #2 du panneau *Domaine personnalisé* dans le [portail Azure](https://portal.azure.com).
1. Dans la zone de texte sur le panneau *Domaine personnalisé* dans le [portail Azure](https://portal.azure.com), entrez le nom de votre domaine personnalisé, avec le sous-domaine. N’incluez pas *asverify*. Par exemple, si votre domaine est **contoso.com** et votre alias de sous-domaine est **www**, entrez **www.contoso.com**. Si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Le sous-domaine est obligatoire.
1. Cochez la case **Utiliser la validation CNAME indirecte**.
1. Sélectionnez **Enregistrer** sur le panneau *Domaine personnalisé* pour inscrire votre domaine personnalisé. Si l’inscription réussit, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour. À ce stade, votre domaine personnalisé a été vérifié par Azure, mais le trafic en direction de votre domaine n'est pas encore dirigé vers votre compte de stockage.
1. Retournez sur le site web du fournisseur DNS et créez un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison de service BLOB. Par exemple, spécifiez le sous-domaine sous la forme **www** ou **photos** (sans *asverify*) et le nom d’hôte sous la forme **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Cette étape marque la fin de l'enregistrement de votre domaine personnalisé.
1. Pour finir, vous pouvez supprimer l’enregistrement CNAME que vous avez créé et qui contient le sous-domaine **asverify**, car il n’a plus d’utilité.

Une fois votre nouvel enregistrement CNAME propagé via DNS, vos utilisateurs peuvent afficher les données de l’objet blob à l’aide de votre domaine personnalisé, tant qu’ils ont les autorisations appropriées.

## <a name="test-your-custom-domain"></a>Tester votre domaine personnalisé

Pour confirmer que votre domaine personnalisé est correctement mappé à votre point de terminaison de service BLOB, créez un objet blob dans un conteneur public au sein de votre compte de stockage. Dans un navigateur web, utilisez un URI pour accéder à l’objet blob :

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Vous pouvez par exemple utiliser l’URI suivant pour accéder à un formulaire web dans le conteneur **myforms** dans le sous-domaine personnalisé **photos.contoso.com** :

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Annuler l’inscription d’un domaine personnalisé

Pour annuler l’inscription d’un domaine personnalisé pour votre point de terminaison de stockage Blob, utilisez l’une des procédures suivantes.

### <a name="azure-portal"></a>Portail Azure

Pour supprimer le paramètre de domaine personnalisé, effectuez la procédure suivante dans le portail Azure :

1. Accédez à votre compte de stockage dans le [Portail Azure](https://portal.azure.com).
1. Sous **SERVICE BLOB** dans le panneau des menus, sélectionnez **Domaine personnalisé** pour ouvrir le panneau *Domaine personnalisé*.
1. Effacez le contenu de la zone de texte contenant le nom de votre domaine personnalisé.
1. Sélectionnez le bouton **Enregistrer**.

Une fois le domaine personnalisé supprimé, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Utilisez la commande d’interface de ligne de commande [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) et spécifiez une chaîne vide (`""`) pour la valeur d’argument `--custom-domain` afin de supprimer une inscription de domaine personnalisé.

* Format de commande :

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemple de commande :

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande PowerShell [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) et spécifiez une chaîne vide (`""`) pour la valeur d’argument `-CustomDomainName` afin de supprimer une inscription de domaine personnalisé.

* Format de commande :

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemple de commande :

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Étapes suivantes
* [Mapper un domaine personnalisé à un point de terminaison Azure CDN (Content Delivery Network)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
