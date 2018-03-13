---
title: "Exigences de certificat pour infrastructure à clé publique Azure Stack pour systèmes intégrés Azure Stack | Microsoft Docs"
description: "Décrit les exigences du déploiement de certificat pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: f2f71372211dcc9db34beb3fa3fd788920f8bd45
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Exigences de certificat pour infrastructure à clé publique Azure Stack
Azure Stack inclut un réseau d’infrastructure publique utilisant des adresses IP publiques accessibles en externe affectées à un petit ensemble de services Azure Stack et, éventuellement, à des machines virtuelles clientes. Des certificats pour infrastructure à clé publique avec des noms DNS appropriés pour ces points de terminaison d’infrastructure publique Azure Stack sont requis pendant le déploiement Azure Stack. Cet article fournit des informations sur :

- Les certificats requis pour déployer Azure Stack
- Le processus d’obtention de certificats correspondants à ces spécifications
- Comment préparer, valider et utiliser ces certificats pendant le déploiement

> [!NOTE]
> Au cours du déploiement, vous devez copier les certificats dans le dossier de déploiement correspondant au fournisseur d’identité (Azure AD ou AD FS). Si vous utilisez un seul certificat pour tous les points de terminaison, vous devez copier ce fichier de certificat dans chaque dossier de déploiement, comme indiqué dans les tableaux ci-dessous. La structure des dossiers est prédéfinie dans la machine virtuelle de déploiement et se trouve sous : C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Configuration requise des certificats
La liste suivante décrit les exigences de certificat nécessaires pour déployer Azure Stack : 
- Les certificats doivent être émis par une autorité de certification interne ou une autorité de certification publique. Si vous utilisez une autorité de certification publique, elle doit être incluse dans l’image du système d’exploitation de base dans le cadre du projet Microsoft Trusted Root Authority Program. Vous trouverez ici la liste complète : https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Le certificat peut être un certificat générique unique couvrant tous les espaces de noms dans le champ Autre nom de l’objet. Vous pouvez également utiliser des certificats individuels utilisant des caractères génériques pour des points de terminaison tels que ACS et le coffre de clés dans lequel ils sont nécessaires. 
- L’algorithme de signature de certificat ne peut pas être SHA1, car il doit être plus sécurisé. 
- Le format du certificat doit être PFX, car les clés publiques et privées sont requises pour l’installation d’Azure Stack. 
- Les fichiers pfx de certificat doivent avoir une valeur « Signature numérique » et « KeyEncipherment » dans le champ « Utilisation de la clé ».
- Dans le champ « Utilisation avancée de la clé », les fichiers pfx de certificat doivent avoir les valeurs « Authentification du serveur (1.3.6.1.5.5.7.3.1) » et « Authentification du client (1.3.6.1.5.5.7.3.2) ».
- Les mots de passe de tous les fichiers pfx de certificat doivent être identiques au moment du déploiement
- Assurez-vous que les noms d’objets et les autres noms de l’objet de tous les certificats correspondent aux spécifications décrites dans cet article afin d’éviter un échec des déploiements.

> [!NOTE]
> La présence d’autorités de certification intermédiaires dans la chaîne d’approbation d’un certificat est prise en charge. 

## <a name="mandatory-certificates"></a>Certificats obligatoires
Le tableau de cette section décrit les certificats pour infrastructure à clé publique de point de terminaison public Azure Stack qui sont requis pour les déploiements Azure AD et AD FS Azure Stack. Les exigences de certificat sont regroupées par zone, ainsi que les espaces de noms utilisés et les certificats requis pour chaque espace de noms. Le tableau décrit également le dossier dans lequel votre fournisseur de solutions copie les différents certificats par point de terminaison public. 

Des certificats avec des noms DNS appropriés pour chaque point de terminaison d’infrastructure publique Azure Stack sont requis. Le nom DNS de chaque point de terminaison est exprimé au format : *&lt;prefix>.&lt;region>.&lt;fqdn>*. 

