---
title: Personnaliser votre page de connexion dans Azure Active Directory | Microsoft Docs
description: "Découvrir comment ajouter une société à la page de connexion Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 0855129c35c0c3d0f1814e8d29b6f3ae7d950db7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Démarrage rapide : Ajouter la marque de votre société à votre page de connexion dans Azure AD
Pour éviter toute confusion, de nombreuses entreprises veulent que tous les sites Web et services qu’elles gèrent aient un aspect similaire. Azure Active Directory (AD) offre cette possibilité en vous permettant de personnaliser l’apparence la page de connexion pour qu’elle affiche le logo de votre société et sa palette de couleurs personnalisée. La page de connexion s’affiche lorsque vous vous connectez à Office 365 ou à d’autres applications Web qui utilisent Azure AD comme fournisseur d’identité. Vous interagissez avec cette page pour saisir vos informations d’identification.

> [!NOTE]
> * La marque de société est une fonctionnalité disponible uniquement si vous avez effectué une mise à niveau vers l’édition Premium ou De base d’Azure AD, ou si vous disposez d’une licence Office 365. Pour savoir si une fonctionnalité est prise en charge par votre type de licence, consultez la page [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Les clients vivant en Chine peuvent accéder aux éditions De base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous via le [Forum Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personnalisation de la page de connexion

<!--You can customize the following elements on the sign-in page: <attach image>-->

Les personnalisations de la marque de société s’affichent sur la page de connexion Azure AD lorsque les utilisateurs accèdent à une URL spécifique à un locataire, par exemple : [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Lorsque les utilisateurs accèdent à un service se trouvant à une adresse URL générique, comme www.office.com, la page de connexion ne contient pas de personnalisations de la marque de société, car le système ne connaît pas cet utilisateur. La marque de société s’affiche une fois que les utilisateurs ont indiqué leur identifiant d’utilisateur, ou sélectionné la mosaïque d’un utilisateur.

> [!NOTE]
> * Votre nom de domaine doit apparaître comme étant « Actif » dans la partie **Domaines** du portail Azure dans lequel vous avez effectué la personnalisation de société. Pour plus d’informations, consultez [Ajouter un nom de domaine personnalisé](add-custom-domain.md).
> * La personnalisation de marque de la page de connexion ne s’étend pas à la page de connexion des comptes Microsoft personnels. Si les employés ou des invités professionnels se connectent avec un compte Microsoft personnel, leur page de connexion ne reflète pas la marque de votre organisation.


### <a name="banner-logo"></a>Logo de bannière 

Description | Contraintes | Recommandations
------- | ------- | ----------
Le logo de bannière s’affiche sur les pages de connexion et du volet d’accès.<br>Sur la page de connexion, cela s’affiche une fois que l’organisation de l’utilisateur est identifiée (en règle générale, une fois le nom d’utilisateur saisi).  | JPG ou PNG transparent<br>Hauteur max. : 36 px<br>Largeur max. : 245 px | Utilisez le logo de votre organisation ici.<br>Utilisez une image transparente. Ne partez pas du principe que l’arrière-plan est blanc.<br>N’ajoutez pas de marge intérieure autour de votre logo dans l’image, sinon le logo s’affichera disproportionnellement petit.

### <a name="username-hint"></a>Indication sur le nom d’utilisateur   
Description | Contraintes | Recommandations
------- | ------- | ----------
Cela permet de personnaliser le texte d’information dans le champ du nom d’utilisateur. | Texte Unicode, jusqu’à 64 caractères<br>Texte brut uniquement | Nous vous recommandons de ne pas définir cette option si vous prévoyez que des utilisateurs invités en dehors de votre organisation se connectent à votre application.
            
### <a name="sign-in-page-text"></a>Texte de la page de connexion   
Description | Contraintes | Recommandations
------- | ------- | ----------
Cela apparaît en bas du formulaire de connexion et peut être utilisé pour communiquer des informations supplémentaires telles que le numéro de téléphone à votre support technique ou une mention légale. | Texte Unicode, jusqu’à 256 caractères<br>Texte brut uniquement (aucun lien ni balise HTML)   

### <a name="sign-in-page-image"></a>Image de la page de connexion  
Description | Contraintes | Recommandations
------- | ------- | ----------
Cela s’affiche dans l’arrière-plan de la page de connexion. L’image est ancrée au centre de l’espace visible, mise à l’échelle et rognée pour remplir la fenêtre du navigateur.    <br>Sur les écrans étroits tels que les téléphones mobiles, l’image n’est pas affichée.<br>Un masque noir avec une opacité de 0,55 sera appliqué sur cette image par notre code lors du chargement de la page. | JPG ou PNG<br>Dimensions de l’image : 1 920 x 1 080 px<br>Taille de fichier : &lt; 300 Ko | <br>Utilisez des images lorsqu’il n’y a pas de focalisation forte sur l’objet. Le formulaire de connexion opaque apparaît au centre de cette image et peut couvrir une partie de l’image en fonction de la taille de la fenêtre du navigateur.<br>Conservez la taille du fichier aussi réduite que possible pour garantir des temps de chargement rapides. 

### <a name="background-color"></a>Couleur d’arrière-plan
Description | Contraintes | Recommandations
------- | ------- | ----------
Cette couleur est utilisée à la place de l’image d’arrière-plan sur les connexions à faible bande passante. | Couleur RVB en hexadécimal (par exemple #FFFFFF) | Nous vous suggérons d’utiliser la couleur principale du logo de bannière ou la couleur de votre organisation.

### <a name="show-option-to-remain-signed-in"></a>Afficher l’option permettant de rester connecté
Description | Contraintes | Recommandations
------- | ------- | ----------
La connexion Azure AD permet à l’utilisateur de rester connecté lorsqu’il ferme et ouvre à nouveau son navigateur. Utilisez cela pour masquer cette option.<br>Définissez cet élément sur « Non » pour que vos utilisateurs ne voient pas cette option. | &nbsp; | Cela n’a pas d’effet sur la durée de vie de session.<br>Certaines fonctionnalités de SharePoint Online et Office 2010 dépendent du choix des utilisateurs de rester connecté. Si vous définissez cet élément pour qu’il soit masqué, il se peut que vos utilisateurs voient des invites de connexion supplémentaires et inattendues.

> [!NOTE]
> Tous ces éléments sont facultatifs. Par exemple, si vous spécifiez un logo de bannière sans image d’arrière-plan, la page de connexion affiche votre logo et l’image d’arrière-plan pour le site de destination (par exemple, Office 365).

## <a name="add-company-branding-to-your-directory"></a>Ajouter la marque de société à votre répertoire

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur global pour l’annuaire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![Ouvrir la gestion des utilisateurs](./media/customize-branding/user-management.png)
3. Sur le panneau **Utilisateurs et groupes** panneau, sélectionnez **Personnalisation de société**.
4. Sur le panneau **Utilisateurs et groupes - Personnalisation de la société**, sélectionnez la commande **Modifier**.

    ![Modifier la personnalisation de société](./media/customize-branding/edit-branding.png)
5. Modifiez les éléments que vous voulez personnaliser. Tous ces éléments sont facultatifs.
6. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Ajouter une marque de société spécifique à une langue à votre répertoire

1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![Ouvrir la gestion des utilisateurs](./media/customize-branding/user-management.png)
3. Sur le panneau **Utilisateurs et groupes** panneau, sélectionnez **Personnalisation de société**.
4. Sur le panneau **Utilisateurs et groupes - Personnalisation de la société**, sélectionnez la commande **Ajouter une langue**.

    ![Ajouter des éléments spécifiques à une langue](./media/customize-branding/add-language.png)
5. Modifiez les éléments que vous voulez personnaliser. Tous ces éléments sont facultatifs.
6. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez appris à ajouter la marque de société à votre annuaire Azure AD. 

Vous pouvez utiliser le lien suivant pour configurer la marque de votre société dans Azure AD à partir du portail Azure.

> [!div class="nextstepaction"]
> [Configurer la marque de la société](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 