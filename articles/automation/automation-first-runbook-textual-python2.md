---
title: "Mon premier Runbook Python dans Azure Automation | Microsoft Docs"
description: "Ce didacticiel vous familiarise avec la création, le test et la publication d’un Runbook Python simple."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 1f005ae28309879f36a28df499685937c37a3be0
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-python-runbook"></a>Mon premier runbook Python

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ce didacticiel vous guide dans la création d’un [Runbook Python](automation-runbook-types.md#python-runbooks) dans Azure Automation. Vous partez d’un runbook simple, que vous testez et publiez. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous le rendrez plus robuste en lui ajoutant des paramètres.

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Vous arrêtez et démarrez cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="create-a-new-runbook"></a>Créer un Runbook

Commençons par créer un runbook simple qui retourne le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.

    La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).<br>

1. Sélectionnez **Runbooks** sous **GESTION DE PROCESSUS** pour ouvrir la liste des runbooks.
2. Sélectionnez **+ Ajouter un runbook** pour créer un runbook.
3. Nommez le runbook *MyFirstRunbook-Python*.
4. Dans ce cas précis, nous allons créer un [Runbook Python](automation-runbook-types.md#python-runbooks). Par conséquent, sélectionnez **Python 2** comme **Type de runbook**.
5. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="add-code-to-the-runbook"></a>Ajouter du code au Runbook

Ajoutons maintenant une commande simple pour imprimer le texte « Hello World » :

```python
print("Hello World!")
```

Cliquez sur **Enregistrer** pour enregistrer le runbook.

## <a name="test-the-runbook"></a>Tester le Runbook

Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.
   L’état initial du travail est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. Il passe à *En cours de démarrage* lorsqu’un Worker sélectionne la tâche, puis à *En cours d’exécution* lorsque le runbook se lance.
4. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans ce cas, *Hello World* devrait apparaître.
5. Fermez le volet de test pour revenir au canevas.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que vous avez créé est toujours en mode brouillon. Il faut le publier pour pouvoir l’exécuter en production.
Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon.
Dans ce cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
2. Si vous faites maintenant défiler la page vers la gauche pour visualiser le runbook sur la page **Runbooks**, celle-ci affiche **l’État de création** **Publié**.
1. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Python**.
   Les options de la partie supérieure nous permettent de démarrer le runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
2. L’objectif étant de démarrer le runbook, cliquez sur **Démarrer**, puis sur **Ok** à l’ouverture du panneau Démarrer le runbook.
3. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Vous pouvez le fermer, mais, dans ce cas précis, laissez-le ouvert pour pouvoir suivre la progression de la tâche.
4. L’état de la tâche, indiqué dans le champ **Résumé de la tâche**, correspond aux états constatés lors du test du runbook.
5. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.
6. Fermez le volet Sortie.
7. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Vous devez uniquement voir le message *Hello World* dans le flux de sortie, mais d’autres flux peuvent s’afficher pour un travail de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.
8. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook-Python.
9. Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.
10. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que vous avez consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="add-authentication-to-manage-azure-resources"></a>Ajouter une authentification pour gérer les ressources Azure

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure.
Pour gérer les ressources Azure, le script doit s’authentifier à l’aide des informations d’identification de votre [compte Automation](automation-offering-get-started.md).

> [!NOTE]
> Le compte Automation doit avoir été créé avec la fonctionnalité de principal de service pour qu’un certificat puisse être exécuté.
> Si vous n’avez pas créé votre compte Automation avec cette fonctionnalité, vous pouvez vous authentifier à l’aide de la méthode décrite dans [S’authentifier avec les bibliothèques de gestion Azure pour Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Python.
1. Ajoutez le code suivant pour vous authentifier dans Azure :
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Ajouter du code pour créer le client Compute Python et démarrer la machine virtuelle

Pour utiliser des machines virtuelles Azure, créez une instance du [client Compute Azure pour Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Utilisez le client Compute pour démarrer la machine virtuelle. Ajoutez le code suivant au Runbook :

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

_MyResourceGroup_ étant le nom du groupe de ressource qui contient la machine virtuelle, et _TestVM_ le nom de la machine virtuelle que vous souhaitez démarrer. 

Testez et exécutez une nouvelle fois le Runbook pour vérifier qu’il démarre la machine virtuelle.

## <a name="use-input-parameters"></a>Utilisez les paramètres d’entrée

Actuellement, le Runbook utilise des valeurs codées en dur pour le nom du groupe de ressources et celui de la machine virtuelle.
Nous ajoutons maintenant du code pour obtenir ces valeurs des paramètres d’entrée.

Utilisez la variable `sys.argv` pour obtenir les valeurs du paramètre.
Ajoutez le code suivant au Runbook à la suite des autres instructions `import` :

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Cette action importe le module `sys` et crée deux variables pour stocker le nom du groupe de ressources et celui de la machine virtuelle.
Remarque : l’élément `sys.argv[0]` dans la liste des arguments correspond au nom du script et n’est pas entré par l’utilisateur.

Vous pouvez maintenant modifier les deux dernières lignes du runbook de façon à utiliser les valeurs du paramètre d’entrée à la place des valeurs codées en dur :

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Lorsque vous démarrez un runbook Python (dans la page **Test** ou en tant que runbook publié), vous pouvez entrer les valeurs des paramètres dans la page **Démarrer le Runbook** sous **Paramètres**.

Après avoir entré une valeur dans la première boîte, une deuxième apparaît, et ainsi de suite. Vous pouvez entrer autant de valeurs de paramètres que nécessaire.

Les valeurs sont disponibles pour le script, tout comme le tableau `sys.argv`, dans le code qui vient d’être ajouté.

Entrez le nom du groupe de ressources comme valeur du premier paramètre, et le nom de la machine virtuelle à démarrer comme valeur du deuxième paramètre.

![Entrer des valeurs de paramètres](media/automation-first-runbook-textual-python/runbook-python-params.png)

Cliquez sur **OK** pour démarrer le Runbook. Le Runbook s’exécute et démarre la machine virtuelle que vous avez spécifiée.

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour en savoir plus sur le développement pour Azure avec Python, consultez [Azure pour les développeurs Python](https://docs.microsoft.com/python/azure/?view=azure-python).
* Pour afficher des exemples de runbooks Python 2, consultez [GitHub Azure Automation](https://docs.microsoft.com/python/azure/?view=azure-python).
