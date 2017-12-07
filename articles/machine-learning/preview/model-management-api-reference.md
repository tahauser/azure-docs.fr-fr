---
title: "Créer une image Docker pour la gestion des modèles dans Azure Machine Learning | Microsoft Docs"
description: "Cet article décrit les étapes à suivre pour créer une image Docker pour votre service web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 03e51ab298a08386f0094d6d0290aa1ec85d337f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Informations de référence sur l’API de compte de gestion des services Azure Machine Learning

Pour plus d’informations sur la configuration de l’environnement de déploiement, consultez [Configuration d’un compte de gestion des modèles](deployment-setup-configuration.md).

L’API de compte Gestion des modèles Azure Machine Learning implémente les opérations suivantes :

- Inscription de modèles
- Création de manifestes
- Création d’images Docker
- Création de services web

Vous pouvez utiliser cette image pour créer un service web localement, sur un cluster Azure Container Service distant ou dans un autre environnement compatible avec Docker.

## <a name="prerequisites"></a>Composants requis
Veillez à parcourir les étapes d’installation dans le document [Guide de démarrage rapide d’installation et de création](quickstart-installation.md).

Avant de poursuivre, vous devez disposer des éléments suivants :
1. Compte de gestion des modèles provisionné
2. Environnement créé pour le déploiement et la gestion des modèles
3. Modèle Machine Learning

### <a name="azure-ad-token"></a>Jeton Azure AD
Si vous utilisez Azure CLI, connectez-vous à l’aide de `az login`. L’interface CLI utilise votre jeton Azure Active Directory (Azure AD) à partir du fichier .azure. Si vous voulez utiliser les API, voici les possibilités qui s’offrent à vous :

##### <a name="acquire-the-azure-ad-token-manually"></a>Acquisition du jeton Azure AD manuellement
Vous pouvez utiliser `az login` et obtenir le dernier jeton à partir du fichier .azure dans votre répertoire de base.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Acquisition du jeton Azure AD par programmation
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Une fois que vous avez créé le principal du service, enregistrez la sortie. Maintenant, vous pouvez vous en servir pour obtenir un jeton auprès d’Azure AD :

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Le jeton est placé dans un en-tête d’autorisation pour les appels d’API.


## <a name="register-a-model"></a>Inscrivez un modèle

À l’étape d’inscription de modèle, votre modèle Machine Learning est inscrit auprès du compte Gestion des modèles Azure que vous avez créé. Cette inscription permet le suivi des modèles et de leurs versions qui sont assignées au moment de l’inscription. L’utilisateur indique le nom du modèle. L’inscription ultérieure de modèles sous le même nom génère une nouvelle version et un nouvel ID.

### <a name="request"></a>Demande

| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Description
Inscrit un modèle.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| model | body | Charge utile utilisée pour inscrire un modèle | Oui | [Modèle](#model) |


### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | OK. L’inscription du modèle a abouti. | [Modèle](#model) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Interroger la liste des modèles dans un compte
### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Description
Interroge la liste des modèles dans un compte. Vous pouvez filtrer la liste des résultats par balise et par nom. Si aucun filtre n’est passé, la requête répertorie tous les modèles dans le compte. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| name | query | Nom d’objet | Non | string |
| tag | query | Balise de modèle | Non | string |
| count | query | Nombre d’éléments à récupérer dans une page | Non | string |
| $skipToken | query | Jeton de continuation pour récupérer la page suivante | Non | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [PaginatedModelList](#paginatedmodellist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obtenir les détails du modèle
### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Description
Obtient un modèle par ID.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’objet | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [Modèle](#model) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Inscrire un manifeste auprès du modèle inscrit et de toutes les dépendances

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Description
Inscrit un manifeste auprès du modèle inscrit et de toutes ses dépendances.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| manifestRequest | body | Charge utile utilisée pour inscrire un manifeste | Oui | [Manifest](#manifest) |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | L’inscription du manifeste a abouti. | [Manifest](#manifest) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Interroger la liste des manifestes dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Description
Interroge la liste des manifestes dans un compte. Vous pouvez filtrer la liste de résultats par ID de modèle et par nom de manifeste. Si aucun filtre n’est passé, la requête répertorie tous les manifestes dans le compte. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| modelId | query | ID du modèle | Non | string |
| manifestName | query | Nom du manifeste | Non | string |
| count | query | Nombre d’éléments à récupérer dans une page | Non | string |
| $skipToken | query | Jeton de continuation pour récupérer la page suivante | Non | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obtenir les détails du manifeste

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Description
Obtient le manifeste par ID.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’objet | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [Manifest](#manifest) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Créer une image

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Description
Crée une image en tant qu’une image Docker dans Azure Container Registry.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| imageRequest | body | Charge utile utilisée pour créer une image | Oui | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Réponses
| Code | Description | headers | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET vous indiquera l’état de la tâche de création d’image. | Operation-Location |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Interroger la liste d’images dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Description
Interroge la liste d’images dans un compte. Vous pouvez filtrer la liste de résultats par nom et ID de manifeste. Si aucun filtre n’est passé, la requête répertorie toutes les images dans le compte. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| manifestId | query | ID du manifeste | Non | string |
| manifestName | query | Nom du manifeste | Non | string |
| count | query | Nombre d’éléments à récupérer dans une page | Non | string |
| $skipToken | query | Jeton de continuation pour récupérer la page suivante | Non | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [PaginatedImageList](#paginatedimagelist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obtenir les détails de l’image

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Description
Obtient une image par ID.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID de l’image | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [Image](#image) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Créer un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Description
Crée un service à partir d’une image.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| serviceRequest | body | Charge utile utilisée pour créer un service | Oui | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Réponses
| Code | Description | headers | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET indiquera l’état de la tâche de création de service. | Operation-Location |
| 409 | Un service existe déjà sous le nom spécifié. |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Interroger la liste de services dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Description
Interroge la liste de services dans un compte. Vous pouvez filtrer la liste de résultats par nom/ID du modèle, nom/ID du manifeste, ID de l’image, nom du service ou ID de ressource Compute Machine Learning. Si aucun filtre n’est passé, la requête répertorie tous les services dans le compte. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| serviceName | query | Nom du service | Non | string |
| modelId | query | Nom du modèle | Non | string |
| modelName | query | ID du modèle | Non | string |
| manifestId | query | ID du manifeste | Non | string |
| manifestName | query | Nom du manifeste | Non | string |
| imageId | query | ID de l’image | Non | string |
| computeResourceId | query | ID de ressource Compute Machine Learning | Non | string |
| count | query | Nombre d’éléments à récupérer dans une page | Non | string |
| $skipToken | query | Jeton de continuation pour récupérer la page suivante | Non | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [PaginatedServiceList](#paginatedservicelist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obtenir les détails du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Obtient un service par ID.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’objet | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [ServiceResponse](#serviceresponse) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Mettre à jour un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Met à jour un service existant.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’objet | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| serviceUpdateRequest | body | Charge utile utilisée pour mettre à jour un service existant | Oui |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Réponses
| Code | Description | headers | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET indiquera l’état de la tâche de mise à jour de service. | Operation-Location |
| 404 | Le service associé à l’ID spécifié n’existe pas. |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Supprimer un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| SUPPRIMER |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Supprime un service.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’objet | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! |  |
| 204 | Le service associé à l’ID spécifié n’existe pas. |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obtenir les clés du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Description
Obtient les clés du service.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID du service | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [AuthKeys](#authkeys)
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Régénérer les clés du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Description
Régénère les clés du service, puis les retourne.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID du service | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| regenerateKeyRequest | body | Charge utile utilisée pour mettre à jour un service existant | Oui | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [AuthKeys](#authkeys)
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Interroger la liste des déploiements dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Description
Interroge la liste des déploiements dans un compte. Vous pouvez filtrer la liste de résultats par ID du service, ce qui retourne uniquement les déploiements créés pour le service en question. Si aucun filtre n’est passé, la requête répertorie tous les déploiements dans le compte.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |
| serviceId | query | ID du service | Non | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [DeploymentList](#deploymentlist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obtenir les détails relatifs au déploiement

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Description
Obtient le déploiement par ID.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID de déploiement | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [Déploiement](#deployment) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obtenir les détails de l’opération

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Description
Obtient l’état de l’opération asynchrone par ID d’opération.

### <a name="parameters"></a>parameters
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID d’abonnement Azure. | Oui | string |
| resourceGroupName | path | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | string |
| accountName | path | Nom du compte Gestion des modèles | Oui | string |
| id | path | ID d’opération | Oui | string |
| api-version | query | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | string |
| Autorisation | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | string |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Vous avez réussi ! | [OperationStatus](#asyncoperationstatus) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Définitions

<a name="asset"></a>
### <a name="asset"></a>Asset
Objet de ressource qui sera nécessaire pendant la création d’images Docker


|Nom|Description|Schéma|
|---|---|---|
|**id**  <br>*facultatif*|ID de la ressource|string|
|**mimeType**  <br>*facultatif*|Type MIME du contenu du modèle. Pour plus d’informations sur le type MIME, consultez la [liste des types de média IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**unpack**  <br>*facultatif*|Indique l’emplacement où le contenu doit être décompressé pendant la création d’images Docker.|booléenne|
|**url**  <br>*facultatif*|URL de l’emplacement de la ressource|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
État de l’opération asynchrone

*Type* : enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
État de l’opération


|Nom|Description|Schéma|
|---|---|---|
|**createdTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) de l’opération asynchrone|chaîne (date-heure)|
|**endTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de fin (UTC) de l’opération asynchrone|chaîne (date-heure)|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facultatif*|ID de l’opération asynchrone|string|
|**operationType**  <br>*facultatif*|Type de l’opération asynchrone|enum (Image, Service)|
|**resourceLocation**  <br>*facultatif*|Ressource créée ou mise à jour par l’opération asynchrone|string|
|**state**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Clés d’authentification d’un service


|Nom|Description|Schéma|
|---|---|---|
|**primaryKey**  <br>*facultatif*|Clé primaire|string|
|**secondaryKey**  <br>*facultatif*|Clé secondaire|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Paramètres de l’outil de mise à l’échelle automatique


|Nom|Description|Schéma|
|---|---|---|
|**autoscaleEnabled**  <br>*facultatif*|Active ou désactive l’outil de mise à l’échelle automatique.|booléenne|
|**maxReplicas**  <br>*facultatif*|Nombre maximal de réplicas pod prévus pour la montée en puissance  <br>**Valeur minimale** : `1`|integer|
|**minReplicas**  <br>*facultatif*|Nombre minimal de réplicas pod prévus pour la descente en puissance  <br>**Valeur minimale** : `0`|integer|
|**refreshPeriodInSeconds**  <br>*facultatif*|Durée d’actualisation pour le déclencheur de la mise à l’échelle automatique  <br>**Valeur minimale** : `1`|integer|
|**targetUtilization**  <br>*facultatif*|Pourcentage d’utilisation déclenchant la mise à l’échelle automatique  <br>**Valeur minimale** : `0`  <br>**Valeur maximale** : `100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Ressource Compute Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**id**  <br>*facultatif*|ID de la ressource|string|
|**type**  <br>*facultatif*|Type de ressource.|enum (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuration de la réservation de ressources pour un conteneur du cluster


|Nom|Description|Schéma|
|---|---|---|
|**cpu**  <br>*facultatif*|Spécifie la réservation du processeur. Format pour Kubernetes : voir [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) (Principe de l’UC).|string|
|**memory**  <br>*facultatif*|Spécifie la réservation de mémoire. Format pour Kubernetes : voir [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory) (Principe de la mémoire).|string|


<a name="deployment"></a>
### <a name="deployment"></a>Déploiement
Instance d’un déploiement Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du déploiement|chaîne (date-heure)|
|**expiredAt**  <br>*facultatif*  <br>*en lecture seule*|Heure d’expiration (UTC) du déploiement|chaîne (date-heure)|
|**id**  <br>*facultatif*|ID de déploiement|string|
|**imageId**  <br>*facultatif*|ID d’image associé à ce déploiement|string|
|**serviceName**  <br>*facultatif*|Nom du service|string|
|**statut**  <br>*facultatif*|État du déploiement actuel|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Tableau d’objets de déploiement

*Type* : <[déploiement](#deployment)> tableau


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Détail de l’erreur du service Gestion des modèles


|Nom|Description|Schéma|
|---|---|---|
|**code**  <br>*obligatoire*|Code d’erreur|string|
|**message**  <br>*obligatoire*|Message d’erreur.|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Objet d’erreur du service Gestion des modèles


|Nom|Description|Schéma|
|---|---|---|
|**code**  <br>*obligatoire*|Code d’erreur|string|
|**details**  <br>*facultatif*|Tableau d’objets de détail d’erreur|<[ErrorDetail](#errordetail)> tableau|
|**message**  <br>*obligatoire*|Message d’erreur.|string|
|**statusCode**  <br>*facultatif*|Code d'état HTTP.|integer|


<a name="image"></a>
### <a name="image"></a>Image
Image Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**computeResourceId**  <br>*facultatif*|ID de l’environnement créé dans la ressource Compute Machine Learning|string|
|**createdTime**  <br>*facultatif*|Heure de création (UTC) de l’image|chaîne (date-heure)|
|**creationState**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*facultatif*|Texte de description de l’image|string|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facultatif*|ID de l’image|string|
|**imageBuildLogUri**  <br>*facultatif*|URI des journaux chargés à partir de la génération de l’image|string|
|**imageLocation**  <br>*facultatif*|Chaîne d’emplacement Azure Container Registry pour l’image créée|string|
|**imageType**  <br>*facultatif*||[ImageType](#imagetype)|
|**manifest**  <br>*facultatif*||[Manifest](#manifest)|
|**name**  <br>*facultatif*|Nom de l’image|string|
|**version**  <br>*facultatif*|Version de l’image définie par le service Gestion des modèles|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Demande de création d’une image Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**computeResourceId**  <br>*obligatoire*|ID de l’environnement créé dans la ressource Compute Machine Learning|string|
|**description**  <br>*facultatif*|Texte de description de l’image|string|
|**imageType**  <br>*obligatoire*||[ImageType](#imagetype)|
|**manifestId**  <br>*obligatoire*|ID du manifeste à partir duquel l’image sera créée|string|
|**name**  <br>*obligatoire*|Nom de l’image|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Spécifie le type de l’image

*Type* : enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifeste
Manifeste Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**assets**  <br>*obligatoire*|Liste de ressources|<[Ressource](#asset)> tableau|
|**createdTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du manifeste|chaîne (date-heure)|
|**description**  <br>*facultatif*|Texte de description du manifeste|string|
|**driverProgram**  <br>*obligatoire*|Programme pilote du manifeste|string|
|**id**  <br>*facultatif*|ID du manifeste|string|
|**modelIds**  <br>*facultatif*|Liste d’ID des modèles inscrits La demande échoue si l’un des modèles inclus n’est pas inscrit.|<string> tableau|
|**modelType**  <br>*facultatif*|Indique que les modèles sont déjà inscrits auprès du service Gestion des modèles.|enum (Registered)|
|**name**  <br>*obligatoire*|Nom du manifeste|string|
|**targetRuntime**  <br>*obligatoire*||[TargetRuntime](#targetruntime)|
|**version**  <br>*facultatif*  <br>*en lecture seule*|Version du manifeste affectée par le service Gestion des modèles|integer|
|**webserviceType**  <br>*facultatif*|Spécifie le type souhaité du service web qui sera créé à partir du manifeste.|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Modèle
Instance d’un modèle Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du modèle|chaîne (date-heure)|
|**description**  <br>*facultatif*|Texte de description du modèle|string|
|**id**  <br>*facultatif*  <br>*en lecture seule*|ID du modèle|string|
|**mimeType**  <br>*obligatoire*|Type MIME du contenu du modèle. Pour plus d’informations sur le type MIME, consultez la [liste des types de média IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**name**  <br>*obligatoire*|Nom du modèle|string|
|**balises**  <br>*facultatif*|Liste de balises du modèle|<string> tableau|
|**unpack**  <br>*facultatif*|Indique si le modèle doit être décompressé pendant la création d’images Docker.|booléenne|
|**url**  <br>*obligatoire*|URL du modèle. Nous insérons généralement ici une URL de signature d’accès partagé.|string|
|**version**  <br>*facultatif*  <br>*en lecture seule*|Version du modèle affectée par le service Gestion des modèles|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informations sur la collecte de données du modèle


|Nom|Description|Schéma|
|---|---|---|
|**eventHubEnabled**  <br>*facultatif*|Active le concentrateur d’événements pour un service.|booléenne|
|**storageEnabled**  <br>*facultatif*|Active le stockage pour un service.|booléenne|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Liste paginée d’images


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|string|
|**value**  <br>*facultatif*|Tableau d’objets de modèle|<[Image](#image)> tableau|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Liste paginée de manifestes


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|string|
|**value**  <br>*facultatif*|Tableau d’objets de manifeste|<[Manifeste](#manifest)> tableau|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Liste paginée de modèles


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|string|
|**value**  <br>*facultatif*|Tableau d’objets de modèle|<[Modèle](#model)> tableau|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Liste paginée de services.


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|string|
|**value**  <br>*facultatif*|Tableau d’objets de service|<[ServiceResponse](#serviceresponse)> tableau|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Demande de création d’un service


|Nom|Description|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service.|booléenne|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatoire*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obligatoire*|Image pour créer le service|string|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|integer|
|**name**  <br>*obligatoire*|Nom du service|string|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Demande de régénération de clé pour un service


|Nom|Description|Schéma|
|---|---|---|
|**keyType**  <br>*facultatif*|Spécifie la clé à régénérer.|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
État détaillé du service


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*|Heure de création (UTC) du service|chaîne (date-heure)|
|**Identifiant**  <br>*facultatif*|ID du service|string|
|**image**  <br>*facultatif*||[Image](#image)|
|**manifest**  <br>*facultatif*||[Manifest](#manifest)|
|**models**  <br>*facultatif*|Liste de modèles|<[Modèle](#model)> tableau|
|**name**  <br>*facultatif*|Nom du service|string|
|**scoringUri**  <br>*facultatif*|URI pour la notation du service|string|
|**state**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*facultatif*|Heure (UTC) de la dernière mise à jour|chaîne (date-heure)|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service.|booléenne|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatoire*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|integer|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|integer|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Demande de mise à jour d’un service


|Nom|Description|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service.|booléenne|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*facultatif*|Image pour créer le service|string|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|integer|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Type du runtime cible.


|Nom|Description|Schéma|
|---|---|---|
|**properties**  <br>*obligatoire*||<chaîne, chaîne> mappage|
|**runtimeType**  <br>*obligatoire*|Spécifie le runtime.|enum (SparkPython, Python)|

