---
title: Déploiement de votre offre sur Azure Marketplace | Microsoft Docs
description: Lisez et suivez les instructions pour déployer votre offre (une image de machine virtuelle, un service de développement, des services de données, etc.) dans Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Déploiement de votre offre sur Azure Marketplace
Quand vous avez finalisé votre offre (avec des scénarios clients testés, du contenu marketing, etc.) et êtes prêt à la lancer, cliquez sur **Push to Production** (Lancer la production) sous l’onglet **Publish** (Publier).  

1. Les quatre étapes de la page PROCÉDURE PAS À PAS du portail de publication doivent être terminées et s’afficher en vert. Pour les offres de machines virtuelles, vérifiez que les instructions suivantes sont respectées.
   
    ![drawing][img-pubportal-walkthru-checked]
2. Sélectionnez l'onglet **Publish** dans la liste située à gauche.
   
    ![drawing][img-pubportal-menu-publish]
3. Cliquez sur le bouton **Request approval to push to production** (Demander une approbation pour lancer la production). Une fois la demande effectuée, l’équipe d’approbation exécute une vérification finale. Votre offre sera ensuite disponible sur Azure Marketplace.
   
    ![drawing][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Dans le cas d’ordinateurs virtuels, lorsque vous cliquez sur le bouton Demander une approbation pour lancer la production, les étapes suivantes sont effectuées en arrière-plan. Vous pourrez voir la progression de chaque étape sous l’onglet PUBLIER du portail de publication. Vous devez vérifier cette page à intervalles réguliers (jusqu’à ce que l’état affiche « Répertorié ») en cas d’apparition d’informations d’échec nécessitant une correction de votre part.
> 
> * Dans un premier temps, votre demande de lancement de production est transmise à l’équipe de certification, qui valide le disque dur virtuel. Toutefois, si vous mettez à jour votre offre déjà répertoriée et que la demande ne contient que des modifications d’ordre marketing, l’étape de certification est ignorée.
> * À l’étape suivante, la demande est transmise à l’équipe de validation du contenu, chargée de vérifier le contenu marketing de l’offre.
> * Si les étapes ci-dessus sont terminées avec succès, l’offre est approuvée pour production. À ce stade, l’état devient « Répertorié » dans le portail de publication. Toutefois, cet état « Répertorié » n’implique pas que le processus est terminé. Les étapes suivantes doivent être terminées avant que l’offre soit disponible dans Azure Marketplace.
> * Une fois que l’offre est approuvée pour production à l’étape précédente, la réplication de l’offre commence sur tous les centres de données Azure. En règle générale, il faut 24 à 48 heures pour que la réplication soit complète, mais elle peut prendre jusqu’à une semaine selon la taille du disque dur virtuel. Toutefois, si vous mettez à jour votre offre déjà répertoriée et que la demande ne contient que des modifications d’ordre marketing, la réplication est plus rapide.
> * Une fois la réplication terminée, l’offre sera répertoriée dans Azure Marketplace.
> 
> Vous pouvez toujours supprimer l’offre si elle a le statut **Draft** (Brouillon) (c’est-à-dire jamais **Push to staging** (passer à l’état intermédiaire) ni **Push to production** (Lancer la production)). Sous l’onglet **History** (Historique), cliquez sur le bouton **Discard draft** (Supprimer le brouillon) en bas de la page pour supprimer un brouillon.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Liste de contrôle de production pour toutes les offres de machines virtuelles
* Assurez-vous que vous avez le statut de partenaire Microsoft Azure Certified
* Sous l’onglet SKU, l’option « Masquer cette référence (SKU) à partir du Marketplace, car elle doit toujours être achetée via un modèle de solution » doit être marquée comme « OUI » uniquement si le SKU fait partie d’un modèle de solution. Dans tous les autres cas, cette option doit toujours être marquée comme « NON ».
* Rappel : vous ne devez pas modifier le paramètre de visibilité de la référence lorsque cette dernière est répertoriée. Nous ne prenons pas en charge cette fonctionnalité.
* Vérifiez que les logos suivent les instructions relatives aux logos Azure Marketplace données ci-dessous.
* La description de l’offre et celle de la référence ne peuvent pas être les mêmes.
* Le titre de la référence et le résumé long de l’offre ne peuvent pas être les mêmes.
* Le titre de la référence et le résumé de l’offre ne peuvent pas être les mêmes.
* Les titres de référence ne peuvent pas être les mêmes pour une offre avec plusieurs références.

**Instructions relatives au logo de la place de marché Azure**

* Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
* Les couleurs de thème du portail Azure sont le blanc et le noir. Par conséquent, évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc ou noir.
* N'utilisez pas d’arrière-plan dégradé sur le logo.
* Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo.
* L'apparence de votre logo doit être « plate » et éviter les dégradés.
* Le logo ne doit pas être étiré.

**Recommandations supplémentaires pour la bannière :**

* La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. **Toutefois l’icône ne peut pas être supprimée du portail de publication une fois téléchargée. À ce moment, le partenaire doit suivre les instructions d’Azure Marketplace pour les icônes, sans quoi l’offre ne sera pas approuvée en production.**
* Le nom d’affichage de l’éditeur, le titre de la référence et le résumé long de l’offre s’affichent en couleur de police blanche. Par conséquent, vous devez éviter de conserver toute couleur claire dans l’arrière-plan l’icône. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
* Le nom d’affichage de l’éditeur, le titre de la référence, le résumé long de l’offre et le bouton Créer sont incorporés programmatiquement à la bannière une fois l’offre répertoriée. Vous ne devez par conséquent pas saisir de texte lorsque vous concevez la bannière. Laissez un espace vide à droite, car nous incluons le texte (nom d’affichage de l’éditeur, titre de la référence SKU, résumé détaillé de l’offre) à cet emplacement par programmation. L’espace vide pour le texte doit être de 415 x 100 sur la droite (décalé de 370px à partir de la gauche).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Liste de contrôle de production supplémentaire pour les offres de machines virtuelles déjà répertoriées
* Vérifiez s’il existe déjà une offre de votre entreprise portant le même nom. Si Oui, vous devez ajouter une nouvelle version de la référence à l’offre existante au lieu de créer une offre en double.
* Le disque de données ne doit pas changer entre deux versions de la même référence.
* Azure Marketplace ne gère pas le changement de tarification des références répertoriées, car cela a un impact sur la facturation des clients existants. Assurez-vous que vous ne modifiez pas la tarification des références répertoriées dans les régions où la référence est disponible. Toutefois, vous pouvez ajouter de nouvelles références ou ajouter de nouvelles régions à une référence existante.

## <a name="next-steps"></a>Étapes suivantes
Une fois l'offre publiée, testez les scénarios de clients pour valider le fonctionnement correct de tous les contrats et de toutes les fonctionnalités dans l'environnement de production de la même manière qu'ils ont été testés et validés dans l'environnement intermédiaire.

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
