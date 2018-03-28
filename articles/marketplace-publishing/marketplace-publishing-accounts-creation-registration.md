---
title: Création et enregistrement d’un compte de publication | Microsoft Docs
description: Instructions relatives à la création d’un compte de développeur Microsoft, qui vous permet, dès l’approbation du compte, de mettre en vente différents types d’offres sur Azure Marketplace.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: mbaldwin
ms.openlocfilehash: da805bead3f6af4ebd0b4b94b30a51ff2abc62dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-microsoft-developer-account"></a>Créer un compte de développeur Microsoft
Cet article décrit la procédure de création de compte et d’inscription que vous devez suivre pour devenir un développeur Microsoft agréé pour Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Création d'un compte Microsoft
Pour démarrer le processus de publication, vous devez créer un compte Microsoft. Celui-ci vous permettra de vous enregistrer au **Centre de développement Microsoft** et au **portail de publication Azure**. Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres spécifiques.

L’adresse correspondant au nom d’utilisateur doit figurer dans votre domaine et être contrôlée par votre équipe informatique. Toutes les activités de publication connexes doivent être effectuées via ce compte.

> [!WARNING]
> Les termes tels qu’« **Azure** » et « **Microsoft** » ne sont pas pris en charge pour l’inscription d’un compte Microsoft. Évitez de les utiliser si vous souhaitez finaliser le processus de création du compte et d’enregistrement.
>
>

### <a name="guidelines-for-company-accounts"></a>Instructions en matière de comptes de société
Lorsque vous créez un compte de société, suivez ces instructions si plusieurs personnes devront accéder au compte en se connectant avec le compte Microsoft qui a ouvert le compte.

