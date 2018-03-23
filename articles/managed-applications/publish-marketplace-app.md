---
title: Applications gérées Azure sur la Place de marché | Microsoft Docs
description: Décrit les applications gérées Azure disponibles via la Place de marché.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/15/2018
ms.author: tomfitz
ms.openlocfilehash: 61815098a4935983f19b3dcea6e001230aee4771
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Applications gérées Azure sur la Place de marché

Les fournisseurs peuvent utiliser les applications managées Azure pour offrir leurs solutions à tous les clients de la Place de marché Azure. Il peut s’agir de fournisseurs de services managés, d’éditeurs de logiciels indépendants et d’intégrateurs de système. Les applications managées réduisent les opérations de maintenance et de mise à jour des clients. Les fournisseurs vendent des infrastructures et des logiciels sur la Place de marché. Ils peuvent joindre des services et le support opérationnel pour les applications gérées. Pour plus d’informations, consultez [Vue d’ensemble des applications gérées](overview.md).

Cet article explique comment publier une application sur la Place de marché et la mettre à la disposition des clients.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Conditions préalables à la publication d’une application gérée

Pour suivre cet article, vous devez déjà disposer du fichier .zip pour votre définition d’application managée. Pour plus d’informations, consultez [Créer l’application de catalogue de services](publish-service-catalog-app.md).

Il existe également plusieurs prérequis métier. Il s'agit de :

* Votre entreprise (ou sa filiale) doit être située dans un pays où les ventes sont prises en charge par la Place de marché.
* Votre produit doit être concédé sous licence de manière à être compatible avec les modèles de facturation pris en charge par la Place de marché.
* Consentez tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
* Gérez les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
* Fournissez du contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de Marché et sur le portail Microsoft Azure.
* Acceptez les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de Marché Microsoft Azure.
* Engagez-vous à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

## <a name="become-a-publisher"></a>Devenir éditeur

Pour devenir éditeur sur la Place de marché Azure, vous devez :

