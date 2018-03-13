---
title: "Proxy d’application Azure AD - bien démarrer avec l’installation du connecteur | Microsoft Docs"
description: "Activez le proxy d’application dans le portail Azure et installez les connecteurs pour le proxy inverse."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2c2e0f977cdf0d31da19f71d9260b500184858e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Bien démarrer avec le proxy d’application et l’installation du connecteur
Cet article vous guide tout au long des étapes d’activation du proxy d’application Microsoft Azure AD pour votre répertoire de cloud dans Azure AD.

Si vous n’êtes pas encore informés sur les avantages de sécurité et de productivité que l’utilisation du Proxy d’application procure à votre organisation, obtenez plus d’informations sur [ la fourniture d’un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Conditions préalables pour le proxy d’application
Pour pouvoir activer et utiliser les services Proxy d’application, vous devez disposer des éléments suivants :

* Un [abonnement Microsoft Azure AD de base ou Premium](active-directory-editions.md) et un annuaire Azure AD sur lequel vous êtes administrateur général.
* Un serveur exécutant Windows Server 2012 R2 ou 2016, sur lequel vous pouvez installer le connecteur de proxy d’application. Le serveur doit pouvoir se connecter aux services Proxy d’application dans le cloud et aux applications locales que vous publiez.
  * Pour permettre une authentification unique de vos applications publiées à l’aide de la délégation contrainte, cette machine doit être jointe au même domaine AD que les applications que vous publiez. Pour plus d’informations, consultez [KCD pour authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md).

Si votre organisation utilise des serveurs proxy pour se connecter à Internet, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md) pour découvrir comment les configurer avant la prise en main du Proxy d’application.

## <a name="open-your-ports"></a>Ouvrir vos ports

Pour préparer votre environnement pour le proxy d’application Azure AD, vous devez d’abord activer la communication avec les centres de données Azure. Si un pare-feu existe, assurez-vous qu’il autorise les requêtes HTTPS (TCP) du connecteur au proxy d’application.

1. Ouvrez les ports suivants pour le trafic **sortant** :

   | Numéro de port | Utilisation |
   | --- | --- |
   | 80 | Téléchargement de la liste de révocation de certificats lors de la validation du certificat SSL |
   | 443 | Toutes les communications sortantes avec le service de proxy d’application |

   Si votre pare-feu régule le trafic en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.

   > [!IMPORTANT]
   > Le tableau reflète la configuration des ports nécessaire pour les versions du connecteur 1.5.132.0 et ultérieures. Si vous avez encore une version plus ancienne du connecteur, vous devez également activer les ports suivants, en plus de 80 et 443 : 5671, 8080, 9090-9091, 9350, 9352, 10100–10120.
   >
   >Pour plus d’informations sur la mise à jour de vos connecteurs vers la version la plus récente, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Si votre pare-feu ou votre proxy autorise la mise en liste verte de DNS, vous pouvez y placer les connexions à msappproxy.net et à servicebus.windows.net. Dans le cas contraire, vous devez autoriser l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.

3. Microsoft utilise quatre adresses pour vérifier les certificats. Autorisez l’accès aux URL suivantes si vous ne l’avez pas encore fait pour d’autres produits :
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Votre connecteur doit accéder à login.windows.net et login.microsoftonline.net pour effectuer le processus d’inscription.

5. Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue.

## <a name="install-and-register-a-connector"></a>Installer et inscrire un connecteur
1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur.
2. Votre répertoire actuel apparaît sous votre nom d’utilisateur dans le coin supérieur droit. Si vous avez besoin de changer de répertoire, sélectionnez cette icône.
3. Accédez à **Azure Active Directory** > **Proxy d’application**.

   ![Accédez à Proxy d’application](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Sélectionnez **Télécharger le connecteur**.

   ![Télécharger le connecteur](./media/active-directory-application-proxy-enable/download_connector.png)

5. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé conformément aux conditions préalables.
6. Suivez les instructions de l’Assistant pour effectuer l’installation. Au cours de l’installation, vous êtes invité à inscrire le connecteur auprès du service Proxy d’application de votre locataire Azure AD.

   * Fournissez vos informations d’identification d’administrateur général d’Azure AD. Votre client d’administrateur global peut être différent de vos informations d’identification Microsoft Azure.
   * Assurez-vous que l’administrateur qui inscrit le connecteur figure dans l’annuaire où vous avez activé le service Proxy d’application. Par exemple, si le domaine du client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
   * Si l’option **Configuration de sécurité renforcée d’Internet Explorer** présente la valeur **Activé** sur le serveur sur lequel vous installez le connecteur, il est possible que vous ne voyiez pas l’écran d’inscription. Suivez les instructions du message d’erreur pour obtenir l’accès. Vérifiez que la configuration de sécurité renforcée d’Internet Explore est désactivée.

Pour bénéficier d’une haute disponibilité, vous devez déployer au moins deux connecteurs. Chaque connecteur doit être inscrit séparément.

## <a name="test-that-the-connector-installed-correctly"></a>Tester si le connecteur a été correctement installé

Vous pouvez vous assurer qu’un nouveau connecteur a été correctement installé en le vérifiant dans le portail Azure ou sur votre serveur. 

Dans le portail Azure, connectez-vous à votre locataire et accédez à **Azure Active Directory** > **Proxy d’Application**. Tous les connecteurs et les groupes de connecteurs apparaissent sur cette page. Sélectionnez un connecteur pour afficher ses détails ou déplacez-le dans un groupe de connecteurs différents. 

Sur votre serveur, vérifiez la liste des services actifs pour le connecteur et sa mise à jour. Les deux services doivent démarrer immédiatement, mais dans le cas contraire, activez-les : 

   * **connecteur de proxy d’application Microsoft AAD** active la connectivité.

   * **Le programme de mise à jour du connecteur de proxy d’application Microsoft AAD** est un service de mise à jour automatique. Régulièrement, ce service recherche de nouvelles versions du connecteur et le met à jour si besoin.

   ![Services de connecteur de proxy d’application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Pour plus d’informations sur les connecteurs et leur mise à jour, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Étapes suivantes
Vous voici prêt à [publier des applications avec le proxy d’application](application-proxy-publish-azure-portal.md).

Si vous disposez d’applications figurant sur des réseaux distincts ou à différents emplacements, utilisez des groupes de connecteurs pour organiser les différents connecteurs en unités logiques. Apprenez-en davantage sur [l’utilisation de connecteurs de proxy d’application](active-directory-application-proxy-connectors-azure-portal.md).
