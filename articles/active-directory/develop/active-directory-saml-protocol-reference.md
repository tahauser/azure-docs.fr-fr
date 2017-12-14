---
title: "Informations de référence sur le protocole SAML d’Azure AD | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des profils SAML d’authentification unique et de déconnexion unique dans Azure Active Directory."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: priyamo
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 84bd6ae5e1624ade18dc7ee2b73fe1c94914978e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Prise en charge du protocole SAML dans Azure Active Directory
Azure Active Directory (Azure AD) utilise le protocole SAML 2.0 pour permettre aux applications de fournir une expérience d’authentification unique à leurs utilisateurs. Les profils SAML [Authentification unique](active-directory-single-sign-on-protocol-reference.md) et [Déconnexion unique](active-directory-single-sign-out-protocol-reference.md) d’Azure AD expliquent comment les assertions, les protocoles et les liaisons SAML sont utilisées dans le service de fournisseur d’identité.

Le protocole SAML a besoin du fournisseur d’identité (Azure AD) et du fournisseur de services (l’application) pour échanger des informations à leur sujet.

Lorsqu’une application est enregistrée auprès d’Azure AD, le développeur d’applications enregistre les informations liées à la fédération auprès d’Azure AD. Ces informations englobent notamment **l’URI de redirection** et **l’URI des métadonnées** de l’application.

Azure AD utilise **l’URI des métadonnées** du service cloud pour récupérer la clé de signature et l’URI de déconnexion du service cloud. Si l’application ne prend pas en charge l’URI des métadonnées, le développeur doit contacter le support Microsoft pour obtenir l’URI de déconnexion et la clé de signature.

Azure Active Directory expose les points de terminaison d’authentification unique et de déconnexion unique communs (indépendants du client) et spécifiques au client. Ces URL représentent les emplacements adressables et ne jouent pas simplement le rôle d’identificateurs. Vous pouvez donc accéder au point de terminaison pour lire les métadonnées.

* Le point de terminaison propre au client se trouve à l’adresse `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  L’espace réservé <TenantDomainName> représente un nom de domaine inscrit ou le GUID TenantID d’un locataire Azure AD. Par exemple, les métadonnées de fédération du locataire contoso.com se trouvent à l’adresse : https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Le point de terminaison indépendant du client se trouve à l’adresse `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Dans cette adresse de point de terminaison, l’élément **common** remplace le nom de domaine ou l’ID du locataire.

Pour plus d’informations sur les documents de métadonnées de fédération publiés par Azure AD, consultez la page [Métadonnées de fédération](active-directory-federation-metadata.md).
