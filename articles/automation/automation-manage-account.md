---
title: Gestion d’un compte Azure Automation
description: Cet article décrit comment gérer la configuration de votre compte Automation, telle que le renouvellement du certificat, la suppression et une configuration incorrecte.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b9ccc46192950af212975304329497785b0babbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-azure-automation-account"></a>Gestion d’un compte Azure Automation
Avant que votre compte Azure Automation n’expire, vous devez renouveler le certificat. Si vous pensez que le compte d’identification a été compromis, vous pouvez le supprimer et le recréer. Cette section décrit comment effectuer ces opérations.

## <a name="self-signed-certificate-renewal"></a>Renouvellement de certificat auto-signé
Le certificat auto-signé que vous avez créé pour le compte d’identification expire 1 an après la date de création. Vous pouvez le renouveler à tout moment avant qu’il n’expire. Lorsque vous le renouvelez, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

> [!NOTE]
> Si vous avez configuré votre compte d’identification Automation pour utiliser un certificat émis par votre autorité de certification d’entreprise et que vous utilisez cette option, ce certificat est remplacé par un certificat auto-signé.

Pour renouveler le certificat, procédez comme suit :

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans la zone **Compte Automation** 
3. , dans le volet **Propriétés du compte**, sous **Paramètres du compte**, sélectionnez **Comptes d’identification**.

    ![Panneau des propriétés du compte Automation](media/automation-manage-account/automation-account-properties-pane.png)
3. Dans la page des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic pour lequel vous souhaitez renouveler le certificat.

4. Sur le volet **Propriétés** du compte sélectionné, cliquez sur **Renouveler le certificat**.

    ![Renouveler le certificat pour le compte d’identification](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Supprimer un compte d’identification standard ou Classic
Cette section décrit comment supprimer et recréer votre compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte d’identification standard ou le compte d’identification Classic, vous pouvez le recréer dans le portail Azure.

1. Dans le portail Azure, ouvrez le compte Automation.

2. Sur la page **Compte Automation**, sélectionnez **Comptes d’identification**.

3. Sur la page de propriétés **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer. Ensuite, dans le volet **Propriétés** du compte sélectionné, cliquez sur **Supprimer**.

 ![Supprimer un compte d’identification](media/automation-manage-account/automation-account-delete-runas.png)

4. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

5. Une fois le compte supprimé, vous pouvez le recréer sur la page de propriétés **Comptes d’identification** en sélectionnant l’option de création **Compte d’identification Azure**.

 ![Recréer le compte d’identification Automation](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Configuration incorrecte
Certains éléments de configuration nécessaires pour que le compte d’identification standard ou le compte d’identification Classic fonctionne correctement ont peut-être été supprimés ou n’ont pas été créés correctement lors de l’installation initiale. Par exemple :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Dans les cas précédents et dans d’autres instances de configuration incorrecte, le compte Automation détecte les modifications et affiche l’état *Incomplet* dans le volet des propriétés **Comptes d’identification** du compte.

![État Incomplet pour la configuration du compte d’identification](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Lorsque vous sélectionnez le compte d’identification, le volet **Propriétés** du compte affiche le message d’erreur suivant :

![Message d’avertissement Incomplet pour la configuration du compte d’identification](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Vous pouvez rapidement résoudre ces problèmes liés au compte d’identification en supprimant et en recréant le compte.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les principaux de service, consultez l’article [Objets Principal du service et Application](../active-directory/active-directory-application-objects.md).
* Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, reportez-vous à l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Pour plus d’informations sur les certificats et les services Azure, consultez la page [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).