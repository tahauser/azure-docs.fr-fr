---
title: "Ajouter un domaine personnalisé à Azure AD | Microsoft Docs"
description: "Explique comment ajouter un domaine personnalisé dans Azure Active Directory."
services: active-directory
author: curtand
manager: mtillman
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: e7b85d5f4cd19c94fe904f16090e174d87ea120b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Démarrage rapide : Ajouter un nom de domaine personnalisé dans Azure Active Directory

Chaque annuaire Azure AD est fourni avec un nom de domaine initial au format suivant : *nom_de_domaine*.onmicrosoft.com. Le nom de domaine initial ne peut pas être modifié ni supprimé. Toutefois, vous pouvez ajouter votre nom de domaine d’entreprise à Azure AD. Par exemple, il se peut que votre organisation dispose d’autres noms de domaine dans le cadre de ses activités et que les utilisateurs se connectent à l’aide de votre nom de domaine d’entreprise. L’ajout de noms de domaine personnalisés à Azure AD vous permet d’attribuer des noms d’utilisateurs avec lesquels vos utilisateurs sont familiarisés, comme « alice@contoso.com ». au lieu d’« alice@*nom de domaine*.onmicrosoft.com ». Le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre annuaire.
2. Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
3. Vérifiez le nom de domaine personnalisé dans Azure AD.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Ajoutez le nom de domaine personnalisé à votre annuaire.
1. Connectez-vous au [portail Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) en utilisant un compte d’administrateur général pour le répertoire.
2. Sur la gauche, sélectionnez **Noms de domaine personnalisé**.
3. Sélectionnez **Ajouter un domaine personnalisé**.
   
   ![Sélectionnez la commande Ajouter](./media/add-custom-domain/add-custom-domain.png)
5. Sous **Noms de domaine personnalisé**, saisissez le nom de votre domaine personnalisé dans la zone, par exemple « contoso.com », puis sélectionnez **Ajouter un domaine**. Veillez à inclure .com, .net ou une autre extension de niveau supérieur.
6. Dans ***nom_domaine*** (autrement dit, le titre indique votre nouveau nom de domaine), rassemblez les informations d’entrée DNS qui vous permettront de vérifier le nom de domaine personnalisé dans Azure AD.
   
   ![obtenir les informations d’entrée DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Si vous envisagez de fédérer votre instance locale de Windows Server Active Directory avec Azure AD, vous devez cocher la case **Je prévois de configurer ce domaine pour l’authentification unique avec mon annuaire Active Directory local** lorsque vous exécutez l’outil Azure AD Connect pour synchroniser vos annuaires. Vous devez également inscrire le même nom de domaine que celui que vous comptez fédérer avec votre annuaire local lors de l’étape **Domaine Azure AD** de l’Assistant. Vous pouvez voir à quoi cette étape de l’assistant ressemble [dans ces instructions](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si vous ne disposez pas de l’outil Azure AD Connect, vous pouvez [le télécharger ici](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Azure AD peut ainsi vérifier que votre organisation est propriétaire du nom de domaine personnalisé. Vous pouvez utiliser [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour vos enregistrements DNS Azure/Office 365/externes dans Azure, ou ajouter l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas accès à ce bureau pour mettre à jour l’entrée DNS, demandez à la personne ou à l’équipe disposant de cet accès d’exécuter l’étape 2 et de vous avertir une fois l’opération effectuée.
2. Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Vérifiez le nom de domaine personnalisé dans Azure AD.
Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD. Le nom de domaine peut être vérifié uniquement une fois les enregistrements DNS propagés. Cette propagation ne prend généralement que quelques secondes, mais peut parfois nécessiter une heure ou davantage. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

1. Connectez-vous à [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) en utilisant un compte d’administrateur général pour le client.
2. Sélectionnez **Noms de domaine personnalisé**.
3. Sélectionnez le nom de domaine non vérifié que vous souhaitez vérifier.
4. Vérifiez ce que vous avez saisi et sélectionnez **Vérifier** pour terminer la vérification.

Vous pouvez désormais [affecter des noms d’utilisateur incluant votre nom de domaine personnalisé](active-directory-users-create-azure-portal.md). Vous pouvez créer des comptes d’utilisateur basés sur le cloud et mettre à jour des informations de compte d’utilisateur local précédemment synchronisées, à l’aide de votre nom de domaine personnalisé. Vous pouvez également changer les informations de suffixe de domaine de compte d’utilisateur synchronisées à l’aide de [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou de l’[API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Vous pouvez ajouter jusqu’à 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération localement avec Active Directory, vous pouvez ajouter jusqu’à 450 noms de domaine dans chaque répertoire. Pour plus d’informations, consultez la section [Noms de domaines fédérés et gérés](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Résolution des problèmes
Si vous ne parvenez pas à vérifier un nom de domaine personnalisé, essayez les étapes de dépannage suivantes :

1. **Attendez une heure**. Les enregistrements DNS doivent être propagés avant qu’Azure AD puisse vérifier le domaine. Cette opération peut prendre une heure ou davantage.
2. **Vérifiez que l’enregistrement DNS a été entré et qu’il est correct**. Exécutez cette tâche sur le site web du bureau d’enregistrement de noms de domaine correspondant au domaine. Azure AD ne peut pas vérifier le nom de domaine si 
  * L’entrée DNS n’est pas présente dans le fichier de zone DNS
  * Il ne s’agit pas d’une correspondance exacte avec l’entrée DNS qu’Azure AD vous a fournie. 
  
  Si vous ne disposez pas d’un accès pour mettre à jour les enregistrements DNS du domaine au niveau du bureau d’enregistrement de noms de domaine, partagez l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter cette entrée.
3. **Supprimez le nom de domaine d’un autre annuaire dans Azure AD**. Un nom de domaine ne peut être vérifié que dans un seul annuaire. Si un nom de domaine est vérifié actuellement dans un annuaire différent, il ne peut pas être vérifié dans votre nouvel annuaire tant qu’il n’a pas été supprimé dans l’autre. Pour plus d’informations sur la suppression des noms de domaine, voir [Gérer les noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md).    

Suivez la procédure décrite dans cet article pour ajouter chacun de vos noms de domaine.

## <a name="learn-more"></a>En savoir plus
[Présentation conceptuelle des noms de domaine personnalisés dans Azure AD](active-directory-domains-manage-azure-portal.md)

[Gérer les noms de domaine personnalisés](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez appris comment ajouter un domaine personnalisé à Azure AD. 

Vous pouvez utiliser le lien suivant pour ajouter un nouveau domaine personnalisé dans Azure AD à partir du portail Azure.

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 