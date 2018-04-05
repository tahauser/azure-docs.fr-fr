---
title: Interface utilisateur d'Azure Mobile Engagement - Campagne Reach
description: Découvrez comment créer et gérer les campagnes de notifications Push à l'aide d'Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d457a8d5f68f70f6bf7a5f086b5e1d65bd1ea95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Création et gestion des campagnes de notification Push
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Vous pouvez utiliser la section Reach de l'interface utilisateur pour créer une nouvelle campagne Push à l'aide d'une formule complexe en indiquant toutes les informations dont vous avez besoin pour envoyer une notification Push. Les options d'une campagne Push varient quelque peu selon les quatre types de campagne : Annonces, Sondages, Push de données et Vignettes (Windows Phone uniquement).

### <a name="option-applies-to"></a>L'option s'applique à :
* Langues : toutes (Annonces, Sondages, Push de données et Vignettes)
* Campagne : toutes (Annonces, Sondages, Push de données et Vignettes)
* Notification : Annonces et Sondages
* Contenu : unique pour chaque type de campagne
* Audience : toutes (Annonces, Sondages, Push de données et Vignettes)
* Délai d’exécution : Annonces, Sondages et Vignettes
* Test : toutes (Annonces, Sondages, Push de données et Vignettes)

![Reach-Campaign1][20]

## <a name="languages"></a>Langues
Vous pouvez utiliser le menu déroulant Langues pour envoyer une version différente de votre notification Push vers des appareils configurés pour d'autres langues. Par défaut, tous les appareils reçoivent la même notification Push, quelle que soit la langue pour laquelle ils sont configurés. Les utilisateurs qui disposent d'un appareil configuré pour une autre langue recevront la version de la notification Push dans la langue par défaut. De nombreuses options de campagne Push vous permettent de spécifier un contenu différent pour chaque langue supplémentaire que vous sélectionnez. 

![Reach-Campaign2][21]

### <a name="language-differences-apply-to"></a>Les différences linguistiques s'appliquent à :
* Langues : des langues uniques peuvent être sélectionnées en complément de la langue par défaut
* Campagne : identique pour toutes les langues
* Notification : unique pour chaque langue ajoutée en complément de la langue par défaut
* Contenu : unique pour chaque langue ajoutée en complément de la langue par défaut
* Audience : peut être filtrée par un critère de langue distinct
* Délai d’exécution : identique pour toutes les langues
* Test : peut être envoyé pour chaque langue en même temps

### <a name="supported-languages"></a>Langues prises en charge :
* Arabe (ar) 
* Bulgare (bg) 
* Catalan (ca) 
* Chinois (zh) 
* Croate (hr) 
* Tchèque (cs) 
* Danois (da) 
* Néerlandais (nl) 
* Anglais (en) 
* Finnois (fi) 
* Français (fr) 
* Allemand (de) 
* Grec (el) 
* Hébreu (he) 
* Hindi (hi) 
* Hongrois (hu) 
* Indonésien (id) 
* Italien (it) 
* Japonais (ja) 
* Coréen (ko) 
* Letton (lv) 
* Lituanien (lt) 
* Malais (macro-langue) (ms) 
* Norvégien Bokmål (nb) 
* Polonais (pl) 
* Portugais (pt) 
* Roumain (ro) 
* Russe (ru) 
* Serbe (sr) 
* Slovaque (sk) 
* Slovène (sl) 
* Espagnol (es) 
* Suédois (sv) 
* Tagalog (tl) 
* Thaï (th) 
* Turc (tr) 
* Ukrainien (uk) 
* Vietnamien (vi) 

