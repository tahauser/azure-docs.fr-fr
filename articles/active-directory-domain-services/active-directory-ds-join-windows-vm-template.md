---
title: "Joindre une machine virtuelle Windows Server à Azure Active Directory Domain Services | Microsoft Docs"
description: "Joindre une machine virtuelle Windows Server à un domaine géré à l’aide de modèles Azure Resource Manager."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: b90198696b7bdb9277fd2f2b8e8e727af42c5cfa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Joindre une machine virtuelle Windows Server à un domaine géré à l’aide d’un modèle Resource Manager
Cet article explique comment joindre une machine virtuelle Windows Server à un domaine géré Azure AD Domain Services à l’aide de modèles Resource Manager.

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :
1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Veillez à configurer les adresses IP du domaine géré en tant que serveurs DNS pour le réseau virtuel. Pour plus d’informations, consultez [Comment mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md)
5. Effectuez les étapes requises pour [synchroniser les mots de passe à votre domaine géré Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Installer et configurer les outils requis
Vous pouvez utiliser l’une des options suivantes pour effectuer les étapes décrites dans ce document :
* **Azure PowerShell** : [Installer et configurer](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interface de ligne de commande multiplateforme Azure** : [Installer et configurer](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Option 1 : Approvisionner une nouvelle machine virtuelle Windows Server et la joindre à un domaine géré
**Nom du modèle de démarrage rapide** : [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Pour déployer une machine virtuelle Windows Server et la joindre à un domaine, procédez comme suit :
1. Accédez au [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Cliquez sur **Déployer dans Azure**.
3. Dans la page **Déploiement personnalisé**, fournissez les informations requises pour approvisionner la machine virtuelle.
4. Sélectionnez le **abonnement Azure** permettant de configurer la machine virtuelle. Choisissez le même abonnement Azure que celui dans lequel vous avez activé Azure AD Domain Services.
5. Sélectionnez un **groupe de ressources** existant ou créez-en un.
6. Choisissez un **emplacement** où déployer la nouvelle machine virtuelle.
7. Dans **Nom du réseau virtuel existant**, indiquez le réseau virtuel dans lequel le domaine géré Azure AD Domain Services a été déployé.
8. Dans **Nom du sous-réseau existant**, indiquez le sous-réseau au sein du réseau virtuel où vous souhaitez déployer cette machine virtuelle. Ne sélectionnez pas le sous-réseau de passerelle dans le réseau virtuel. En outre, ne sélectionnez pas le sous-réseau dédié dans lequel votre domaine géré est déployé.
9. Dans **Préfixe d’étiquette DNS**, spécifiez le nom d’hôte de la machine virtuelle en cours d’approvisionnement. Par exemple, « contoso-win ».
10. Sélectionnez la **taille de machine virtuelle** adéquate pour la machine virtuelle.
11. Dans **Domaine à joindre**, indiquez le nom de domaine DNS de votre domaine géré.
12. Dans **Nom d’utilisateur de domaine**, spécifiez le nom du compte d’utilisateur de votre domaine géré qui doit être utilisé pour joindre la machine virtuelle au domaine géré.
13. Dans **Mot de passe du domaine**, spécifiez le mot de passe du compte d’utilisateur de domaine référencé par le paramètre « domainUsername ».
14. Facultatif : Vous pouvez spécifier le **chemin d’accès d’unité organisationnelle** à une unité organisationnelle personnalisée, où ajouter la machine virtuelle. Si vous ne spécifiez pas de valeur pour ce paramètre, la machine virtuelle est ajoutée à l’unité organisationnelle **AAD DC Computers** par défaut dans le domaine géré.
15. Dans le champ **Nom d’utilisateur administrateur de la machine virtuelle**, spécifiez un nom de compte administrateur local pour la machine virtuelle.
16. Dans le champ **Mot de passe d’administrateur de la machine virtuelle**, spécifiez un mot de passe d’administrateur local pour la machine virtuelle. Entrez un mot de passe d’administrateur local fort pour la machine virtuelle afin de la protéger contre les attaques de mot de passe par force brute.
17. Cliquez sur **J’accepte les termes et conditions mentionnés ci-dessus**.
18. Cliquez sur **Acheter** pour approvisionner la machine virtuelle.

> [!WARNING]
> **Gérez les mots de passe avec prudence.**
> Le fichier de paramètres du modèle contient les mots de passe des comptes de domaine, ainsi que les mots de passe d’administrateur local pour la machine virtuelle. Veillez à ne pas conserver ce fichier sur des partages de fichiers ou d’autres emplacements partagés. Nous vous recommandons de supprimer ce fichier une fois le déploiement de vos machines virtuelles terminé.
>

Une fois le déploiement terminé, votre machine virtuelle Windows nouvellement approvisionnée est jointe au domaine géré.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Option 2 : Joindre une machine virtuelle Windows Server existante à un domaine géré
**Modèle de démarrage rapide** : [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Pour joindre une machine virtuelle Windows Server existante à un domaine géré, procédez comme suit :
1. Accédez au [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Cliquez sur **Déployer dans Azure**.
3. Dans la page **Déploiement personnalisé**, fournissez les informations requises pour approvisionner la machine virtuelle.
4. Sélectionnez le **abonnement Azure** permettant de configurer la machine virtuelle. Choisissez le même abonnement Azure que celui dans lequel vous avez activé Azure AD Domain Services.
5. Sélectionnez un **groupe de ressources** existant ou créez-en un.
6. Choisissez un **emplacement** où déployer la nouvelle machine virtuelle.
7. Dans le champ **Liste de machines virtuelles**, spécifiez les noms des machines virtuelles existantes à joindre au domaine géré. Utilisez une virgule pour séparer les noms des machines virtuelles individuelles. Par exemple, **contoso-web, contoso-api**.
8. Dans **Nom d’utilisateur de jonction de domaine**, spécifiez le nom du compte d’utilisateur de votre domaine géré qui doit être utilisé pour joindre la machine virtuelle au domaine géré.
9. Dans **Mot de passe utilisateur de jonction de domaine**, spécifiez le mot de passe du compte d’utilisateur de domaine référencé par le paramètre « domainUsername ».
10. Dans **Nom complet du domaine**, indiquez le nom de domaine DNS de votre domaine géré.
11. Facultatif : Vous pouvez spécifier le **chemin d’accès d’unité organisationnelle** à une unité organisationnelle personnalisée, où ajouter la machine virtuelle. Si vous ne spécifiez pas de valeur pour ce paramètre, la machine virtuelle est ajoutée à l’unité organisationnelle **AAD DC Computers** par défaut dans le domaine géré.
12. Cliquez sur **J’accepte les termes et conditions mentionnés ci-dessus**.
13. Cliquez sur **Acheter** pour approvisionner la machine virtuelle.

> [!WARNING]
> **Gérez les mots de passe avec prudence.**
> Le fichier de paramètres du modèle contient les mots de passe des comptes de domaine, ainsi que les mots de passe d’administrateur local pour la machine virtuelle. Veillez à ne pas conserver ce fichier sur des partages de fichiers ou d’autres emplacements partagés. Nous vous recommandons de supprimer ce fichier une fois le déploiement de vos machines virtuelles terminé.
>

Une fois le déploiement terminé, les machines virtuelles Windows spécifiées sont jointes au domaine géré.


## <a name="related-content"></a>Contenu connexe
* [Vue d’ensemble d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Modèle de démarrage rapide Azure - Jonction de domaine d’une nouvelle machine virtuelle](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modèle de démarrage rapide Azure - Jonction de domaine de machines virtuelles existantes](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
