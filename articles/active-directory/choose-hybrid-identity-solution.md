---
title: "Choisir une solution d’identité hybride Azure | Documents Microsoft"
description: "Acquérez une compréhension élémentaire des solutions et recommandations liées aux identités hybrides afin de pouvoir prendre une décision optimale en matière de gouvernance des identités pour votre organisation."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 01/03/2018
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 78813cfbfc9d28e9578f1970083e17d423e071b5
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="microsoft-hybrid-identity-solutions"></a>Solutions d'identité hybride Microsoft
Les solutions d’identité hybride [Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) permettent de synchroniser des objets de répertoire locaux avec Azure AD tout en gérant vos utilisateurs locaux. La première décision à prendre lorsque vous envisagez de synchroniser votre Active Directory Windows Server local avec Azure AD est de savoir si vous souhaitez utiliser une identité synchronisée ou une identité fédérée. Les identités synchronisées et éventuellement les hachages du mot de passe, permettent à vos utilisateurs d’utiliser le même mot de passe pour accéder aux ressources de l’organisation locales et sur le cloud. Pour obtenir des spécifications de scénario plus avancées, telles que l’authentification unique (SSO) ou l’authentification multifacteur locale, vous devez déployer Active Directory Federation Services (AD FS) pour fédérer les identités. 

Plusieurs options sont disponibles pour la configuration d’identités hybrides. Cet article fournit des informations pour vous aider à choisir la meilleure option pour votre organisation, en fonction de la facilité de déploiement et de vos besoins spécifiques en matière de gestion des identités et des accès. Lorsque vous considérez le modèle d’identité le mieux adapté aux besoins de votre organisation, vous devez également prendre en compte les délais, l’infrastructure existante, la complexité et le coût. Ces facteurs sont différents pour chaque organisation et peuvent changer au fil du temps. Toutefois, si vos exigences changent, vous avez la possibilité de basculer vers un autre modèle d’identité.