## <a name="campaign"></a>Campagne
Vous pouvez utiliser la section Campagne pour définir le nom et la catégorie de votre campagne et si vous envisagez d'ignorer la section Audience d'une campagne Push et d'envoyer cette campagne via l'API Reach (et certains éléments de l'API Push de bas niveau). Des catégories peuvent être utilisées avec un modèle de notification personnalisé pour contrôler les notifications dans l'application suivant des paramètres prédéfinis. Vous pouvez obtenir une liste de vos « Catégories » existantes via l'API Reach.

> [!WARNING]
> Si vous utilisez l'option « Ignorer l'audience, la notification Push sera envoyée aux utilisateurs via l'API Reach » dans la section « Campagne » de la campagne Reach, cette campagne ne sera pas automatiquement envoyée. Vous devrez l'envoyer manuellement via l'API Reach.

![Reach-Campaign3][22]

### <a name="option-applies-to"></a>L'option s'applique à :
* Nom : toutes
* Catégorie : Annonces et Sondages
* Ignorer l’audience, la notification Push sera envoyée aux utilisateurs via l’API : toutes

## <a name="notification"></a>Notification
Utilisez la section Notification pour définir les paramètres de base pour votre notification Push, y compris : le titre de la notification Push, le message, une image dans l'application ou si elle peut être révocable. De nombreux paramètres de notification sont spécifiques à la plateforme de votre appareil. Vous pouvez choisir d'envoyer votre notification Push depuis l'application ou en dehors de l'application, ou les deux. (N'oubliez pas que les utilisateurs peuvent accepter ou refuser les notifications Push envoyées en dehors de l'application au niveau du système d'exploitation de leurs appareils. Azure Mobile Engagement ne peut pas remplacer ce paramètre. Rappelez-vous également que l'API Reach gère les notifications Push dans et en dehors de l'application L'API Push peut également être utilisée pour gérer les notifications Push envoyées en dehors de l'application.) Les notifications Push peuvent être personnalisées avec des images ou du contenu HTML, notamment des liens ciblés menant hors de votre application ou vers un autre emplacement dans votre application (Kit de développement logiciel (SDK) Android 2.1.0 ou catégories d'intention ultérieures requises). Vous pouvez modifier l'icône ou le badge iOS et envoyer du texte ou du contenu Web (une fenêtre contextuelle avec du contenu HTML, un lien URL vers un autre emplacement dans ou en dehors de l'application). Vous pouvez aussi faire sonner ou vibrer les appareils Android avec la notification Push. (Rappelez-vous que vous aurez besoin des bonnes autorisations du Kit de développement logiciel (SDK) dans votre fichier manifeste Android pour faire sonner ou vibrer un appareil.) Il n'y a actuellement aucune norme industrielle pour les tailles « Grand format » puisque la taille des écrans est différente pour chaque appareil. Toutefois, les images 400 x 100 fonctionnent sur presque toutes les tailles d'écran.

### <a name="delivery-types"></a>Types d'envoi :
* En dehors de l'application uniquement : la notification sera envoyée lorsque l'utilisateur n'utilisera pas son application.
* Ce type de notification requiert un certificat d'Apple ou de Google (certificat APNs ou GCM).
* Dans l'application uniquement : la notification apparaît uniquement lorsque l'application est exécutée.
* Cette notification utilise le système de messagerie Capptain pour contacter l'utilisateur. Vous pouvez personnaliser entièrement l'affichage ou la disposition de votre notification Push.
* À tout moment : cette option garantit l'envoi de votre notification, que l'application soit exécutée ou non.

![Reach-Campaign4][23]

### <a name="option-applies-to"></a>L'option s'applique à :
* Notification : Annonces et Sondages

## <a name="content"></a>Contenu
Vous pouvez utiliser la section Contenu pour modifier le contenu de vos annonces, de vos sondages, de vos Push de données et de vos vignettes (Windows Phone uniquement). Le paramètre Contenu de vos campagnes Push est spécifique à chaque type de campagne. 

### <a name="see-also"></a>Voir aussi
* [Documentation de l’interface utilisateur - Reach - Contenu Push][Link 29]

![Reach-Campaign5][24]

## <a name="audience"></a>Audience
Utilisez la section Audience pour définir une liste d'éléments standard pour limiter votre campagne ou pour définir les limites de votre campagne en fonction des critères personnalisés. L'ensemble des options standard pour limiter votre audience vous permet d'envoyer des notifications Push aux nouveaux ou aux anciens utilisateurs, ou aux utilisateurs des Push natifs uniquement. Vous pouvez également définir un quota pour limiter le nombre d'utilisateurs recevant la notification Push. Vous pouvez modifier manuellement l'expression pour définir la façon dont votre campagne est filtrée afin d'inclure un ou plusieurs critères pour les utilisateurs cibles. Vous pouvez taper manuellement une expression d'audience. Ce type d'expression doit définir explicitement la relation entre les critères. Un critère est décrit par un identificateur qui doit commencer par une majuscule et ne peut pas contenir d'espace. La relation entre les critères peut être décrite à l'aide des opérateurs and, or, not (et, ou, pas) ainsi que '(', ')'. Exemple : « Critère1 ou (Critère1 sans Critère2) ».

