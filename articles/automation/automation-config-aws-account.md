---
title: Configurer l’authentification avec Amazon Web Services
description: Cet article décrit comment créer et valider des informations d’identification AWS pour les runbooks dans Azure Automation gérant des ressources AWS.
keywords: authentification AWS, configurer aws
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 93e2a95e8b1ed2cf39b6e839908be54815f8fa63
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Authentification des Runbooks avec Amazon Web Services
Il est possible d’automatiser les tâches courantes avec les ressources Amazon Web Services (AWS) à l’aide des runbooks Automation dans Azure.  Vous pouvez automatiser de nombreuses tâches dans AWS à l’aide des runbooks Automation, tout comme avec les ressources dans Azure.  Pour ce faire, deux choses sont nécessaires :

* Un abonnement AWS et un ensemble d’informations d’identification.  Plus précisément, votre clé d’accès AWS et votre clé secrète.  Pour plus d’informations, consultez l’article [Utilisation des informations d’identification AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Un abonnement Azure et un compte Automation.  Pour plus d’informations sur la configuration d’un compte Azure Automation, consultez l’article [Planification d’authentification](automation-offering-get-started.md#authentication-planning).  

Pour vous authentifier avec AWS, vous devez spécifier un ensemble d’informations d’identification AWS pour authentifier vos runbooks en cours d’exécution à partir d’Azure Automation. Si vous avez déjà un compte Automation et que vous souhaitez l’utiliser pour vous authentifier avec AWS, vous pouvez suivre les étapes décrites dans la section suivante.  Pour dédier un compte aux Runbooks ciblant les ressources AWS, vous devez au préalable créer un nouveau [compte Automation](automation-offering-get-started.md) (ignorez l’option de création d’un principal du service). Suivez ensuite les étapes ci-dessous.

## <a name="configure-automation-account"></a>Configuration d’un compte Automation
Pour qu’Azure Automation communique avec AWS, vous devez d’abord récupérer vos informations d’identification AWS et les stocker en tant que ressources dans Azure Automation.  Exécutez les opérations décrites dans le document AWS [Managing Access Keys for your AWS Account](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) (Gestion des clés d’accès de votre compte AWS) pour créer une clé d’accès et copiez **l’ID de clé d’accès** et la **clé d’accès secrète** (vous pouvez également télécharger votre fichier de clés pour le stocker dans un endroit sûr).

Une fois que vous avez créé et copié vos clés de sécurité AWS, vous devez créer une ressource Informations d’identification avec un compte Azure Automation pour les stocker en toute sécurité et les référencer avec vos Runbooks.  Suivez les étapes décrites dans la section **To create a new credential (Pour créer de nouvelles informations d’identification)** de l’article [Ressources d’informations d’identification d’Azure Automation](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal), et entrez les informations suivantes :

1. Dans la zone **Nom**, entrez **AWScred** ou une valeur appropriée, suivant vos normes d’affectation de noms.  
2. Dans la zone **Nom d’utilisateur**, entrez votre **ID de clé d’accès** et votre **clé d’accès secrète** dans les zones **Mot de passe** et **Confirmer le mot de passe**.   

## <a name="next-steps"></a>Étapes suivantes
* Consultez l’article de la solution [Automatisation du déploiement d’une machine virtuelle dans Amazon Web Services](automation-scenario-aws-deployment.md) pour apprendre à créer des Runbooks afin d’automatiser les tâches dans AWS.

