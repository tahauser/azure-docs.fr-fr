---
title: 'Azure Active Directory Connect : Forum Aux Questions | Microsoft Docs'
description: Cette page comporte le Forum Aux Questions relatif à Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: billmath
ms.openlocfilehash: 07b0209ef94f91c00b98b8801323a58cd9d14494
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Forum Aux Questions sur Azure Active Directory Connect

## <a name="general-installation"></a>Installation générale
**Q : L’installation fonctionnera-t-elle si l’option 2FA est activée pour l’administrateur global AD Azure ?**  
Cela est pris en charge avec les builds de février 2016.

**Q : Existe-t-il une manière d’installer Azure AD Connect sans assistance ?**  
L’installation d’Azure AD Connect n’est possible qu’à partir de l’Assistant Installation. Une installation automatique et silencieuse n’est pas prise en charge.

**Q : Je possède une forêt dans laquelle un domaine ne peut pas être contacté. Comment installer Azure AD Connect ?**  
Cela est pris en charge avec les builds de février 2016.

**Q : L’agent AD DS Health fonctionne-t-il sur Server Core ?**  
Oui. Après avoir installé l’agent, vous pouvez terminer le processus d’inscription à l’aide de l’applet de commande PowerShell suivante : 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q : AADConnect prend-il en charge la synchronisation à partir de deux domaines vers Azure AD ?**</br>
Oui, cette méthode est prise en charge. Reportez-vous à [Plusieurs domaines](active-directory-aadconnect-multiple-domains.md).
 
**Q : Est-il possible d’avoir plusieurs connecteurs pour le même domaine Active Directory dans Azure AD Connect ?**</br> Non. Ce n’est pas possible. 

## <a name="network"></a>Réseau
**Q : J’ai un pare-feu, un périphérique réseau ou autre chose qui limite la durée maximale pendant laquelle les connexions peuvent rester ouvertes sur mon réseau. Quel doit être le seuil de délai côté client lors de l’utilisation d’Azure AD Connect ?**  
Tous les logiciels réseau, périphériques physiques ou autres limitant la durée maximale pendant laquelle les connexions peuvent rester ouvertes doivent utiliser un seuil d'au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où est installé le client Azure AD Connect et Azure Active Directory. Cela s'applique également à tous les outils de synchronisation de Microsoft Identity précédemment publiés.

**Q : Les domaines avec un nom en une seule partie sont-ils pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux utilisant des noms de domaine en une seule partie.

**Q : Les forêts comportant des domaines AD disjoints sont-elles prises en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts locales contenant des espaces de noms disjoints.

**Q : Les noms NetBios comportant un point sont-ils pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux  dont le nom NetBios contient un point « . ».

**Q : L'environnement IPv6 pur est-il pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge l'environnement IPv6 pur.

## <a name="federation"></a>Fédération
**Q : Que faire si je reçois un e-mail me demandant de renouveler mon certificat Office 365**  
Suivez les instructions décrites dans la rubrique [Renouveler les certificats](active-directory-aadconnect-o365-certs.md) concernant le renouvellement du certificat.