> [!NOTE]
> Si l'audience incluse dans les campagnes est importante, l'analyse de ciblage côté serveur peut être lente, en particulier si vous essayez de démarrer plusieurs campagnes en même temps.

* Si possible, ne démarrez qu'une campagne à la fois.
* Tout au plus, ne démarrez que quatre campagnes en même temps.
* Envoyez des notifications Push à vos utilisateurs actifs uniquement (case à cocher « Solliciter seulement les utilisateurs qui peuvent être contactés via un Push natif » et « Solliciter seulement les utilisateurs actifs ») de façon à ce que seuls les utilisateurs qui ont installé votre application et l'utilisent toujours doivent être analysés.
  Une fois votre audience définie, vous pouvez utiliser le bouton de simulation pour savoir combien d'utilisateurs recevront cette notification Push. Ce bouton calculera le nombre d'utilisateurs connus potentiellement ciblés par cette audience (il s'agit d'une estimation basée sur un échantillon aléatoire d'utilisateurs). Notez que les utilisateurs qui ont désinstallé l'application font également partie de l'audience mais ne peuvent pas être contactés.

### <a name="see-also"></a>Voir aussi
* [Documentation sur l’interface utilisateur - Reach - Nouveau critère Push][Link 28]

![Reach-Campaign6][25]

### <a name="edit-expression"></a>Modifier l'expression
![Reach-Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>L'option de limitation de votre audience s'applique à :
* Solliciter uniquement un sous-ensemble d’utilisateurs : toutes (Annonces, Sondages, Push de données et Vignettes)
* Solliciter uniquement d’anciens utilisateurs : toutes (Annonces, Sondages, Push de données et Vignettes)
* Solliciter uniquement de nouveaux utilisateurs : toutes (Annonces, Sondages, Push de données et Vignettes)
* Solliciter uniquement des utilisateurs inactifs : Annonces, Sondages et Vignettes
* Solliciter uniquement des utilisateurs actifs : toutes (Annonces, Sondages, Push de données et Vignettes)
* Solliciter uniquement des utilisateurs qui peuvent être contactés via un Push natif : Annonces et Sondages

## <a name="time-frame"></a>Délai d'exécution
Vous pouvez utiliser la section Délai d'exécution pour définir à quel moment la notification Push sera envoyée ou vous pouvez laisser le champ Délai d'exécution vide pour démarrer immédiatement la campagne. Notez que l'utilisation du fuseau horaire de l'utilisateur final peut faire démarrer la campagne un jour plus tôt que prévu pour vos utilisateurs en Asie et envoyer des petits lots de notifications Push à une certaine heure jusqu'à ce que tous les fuseaux horaires du monde correspondent au délai d'exécution défini pour votre campagne. L'utilisation du fuseau horaire des utilisateurs finaux peut également entraîner des retards dans les campagnes puisque cela nécessite de connaître l'heure du téléphone avant de démarrer l'envoi des notifications Push.

> [!NOTE]
> Les campagnes qui n'ont pas de date de fin peuvent mettre en cache des notifications Push localement et continuer de les afficher une fois que vous avez terminé manuellement ces campagnes. Pour éviter cela, indiquer une date de fin pour ces campagnes.

### <a name="see-also"></a>Voir aussi
* [Reach - Procédures - Planification][Link 3] 

![Reach-Campaign8][27]

### <a name="settings-apply-to"></a>Les paramètres s'appliquent à :
* Délai d’exécution : Annonces, Sondages et Vignettes

## <a name="test"></a>Test
Vous pouvez utiliser la section Test pour envoyer cette notification Push à votre propre appareil de test avant d'enregistrer la campagne. Si vous avez configuré des langues personnalisées pour cette campagne, vous pouvez tester la notification Push pour ces langues. Vous pouvez configurer un appareil de test depuis « Mon compte ».

> [!NOTE]
> Aucune donnée côté serveur n'est enregistrée lorsque vous utilisez le bouton pour « tester » les notifications Push. Les données sont enregistrées uniquement pour les vraies campagnes de notification Push.

### <a name="see-also"></a>Voir aussi
* [Documentation sur l’interface utilisateur - Mon compte][Link 14]

![Reach-Campaign9][28]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