> [!TIP]
> Ces solutions sont toutes fournies par [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Identité synchronisée 
L’identité synchronisée constitue la façon la plus simple de synchroniser des objets de répertoire locaux (utilisateurs et groupes) avec Azure AD. 

![Identité hybride synchronisée](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Bien que l’identité synchronisée soit la méthode la plus rapide et la plus facile, vos utilisateurs doivent cependant conserver un mot de passe distinct pour les ressources de cloud. Pour éviter ce problème, vous pouvez également (en option) [synchroniser un hachage du mot de passe utilisateur](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) à votre répertoire Azure AD. La synchronisation des hachages du mot de passe permet aux utilisateurs de se connecter à des ressources d’entreprise sur le cloud avec les mêmes nom d’utilisateur et mot de passe utilisés localement. Azure AD Connect contrôle régulièrement les modifications apportées à votre répertoire local pour le synchroniser dans votre répertoire Azure AD. Si un attribut utilisateur ou un mot de passe est modifié localement dans Active Directory, il est automatiquement mis à jour dans Azure AD. 

Pour la plupart des organisations qui doivent uniquement permettre à leurs utilisateurs de se connecter à Office 365, aux applications Saas et aux autres ressources Azure AD, l’option de synchronisation du mot de passe par défaut est recommandée. Si cela ne fonctionne pas pour vous, vous devrez décider entre l’authentification directe et AD FS.

> [!TIP]
> Les mots de passe utilisateur sont stockés dans Active Directory Windows Server local, sous la forme d’une valeur de hachage qui représente le mot de passe utilisateur réel. Une valeur de hachage est le résultat d’une fonction mathématique unidirectionnelle (l’algorithme de hachage). Il n’existe aucune méthode pour retrouver la version en texte brut du mot de passe à partir du résultat d’une fonction unidirectionnelle. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local. Lorsque vous choisissez de synchroniser les mots de passe, Azure AD Connect extrait les hachages du mot de passe à partir d’Active Directory local et applique le traitement de sécurité supplémentaire au hachage du mot de passe avant sa synchronisation avec Azure AD. La synchronisation du mot de passe peut être utilisée également avec la réécriture du mot de passe pour activer la réinitialisation du mot de passe libre-service dans Azure AD. En outre, vous pouvez activer également l’authentification unique (SSO) pour des utilisateurs sur des ordinateurs joints à un domaine connectés au réseau d’entreprise. Avec l’authentification unique, les utilisateurs activés doivent seulement entrer un nom d’utilisateur pour accéder en toute sécurité aux ressources du cloud. 

## <a name="pass-through-authentication"></a>Authentification directe
[L’authentification directe Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) fournit une solution de validation du mot de passe simple pour les services Azure AD à l’aide de votre répertoire Active Directory local. Si les stratégies de sécurité et de conformité pour votre organisation n’autorisent pas l’envoi des mots de passe utilisateurs, même dans un format haché et que vous devez uniquement prendre en charge l’authentification unique de bureau pour les appareils joints à un domaine, l’évaluation doit être effectuée via l’authentification directe. L’authentification directe ne nécessite aucun déploiement dans le réseau de périmètre, ce qui simplifie l’infrastructure de déploiement lors de la comparaison avec AD FS. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette méthode d’authentification valide les mots de passe utilisateurs directement à partir d’Active Directory local.

![Authentification directe](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Avec l’authentification directe, une infrastructure réseau complexe est inutile et vous n’avez pas besoin de stocker les mots de passe locaux dans le cloud. Combiné avec l’authentification unique, l’authentification directe fournit une expérience entièrement intégrée lors de la connexion à Azure AD ou d’autres services cloud.

L’authentification directe peut être configurée par le biais d’Azure AD Connect. Elle utilise un agent local simple qui écoute les requêtes de validation du mot de passe. L’agent peut facilement être déployé sur plusieurs ordinateurs afin de fournir une haute disponibilité et un équilibrage de charge. Étant donné que toutes les communications sont uniquement sortantes, il n’est pas obligatoire d’installer le connecteur dans une zone DMZ. La configuration requise du serveur pour le connecteur est la suivante :

- Windows Server 2012 R2 ou version ultérieure
- Joint à un domaine dans la forêt dans laquelle les utilisateurs sont validés

L’authentification directe n’est pas prise en charge si vous utilisez les périphériques Windows 10 joints à Azure AD. Toutefois, vous pouvez utiliser la synchronisation du hachage du mot de passe comme solution de secours automatique pour prendre en charge Windows 10 et les clients hérités mentionnés précédemment. Lors de l’aperçu, la synchronisation du hachage du mot de passe est activée par défaut lorsque l’authentification directe est sélectionnée en tant qu’option de connexion dans Azure AD Connect.


## <a name="federated-identity-ad-fs"></a>Identité fédérée (AD FS)
Pour obtenir un meilleur contrôle sur le mode d’accès des utilisateurs à Office 365 et autres services cloud, vous pouvez configurer la synchronisation de répertoires avec authentification unique (SSO) à l’aide de [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). La fédération des connexions utilisateurs avec AD FS délègue l’authentification à un serveur local qui valide les informations d’identification de l’utilisateur. Dans ce modèle, les informations d’identification Active Directory locales ne sont jamais passées à Azure AD.

![Identité fédérée](./media/choose-hybrid-identity-solution/federated-identity.png)

Également appelée fédération des identités, cette méthode de connexion garantit que toutes les authentifications utilisateur sont contrôlées localement et permet aux administrateurs de mettre en œuvre des niveaux de contrôle d’accès plus rigoureux. La fédération des identités avec AD FS est l’option la plus complexe qui nécessite le déploiement de serveurs supplémentaires dans votre environnement local. La fédération d’identité vous engage également à fournir une assistance 24 h/24 et 7 j/7 pour votre infrastructure Active Directory et AD FS. Ce niveau élevé d’assistance est nécessaire, car en cas de non disponibilité de votre accès à l’Internet local, du contrôleur de domaine ou des serveurs AD FS, les utilisateurs ne peuvent pas se connecter aux services cloud.

> [!TIP]
> Si vous choisissez d’utiliser la Fédération avec Active Directory Federation Services (AD FS), vous pouvez configurer, en option, une synchronisation du mot de passe comme sauvegarde en cas d’échec de votre infrastructure AD FS.


## <a name="common-scenarios-and-recommendations"></a>Scénarios et recommandations courants
Voici quelques scénarios courants de gestion des identités hybrides et des accès avec des recommandations concernant l’option (ou les options) d’identité hybride la ou les mieux appropriées dans chaque cas.

|J’ai besoin de :|PWS et SSO<sup>1</sup>| PTA et SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synchroniser de nouveaux comptes d’utilisateurs, de contacts et de groupes créés automatiquement dans mon Active Directory local vers le cloud.|![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png) |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Configurer mon client pour des scénarios hybrides Office 365|![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png) |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Permettre à mes utilisateurs de se connecter et d’accéder aux services cloud à l’aide de leur mot de passe local|![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png) |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implémenter l’authentification unique à l’aide des informations d’identification d’entreprise|![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png) |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|M’assurer qu’aucun hachage du mot de passe n’est stocké dans le cloud| |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Activer des solutions d’authentification multifacteur locales| | |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Prendre en charge l’authentification par carte à puce pour mes utilisateurs<sup>4</sup>| | |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|
|Afficher les notifications d’expiration du mot de passe dans le portail Office et sur le bureau Windows 10| | |![Recommandé](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Synchronisation du mot de passe avec l’authentification unique. 

> <sup>2</sup> Authentification directe et authentification unique. 

> <sup>3</sup> Authentification unique fédérée avec AD FS.

> <sup>4</sup> AD FS peut être intégré à l’infrastructure de clé publique (PKI) de votre entreprise pour permettre l’authentification à l’aide de certificats. Ces certificats peuvent être des certificats logiciels déployés via des canaux d’approvisionnement approuvés tels que les certificats de gestion des périphériques mobiles (GPM), d’objet de stratégie de groupe (GPO), de carte à puce (y compris les cartes PIV/CAC) ou Hello for Business (approbation de certificat). Pour plus d’informations sur la prise en charge de l’authentification par carte à puce, consultez [ce  blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Étapes suivantes
[En apprendre davantage dans un environnement de preuve de concept Azure](https://aka.ms/aad-poc)

[Installer Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Surveiller la synchronisation des identités hybrides](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