**Q : « Mettre à jour automatiquement la partie de confiance » est défini pour la partie de confiance Office 365. Dois-je effectuer une action lorsque mon certificat de signature de jetons bascule automatiquement ?**  
Utilisez les instructions décrites dans l’article [Renouveler les certificats](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environnement
**Q : Est-il possible de renommer le serveur une fois qu’Azure AD Connect est installé ?**  
Non. La modification du nom du serveur empêche le moteur de synchronisation de se connecter à la base de données SQL et le service de démarrer.

## <a name="identity-data"></a>Données d’identité
**Q : L’attribut UPN (userPrincipalName) dans Azure AD ne correspond pas à l’UPN local. Pourquoi ?**  
Reportez-vous aux articles suivants :

* [Les noms d’utilisateur dans Office 365, Azure ou Intune ne correspondent pas à l’UPN local ou à l’ID de connexion secondaire](https://support.microsoft.com/en-us/kb/2523192)
* [Les modifications ne sont pas synchronisées par l’outil de synchronisation Azure Active Directory une fois que vous avez modifié l’UPN d’un compte utilisateur afin qu’il utilise un autre domaine fédéré](https://support.microsoft.com/en-us/kb/2669550)

Vous pouvez également configurer Azure AD pour permettre au moteur de synchronisation de mettre à jour userPrincipalName comme décrit dans [fonctionnalités du service de synchronisation Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**Q : Est-ce que la correspondance approximative des objets Groupe/Contact AD locaux avec les objets Azure AD Groupe/Contact existants est prise en charge ?**  
Oui, et elle est basée sur l’adresse proxy.  La correspondance approximative n’est pas prise en charge pour les groupes qui ne sont pas à extension messagerie.

**Q : Est-il possible de définir manuellement l’attribut ImmutableId sur des objets Groupe/Contact Azure AD existants pour la mise en correspondance exacte avec les objets Groupe/Contact locaux ?**  
Non, cela n’est pas pris en charge actuellement.

## <a name="custom-configuration"></a>Configuration personnalisée
**Q : Où réside la documentation sur les applets de commande PowerShell pour Azure Active Directory ?**  
À l’exception des applets de commande décrites sur ce site, les autres applets de commande PowerShell disponibles dans Azure AD Connect ne sont pas prises en charge par le client.

**Q : Puis-je utiliser « Exportation serveur/importation serveur » dans *Synchronization Service Manager* pour déplacer la configuration entre des serveurs ?**  
Non. Cette option ne récupérera pas tous les paramètres de configuration et ne doit pas être utilisée. Vous devez plutôt utiliser l’Assistant pour créer la configuration de base sur le deuxième serveur et utiliser l’éditeur de règles de synchronisation pour générer des scripts PowerShell afin de déplacer une règle personnalisée entre les serveurs. Consultez la section [Migration « Swing »](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Q : Les mots de passe peuvent-ils être mis en cache pour la page de connexion Azure, et est-il possible d’empêcher cela, étant donné que la page contient un élément d’entrée de mot de passe avec l’attribut autocomplete = "false" ?**</br>
Pour l’instant, nous ne prenons pas en charge la modification des attributs HTML du champ d’entrée de mot de passe, et notamment de la balise « autocomplete ». Nous travaillons actuellement à l’élaboration d’une fonctionnalité d’autorisation d’un code Javascript personnalisé qui vous permettra d’ajouter n’importe quel attribut au champ du mot de passe. Cette fonctionnalité devrait être accessible courant 2017.

**Q : La page de connexion Azure affiche les noms d’utilisateur des utilisateurs qui se sont déjà connectés avec succès.  Est-il possible de désactiver ce comportement ?**</br>
Pour l’instant, nous ne prenons pas en charge la modification des attributs HTML de la page de connexion. Nous travaillons actuellement à l’élaboration d’une fonctionnalité d’autorisation d’un code Javascript personnalisé qui vous permettra d’ajouter n’importe quel attribut au champ du mot de passe. Cette fonctionnalité devrait être accessible courant 2017.

**Q : Existe-t-il un moyen d’empêcher les sessions simultanées ?**</br>
Non.

## <a name="troubleshooting"></a>Résolution de problèmes
**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Recherche dans la Base de connaissances Microsoft (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Recherchez dans la Base de connaissances Microsoft (KB) des solutions techniques aux problèmes courants couverts par la garantie de réparation et d’assistance, relatifs à la prise en charge d’Azure AD Connect.

[Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Vous pouvez naviguer et rechercher des questions et des réponses techniques de la communauté ou poser votre question en cliquant [ici](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Comment bénéficier du support pour Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Cliquez sur ce lien pour bénéficier du support par le biais du portail Azure.