> [!Important]
> Important : pour permettre à plusieurs utilisateurs d’accéder à votre compte Centre de développement, nous vous recommandons d’utiliser Azure Active Directory pour assigner des rôles aux différents utilisateurs, qui peuvent accéder au compte en se connectant avec leurs informations d’identification Azure AD. Pour plus d’informations, consultez [Gérer les utilisateurs du compte](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Créez votre compte Microsoft avec une adresse e-mail qui appartient au domaine de votre entreprise, mais pas à une seule personne, par exemple, windowsapps@fabrikam.com.
* Limitez l’accès à ce compte Microsoft au plus petit nombre possible de développeurs.
* Créez une liste de distribution d’entreprise qui inclut tous les utilisateurs devant accéder au compte de développeur et ajoutez cette adresse e-mail à vos informations de sécurité. Ainsi, tous les employés de la liste peuvent recevoir les codes de sécurité si nécessaire et gérer les informations de sécurité de votre compte Microsoft. Si la création d’une liste de distribution n’est pas possible, le propriétaire du compte devra pouvoir être joignable pour accéder au code de sécurité et le partager si nécessaire (par exemple, lorsque de nouvelles informations de sécurité sont ajoutées au compte ou lorsque le compte doit être utilisé depuis un nouvel appareil).
* Ajoutez un numéro de téléphone d’entreprise qui ne nécessite pas d’extension et qui reste accessible par les membres de l’équipe.
* En règle général, les développeurs doivent utiliser des appareils de confiance pour se connecter au compte de développeur de votre société. Les membres principaux de l’équipe doivent avoir accès à ces appareils de confiance. Cela réduit le besoin d’envoyer les codes de sécurité pour accéder au compte.
* Si vous souhaitez autoriser l’accès au compte à partir d’un ordinateur non approuvé, limitez cet accès à un maximum de cinq développeurs. Dans l’idéal, ces développeurs doivent accéder au compte à partir d’ordinateurs qui partagent le même emplacement géographique et le même emplacement réseau.
* Consultez régulièrement les informations de sécurité de votre entreprise à l’adresse [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) pour vérifier qu’elles sont à jour.

Votre compte de développeur doit être principalement utilisé à partir d’ordinateurs approuvés. Cela est essentiel, car il existe une limite au nombre de codes générés par semaine pour chaque compte. Ceci rend également l’expérience de connexion plus transparente.

Pour plus d’informations sur la sécurité et sur les instructions relatives au compte de développeur, cliquez [ici](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instructions
1. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte existant.
2. Inscrivez l’e-mail (en suivant les instructions ci-dessus, par exemple windowsapp@fabrikam.com), en tant que compte Microsoft à l’aide du lien [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Procédez comme suit.

   1. Lors de l’inscription de votre compte comme un compte Microsoft, vous devez fournir un numéro de téléphone valide pour que le système vous envoie un code de vérification de compte sous forme de message texte ou d’appel automatique.
   2. Lors de l’inscription de votre compte comme un compte Microsoft, vous devez fournir un ID de messagerie valide pour recevoir un courrier automatique pour la vérification du compte.
3. Vérifiez l'adresse de messagerie envoyée à la liste de distribution.
4. Vous êtes maintenant prêt à utiliser votre nouveau compte Microsoft dans le Centre de développement Microsoft.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Inscription de votre compte dans le centre de développement Microsoft
Le Centre de développement Microsoft est utilisé pour enregistrer les informations de la société en une seule opération. Pour pouvoir vous inscrire, vous devez agir en tant que représentant agréé de l’entreprise ; vous aurez à fournir des informations personnelles afin de valider votre identité. Vous devrez également utiliser un compte Microsoft partagé pour l’ensemble de l’entreprise et utiliser **le même compte dans le portail de publication Azure**. Vérifiez que votre entreprise ne possède pas déjà de compte Centre de développement Microsoft avant d’essayer d’en créer un. Dans le cadre de ce processus, nous recueillons l’adresse de votre entreprise, ainsi que vos informations bancaires et fiscales. Vous pouvez généralement obtenir ces informations auprès du service financier ou commercial.

> [!IMPORTANT]
> Vous devez renseigner les éléments de profil de développeur suivants pour avancer dans les différentes phases de création et de déploiement d’offres.
>
>

| Profil de développeur | Pour démarrer le projet | Staging | Publier un produit gratuit et un modèle de solution | Publier un produit commercial |
| --- | --- | --- | --- | --- |
| Enregistrement de l'entreprise |Obligatoire |Obligatoire |Obligatoire |Obligatoire |
| Numéro fiscal |Facultatif |Facultatif |Facultatif |Obligatoire |
| Compte bancaire |Facultatif |Facultatif |Facultatif |Obligatoire |

> [!NOTE]
> Le concept BYOL (avec apport de sa propre licence) est pris en charge uniquement pour les machines virtuelles et est considéré comme une offre **gratuite** .
>
>

### <a name="register-your-company-account"></a>Enregistrer votre compte de société
1. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n’êtes pas connecté à un compte personnel.
2. Accédez à [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) pour vous inscrire en tant que vendeur dans le centre de développement. Veuillez lire la remarque importante ci-dessous avant de continuer.

   > [!IMPORTANT]
   > Vérifiez que l’ID de messagerie ou la liste de distribution (une liste de distribution est recommandée pour supprimer la dépendance à des personnes spécifiques) que vous utiliserez pour l’inscription dans le centre de développement est d’abord enregistrée comme un compte Microsoft. Dans le cas contraire, effectuez l’enregistrement en suivant ce [lien](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). En outre, **il est impossible d’utiliser un ID de messagerie du domaine de l’entreprise Microsoft, à savoir @microsoft.com** pour l’inscription sur le centre de développement.
   >
   >

    ![drawing][img-signin]
3. Suivez les étapes de l’Assistant Aidez-nous à protéger votre compte. Celui-ci vérifie votre identité via un numéro de téléphone ou une adresse de messagerie.

    ![drawing][img-verify]
4. Dans la section « Inscription - Informations sur le compte », sélectionnez l’option **Pays/région du compte** dans le menu déroulant.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **Pays à partir duquel vous vendez :** avant de pouvoir vendre vos services dans Azure Marketplace, l’entité inscrite doit résider dans l’un des pays autorisés. Cette restriction s’applique pour des raisons de revenus et de taxes. Nous cherchons activement à étendre cette liste de pays dans un avenir proche, donc restez connecté. Pour plus d’informations, consultez [Politiques concernant la participation à Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Sélectionnez votre **Entreprise** comme Type de compte, puis cliquez sur le bouton **Suivant**.

   > [!IMPORTANT]
   > Pour mieux comprendre les types de comptes et choisir celui qui vous convient le mieux, consultez la page [Types de compte, emplacements et frais](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Entrez le **Nom complet de l’éditeur**, en général le nom de votre société.

   > [!TIP]
   > Le nom d’affichage de l’éditeur entré dans le centre de développement ne figure pas dans Azure Marketplace une fois que votre offre est répertoriée. Toutefois, ce champ doit être complété pour terminer le processus d’inscription.
   >
   >
7. Entrez les **coordonnées** pour la vérification du compte.

   > [!IMPORTANT]
   > Vous devez fournir des coordonnées précises, car elles seront utilisées dans notre processus de vérification de votre société pour l’approbation dans le Centre de développement.
   >
   >
8. Saisissez les coordonnées de **l’approbateur de l’entreprise**. L’approbateur de l’entreprise est la personne qui peut vérifier que vous êtes autorisé à créer un compte dans le centre de développement au nom de votre organisation. Quand vous avez terminé, cliquez sur **Suivant** pour passer à la **« section Paiement »**.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Entrez vos informations de paiement pour le paiement de votre compte. Si vous disposez d’un code promotionnel qui couvre le coût de l’inscription, vous pouvez le saisir ici. Sinon, fournissez vos informations de carte de crédit (PayPal est pris en charge pour certains marchés). Quand vous avez terminé, cliquez sur **Suivant** pour passer à la **« section Vérification »**.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Vérifiez vos informations de compte et confirmez que tout est correct. Ensuite, lisez et acceptez les conditions du [Contrat d’éditeur Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Cochez la case pour indiquer vous avez lu et que vous acceptez ces conditions.
11. Cliquez sur **Terminer** pour confirmer votre inscription. Nous enverrons un message de confirmation à votre adresse de messagerie.
12. Si vous envisagez de publier uniquement des offres gratuites, cliquez sur **Accéder au portail de publication Azure Marketplace** , et passez à la section 3 de ce document, [Enregistrer votre compte dans le Portail de publication](#3-register-your-account-in-the-publishing-portal).

Si vous envisagez de publier des offres commerciales (par ex. des offres de machine virtuelle avec modèle de facturation à l’heure), cliquez sur **Mettre à jour vos informations de compte**. Vous devrez alors remplir vos informations bancaires et fiscales dans votre compte Centre de développement.

Si vous préférez mettre à jour les informations fiscales et bancaires ultérieurement, vous pouvez accéder à la section suivante, à savoir la section 3 de ce document, [Enregistrer votre compte dans le Portail de publication](#3-register-your-account-in-the-publishing-portal), puis revenir ici ultérieurement en utilisant les liens dans le portail de publication Azure.

> [!IMPORTANT]
> En cas d’offres commerciales, vous ne pourrez pas mettre en production vos offres si vous n’avez pas renseigné vos informations fiscales et bancaires.
>
>

Si vous préférez mettre à jour les informations fiscales et bancaires ultérieurement, vous pouvez accéder à la section 3 [Enregistrer votre compte dans le Portail de publication](#3-register-your-account-in-the-publishing-portal), puis revenir ici ultérieurement en utilisant les liens dans le portail de publication Azure.

### <a name="add-tax-and-banking-information"></a>Ajouter des informations fiscales et bancaires
 Si vous souhaitez mettre en vente des offres commerciales, vous devez également ajouter vos informations de paiement et vos données fiscales, puis les soumettre pour validation dans le Centre de développement. Si vous souhaitez publier uniquement des offres gratuites (ou offres BYOL), vous n’aurez pas lieu d’ajouter ces informations. Vous pourrez les ajouter ultérieurement, mais sachez que la validation des informations fiscales peut prendre un certain temps. Si vous savez que vous allez proposer des offres commerciales à l’achat, nous vous recommandons d’ajouter ces informations dès que possible.

**Informations bancaires**

1. Connectez-vous au [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) avec votre compte Microsoft.
2. Cliquez sur **Compte de paiement** dans le menu de gauche, puis sous **Choisir le moyen de paiement**, cliquez sur **Compte bancaire** ou sur **PayPal**.

   > [!IMPORTANT]
   > Si des clients achètent vos offres commerciales sur le Marketplace, il s’agit du compte qui sera crédité du montant de ces achats.
   >
   >
3. Entrez les informations de paiement, puis cliquez sur **Enregistrer** lorsque vous êtes satisfait.

   > [!IMPORTANT]
   > Si vous devez mettre à jour ou modifier votre compte de paiement, suivez les mêmes étapes ci-dessus, en remplaçant les informations actuelles par les nouvelles informations. La modification de votre compte de paiement peut retarder vos paiements jusqu’à un cycle de paiement. Ce retard se produit parce que nous devons vérifier le changement de compte, comme nous l’avons fait lorsque vous avez configuré le compte de paiement pour la première fois. Vous serez payé pour le montant total une fois que votre compte aura été vérifié. Tout paiement dû dans le cycle de paiement actuel sera ajouté au cycle suivant
   >
   >
4. Cliquez sur **Suivant**.

**Informations fiscales**

1. Connectez-vous au [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) avec votre compte Microsoft (si nécessaire).
2. Cliquez sur **Profil fiscal** dans le menu de gauche.
3. Sur la page **Établir votre déclaration d’impôts** , sélectionnez le pays ou la région où vous avez une résidence permanente, puis sélectionnez le pays ou la région dont vous êtes ressortissant. Cliquez sur **Suivant**.
4. Saisissez vos informations fiscales, puis cliquez sur **Suivant**.

> [!WARNING]
> Vous ne pourrez pas mettre en production vos offres commerciales si vous n’avez pas renseigné vos informations fiscales et bancaires dans votre compte Centre de développement Microsoft.
>
>

Si vous rencontrez des problèmes pendant l’inscription au Centre de développement, ouvrez un ticket de support comme indiqué ci-dessous

1. Allez au lien de la prise en charge [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Sous la section **Nous contacter**, cliquez sur le bouton **Soumettre un incident** (comme illustré dans la capture d’écran ci-dessous)

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Choisissez « Aide avec le centre de développement » comme **Type de problème** et « Publication et gestion des applications » en tant que **Catégorie**. Cliquez ensuite sur le bouton « Démarrer messagerie ».

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Vous irez vers une page de connexion. Utilisez un compte Microsoft pour la connexion. Si vous n’avez pas de compte Microsoft, créez-en un en utilisant ce [lien](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Remplissez les détails du problème et envoyez le ticket de support en cliquant sur le bouton **Envoyer** .

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Enregistrer votre compte dans le Portail de publication
Le [Portail de publication](http://publish.windowsazure.com) permet de publier et gérer vos offres.

1. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte personnel.
2. Accédez à [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Si vous êtes un nouvel utilisateur que vous vous connectez au portail de publication pour la première fois, vous devez vous connecter avec le même ID de messagerie que celui utilisé sur votre centre de développement. De cette façon, votre compte de centre de développement et votre compte de portail de publication seront liés entre eux. Vous pouvez ultérieurement ajouter les autres membres de l’entreprise, qui travaillent sur l’application, comme coadministrateurs dans le portail de publication en suivant les étapes ci-dessous.

Si vous êtes ajouté en tant que coadministrateur dans le portail de publication, vous pouvez vous connecter avec votre compte de coadministrateur.

> [!TIP]
> Les politiques concernant la participation sont décrites sur le [site web Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Étapes pour ajouter un coadministrateur dans le portail de publication
**En supposant que vous êtes l’administrateur,** voici les étapes pour ajouter un coadministrateur.

> [!NOTE]
> **Pour les nouveaux utilisateurs,** avant d’ajouter un coadministrateur au portail de publication, assurez-vous que vous avez créé au moins une application dans le portail de publication. Cela est nécessaire, car l’onglet **ÉDITEURS** s’affiche uniquement après la création d’au moins une application dans le portail de publication.
>
>

1. Assurez-vous que l’ID de messagerie du coadministrateur est un compte Microsoft (MSA). Si ce n’est pas le cas, enregistrez-le comme MSA à l’aide de ce [lien](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Vérifiez qu’il existe au moins une application sous le compte administrateur avant d’essayer d’ajouter un coadministrateur.
3. Une fois les étapes ci-dessus effectuées, connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur et déconnectez-vous.
4. Maintenant, connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur.
5. Accédez à Éditeurs->Sélectionnez votre compte->Administrateurs->Ajouter le compte (voir capture d’écran ci-dessous)

   ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Étapes pour supprimer un coadministrateur dans le portail de publication
**En supposant que vous êtes l’administrateur,** voici les étapes pour supprimer un coadministrateur.

1. Connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur.
2. Accédez à **Éditeurs** -> sélectionnez votre compte -> **Administrateurs** -> **Coadministrateurs**.
3. Cliquez sur le bouton **X** en regard du coadministrateur que vous souhaitez supprimer (capture d’écran ci-dessous).

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre compte est créé et enregistré, assurez-vous de répondre à toutes les conditions préalables non techniques requises pour publier votre offre en consultant l’article [Conditions préalables non techniques](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