Pour votre déploiement, les valeurs [region] et [externalfqdn] doivent correspondre à la région et aux noms de domaines externes que vous avez choisis pour votre système Azure Stack. Par exemple, si le nom de la région était *Redmond* et le nom de domaine externe *contoso.com*, les noms DNS aurait le format *&lt;prefix>.redmond.contoso.com*. Les valeurs *&lt;prefix>* sont prédéfinies par Microsoft pour décrire le point de terminaison sécurisé par le certificat. Les valeurs *&lt;prefix>* des points de terminaison d’infrastructure externe dépendent également du service Azure Stack qui utilise un point de terminaison spécifique. 

|Dossier de déploiement|Objet et autres noms de l’objet (SAN) du certificat requis|Étendue (par région)|Espace de noms de sous-domaine|
|-----|-----|-----|-----|
|Portail public|portal.*&lt;region>.&lt;fqdn>*|Portails|*&lt;region>.&lt;fqdn>*|
|Portail d’administration|adminportal.*&lt;region>.&lt;fqdn>*|Portails|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager Public|management.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager Admin|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|Un certificat générique à plusieurs sous-domaines avec d’autres noms de l’objet pour :<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|Stockage|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique)|Key Vault|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique)|Coffre de clés interne|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> Le certificat ACS requiert trois SAN génériques sur un même certificat. Plusieurs SAN génériques sur un même certificat peuvent ne pas être pris en charge par toutes les autorités de certification publiques. 

Si vous déployez Azure Stack à l’aide du mode de déploiement Azure AD, vous devez simplement demander les certificats répertoriés dans le tableau précédent. Toutefois, si vous déployez Azure Stack à l’aide du mode de déploiement AD FS, vous devez également demander les certificats décrits dans le tableau suivant :

|Dossier de déploiement|Objet et autres noms de l’objet (SAN) du certificat requis|Étendue (par région)|Espace de noms de sous-domaine|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Tous les certificats répertoriés dans cette section doivent avoir le même mot de passe. 

## <a name="optional-paas-certificates"></a>Certificats PaaS facultatifs
Si vous envisagez de déployer les services PaaS Azure Stack supplémentaires (SQL, MySQL et App Service) après le déploiement et la configuration d’Azure Stack, vous devrez demander des certificats supplémentaires pour couvrir les points de terminaison des services PaaS. 

> [!IMPORTANT]
> Les certificats que vous utilisez pour les fournisseurs de ressources App Service, SQL et MySQL doivent avoir la même autorité racine que ceux utilisés pour les points de terminaison Azure Stack globaux. 

Le tableau suivant décrit les points de terminaison et les certificats requis pour les adaptateurs SQL et MySQL et pour App Service. Vous n’avez pas besoin de copier ces certificats dans le dossier de déploiement Azure Stack. À la place, vous fournissez ces certificats lorsque vous installez les fournisseurs de ressources supplémentaires. 

|Étendue (par région)|Certificat|Objet et autres noms de l’objet (SAN) du certificat requis|Espace de noms de sous-domaine|
|-----|-----|-----|-----|
|SQL, MySQL|SQL et MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Certificat SSL par défaut de trafic web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique à plusieurs domaines<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Authentification unique|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> Requiert un certificat avec plusieurs autres noms de l’objet génériques. Plusieurs SAN génériques sur un même certificat peuvent ne pas être pris en charge par toutes les autorités de certification publiques 

<sup>2</sup> Un certificat générique &#42;.appservice.*&lt;region>.&lt;fqdn>* ne peut pas être utilisé à la place de ces trois certificats (api.appservice.*&lt;region>.&lt;fqdn>*, ftp.appservice.*&lt;region>.&lt;fqdn>* et sso.appservice.*&lt;region>.&lt;fqdn>*. App Service requiert explicitement l’utilisation de certificats distincts pour ces points de terminaison. 

## <a name="learn-more"></a>En savoir plus
Découvrez comment [générer des certificats d’infrastructure à clé publique pour le déploiement d’Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration des identités](azure-stack-integrate-identity.md)

