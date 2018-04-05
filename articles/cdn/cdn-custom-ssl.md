---
title: Configurer HTTPS sur un domaine personnalisé Microsoft Azure CDN (Content Delivery Network) | Microsoft Docs
description: Découvrez comment activer ou désactiver HTTPS sur votre point de terminaison Microsoft Azure CDN avec un domaine personnalisé.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: casoper
ms.openlocfilehash: fea7121fc67944b20b8f39007edb0c0aad86aeaa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Configurer HTTPS sur un domaine personnalisé Microsoft Azure CDN (Content Delivery Network)

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft prend en charge le protocole HTTPS pour les domaines personnalisés sur Azure Content Delivery Network (CDN). Avec la prise en charge de domaines personnalisés HTTPS, vous pouvez livrer du contenu sécurisé via SSL à l’aide de votre propre nom de domaine et améliorer la sécurité des données lors de leur transit. Le workflow permettant d’activer HTTPS pour votre domaine personnalisé est simplifié via l’activation en un clic et la gestion complète des certificats, le tout sans coût supplémentaire.

Il est essentiel de garantir la confidentialité et l’intégrité des données sensibles de vos applications web lors de leur transit. En utilisant le protocole HTTPS, vous êtes sûr que vos données sensibles sont chiffrées lorsqu’elles sont envoyées sur Internet. Il assure la confiance et l’authentification, mais protège également vos applications web contre les attaques. Par défaut, Azure CDN prend en charge HTTPS sur un point de terminaison CDN. Ainsi, si vous créez un point de terminaison CDN à partir d’Azure CDN (par exemple, https:\//contoso.azureedge.net), HTTPS est automatiquement activé. De plus, avec la prise en charge de HTTPS sur un domaine personnalisé, vous pouvez également activer la livraison sécurisée pour un domaine personnalisé (par exemple, https:\//www.contoso.com). 

Voici quelques-uns des attributs clés de la fonctionnalité HTTPS :

- Aucun coût supplémentaire : l’acquisition ou le renouvellement de certificat n’entraîne aucun coût supplémentaire, tout comme le trafic HTTPS. Vous payez uniquement pour l’acheminement des Go du CDN.

- Activation simple : l’approvisionnement en un clic est disponible à partir du [portail Azure](https://portal.azure.com). Vous pouvez également utiliser l’API REST ou d’autres outils de développeur pour activer la fonctionnalité.

- Gestion complète de certificats : l’approvisionnement et la gestion de tous les certificats sont assurés pour vous. Les certificats sont automatiquement provisionnés et renouvelés avant expiration, ce qui élimine les risques d’interruption de service en raison d’une expiration de certificat.

>[!NOTE] 
>Avant d’activer la prise en charge du protocole HTTPS, vous devez avoir déjà établi un [domaine personnalisé Azure CDN](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Activation de HTTPS

Pour activer HTTPS sur un domaine personnalisé, suivez ces étapes :

### <a name="step-1-enable-the-feature"></a>Étape 1 : Activation de la fonctionnalité 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN Verizon Standard ou Premium.

2. Dans la liste des points de terminaison, cliquez sur celui contenant votre domaine personnalisé.

3. Cliquez sur le domaine personnalisé pour lequel vous souhaitez activer le protocole HTTPS.

    ![Liste Domaines personnalisés](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Cliquez sur **Activer** pour activer HTTPS, puis sur **Appliquer**.

    ![Statut HTTPS sur un domaine personnalisé](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Étape 2 : Validation du domaine

>[!NOTE]
>Si vous avez un enregistrement CAA (Certificate Authority Authorization) auprès de votre fournisseur DNS, il doit inclure DigiCert en tant qu’autorité de certification valide. Un enregistrement CAA permet aux propriétaires de domaine de spécifier avec leurs fournisseurs DNS les autorités de certification autorisées à émettre des certificats pour leur domaine. Si une autorité de certification reçoit une commande de certificat pour un domaine qui contient un enregistrement CCA et que cette autorité de certification n’est pas répertoriée comme émetteur autorisé, elle ne peut pas émettre le certificat pour ce domaine ou sous-domaine. Pour plus d’informations sur la gestion des enregistrements CAA, consultez [Administrer les enregistrements CAA](https://support.dnsimple.com/articles/manage-caa-record/). Pour obtenir un outil d’enregistrement CAA, consultez [Assistance pour les enregistrements CAA](https://sslmate.com/caa/).

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>Le domaine personnalisé est mappé à un point de terminaison CDN

Lors de l’ajout d’un domaine personnalisé à votre point de terminaison, vous avez créé un enregistrement CNAME dans la table DNS de votre registre de domaines à mapper au nom d’hôte de votre point de terminaison CDN. Si cet enregistrement CNAME existe toujours et qu’il ne contient pas le sous-domaine cdnverify, l’autorité de certification (AC) DigiCert l’utilise pour valider la propriété de votre domaine personnalisé. 

Votre enregistrement CNAME doit être au format suivant, où *Nom* est le nom de votre domaine personnalisé et *Valeur* est le nom d’hôte de votre point de terminaison CDN :

| NOM            | type  | Valeur                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Pour plus d’informations sur les enregistrements CNAME, consultez [Create the CNAME DNS record](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records) (Créer l’enregistrement DNS CNAME).

Si le format de votre enregistrement CNAME est correct, DigiCert vérifie automatiquement le nom de votre domaine personnalisé et l’ajoute au certificat de noms SAN (Subject Alternative Names). DigiCert ne vous envoie pas de courrier électronique de vérification, et vous ne devez pas approuver votre requête. Le certificat est valide pendant un an et est automatiquement renouvelé avant son expiration. Passez à l’[Étape 3 : En attente de la propagation](#step-3-wait-for-propagation). 

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>L’enregistrement CNAME n’est pas mappé à un point de terminaison CDN

Si l’entrée d’enregistrement CNAME pour votre point de terminaison n’existe plus ou s’il contient le sous-domaine cdnverify, suivez les autres instructions de cette étape.

Dès que vous avez activé HTTPS sur votre domaine personnalisé, l’autorité de certification DigiCert valide la propriété de votre domaine en contactant son inscrit, en fonction de ses informations [WHOIS](http://whois.domaintools.com/) du domaine. Une prise de contact est établie par le biais de l’adresse de messagerie (par défaut) ou le numéro de téléphone répertorié dans l’inscription WHOIS. Vous devez effectuer la validation de domaine avant que le protocole HTTPS soit activé sur votre domaine personnalisé. Vous disposez de six jours ouvrables pour approuver le domaine. Les requêtes qui ne sont pas approuvées dans ce délai de six jours ouvrables sont automatiquement annulées. 

![Enregistrement WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert envoie également un e-mail de vérification à des adresses e-mail supplémentaires. Si les informations sur l’inscrit WHOIS sont privées, vérifiez que vous pouvez effectuer directement l’approbation à partir de l’une des adresses suivantes :

admin@&lt;votre-nom-de-domaine.com&gt;  
administrateur@&lt;votre-nom-de-domaine.com&gt;  
webmaster@&lt;votre-nom-de-domaine.com&gt;  
hostmaster@&lt;votre-nom-de-domaine.com&gt;  
postmaster@&lt;votre-nom-de-domaine.com&gt;  

Au bout de quelques minutes, vous devriez recevoir un e-mail similaire à celui de l’exemple suivant et vous demandant d’approuver la requête. Si vous utilisez un filtre de courrier indésirable, ajoutez admin@digicert.com à sa liste verte. Si vous ne recevez pas d’e-mail dans les 24 heures, contactez le support Microsoft.
    
![E-mail de validation du domaine](./media/cdn-custom-ssl/domain-validation-email.png)

Lorsque vous cliquez sur le lien d’approbation, vous êtes dirigé vers le formulaire d’approbation en ligne suivant : 
    
![Formulaire de validation du domaine](./media/cdn-custom-ssl/domain-validation-form.png)

Suivez les instructions du formulaire ; deux options de vérification vous sont proposées :

- Vous pouvez approuver toutes les commandes futures passées par l’intermédiaire du même compte pour le même domaine racine, par exemple contoso.com. Nous vous recommandons cette approche si vous prévoyez d’ajouter des domaines personnalisés pour le même domaine racine.

- Vous pouvez approuver uniquement le nom d’hôte spécifique utilisé dans cette requête. L’approbation supplémentaire est nécessaire pour les demandes suivantes.

Après l’approbation, DigiCert ajoute votre nom de domaine personnalisé au certificat SAN. Le certificat est valide pendant un an et est automatiquement renouvelé avant son expiration.

### <a name="step-3-wait-for-propagation"></a>Étape 3 : En attente de la propagation

Une fois le nom de domaine validé, l’activation de la fonctionnalité HTTPS sur un domaine personnalisé peut prendre jusqu’à 6 à 8 heures. Quand le processus est terminé, l’état HTTPS personnalisé est défini sur **Activé** dans le portail Azure, et les quatre étapes de l’opération sont marquées comme terminées dans la boîte de dialogue Domaine personnalisé. Votre domaine personnalisé est désormais prêt à utiliser HTTPS.

![Boîte de dialogue de l’activation de HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progression de l’opération

Le tableau suivant présente le déroulement de l’opération qui s’exécute lorsque vous activez HTTPS. Une fois que vous avez activé HTTPS, les quatre étapes de l’opération s’affichent dans la boîte de dialogue Domaine personnalisé. À mesure que chaque étape devient active, des détails de sous-étape supplémentaires s’affichent sous l’étape qui est en cours d’exécution. Toutes ces sous-étapes ne se produiront pas. Lorsqu’une étape se termine correctement, une coche verte apparaît en regard de celle-ci. 

| Étape de l’opération | Détails de la sous-étape de l’opération | 
| --- | --- |
| 1 Soumission de la requête | Envoi de la requête |
| | Votre requête HTTPS est en cours de soumission. |
| | Votre requête HTTPS a été correctement envoyée. |
| 2 Validation du domaine | Nous vous avons envoyé un e-mail vous demandant de valider la propriété du domaine. En attente de votre confirmation. ** |
| | La propriété du domaine a été correctement validée. |
| | La requête de validation de la propriété du domaine a expiré ( le client n’a probablement pas répondu dans les 6 jours). Le protocole HTTPS n’est pas activé sur votre domaine. * |
| | La requête de validation de la propriété du domaine a été rejetée par le client. Le protocole HTTPS n’est pas activé sur votre domaine. * |
| 3 Approvisionnement du certificat | L’émission, par l’autorité de certification, du certificat nécessaire à l’activation HTTPS sur votre domaine est en cours. |
| | Le certificat a été émis et son déploiement est en cours sur le réseau CDN. Cette opération peut durer 6 heures. |
| | Le certificat a été correctement déployé sur le réseau CDN. |
| 4 Fin | Le protocole HTTPS a été correctement activé sur votre domaine. |

\* Ce message ne s’affiche pas à moins qu’une erreur se soit produite. 

\** Ce message n’apparaît pas si vous avez une entrée CNAME pour votre domaine personnalisé qui pointe directement vers le nom d’hôte de votre point de terminaison CDN.

Si une erreur survient avant la soumission de la requête, le message d’erreur suivant s’affiche :

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Désactivation de HTTPS

Après avoir activé le protocole HTTPS sur un domaine personnalisé, vous pouvez le désactiver ultérieurement. Pour désactiver HTTPS, suivez ces étapes :

### <a name="step-1-disable-the-feature"></a>Étape 1 : Désactivation de la fonctionnalité 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN Verizon Standard ou Premium.

2. Dans la liste des points de terminaison, cliquez sur celui contenant votre domaine personnalisé.

3. Cliquez sur le domaine personnalisé pour lequel vous souhaitez désactiver HTTPS.

    ![Liste Domaines personnalisés](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Cliquez sur **Désactiver** pour désactiver HTTPS, puis sur **Appliquer**.

    ![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Étape 2 : En attente de la propagation

Après la désactivation de la fonctionnalité HTTPS sur un domaine personnalisé, il peut s’écouler jusqu’à 6 à 8 heures avant que cette modification soit effective. Quand le processus est terminé, l’état HTTPS personnalisé est défini sur **Désactivé** dans le portail Azure, et les trois étapes de l’opération sont marquées comme terminées dans la boîte de dialogue Domaine personnalisé. Votre domaine personnalisé ne peut plus utiliser HTTPS.

![Boîte de dialogue de la désactivation de HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progression de l’opération

Le tableau suivant présente le déroulement de l’opération qui s’exécute lorsque vous désactivez HTTPS. Une fois que vous avez désactivé HTTPS, les trois étapes de l’opération s’affichent dans la boîte de dialogue Domaine personnalisé. À mesure que chaque étape devient active, des détails supplémentaires s’affichent sous l’étape en cours d’exécution. Lorsqu’une étape se termine correctement, une coche verte apparaît en regard de celle-ci. 

| Progression de l’opération | Détails de l’opération | 
| --- | --- |
| 1 Soumission de la requête | Envoi de la requête |
| 2 Annulation de l’approvisionnement du certificat | Suppression du certificat |
| 3 Fin | Le certificat est supprimé |

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

1. *Qui est le fournisseur de certificats et quel est le type de certificat utilisé ?*

    Microsoft utilise un certificat SAN (Subject Alternative Names) fourni par DigiCert. Un certificat SAN peut sécuriser plusieurs noms de domaine complets avec un certificat.

2. *Puis-je utiliser mon certificat dédié ?*
    
    Pas actuellement, mais nous y travaillons.

3. *Que se passe-t-il si je ne reçois pas l’e-mail de vérification de domaine de DigiCert ?*

    Contactez le support Microsoft si vous ne recevez pas d’e-mail dans les 24 heures. Si vous avez une entrée CNAME pour votre domaine personnalisé qui pointe directement vers le nom d’hôte de votre point de terminaison (et si vous n’utilisez pas le nom de sous-domaine cdnverify), vous ne recevrez pas de courrier électronique de vérification du domaine. La validation se fait automatiquement.

4. *Un certificat SAN est-il moins sécurisé qu’un certificat dédié ?*
    
    Un certificat SAN suit les mêmes normes de sécurité et de chiffrement qu’un certificat dédié. Tous les certificats SSL émis utilisent la norme SHA-256 pour une sécurité améliorée du serveur.

5. *Puis-je utiliser un protocole HTTPS de domaine personnalisé avec Azure CDN Akamai ?*

    Actuellement, cette fonctionnalité est uniquement disponible avec Azure CDN Verizon. Microsoft travaille pour que la prise en charge de cette fonctionnalité par Azure CDN Akamai soit disponible dans les prochains mois.

6. *Ai-je besoin d’un enregistrement CAA (Certificate Authority Authorization) auprès de mon fournisseur DNS ?*

    Non, un enregistrement CAA n’est pas requis. Toutefois, si vous en avez un, il doit inclure DigiCert en tant qu’autorité de certification valide.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer un [domaine personnalisé sur votre point de terminaison Azure CDN](./cdn-map-content-to-custom-domain.md)


