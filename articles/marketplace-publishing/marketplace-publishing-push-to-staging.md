---
title: Préparer et tester votre offre pour le déploiement sur Azure Marketplace | Microsoft Docs
description: Instructions détaillées sur l’indication de contenu marketing, la configuration de plans de tarification et le test de votre offre avant le déploiement sur Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: mbaldwin
ms.openlocfilehash: 5bec9ac89487a824b9de9209547f0b96fa5fe678
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Compléter la création d’offre avec du contenu marketing
Dans cette étape du processus de publication, vous devez fournir du contenu marketing et des informations sur votre offre et/ou vos références SKU dans Azure Marketplace. Par exemple, vous devez fournir une description de votre produit, le logo de votre entreprise, les offres tarifaires, le détail des offres et d’autres informations nécessaires pour envoyer votre offre et/ou référence SKU dans l’environnement intermédiaire. Ces informations sont utilisées comme contenu marketing dans le portail Azure. Vous allez commencer ce processus dans le [portail de publication][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Étape 1 : Fournir du contenu marketing au Marketplace
**L’anglais est la langue par défaut et la seule prise en charge.** Assurez-vous que toutes les informations fournies dans les champs sont en anglais. Toutes les informations peuvent être modifiées à tout moment jusqu’à ce que vous procédiez au déploiement dans un environnement intermédiaire.

1. Allez au portail de publication, [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. Dans le menu de gauche, cliquez sur l’onglet **Marketing** .
3. Dans le volet principal, cliquez sur le bouton **English (US)** .
   
   > [!IMPORTANT]
   > Tous les champs doivent avoir des entrées, y compris les images, pour que vous puissiez procéder au déploiement dans un environnement intermédiaire.
   > 
   > 

### <a name="details-and-plans"></a>Détails et plans
1. Entrez le titre de l'offre (50 caractères maximum), le résumé de l’offre (100 caractères maximum), le résumé long de l’offre (256 caractères maximum), la description de l’offre (1 300 caractères maximum) et les logos sous l’onglet **Détails**
2. Entrez le titre du plan (50 caractères maximum), le résumé du plan (100 caractères maximum), la description du plan (2000 caractères maximum) sous l’onglet **Plans** .
   
   > [!NOTE]
   > Vous pouvez utiliser les balises HTML suivantes pour mettre en forme le résumé, le résumé long et la description de l’offre et des plans. Les balises HTML autorisées sont « h1 »,« h2 », « h3 », « h4 », « h5 », « p », « ol », « ul », « li », « a[target|href] », « br », « strong », « em », « b », « i ».
   > 
   > 
3. N’entrez pas deux fois le même texte dans la description de l’offre et du plan.
4. N’entrez pas deux fois le même texte sous le titre du plan et le résumé long de l’offre.
5. N’entrez pas deux fois le même texte sous le titre du plan et le résumé de l’offre.
6. N’entrez pas de titres de plan identiques pour une offre avec plusieurs plans.
7. Téléchargez des images des spécifications requises (mentionnées sur le portail de publication) au format PNG, une pour chaque taille.
8. Vérifiez que les logos suivent les instructions relatives aux logos Azure Marketplace indiquées ci-dessous.
   
   ![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Instructions relatives aux logos Azure Marketplace**

Tous les logos téléchargés dans le portail de publication doivent respecter les instructions ci-dessous :

* Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
* Les couleurs de thème du portail Azure sont le blanc et le noir. Par conséquent, évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. **Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc, noir ou bleu.**
* N'utilisez pas d’arrière-plan dégradé sur le logo.
* Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo. L'apparence de votre logo doit être « plate » et éviter les dégradés.
* Le logo ne doit pas être étiré.
* Les petits logos doivent être de la taille 40 x 40 px
* Les logos moyens doivent être de la taille 90 x 90 px
* Les grands logos doivent être de la taille 115 x 115 px
* Les logos larges doivent être de la taille 255 x 115 px
* Les bannières doivent être de la taille 815 x 290 px

> [!NOTE]
> La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. Toutefois l’icône ne peut pas être supprimée du portail de publication une fois téléchargée. À ce moment, le partenaire doit suivre les instructions d’Azure Marketplace pour les icônes.
> 
> 

  ![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Recommandations supplémentaires pour les icônes de bannière (facultatif) :**

* La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. **Toutefois l’icône ne peut pas être supprimée du portail de publication une fois téléchargée. À ce moment, le partenaire doit suivre les instructions d’Azure Marketplace pour les icônes, sans quoi l’offre ne sera pas approuvée en production.**
* Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche. Par conséquent, vous devez éviter de conserver toute couleur claire dans l’arrière-plan l’icône. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
* Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés automatiquement à la bannière une fois l’offre publiée. Vous ne devez par conséquent pas saisir de texte lorsque vous concevez la bannière. Laissez un espace vide à droite, car nous incluons le texte (nom d’affichage de l’éditeur, titre du plan, résumé détaillé de l’offre) par programmation à cet emplacement. L’espace vide pour le texte doit être de 415 x 100 sur la droite (décalé de 370px à partir de la gauche).
  
  ![drawing](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Liens
Sous l’onglet **Links** (Liens) de la barre de gauche, entrez des liens utiles ainsi que toute information susceptible d’aider les clients. Entrez un nom et une URL pour chaque lien.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Images d’exemple (facultatif)
> [!NOTE]
> L’ajout d’une image d’exemple est une étape facultative.
> Même si vous pouvez télécharger plusieurs exemples d’images dans le portail de publication, une seule image (sélectionnée au hasard par le système) s’affiche dans le portail Azure. Pour cette raison, nous vous recommandons de télécharger au plus un exemple d’image.
> 
> 

Sous l’onglet **Sample Images** (Images d’exemple) dans le menu de gauche, cliquez sur **Télécharger une nouvelle image** pour télécharger une image. Si vous avez déjà chargé une image que vous voulez remplacer, cliquez sur **Remplacer l’image**.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Informations juridiques
Sous l’onglet **Legal** (Informations légales), indiquez un lien vers votre politique ou vos conditions d’utilisation. Entrez ou collez les conditions d’utilisation dans la grande zone **Conditions d’utilisation** . La limite de caractères pour les conditions d'utilisation est de 1 000 000 caractères.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Remarque :** pour les offres de machines virtuelles, une fois qu’une offre/référence est présentée dans le portail Azure, vous ne pouvez pas modifier les champs ci-dessous :

* **Identificateur de l’offre :** [Portail de publication -&gt; Machines virtuelles -&gt; Votre offre -&gt; Onglet Images de machines virtuelles -&gt; Identificateur de l’offre]
* **Identificateur de référence :** [Portail de publication -&gt; Machines virtuelles -&gt; Sélectionnez votre offre -&gt; Onglet Références -&gt; Ajouter une référence]
* **Espace de noms d’éditeur :** [Portail de publication -&gt; Machines virtuelles -&gt; Onglet Procédure pas à pas (en « Étape 2 : inscrire votre entreprise ») -&gt; Espace de noms d’éditeur -&gt; Espace de noms]

Pour les offres de machines virtuelles, une fois qu’une offre/référence est présentée dans Azure Marketplace, vous ne pouvez pas modifier les champs ci-dessous :

* **Identificateur de l’offre :** [Portail de publication -&gt; Machines virtuelles -&gt; Sélectionnez votre offre -&gt; Images de machines virtuelles -&gt; Identificateur de l’offre]
* **Identificateur de référence :** [Portail de publication -&gt; Machines virtuelles -&gt; Sélectionnez votre offre -&gt; Onglet Références -&gt; Ajouter une référence]
* **Espace de noms d’éditeur :** [Portail de publication -&gt; Machines virtuelles -&gt; Onglet Procédure pas à pas (en Étape 2 : inscription) -&gt; Espace de noms d’éditeur -&gt; Espace de noms]
* **Ports :** [Portail de publication -&gt; Machines virtuelles -&gt; Votre offre -&gt; Onglet Images de machines virtuelles -&gt; Ports ouverts]
* **Modification de la tarification des références répertoriées**
* **Modification du modèle de facturation des références répertoriées**
* **Suppression des régions de facturation des références répertoriées**
* **Modification du nombre de disques de données des références répertoriées**

## <a name="step-2-set-your-prices"></a>Étape 2 : Définir vos tarifs
### <a name="pricing-models"></a>Modèles de tarification
| Modèle de tarification | Description |
| --- | --- |
| De base |Forfait mensuel payé au moment de l’achat, par exemple 10 $ par mois |
| Consommation (également appelé Utilisation, Compteur) |Paiement à l’utilisation, définie par l’éditeur de l’offre. Le dépassement ne peut pas être exprimé en nombre d’utilisateurs ou de licences, puisqu’il est impossible d’utiliser une fraction d’utilisateur ou de calculer un prorata. L’utilisation est signalée par le partenaire sur une base horaire. Le client paye au terme du cycle de facturation mensuel, et non en avance comme pour les offres mensuelles. |
| Essai gratuit |Le client jouit d’une utilisation gratuite pendant une durée limitée, puis paye un tarif normal. |
| Niveau Gratuit |L’offre est toujours gratuite. |
| Migration (également appelé conversion ou mise à niveau/rétrogradation) de l’offre |L’utilisateur change son offre actuelle pour une autre offre autorisée, définie par le partenaire. |

**Modèles de tarification disponibles par type d’offre**

> [!IMPORTANT]
> La disponibilité de certains modèles de tarification varie en fonction du type d’offre. Consultez le tableau ci-dessous.
> 
> 

|  | De base uniquement | Consommation uniquement | De base + Consommation |
| --- | --- | --- | --- |
| Image de machine virtuelle |Non  |OUI |Non  |
| Service de développement |OUI |OUI |OUI |

### <a name="21-set-your-vm-prices"></a>2.1. Définir les prix de votre machine virtuelle
Actuellement, nous proposons les **3 types de modèles de facturation**

* **Horaire :** les clients sont facturés à l’heure en fonction des tarifs définis par les éditeurs sur les tailles de machine virtuelle. Dans le cas du modèle de **facturation horaire** des références, le prix total est la somme du coût logiciel facturé par l’éditeur et du coût d’infrastructure facturé par Microsoft. Ce coût total s’affichera pour le client sous forme de tarif horaire et mensuel lorsqu’ils considéreront l’achat (voir la capture d’écran ci-dessous). **L’éditeur reçoit 80 % du coût logiciel qu’il facture.** Par conséquent, calculez vos tarifs en conséquence avant de définir les prix pour vos références.
  
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Version d’évaluation gratuite :** il s’agit d’une autre version du modèle horaire. Ici, le client n’est pas facturé pour le coût logiciel (gratuit) pendant les 30 premiers jours après le déploiement de la machine virtuelle. Après ces 30 jours, les clients sont facturés à l’heure en fonction des tarifs définis par les éditeurs dans le modèle horaire.
* **Apportez votre propre licence (BYOL) :** les éditeurs gèrent les licences des logiciels exécutés sur la machine virtuelle.

**Important :** une fois qu’une offre/référence est présentée dans Azure Marketplace, vous ne pouvez pas modifier les champs ci-dessous.

* **Modification de la tarification des références répertoriées**
* **Modification du modèle de facturation des références répertoriées**
* **Suppression des régions de facturation des références répertoriées**
* **Modification du nombre de disques de données des références répertoriées**
* **Identificateur de l’offre :** [Portail de publication -&gt; Machines virtuelles -&gt; Sélectionnez votre offre -&gt; Images de machines virtuelles -&gt; Identificateur de l’offre]
* **Identificateur de référence :** [Portail de publication -&gt; Machines virtuelles -&gt; Sélectionnez votre offre -&gt; Onglet Références -&gt; Ajouter une référence]
* **Espace de noms d’éditeur :** [Portail de publication -&gt; Machines virtuelles -&gt; Onglet Procédure pas à pas (en Étape 2 : inscription) -&gt; Espace de noms d’éditeur -&gt; Espace de noms]
* **Ports :** [Portail de publication -&gt; Machines virtuelles -&gt; Votre offre -&gt; Onglet Images de machines virtuelles -&gt; Ports ouverts]

### <a name="sell-to-countries-of-the-sku"></a>Pays cibles de la référence
Vous devez considérer soigneusement dans lesquels vous rendrez vos références disponibles. Certains pays sont classés comme « Versement par Microsoft » et d’autres comme « Versement par l’éditeur indépendant ».

* Dans les pays « Versement par Microsoft », Microsoft collecte les taxes auprès des clients et paie (verse) les taxes au gouvernement.
* Dans les pays « Versement par l’éditeur indépendant », les partenaires sont responsables de la collecte des taxes et de leur versement aux gouvernements respectifs. Si vous choisissez de vendre dans des pays « Versement par l’éditeur indépendant », vous devez avoir les capacités de calculer et payer des taxes dans les pays que vous sélectionnez.

> [!NOTE]
> Votre référence ne sera pas disponible dans un pays tant que vous ne définissez pas les tarifs dans le [Portail de publication](https://publish.windowsazure.com). Vous trouverez ci-dessous des instructions pour définir la tarification des références horaires et BYOL.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 Comment configurer le modèle de tarification horaire pour une référence
Suivez les étapes ci-dessous pour configurer un modèle de tarification horaire pour une référence :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** .
4. Assurez-vous que la référence est marquée comme « Modèle de facturation horaire ». Dans le cas contraire, cliquez sur le bouton **MODIFIER** pour rétablir le modèle de facturation. Une fenêtre s’affiche. Décochez la case « La facturation et la gestion des licences sont traitées à l’extérieur d’Azure (apportez votre propre licence) » et enregistrez les modifications.
5. Si vous souhaitez activer l’évaluation gratuite pour les 30 premiers jours du déploiement de la référence, sélectionnez l’option « Un mois » à la question « Une version d’évaluation gratuite est-elle disponible ? » Sinon, sélectionnez l’option « Aucune version d’évaluation ». Suivez maintenant les étapes indiquées ci-dessous.
6. Dans le menu de gauche, cliquez sur l’onglet **TARIFICATION** .
7. Sélectionnez votre région de base.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Définissez les prix de tous les cœurs. **Vous devez fournir des prix pour tous les cœurs d’une référence (SKU), même si votre référence ne le prend pas en charge.**
   
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Définissez les prix pour les autres régions manuellement, ou utilisez l’assistant TARIFICATION AUTOMATIQUE pour définir les prix des autres régions en fonction de la région de base. Pour utiliser l’assistant TARIFICATION AUTOMATIQUE, cliquez sur le bouton **TARIFICATION AUTOMATIQUE POUR LES AUTRES MARCHÉS EN FONCTION DES PRIX POUR LES ÉTATS-UNIS.** **Remarque :** le libellé du bouton peut être différent selon la région que vous avez sélectionnée. Comme nous avons sélectionné les États-Unis lors de la création de ce document, le bouton est intitulé « Tarification automatique pour les autres marchés en fonction des pris pour les États-Unis » dans la capture d’écran ci-dessous.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. L’Assistant de tarification automatique s’ouvre. La première page affiche la sélection pour le marché de base. Effectuez votre sélection et passez à la page suivante en cliquant sur le bouton « -> ».
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. L’option permettant de sélectionner les cœurs et les plans s’affichera sur la page 2. Sélectionnez les plans voulus, puis cliquez sur « -> ». Cliquez sur le bouton **Activer/désactiver tout** pour sélectionner tous les **plans de service** et **compteurs**, ou vous pouvez cocher manuellement les cases. **Vous devez fournir des prix pour tous les cœurs d’une référence (SKU), même si votre référence ne le prend pas en charge.** Par conséquent, assurez-vous que toutes les tailles de cœur sont sélectionnées.
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. La page 3 affiche les marchés et régions. Cliquez sur le bouton **Activer/désactiver tout** pour sélectionner toutes les régions, ou cochez-les manuellement. Cliquez sur le bouton « -> » pour passer à la page suivante. **Remarque :** les pays pour lesquels Microsoft verse les taxes sont indiqués par un symbole de maison. Pour plus d’informations, consultez la section Pays cibles de la référence sur cette page.
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. La page 4 affiche le taux de change. Cliquez sur le bouton Terminer pour terminer la procédure.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 Comment configurer le modèle de tarification BYOL pour une référence
Suivez les étapes ci-dessous pour configurer un modèle de tarification BYOL pour une référence :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** .
4. Assurez-vous que la référence est marquée comme « Apportez votre propre licence ». Dans le cas contraire, cliquez sur le bouton MODIFIER pour rétablir le modèle de facturation. Une fenêtre s’affiche. Cochez la case « La facturation et la gestion des licences sont traitées à l’extérieur d’Azure (apportez votre propre licence) » et enregistrez les modifications.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. Dans le menu de gauche, cliquez sur l’onglet **TARIFICATION** .
6. Sélectionnez la région de base et rendez la référence disponible dans la région en cochant la case à côté de la référence sous la section DISPONIBILITÉ DE LA RÉFÉRENCE AVEC LICENCE EXTERNE (BYOL) (voir la capture d’écran ci-dessous).
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Rendez la région disponible dans les autres régions manuellement ou utilisez l’assistant TARIFICATION AUTOMATIQUE à cette fin. Consultez les points #9 à &#13; (qui détaillent l’utilisation de l’assistant de tarification automatique) dans la section **2.1.1 Comment configurer le modèle de tarification horaire pour une référence** de cette page.

### <a name="22-set-your-developer-service-prices"></a>2.2. Définir le prix de vos services de développement
Les offres peuvent constituer une combinaison quelconque de tarifs De base + Consommation, où De base est le tarif mensuel et Dépassement est le tarif à l’utilisation. (Voir détails ci-dessous)

**Exemple :** offre de services de développement Contoso

| Planification | Prix | Inclus | Chemin de migration |
| --- | --- | --- | --- |
| Gratuit |0 $/mois |Fonctionnalités de base. |Peut migrer vers une autre offre |
| Bronze |10 $/mois |Fonctionnalités de base et un quota de 1 000 pour la fonctionnalité X. |Possibilité de migration vers les offres Bronze Plus, Silver et Gold |
| Bronze Plus |Période d’essai gratuite : 0 $/mois + 0 $/meter01 |Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01. |Possibilité de migration vers les offres Silver Plus et Gold |
| Bronze Plus |Période payée (également appelé essai gratuit expiré) : 10 $/mois + 0,05 $/meter01 |Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01. |Possibilité de migration vers les offres Silver Plus et Gold |
| Silver |0,15 $/meter01 |Le client peut payer à l’utilisation en fonction de meter01, qui s’applique à la fonctionnalité X. |Possibilité de migration vers les offres Bronze et Gold |
| Silver Plus |20 $/mois + 0,15 $/meter01 + 0,01 $/meter02 |Fonctionnalités de base et un quota de 10 000 pour la fonctionnalité X et de 100 pour la fonctionnalité Y. Une fois le quota pour la fonctionnalité X utilisé, le client peut payer en fonction de l’utilisation via meter01.  Une fois le quota pour la fonctionnalité Y utilisé, le client peut payer en fonction de l’utilisation via meter02. |Possibilité de migration vers les offres Bronze Plus et Gold |
| Gold |1 000 $ par mois |Quota de 10 000 pour la fonctionnalité X, 1 000 pour la fonctionnalité Y et illimité pour la fonctionnalité Z. |Permet de migrer vers toutes les offres sauf Gratuit |

## <a name="step-3-provide-support-information"></a>Étape 3 : Fournir les informations de support
Les coordonnées sont utilisées uniquement pour les communications internes entre le partenaire et Microsoft. L’URL de support sera disponible pour les clients finaux.

1. Accédez à l’en-tête **Support** à gauche du portail de publication.
2. Entrez les informations sous **Engineering Contact**(Contact Ingénieur).
3. Entrez les informations sous **Customer Support**(Service client). Si vous indiquez uniquement un support électronique, entrez un numéro de téléphone factice, et l’adresse de messagerie fournie sera utilisée.
4. Entrez l’URL de support.

## <a name="step-4-choose-azure-marketplace-categories"></a>Étape 4 : Choisir les catégories Azure Marketplace
L’onglet **Categories** (Catégories) offre diverses options. Votre offre peut appartenir à l’une d’elles et vous pouvez sélectionner jusqu’à cinq catégories.

## <a name="how-your-marketing-will-appear"></a>Présentation de votre contenu marketing
Voici une vue détaillée de l’utilisation des informations marketing de l’offre sur le [site web Azure Marketplace](https://azure.microsoft.com/marketplace/) et dans le [portail Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Site web Azure Marketplace
![drawing](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![drawing](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Liste des offres sur le site web Azure Marketplace*

![drawing](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Détails de la description de l’offre sur le site web Azure Marketplace*

![drawing](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Détails de tarification de la description de l’offre sur le site web Azure Marketplace*

### <a name="azure-portal"></a>Portail Azure
![drawing](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Liste des offres dans le portail Azure*

![drawing](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Détails de la description de l’offre sur le portail Azure*

## <a name="next-steps"></a>Étapes suivantes
Votre contenu Marketplace étant chargé, nous allons maintenant tester votre offre dans l’environnement intermédiaire. Vous devez toutefois sélectionner le type d’offre approprié dans la liste ci-dessous, car les étapes diffèrent selon le type d’offre.

* [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md)
* [Tester votre offre de modèle de solution en mode intermédiaire](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
