---
title: "Utiliser des révisions pour apporter des modifications sans rupture en toute sécurité dans le service Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à apporter des modifications sans rupture à l’aide de révisions dans Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 50d7ac17faebb34f1a1f9a3259aa0196950391d9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Utiliser des révisions pour apporter des modifications sans rupture en toute sécurité
Quand votre API est prête et commence à être utilisée par les développeurs, vous devez généralement veiller à ne pas interrompre les appelants de votre API quand vous apportez des modifications à cette dernière. Il est également utile d’informer les développeurs des modifications apportées. Les **révisions** le permettent dans Gestion des API Azure. Pour plus d’informations, consultez [Versions et révisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) et [Gestion des versions d’API avec la Gestion des API Azure](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une révision
> * Apporter des modifications sans rupture à une révision
> * Rendre cette révision actuelle et ajouter une entrée au journal des modifications
> * Parcourir le portail des développeurs pour voir les modifications et le journal des modifications

![Journal des modifications sur le portail des développeurs](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Prérequis

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-revision"></a>Ajouter une révision

1. Sélectionnez la page **API**.
2. Sélectionnez **API de conférence** dans la liste des API (ou une autre API à laquelle vous souhaitez ajouter des révisions).
3. Cliquez sur l’onglet **Révisions** dans le menu situé en haut de la page.
4. Sélectionnez **+ Ajouter une révision**

    > [!TIP]
    > Vous pouvez également choisir **Ajouter une révision** dans le menu contextuel (**...**) de l’API.
    
    ![Menu Révisions situé près du haut de l’écran](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Indiquez une description pour la nouvelle révision qui vous aidera à vous souvenir à quoi elle sert.
6. Sélectionnez **Créer**
7. Vous venez de créer une nouvelle révision.

    > [!NOTE]
    > Votre API d’origine reste dans **Revision 1**. Il s’agit de la révision que vos utilisateurs continuent à appeler, jusqu’à ce que vous choisissiez d’actualiser une autre révision.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apporter des modifications sans rupture à une révision

1. Sélectionnez **API de conférence de démonstration** dans la liste des API.
2. Sélectionnez l’onglet **Conception** situé près du haut de l’écran.
3. Notez que le **sélecteur de révision** (juste au-dessus de l’onglet Conception) affiche la révision actuelle en tant que **Revision 2**.

    > [!TIP]
    > Utilisez le sélecteur de révision pour basculer entre les révisions sur lesquelles vous souhaitez travailler.

4. Sélectionnez **+ Ajouter une opération**.
5. Définissez votre opération sur **POST**, puis attribuez au nom et au nom complet de l’opération la valeur **test**.
6. **Enregistrez** votre nouvelle opération.
7. Nous avons modifié **Revision 2**. Utilisez le **sélecteur de révision** situé près du haut de la page pour revenir à **Revision 1**.
8. Notez que votre nouvelle opération n’apparaît pas dans **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendre cette révision actuelle et ajouter une entrée au journal des modifications
1. Sélectionnez l’onglet **Révisions** dans le menu situé en haut de la page.

    ![Menu de révision sur l’écran de révision.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Ouvrez le menu contextuel (**...**) pour **Revision 2**.
2. Sélectionnez **Rendre actuel**. Cochez **Publier sur le journal des modifications public de cette API**, si vous souhaitez publier les remarques relatives à cette modification.
3. Sélectionnez **Publier sur le journal des modifications public de cette API**
4. Indiquez une description de votre modification que les développeurs peuvent voir, par exemple, **Test de révisions. Nouvelle opération « test » ajoutée.**
5. **Revision 2** est maintenant la révision actuelle.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Parcourir le portail des développeurs pour voir les modifications et le journal des modifications
1. Dans le Portail Azure, sélectionnez **APIs**.
2. Sélectionnez **Portail des développeurs** dans le menu supérieur.
3. Sélectionnez **API**, puis **API Conference**.
4. Notez que votre nouvelle opération **test** est désormais disponible.
5. Sélectionnez **Historique des modifications de l’API** sous le nom de l’API.
6. Notez que l’entrée du journal des modifications apparaît dans cette liste.

    ![Portail des développeurs](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une révision
> * Apporter des modifications sans rupture à une révision
> * Rendre cette révision actuelle et ajouter une entrée au journal des modifications
> * Parcourir le portail des développeurs pour voir les modifications et le journal des modifications

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Publier plusieurs versions de votre API](api-management-get-started-publish-versions.md)