1. Créer un identifiant Microsoft - Créez votre compte Microsoft avec une adresse e-mail qui appartient au domaine de votre entreprise, mais pas à une seule personne. Cette adresse e-mail est utilisée pour le Portail Cloud Partner et pour le Centre de développement Microsoft. Pour plus d’informations, consultez le [Guide pour les éditeurs de la Place de marché Azure](https://aka.ms/sellerguide).
1. Envoyer le [formulaire de candidature pour la Place de marché Azure](https://aka.ms/ampnomination) - Pour **Solution that you intend to publish? (Quelle solution avez-vous l’intention de publier ?)**, sélectionnez **Managed Application (Application managée)**. Une fois le formulaire envoyé, l’équipe d’intégration de la Place de marché examine la candidature et valide la demande. Le processus d’approbation peut prendre un à trois jours. Une fois votre candidature approuvée, vous recevez un code promotionnel vous dispensant des frais d’inscription au Centre de développement. Si vous ne remplissez **pas** le formulaire de candidature pour la Place de marché, vous devez payer des frais d’inscription de 99 $.
1. Vous inscrire auprès du [Centre de développement](http://dev.windows.com/registration?accountprogram=azure) - Microsoft vérifie que votre organisation est une entité juridique valide avec un numéro fiscal valide dans le pays où elle est enregistrée. Le processus d’approbation peut prendre de cinq à dix jours. Pour éviter les frais d’inscription, utilisez le code promotionnel que vous avez reçu par e-mail suite à la procédure de candidature. Pour plus d’informations, consultez le [Guide pour les éditeurs de la Place de marché Azure](https://aka.ms/sellerguide).
1. Vous connecter au [Portail Cloud Partner](https://cloudpartner.azure.com) - Dans le profil d’éditeur, associez votre compte du Centre de développement au profil d’éditeur de la Place de marché. Pour plus d’informations, consultez le [Guide pour les éditeurs de la Place de marché Azure](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Création d’une offre d’application Azure

Une fois que vous avez créé votre compte du portail de partenaire, vous êtes prêt à créer votre offre d’application managée.

### <a name="set-up-an-offer"></a>Configuration d’une offre

Une offre d’application gérée correspond à une classe d’offre de produit d’un éditeur. Si vous avez un nouveau type d’application à mettre à disposition sur la Place de marché, vous pouvez en faire une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché.

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

1. Dans le volet de navigation de gauche, sélectionnez **+ Nouvelle offre** > **Applications Azure**.

1. Les formulaires nécessaires sont affichés dans la vue **Éditeur**. Chaque formulaire est décrit plus loin dans cet article.

## <a name="offer-settings-form"></a>Formulaire des paramètres de l’offre

Le formulaire **Paramètres de l’offre** contient les champs suivants :

* **Offer ID** (ID de l’offre) - Il s’agit d’un identificateur unique qui identifie l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret. Il est limité à un maximum de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.
* **Publisher ID** (ID de l’éditeur) : utilisez cette liste déroulante pour choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.
* **Nom** : ce nom d’affichage pour votre offre s’affiche dans le Marketplace et dans le portail. Il ne peut pas comprendre plus de 50 caractères. Incluez un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

Une fois terminé, sélectionnez **Enregistrer** pour enregistrer votre progression.

## <a name="skus-form"></a>Formulaire de références (SKU)

L’étape suivante consiste à ajouter des références (SKU) pour votre offre.

Une référence (SKU) est la plus petite unité achetable d’une offre. Vous pouvez utiliser une référence (SKU) dans la même classe de produit (offre) à des fins de différentiation :

* Les différentes fonctionnalités prises en charge
* Si l’offre est gérée ou non
* Les modèles de facturation pris en charge

Une référence (SKU) s’affiche sous l’offre parente dans la Place de marché. Elle s’affiche comme une entité distincte pouvant être achetée dans le portail Azure.

1. Sélectionnez **Références** > **Nouvelle référence**.

1. Saisissez un **ID de référence**. Un SKU ID (ID de référence) est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

1. Remplissez la section **Détails de la référence** sur le formulaire suivant :

   Renseignez les champs suivants :

   * **Titre** : saisissez un titre pour cette référence. Ce titre s’affiche dans la galerie pour cet élément.
   * **Summary** (Résumé) : saisissez un bref résumé décrivant cette référence (SKU). Ce texte s’affiche en dessous du titre.
   * **Description** : saisissez une description détaillée de la référence (SKU).
   * **SKU Type** (Type de référence) - Les valeurs autorisées sont *Managed Application* (Application gérée) et *Solution Templates* (Modèles de solution). Dans le cas présent, sélectionnez *Managed Application* (Application gérée).
   * **Disponibilité par pays/région** : sélectionnez les pays où l’application managée est disponible.
   * **Tarification** : spécifiez un prix pour la gestion de l’application. Sélectionnez les pays disponibles avant de définir le prix.

1. Ajoutez un nouveau package. Remplissez la section **Détails du package** sur le formulaire suivant :

   Renseignez les champs suivants :

   * **Current Version** (Version actuelle) : saisissez la version du package chargé. Il doit respecter le format `{number}.{number}.{number}{number}`.
   * **Sélectionner un fichier de package** : ce package contient les deux fichiers nécessaires compressés dans un package .zip. L’un des fichiers est un modèle Resource Manager, qui définit les ressources à déployer pour l’application gérée. L’autre fichier définit [l’interface utilisateur](create-uidefinition-overview.md) pour les consommateurs qui déploient l’application gérée via le portail. Dans l’interface utilisateur, vous spécifiez les éléments qui permettent aux consommateurs de fournir des valeurs de paramètre.
   * **PrincipalId** : cette propriété représente l’identificateur Azure Active Directory (Azure AD) d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auquel ou à laquelle l’accès aux ressources de l’abonnement du client a été accordé. La définition de rôle décrit les autorisations.
   * **Role Definition** - Cette propriété dresse une liste de tous les rôles RBAC intégrés fournis par Azure AD. Vous pouvez sélectionner le rôle le mieux adapté pour gérer les ressources pour le compte du client.

Vous pouvez ajouter plusieurs autorisations. Nous vous recommandons de créer un groupe d’utilisateurs AD et de spécifier son ID dans **PrincipalId**. De cette manière, vous pouvez ajouter plus d’utilisateurs au groupe d’utilisateurs sans avoir à mettre à jour la référence (SKU).

Pour plus d’informations sur le sujet, consultez [Bien démarrer avec RBAC dans le portail Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulaire Marketplace

Le formulaire Marketplace (Place de marché) permet de définir les champs qui s’affichent dans la [Place de marché Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Entrez une liste d’ID d’abonnement Azure qui peuvent accéder à l’offre après sa publication. Vous pouvez utiliser ces abonnements autorisés pour tester l’offre préliminaire avant sa publication. Vous pouvez compiler une liste blanche de jusqu'à 100 abonnements dans le portail partenaire.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste jusqu’à cinq catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Place de marché Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur la place de marché](./media/publish-marketplace-app/publishvm10.png)

Les champs suivants s’affichent dans l’onglet **Vue d’ensemble** de votre application gérée :

![Présentation de la Place de marché](./media/publish-marketplace-app/publishvm11.png)

Les champs suivants s’affichent dans l’onglet **Abonnements + Tarification** de votre application gérée :

![Plans sur la place de marché](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Portail Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur le portail](./media/publish-marketplace-app/publishvm12.png)

Les champs suivants s’affichent dans la vue d’ensemble de votre application gérée :

![Présentation du portail](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez ces instructions pour tout logo que vous téléchargez dans le portail partenaire cloud :

*   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
*   Les couleurs de thème du portail sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour votre logo. Utilisez une couleur qui donne à votre logo plus de visibilité dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples. *Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.*
*   N'utilisez pas d’arrière-plan dégradé sur le logo.
*   Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise. L'apparence de votre logo doit être « plate » et éviter les dégradés.
*   Assurez-vous que le logo n’est pas étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. Une fois l’icône de bannière téléchargée, elle ne peut pas être supprimée. À ce moment, le partenaire doit suivre les instructions relatives à la Place de marché pour les icônes.

Suivez ces recommandations concernant l’icône de bannière :

*   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en blanc. Ainsi, n’utilisez pas de couleur claire pour l’arrière-plan de l’icône de bannière. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
*   Une fois l’offre répertoriée, les éléments sont incorporés par programmation à l’intérieur de la bannière. Les éléments incorporés incluent le nom d’affichage de l’éditeur, le titre du plan, le résumé de l’offre et le bouton **Créer**. Par conséquent, ne saisissez pas de texte lorsque vous concevez le l’icône de bannière. Laissez un espace vide à droite, car le texte est inclus par programme dans cet espace. L’espace vide pour le texte doit être de 415 x 100 pixels sur la droite. Il est décalé de 370 pixels à partir de la gauche.

    ![Exemple d’icône de bannière](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formulaire de prise en charge

Remplissez le formulaire **Support** avec les contacts de support de votre entreprise. Ces informations peuvent être les contacts de support d’ingénierie et client.

## <a name="publish-an-offer"></a>Publication d’une offre

Après avoir rempli toutes les sections, sélectionnez **Publish** (Publier) pour mettre votre offre à disposition des clients.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](overview.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](publish-service-catalog-app.md).