---
title: "Ajouter un domaine personnalisé à votre point de terminaison CDN | Microsoft Docs"
description: "Découvrez comment mapper du contenu Azure CDN à un domaine personnalisé."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Ajouter un domaine personnalisé à votre point de terminaison CDN
Généralement, après avoir créé un profil, vous créez également un ou plusieurs [points de terminaison](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) CDN (un sous-domaine de `azureedge.net`) pour distribuer votre contenu à l’aide des protocoles HTTP et HTTPS. Par défaut, ce point de terminaison est inclus dans toutes les URL (par exemple, `https://contoso.azureedge.net/photo.png`). Par commodité, Azure CDN vous permet d’associer un domaine personnalisé (par exemple, `www.contoso.com`) à votre point de terminaison. Vous pouvez ainsi utiliser un domaine personnalisé pour distribuer du contenu au lieu de votre point de terminaison. Cette option est utile si, par exemple, vous souhaitez que votre propre nom de domaine soit visible pour vos clients à des fins de personnalisation.

Si vous ne disposez pas déjà d’un domaine personnalisé, vous devez tout d’abord en acheter un auprès d’un fournisseur de domaine. Après avoir obtenu un domaine personnalisé, procédez comme suit :
1. [Accédez aux enregistrements DNS de votre fournisseur de domaine.](#step-1-access-dns-records-by-using-your-domain-provider)
2. [Créez un ou plusieurs enregistrements DNS CNAME.](#step-2-create-the-cname-dns-records)
    - Option 1 : mappage direct de votre domaine personnalisé au point de terminaison CDN
    - Option 2 : mappage de votre domaine personnalisé au point de terminaison CDN à l’aide de cdnverify 
3. [Activez le mappage d’enregistrement CNAME dans Azure.](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Vérifiez que le sous-domaine personnalisé fait référence à votre point de terminaison CDN.](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Étape subordonnée) Mappez le domaine personnalisé permanent au point de terminaison CDN.](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Étape 1 : accès aux enregistrements DNS à l’aide de votre fournisseur de domaine

Si vous utilisez Azure pour héberger vos [domaines DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview), vous devez déléguer le DNS du fournisseur de domaine à un DNS Azure. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

Sinon, si vous utilisez votre fournisseur de domaine pour gérer votre domaine DNS, connectez-vous à son site web. Recherchez la page permettant de gérer les enregistrements DNS en consultant la documentation du fournisseur ou en recherchant les sections du site web intitulées **Nom de domaine**, **DNS** ou **Gestion des noms de serveur**. Vous pouvez généralement trouver la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements.

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct. 


## <a name="step-2-create-the-cname-dns-records"></a>Étape 2 : création d’un ou plusieurs enregistrements DNS CNAME

Avant de pouvoir utiliser un domaine personnalisé avec un point de terminaison CDN Azure, vous devez créer un enregistrement de nom canonique (CNAME) avec votre fournisseur de domaine. Un enregistrement CNAME est un type d’enregistrement du système DNS (Domain Name System) qui mappe un domaine source à un domaine de destination en spécifiant un nom de domaine d’alias pour le nom de domaine « canonique » ou réel. Pour Azure CDN, le domaine source est votre domaine personnalisé (et votre sous-domaine), et le domaine de destination est votre point de terminaison CDN. Azure CDN vérifie l’enregistrement DNS CNAME lorsque vous ajoutez le domaine personnalisé au point de terminaison à partir du portail ou de l’API. 

Un enregistrement CNAME mappe un domaine spécifique et un sous-domaine, comme `www.contoso.com` ou `cdn.contoso.com`. Il n’est pas possible de mapper un enregistrement CNAME à un domaine racine, tel que `contoso.com`. Un sous-domaine peut être associé à un point de terminaison uniquement. L’enregistrement CNAME que vous créez achemine tout le trafic adressé au sous-domaine vers le point de terminaison spécifié. Par exemple, si vous associez `www.contoso.com` à votre point de terminaison CDN, vous ne pouvez pas l’associer à un autre point de terminaison Azure, comme un point de terminaison de compte de stockage ou un point de terminaison de service cloud. Toutefois, vous pouvez utiliser différents sous-domaines du même domaine pour différents points de terminaison de service. Vous pouvez également mapper différents sous-domaines au même point de terminaison CDN.

Pour mapper votre domaine personnalisé à un point de terminaison CDN, utilisez l’une des options suivantes :

- Option 1 : mappage direct. Si aucun trafic de production ne s’exécute sur le domaine personnalisé, vous pouvez mapper un domaine personnalisé à un point de terminaison CDN directement. Le processus consistant à mapper votre domaine personnalisé à votre point de terminaison CDN peut entraîner un problème d’indisponibilité du service de courte durée au moment où vous inscrivez le domaine dans le portail Azure. Votre entrée de mappage CNAME doit être au format suivant : 
 
  | NOM             | TYPE  | VALEUR                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | contoso\.azureedge.net |


- Option 2 : mappage avec le sous-domaine **cdnverify**. Si un trafic de production qui ne peut pas être interrompu s’exécute sur le domaine personnalisé, vous pouvez créer un mappage CNAME temporaire à votre point de terminaison CDN. Avec cette option, vous utilisez le sous-domaine Azure **cdnverify** pour fournir une étape d’inscription intermédiaire. Les utilisateurs peuvent ainsi accéder à votre domaine sans interruption pendant le mappage DNS.

   1. Créez un enregistrement CNAME et indiquez un alias de sous-domaine qui inclut le sous-domaine **cdnverify**. Par exemple, **cdnverify.www** ou **cdnverify.cdn**. 
   2. Fournissez le nom d’hôte, qui est votre point de terminaison CDN, au format suivant : `cdnverify.<EndpointName>.azureedge.net`. Votre entrée de mappage CNAME doit être au format suivant : 

   | NOM                       | TYPE  | VALEUR                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Étape 3 : activation du mappage d’enregistrement CNAME dans Azure

Une fois que vous avez inscrit votre domaine personnalisé à l’aide de l’une des procédures précédentes, vous pouvez activer la fonctionnalité de domaine personnalisé dans Azure CDN. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis accédez au profil CDN avec le point de terminaison que vous souhaitez mapper à un domaine personnalisé.  
2. Dans le panneau **Profil CDN**, sélectionnez le point de terminaison CDN auquel vous souhaitez associer le sous-domaine.
3. Dans l’angle supérieur gauche du panneau du point de terminaison, cliquez sur **Domaine personnalisé**. 

   ![Bouton Domaine personnalisé](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. Dans la zone de texte **Nom d’hôte personnalisé**, entrez votre domaine personnalisé, y compris le sous-domaine. Par exemple, `www.contoso.com` ou `cdn.contoso.com`.

   ![Boîte de dialogue Ajouter un domaine personnalisé](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Cliquez sur **Add**.

   Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé est validé. La propagation de l’enregistrement CNAME aux serveurs de noms peut prendre du temps. Si votre domaine n’est pas validé immédiatement, vérifiez que l’enregistrement CNAME est correct, patientez quelques minutes, puis réessayez. Pour les points de terminaison du **CDN Azure fourni par Verizon** (Standard et Premium), la propagation des paramètres du domaine personnalisé sur tous les nœuds de périphérie CDN peut prendre jusqu’à 90 minutes.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Étape 4 : vérification que le sous-domaine personnalisé fait référence à votre point de terminaison CDN

Une fois que vous avez terminé l’inscription de votre domaine personnalisé, vérifiez qu’il référence votre point de terminaison CDN.
 
1. Assurez-vous d’avoir un contenu public qui est mis en cache au point de terminaison. Par exemple, si votre point de terminaison CDN est associé à un compte de stockage, le CDN met en cache le contenu dans des conteneurs d'objets blob publics. Pour tester le domaine personnalisé, assurez-vous que votre conteneur est configuré pour autoriser l’accès public et qu’il contient au moins un blob.

2. Dans votre navigateur, accédez à l’adresse du blob à l’aide du domaine personnalisé. Par exemple, si votre domaine personnalisé est `cdn.contoso.com`, l’URL vers le blob mis en cache doit être similaire à l’URL suivante : `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Étape 5 (étape subordonnée) : mappage du domaine personnalisé permanent au point de terminaison CDN

Cette étape est subordonnée à l’étape 2, option 2 (mappage avec le sous-domaine **cdnverify**). Si vous utilisez le sous-domaine temporaire **cdnverify** et avez vérifié qu’il fonctionne, vous pouvez mapper votre domaine personnalisé permanent au point de terminaison CDN.

1. Sur le site web de votre fournisseur de domaine, créez un enregistrement DNS CNAME pour mapper votre domaine personnalisé permanent au point de terminaison CDN. Votre entrée de mappage CNAME doit être au format suivant : 
 
   | NOM             | TYPE  | VALEUR                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | contoso\.azureedge.net |
2. Supprimez l’enregistrement CNAME avec le sous-domaine **cdnverify** que vous avez créé précédemment.

## <a name="see-also"></a>Voir aussi
[Comment activer le réseau de distribution de contenu (CDN) pour Azure](cdn-create-new-endpoint.md)  
[Déléguer votre domaine à Azure DNS](../dns/dns-domain-delegation.md)
