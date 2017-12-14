---
title: "Résoudre les problèmes du proxy d’application | Microsoft Docs"
description: "Explique comment résoudre les erreurs dans le proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 87c88f9ba9932c101e979c949121aae0884e9f24
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Résoudre les problèmes de proxy d’application et les messages d’erreur
Si des erreurs se produisent dans l’accès à une application publiée ou dans la publication d’applications, vérifiez les options suivantes pour voir si le proxy d’application Microsoft Azure Active Directory fonctionne correctement :

* Ouvrez la console Services Windows et vérifiez que le service **Connecteur de proxy d’application Microsoft AAD** est activé et en cours d’exécution. Vous pouvez également consulter la page de propriétés du service Proxy d’application, comme le montre l’image suivante :   
  ![Capture d’écran de la fenêtre Propriétés du connecteur de proxy d’application Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Ouvrez l’Observateur d’événements et recherchez les événements du proxy d’application sous **Journaux des applications et des services** > **Microsoft** > **AadApplicationProxy** > **Connecteur** > **Admin**.
* Si nécessaire, des journaux plus détaillés sont disponibles [en activant les journaux de session du connecteur de proxy d’application](application-proxy-understand-connectors.md#under-the-hood).

Pour plus d’informations sur l’outil de dépannage d’Azure AD, consultez [Troubleshooting tool to validate connector networking prerequisites](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites) (Outil de dépannage pour valider la configuration requise pour la mise en réseau d’un connecteur).

## <a name="the-page-is-not-rendered-correctly"></a>La page ne s’affiche pas correctement
Vous pouvez rencontrer des problèmes d’affichage ou de fonctionnement de votre application sans recevoir de message d’erreur spécifique. Cela peut se produire si vous avez publié le chemin d’accès de l’article, mais que l’application requiert un contenu qui se trouve en dehors de ce chemin d’accès.

Par exemple, si vous publiez le chemin d’accès https://yourapp/app mais que l’application appelle les images dans https://yourapp/media, celles-ci ne seront pas restituées. Assurez-vous que vous publiez l’application en utilisant le niveau du chemin d’accès le plus élevé pour inclure tous les contenus pertinents. Dans le présent exemple, ce serait http://yourapp/.

Si vous modifiez votre chemin d’accès pour inclure le contenu référencé, mais que les utilisateurs doivent quand même accéder à un lien plus détaillé du chemin d’accès, consultez le billet du blog intitulé [Setting the right link for Application Proxy applications in the Azure AD access panel and Office 365 app launcher](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)(Définir le bon lien pour les applications Application Proxy dans le panneau d’accès Azure AD et le lanceur d’applications Office 365).

## <a name="connector-errors"></a>Erreurs de connecteur

Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue. 

Si l’inscription échoue au cours de l’installation de l’assistant Connecteur, il existe deux façons d’afficher la raison de l’échec. Vous pouvez effectuer une recherche dans le journal des événements sous **Journaux des applications et des services\Microsoft\AadApplicationProxy\Connecteur\Admin**, ou exécuter la commande Windows PowerShell suivante :

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Une fois l’erreur de connecteur identifiée dans le journal des événements, reportez-vous à cette table d’erreurs courantes pour résoudre le problème :

| Error | Étapes recommandées |
| ----- | ----------------- |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « Une ou plusieurs erreurs se sont produites. » | Si vous avez fermé la fenêtre d’inscription sans vous connecter à Azure AD, réexécutez l’Assistant Connecteur et inscrivez le connecteur. <br><br> Si la fenêtre d’inscription s’ouvre puis ferme immédiatement sans vous permettre de vous connecter, vous recevrez probablement cette erreur. Cette erreur se produit quand il existe une erreur réseau sur votre système. Assurez-vous qu’il est possible de se connecter à partir d’un navigateur à un site web public et que les ports sont ouverts comme spécifié dans les [conditions préalables du proxy d’application](active-directory-application-proxy-enable.md). |
| L’erreur est présentée en clair dans la fenêtre d’inscription. Impossible de poursuivre | Si cette erreur se produit et que la fenêtre se ferme, cela signifie que vous avez saisi un nom d’utilisateur ou un mot de passe incorrect. Réessayez. |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « AADSTS50059 : pas d’informations d’identification de locataire trouvées dans la demande ou déduites des informations d’identification fournies. Échec de la recherche selon l’URI du principal du service. | Vous essayez de vous connecter en utilisant un compte Microsoft mais pas un domaine qui fait partie de l’ID d’organisation du répertoire auquel vous tentez d’accéder. Assurez-vous que l’administrateur fait partie du même nom de domaine que le domaine du locataire. Par exemple, si le domaine Azure AD est contoso.com, l’administrateur doit être admin@contoso.com. |
| Impossible de récupérer la stratégie d’exécution actuelle pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution de PowerShell n’est pas désactivée. <br><br>1. Ouvrez l’Éditeur de stratégie de groupe.<br>2. Accédez à **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution des scripts**.<br>3. Cette stratégie d’exécution peut être définie sur **Non configuré** ou **Activé**. Si elle est définie sur **Activé**, vérifiez que sous Options, la stratégie d’exécution est définie sur **Autoriser les scripts locaux et les scripts signés distants** ou sur **Autoriser tous les scripts**. |
| Échec du téléchargement de la configuration par le connecteur. | Le certificat client du connecteur, qui est utilisé pour l’authentification, est expiré. Ceci peut également se produire si le connecteur est installé derrière un proxy. Dans ce cas, le connecteur ne peut pas accéder à Internet et ne sera pas en mesure de fournir des applications aux utilisateurs distants. Renouvelez l’approbation manuellement à l’aide de l’applet de commande `Register-AppProxyConnector` dans Windows PowerShell. Si votre connecteur est derrière un proxy, il est nécessaire d’octroyer l’accès à Internet aux comptes du connecteur « services réseau » et « système local ». Ceci peut être effectué en leur accordant l’accès au proxy ou en les configurant pour qu’ils ignorent le proxy. |
| Échec de l’inscription du connecteur : vous devez être un administrateur global de votre Active Directory pour inscrire le connecteur. Erreur : « La demande d’inscription a été refusée. » | L’alias avec lequel vous essayez de vous connecter n’est pas un administrateur sur ce domaine. Votre connecteur est toujours installé pour l’annuaire qui détient le domaine de l’utilisateur. Assurez-vous que l’administrateur de compte que vous voulez utiliser pour vous connecter dispose des autorisations globales sur le client Azure AD. |

## <a name="kerberos-errors"></a>Erreurs Kerberos

Cette table répertorie les erreurs les plus courantes qui proviennent de la configuration et de l’installation de Kerberos et inclut des suggestions de résolution.

| Error | Étapes recommandées |
| ----- | ----------------- |
| Impossible de récupérer la stratégie d’exécution actuelle pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution de PowerShell n’est pas désactivée.<br><br>1. Ouvrez l’Éditeur de stratégie de groupe.<br>2. Accédez à **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution des scripts**.<br>3. Cette stratégie d’exécution peut être définie sur **Non configuré** ou **Activé**. Si elle est définie sur **Activé**, vérifiez que sous Options, la stratégie d’exécution est définie sur **Autoriser les scripts locaux et les scripts signés distants** ou sur **Autoriser tous les scripts**. |
| 12008 - Azure AD a dépassé le nombre maximal de tentatives d’authentification Kerberos autorisé auprès du serveur principal. | Cette erreur peut indiquer une configuration incorrecte entre Azure AD et le serveur d’applications principal, ou un problème de configuration de la date/heure sur les deux ordinateurs. Le serveur principal a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le serveur d’applications principal sont synchronisées. |
| 13016 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur, car il n’existe pas d’UPN dans le jeton de périphérie ou dans le cookie d’accès. | Il existe un problème avec la configuration de STS (SharePoint Team Services). Corrigez la configuration de la revendication UPN dans STS. |
| 13019 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur en raison de l’erreur API générale suivante. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine, ou un problème de configuration de la date/heure sur les deux ordinateurs. Le contrôleur de domaine a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes, en particulier les configurations du nom de principal du service (SPN). Assurez-vous qu’Azure AD est joint au même domaine que le contrôleur de domaine pour que le contrôleur de domaine établisse la relation d’approbation avec Azure AD. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le contrôleur de domaine sont synchronisées. |
| 13020 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur, car le SPN du serveur principal n’est pas défini. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine, ou un problème de configuration de la date/heure sur les deux ordinateurs. Le contrôleur de domaine a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes, en particulier les configurations du nom de principal du service (SPN). Assurez-vous qu’Azure AD est joint au même domaine que le contrôleur de domaine pour que le contrôleur de domaine établisse la relation d’approbation avec Azure AD. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le contrôleur de domaine sont synchronisées. |
| 13022 - Azure AD ne peut pas authentifier l’utilisateur, car le serveur principal répond aux tentatives d’authentification Kerberos avec une erreur HTTP 401. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur d’applications principal, ou un problème de configuration de la date/heure sur les deux ordinateurs. Le serveur principal a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le serveur d’applications principal sont synchronisées. |

## <a name="end-user-errors"></a>Erreurs de l’utilisateur final

Cette liste comprend des erreurs que vos utilisateurs finaux peuvent rencontrer en cas d’échec de leur tentative d’accès à l’application. 

| Error | Étapes recommandées |
| ----- | ----------------- |
| Le site web ne peut pas afficher la page. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée si l’application est une application avec l’authentification Windows intégrée. Le SPN défini pour cette application est peut-être incorrect. Pour les applications avec l’authentification Windows intégrée : assurez-vous que le SPN configuré pour cette application est correct. |
| Le site web ne peut pas afficher la page. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée si l’application est une application OWA. La raison peut en être l’une des suivantes : <br><li>Le SPN défini pour cette application est incorrect. Assurez-vous que le SPN configuré pour cette application est correct.</li><li>L’utilisateur qui a tenté d’accéder à l’application utilise un compte Microsoft au lieu du compte d’entreprise approprié pour se connecter, ou bien l’utilisateur est un utilisateur invité. Assurez-vous que l’utilisateur se connecte en utilisant son compte d’entreprise qui correspond au domaine de l’application publiée. Les utilisateurs d’un compte Microsoft et les invités ne peuvent pas accéder aux applications avec l’authentification Windows intégrée.</li><li>L’utilisateur qui a tenté d’accéder à l’application n’est pas défini correctement pour cette application du côté local. Assurez-vous que cet utilisateur a les autorisations appropriées, telles qu’elles sont définies pour cette application principale sur l’ordinateur local. |
| Cette application d’entreprise n’est pas accessible. Vous n’êtes pas autorisé à accéder à cette application. Échec de l’autorisation. Veillez à affecter à l’utilisateur un accès à cette application. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée s’il utilise des comptes Microsoft plutôt que leur compte d’entreprise pour se connecter. Les utilisateurs invités peuvent également obtenir cette erreur. Les utilisateurs d’un compte Microsoft et les invités ne peuvent pas accéder aux applications avec l’authentification Windows intégrée. Assurez-vous que l’utilisateur se connecte en utilisant son compte d’entreprise qui correspond au domaine de l’application publiée.<br><br>Vous n’avez peut-être pas affecté cette application à l’utilisateur. Accédez à l’onglet **Application** et, sous **Utilisateurs et groupes**, affectez cet utilisateur ou ce groupe d’utilisateurs à cette application. |
| Cette application d’entreprise n’est pas accessible pour l’instant. Réessayez ultérieurement... Le connecteur a dépassé le délai d’expiration. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée s’il n’est pas correctement défini pour cette application du côté local. Assurez-vous que votre utilisateur a les autorisations appropriées, telles qu’elles sont définies pour cette application principale sur l’ordinateur local. |
| Cette application d’entreprise n’est pas accessible. Vous n’êtes pas autorisé à accéder à cette application. Échec de l’autorisation. Assurez-vous que l’utilisateur a une licence pour Azure Active Directory Premium ou Basic. | Votre utilisateur peut recevoir cette erreur lors d’une tentative d’accès à l’application que vous avez publiée si l’administrateur de l’abonné ne lui a pas affecté explicitement une licence De base/Premium. Accédez à l’onglet **Licences** Active Directory de l’abonné et assurez-vous qu’une licence De base ou Premium est affectée à cet utilisateur ou à ce groupe. |

## <a name="my-error-wasnt-listed-here"></a>Mon erreur n’était pas répertoriée ici

Si vous rencontrez une erreur ou un problème avec le proxy d’application Azure AD qui ne figure pas dans ce guide de dépannage, veuillez nous contacter. Envoyez un message électronique à notre [équipe de commentaires](mailto:aadapfeedback@microsoft.com) comportant les détails de l’erreur qui s’est produite.

## <a name="see-also"></a>Voir aussi
* [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable.md)
* [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Activer l’accès conditionnel](application-proxy-enable-remote-access-sharepoint.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
