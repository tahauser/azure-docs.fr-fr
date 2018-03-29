---
title: Liste de contrôle des performances et de l’extensibilité d’Azure Storage | Microsoft Docs
description: Une liste des pratiques éprouvées à utiliser avec Azure Storage dans le développement d’applications performantes.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: 945289a172270eea56625287baf437fd4b70c7f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage
## <a name="overview"></a>Vue d'ensemble
Depuis la publication des services Microsoft Azure Storage, Microsoft a élaboré une série de pratiques éprouvées pour les utiliser de manière performante. Cet article se propose d’énumérer les méthodes les plus importantes sous la forme d’une liste de contrôle. L’objectif de cet article est double : aider les développeurs d’applications à s’assurer qu’ils utilisent des pratiques éprouvées avec Azure Storage et les aider à en identifier d’autres en vue de les adopter. Cet article n’a pas pour but d’aborder toutes les questions relatives à l’optimisation de l’extensibilité et des performances. Sont exclues les pratiques dont l’impact est minime ou celles qui ne sont pas applicables à grande échelle. Dans la mesure où le comportement de l’application peut être prévu au cours de la conception, il convient de tenir compte de ces pratiques suffisamment tôt afin d’éviter les problèmes de performances ultérieurs.  

Chaque développeur d’applications qui utilise Azure Storage doit prendre le temps de lire cet article et s’assurer que son application respecte chacune des pratiques éprouvées répertoriées ci-dessous.  

## <a name="checklist"></a>Liste de contrôle
Dans cet article, les pratiques éprouvées sont classées dans les groupes suivants. Pratiques éprouvées applicables aux éléments suivants :  

* Tous les services Azure Storage (objets blob, tables, files d’attente et fichiers)
* Objets blob
* Tables
* Files d’attente  

| Terminé | Domaine | Catégorie | Question |
| --- | --- | --- | --- |
| &nbsp; | Tous les services |Objectifs d'évolutivité |[Votre application est-elle conçue de manière à éviter d'atteindre les objectifs d'évolutivité ?](#subheading1) |
| &nbsp; | Tous les services |Objectifs d'évolutivité |[Votre convention d'affectation de noms est-elle conçue pour améliorer l'équilibrage de la charge ?](#subheading47) |
| &nbsp; | Tous les services |Mise en réseau |[Les appareils côté client disposent-ils d'une bande passante suffisamment large et d'une latence suffisamment faible pour parvenir aux performances requises ?](#subheading2) |
| &nbsp; | Tous les services |Mise en réseau |[La qualité de la liaison des appareils côté client est-elle suffisamment élevée ?](#subheading3) |
| &nbsp; | Tous les services |Mise en réseau |[L'application cliente est-elle située « à proximité » du compte de stockage ?](#subheading4) |
| &nbsp; | Tous les services |Distribution de contenu |[Utilisez-vous un CDN pour la distribution de contenu ?](#subheading5) |
| &nbsp; | Tous les services |Accès direct au client |[Utilisez-vous SAP et CORS au lieu d'un proxy pour permettre un accès direct au client ?](#subheading6) |
| &nbsp; | Tous les services |Mise en cache |[Votre application met-elle en cache les données qui sont utilisées fréquemment et qui changent rarement ?](#subheading7) |
| &nbsp; | Tous les services |Mise en cache |[Votre application traite-t-elle les mises à jour par lots (mise en cache côté client, suivie du téléchargement dans des ensembles plus grands) ?](#subheading8) |
| &nbsp; | Tous les services |Configuration .NET |[Avez-vous configuré votre client pour qu'il utilise un nombre suffisant de connexions simultanées ?](#subheading9) |
| &nbsp; | Tous les services |Configuration .NET |[Avez-vous configuré .NET pour qu'il utilise un nombre suffisant de threads ?](#subheading10) |
| &nbsp; | Tous les services |Configuration .NET |[Utilisez-vous .NET 4.5 ou version ultérieure, qui présente une méthode de nettoyage de mémoire optimisée ?](#subheading11) |
| &nbsp; | Tous les services |Parallélisme |[Vous êtes-vous assuré que le parallélisme était limité de manière appropriée, de manière à ne pas surcharger les capacités du client, ni dépasser les objectifs d'évolutivité ?](#subheading12) |
| &nbsp; | Tous les services |Outils |[Utilisez-vous la version la plus récente des outils et bibliothèques clientes fournis par Microsoft ?](#subheading13) |
| &nbsp; | Tous les services |Nouvelle tentatives |[Utilisez-vous une stratégie de nouvelles tentatives d'interruption exponentielle pour les erreurs de limitation et les délais d'expiration ?](#subheading14) |
| &nbsp; | Tous les services |Nouvelle tentatives |[Votre application empêche-t-elle les nouvelles tentatives pour les erreurs non renouvelables ?](#subheading15) |
| &nbsp; | Objets blob |Objectifs d'évolutivité |[Disposez-vous d’un grand nombre de clients qui accèdent simultanément à un seul objet ?](#subheading46) |
| &nbsp; | Objets blob |Objectifs d'évolutivité |[Votre application respecte-t-elle l'objectif d'évolutivité relatif aux opérations ou à la bande passante pour un objet blob unique ?](#subheading16) |
| &nbsp; | Objets blob |Copie d'objets blob |[La copie des objets blob s'effectue-t-elle de manière efficace ?](#subheading17) |
| &nbsp; | Objets blob |Copie d'objets blob |[Utilisez-vous AzCopy pour les copies en bloc d'objets blob ?](#subheading18) |
| &nbsp; | Objets blob |Copie d'objets blob |[Utilisez-vous le service Azure Import/Export pour transférer de très gros volumes de données ?](#subheading19) |
| &nbsp; | Objets blob |Utilisation de métadonnées |[Stockez-vous des métadonnées fréquemment utilisées concernant des objets blob dans leurs métadonnées ?](#subheading20) |
| &nbsp; | Objets blob |Téléchargement rapide |[Lorsque vous essayez de télécharger rapidement un seul objet blob, téléchargez-vous les blocs en parallèle ?](#subheading21) |
| &nbsp; | Objets blob |Téléchargement rapide |[Lorsque vous essayez de télécharger rapidement de nombreux objets blob, les téléchargez-vous en parallèle ?](#subheading22) |
| &nbsp; | Objets blob |Type d'objet blob correct |[Utilisez-vous des objets blob de pages ou de blocs lorsque cela s'avère approprié ?](#subheading23) |
| &nbsp; | Tables |Objectifs d'évolutivité |[Vous approchez-vous des objectifs d'évolutivité en termes d'entités par seconde ?](#subheading24) |
| &nbsp; | Tables |Configuration |[Utilisez-vous JSON pour vos demandes de table ?](#subheading25) |
| &nbsp; | Tables |Configuration |[Avez-vous désactivé Nagle pour améliorer les performances des petites demandes ?](#subheading26) |
| &nbsp; | Tables |Tables et partitions |[Avez-vous correctement partitionné vos données ?](#subheading27) |
| &nbsp; | Tables |Partitions actives |[Évitez-vous les modèles « Ajouter après uniquement » ou « Ajouter avant uniquement » ?](#subheading28) |
| &nbsp; | Tables |Partitions actives |[Vos opérations d'insertion/de mise à jour couvrent-elles plusieurs partitions ?](#subheading29) |
| &nbsp; | Tables |Étendue de requête |[Avez-vous conçu votre schéma pour qu'il autorise l'utilisation des requêtes de point dans la plupart des cas et l'utilisation modérée des requêtes de tables ?](#subheading30) |
| &nbsp; | Tables |Densité des requêtes |[En règle générale, vos requêtes n'analysent et ne renvoient-elles que les lignes qui seront utilisées par votre application ?](#subheading31) |
| &nbsp; | Tables |Limitation des données renvoyées |[Utilisez-vous le filtrage pour éviter le renvoi d'entités inutiles ?](#subheading32) |
| &nbsp; | Tables |Limitation des données renvoyées |[Utilisez-vous la projection pour éviter le renvoi de propriétés inutiles ?](#subheading33) |
| &nbsp; | Tables |Dénormalisation |[Avez-vous dénormalisé vos données de manière à éviter les requêtes inefficaces ou les demandes de lecture multiples lorsque vous essayez de récupérer des données ?](#subheading34) |
| &nbsp; | Tables |Insertion/Mise à jour/Suppression |[Effectuez-vous un traitement par lots des demandes qui doivent être transactionnelles ou qui peuvent être effectuées en même temps pour réduire les allers-retours ?](#subheading35) |
| &nbsp; | Tables |Insertion/Mise à jour/Suppression |[Évitez-vous de récupérer une entité pour déterminer simplement s'il faut appeler l'opération insert ou update ?](#subheading36) |
| &nbsp; | Tables |Insertion/Mise à jour/Suppression |[Avez-vous envisagé de stocker des séries de données qui seront fréquemment récupérées ensemble dans une seule entité sous la forme de propriétés plutôt que d'entités multiples ?](#subheading37) |
| &nbsp; | Tables |Insertion/Mise à jour/Suppression |[Dans le cas des tables qui sont toujours récupérées ensemble et qui peuvent être écrites par lots (des données de séries temporelles, par exemple), avez-vous envisagé d'utiliser des objets blob à la place de tables ?](#subheading38) |
| &nbsp; | Files d’attente |Objectifs d'évolutivité |[Vous approchez-vous des objectifs d'évolutivité en termes de messages par seconde ?](#subheading39) |
| &nbsp; | Files d’attente |Configuration |[Avez-vous désactivé Nagle pour améliorer les performances des petites demandes ?](#subheading40) |
| &nbsp; | Files d’attente |Taille des messages |[Vos messages sont-ils compacts pour améliorer les performances de la file d'attente ?](#subheading41) |
| &nbsp; | Files d’attente |Récupération en bloc |[Récupérez-vous plusieurs messages dans une seule opération « Get » ?](#subheading42) |
| &nbsp; | Files d’attente |Fréquence d'interrogation |[Effectuez-vous des interrogations suffisamment fréquentes pour réduire la latence perçue de votre application ?](#subheading43) |
| &nbsp; | Files d’attente |Mise à jour de message |[Utilisez-vous la méthode UpdateMessage pour stocker la progression du traitement des messages et éviter de devoir retraiter l'intégralité du message en cas d'erreur ?](#subheading44) |
| &nbsp; | Files d’attente |Architecture |[Utilisez-vous des files d'attente pour rendre toute votre application plus extensible en excluant les charges de travail de longue durée du chemin critique et pour les faire ensuite évoluer séparément ?](#subheading45) |

## <a name="allservices"></a>Tous les Services
Cette section répertorie les pratiques éprouvées qui s’appliquent à l’utilisation de tout service Azure Storage (objets blob, tables, files d’attente ou fichiers).  

### <a name="subheading1"></a>Objectifs d’extensibilité
Chaque service Azure Storage présente des objectifs d’extensibilité en termes de capacité (Go), de taux de transactions et de bande passante. Si votre application s’approche de l’un des objectifs d’extensibilité, voire le dépasse, une limitation ou des latences de transaction accrues peuvent survenir. Lorsqu’un service de stockage limite votre application, il commence à renvoyer les codes d’erreur « 503 Serveur occupé » ou « 500 Délai d’expiration de l’opération » pour certaines opérations de stockage. Cette section traite de la méthode générale de traitement des objectifs d’extensibilité et, en particulier, des objectifs d’extensibilité de bande passante. Les sections ultérieures portant sur les différents services de stockage traitent des objectifs d’extensibilité dans le cadre de ce service spécifique :  

* [Bande passante des objets blob et demandes par seconde](#subheading16)
* [Entités de table par seconde](#subheading24)
* [Messages de file d’attente par seconde](#subheading39)  

#### <a name="sub1bandwidth"></a>Cible d’extensibilité de bande passante pour tous les services
Au moment de la rédaction du présent document, les objectifs de bande passante, aux États-Unis, d’un compte de stockage géo-redondant (GRS) étaient de 10 Gbits/s (gigabits par seconde) en entrée (données envoyées vers le compte de stockage) et de 20 Gbits/s en sortie (données envoyées à partir du compte de stockage). Ces limites sont plus élevées dans le cas d’un compte de stockage redondant local (LRS), à savoir : 20 Gbits/s en entrée et 30 Gbits/s en sortie.  Les limites de bande passante internationales peuvent être inférieures. Pour les consulter, rendez-vous sur la [page traitant des objectifs d’extensibilité](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Pour plus d’informations sur les options de redondance du stockage, voir les liens de la section [Ressources utiles](#sub1useful) ci-dessous.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Que faire lorsque l’on s’approche d’un objectif d’extensibilité ?
Si votre application s’approche des objectifs d’extensibilité d’un seul compte de stockage, pensez à appliquer l’une des méthodes suivantes :  

* Réexaminez la charge de travail à cause de laquelle votre application s’approche de l’objectif d’extensibilité ou le dépasse. Est-il possible de la concevoir différemment pour qu’elle utilise moins de bande passante ou de capacité, ou moins de transactions ?
* Si une application doit dépasser l’un des objectifs d’extensibilité, vous devez créer plusieurs comptes de stockage et partitionner vos données d’application sur ces comptes. Si vous optez pour ce modèle, veillez à concevoir votre application de telle sorte que vous puissiez ajouter davantage de comptes de stockage à l’avenir en vue de l’équilibrage de la charge. Au moment de la rédaction du présent document, chaque abonnement Azure peut comporter jusqu’à 100 comptes de stockage.  De plus, aucun coût autre que l’utilisation en termes de données stockées, de transactions effectuées ou de données transférées n’est associé à ces comptes de stockage.
* Si votre application atteint les objectifs de bande passante, pensez à compresser les données dans le client afin de réduire la bande passante nécessaire pour envoyer les données au service de stockage.  Bien que cela puisse réduire la bande passante et améliorer les performances du réseau, cette méthode présente des aspects négatifs.  Vous devez évaluer l’impact sur les performances en raison des exigences de traitement supplémentaires liées à la compression et à la décompression des données sur le client. Le stockage de données compressées peut, en outre, rendre plus complexe la résolution des problèmes, étant donné qu’il peut s’avérer plus difficile d’afficher les données stockées à l’aide d’outils standard.
* Si votre application atteint les objectifs d’extensibilité, vérifiez que vous utilisez bien une interruption exponentielle pour les nouvelles tentatives (voir [Nouvelles tentatives](#subheading14)).  Il est préférable de veiller à ne jamais s’approcher des objectifs d’extensibilité (en utilisant l’une des méthodes ci-dessus). Vous vous assurez ainsi que votre application n’effectue pas de nouvelle tentative immédiatement, ce qui aurait pour conséquence d’empirer la limitation.  

#### <a name="useful-resources"></a>Ressources utiles
Suivez les liens ci-dessous pour obtenir des informations détaillées sur les objectifs d'évolutivité :

* Pour plus d’informations sur les objectifs d’extensibilité, consultez [Objectifs de performance et évolutivité d’Azure Storage](storage-scalability-targets.md) .
* Consultez la [réplication Azure Storage](storage-redundancy.md) et le billet de blog [Options de redondance de Microsoft Azure Storage et stockage géo-redondant avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) pour plus d’informations sur les options de redondance de stockage.
* Pour obtenir des informations à jour sur la tarification des services Azure, consultez la page [Tarification Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Conventions d’affectation de noms aux partitions
Azure Storage utilise un schéma de partitionnement basé sur une plage pour mettre à l'échelle et équilibrer la charge du système. La clé de partition sert à partitionner les données en plages, et la charge de ces plages est équilibrée sur l’ensemble du système. Cela signifie que les conventions d'affectation de noms telles que l'ordre lexical (par exemple, msftpayroll, msftperformance, msftemployees, etc.) ou à l'aide d'un horodatage (log20160101, log20160102, log20160102, etc.) utiliseront les partitions potentiellement en colocation sur le même serveur de partition, jusqu’à ce qu’une opération de fractionnement les fractionne en plages plus petites. Par exemple, tous les objets blob d’un conteneur peuvent être fournis par un même serveur jusqu'à ce que la charge sur ces objets blob nécessite un rééquilibrage plus approfondi des plages de la partition. De même, un groupe de comptes faiblement chargés avec leurs noms classés par ordre lexical peut être pris en charge par un même serveur jusqu'à ce que la charge sur un ou tous ces comptes nécessite qu’ils soient répartis entre plusieurs serveurs de partitions. Chaque opération d'équilibrage de la charge peut affecter la latence des appels de stockage lors de l'opération. La capacité du système à gérer une hausse soudaine du trafic vers une partition est limitée par l'évolutivité d'un serveur de partition unique jusqu'à ce que l'opération d'équilibrage de la charge intervienne et rééquilibre la plage clé de la partition.  

Vous pouvez suivre quelques bonnes pratiques pour réduire la fréquence de ces opérations.  

* Examinez attentivement la convention d’affectation de noms que vous utilisez pour les comptes, conteneurs, objets blob, tables et files d'attente. Vous pouvez ajouter un préfixe aux noms de comptes avec un hachage à 3 chiffres à l'aide d'une fonction de hachage qui correspond le mieux à vos besoins.  
* Si vous organisez vos données à l'aide d'horodatages ou d’identificateurs numériques, vous devez veiller à ne pas utiliser des modèles de trafic « Ajouter après uniquement » ou « Ajouter avant uniquement ». Ces modèles ne sont pas adaptés à un système de partitionnement basé sur des plages et risquent de diriger tout le trafic vers une partition unique, empêchant ainsi un équilibrage optimal de la charge sur le système. Par exemple, si vos opérations quotidiennes utilisent un objet blob avec un horodatage comme yyyymmdd, tout le trafic pour cette opération quotidienne est alors dirigé vers un objet unique pris en charge par un serveur de partition unique. Vérifiez si les limites par objet blob et par partition répondent à vos besoins, et pensez à diviser cette opération en plusieurs objets blob si nécessaire. De même, si vous stockez des données chronologiques dans vos tables, tout le trafic pourrait être dirigé vers la dernière partie de l'espace de noms de clé. Si vous devez utiliser des horodatages ou des ID numériques, ajoutez en préfixe un hachage à 3 chiffres à l’ID ou, dans le cas des horodateurs, ajoutez en préfixe la partie des secondes, par exemple ssyyyymmdd. Si des opérations de listage et d'interrogation sont effectuées régulièrement, choisissez une fonction de hachage qui limite le nombre de vos requêtes. Dans d'autres cas, un préfixe aléatoire peut être suffisant.  
* Pour plus d'informations sur le schéma de partitionnement utilisé dans Azure Storage, consultez le document SOSP disponible [ici](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Mise en réseau
Bien que les appels d’API soient importants, les contraintes de réseau physiques de l’application ont souvent un impact majeur sur les performances. Vous trouverez, ci-dessous, certaines des limitations auxquelles les utilisateurs peuvent être confrontés.  

#### <a name="client-network-capability"></a>Fonctionnalités réseau du client
##### <a name="subheading2"></a>Débit
Dans le cas de la bande passante, le problème est souvent dû aux capacités du client. Par exemple, bien qu’un seul compte de stockage puisse gérer un débit de 10 Gbits/s ou plus en entrée (voir [Objectifs d’évolutivité de bande passante](#sub1bandwidth)), la vitesse réseau d’une petite instance Rôle de travail Azure ne permet qu’un débit d’environ 100 Mbits/s. Dans le cas des instances Azure plus importantes, les cartes réseau présentent une capacité supérieure. Si vous avez besoin de limites réseau plus élevées à partir d’un seul ordinateur, vous devez donc envisager d’utiliser une instance plus grande ou davantage de machines virtuelles. Si vous accédez à un service de stockage à partir d’une application locale, alors la même règle s’applique : comprendre les capacités réseau du périphérique client et la connectivité réseau à l’emplacement de stockage Azure et l’améliorer selon vos besoins ou concevoir votre application conformément à ces capacités.  

##### <a name="subheading3"></a>Qualité de la liaison
Comme c’est le cas pour toute utilisation du réseau, veuillez tenir compte du fait que les conditions réseau qui génèrent des erreurs et une perte de paquets ralentissent le débit effectif.  L’utilisation de WireShark ou de NetMon peut vous aider à diagnostiquer ce problème.  

##### <a name="useful-resources"></a>Ressources utiles
Pour plus d’informations sur les tailles de machines virtuelles et la bande passante allouée, consultez la rubrique [Tailles des machines virtuelles](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tailles des machines virtuelles Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Emplacement
Dans un environnement distribué, le fait de placer le client à proximité du serveur se traduit par des performances optimales. Pour accéder à Azure Storage avec un minimum de latence, votre client doit idéalement se trouver dans la même région Azure. Par exemple, dans le cas d’un site web qui utilise Azure Storage, tous deux doivent se trouver dans la même région (Ouest des États-Unis ou Asie du Sud-Est, par exemple). Cela réduit à la fois la latence et les coûts. Au moment de la rédaction du présent document, l’utilisation de la bande passante dans une seule région était gratuite.  

Si votre application cliente n’est pas hébergée dans Azure (c’est le cas, par exemple, des applications pour appareil mobile ou des services d’entreprise locaux), le fait de placer le compte de stockage dans une région proche des appareils qui y accéderont se traduit généralement par une latence plus faible. En cas de distribution à grande échelle de vos clients (par exemple, certains se trouvent en Amérique du Nord et d’autres en Europe), l’utilisation de plusieurs comptes de stockage peut s’avérer judicieuse : un dans une région de l’Amérique du Nord et l’autre dans une région d’Europe. Cela contribue à réduire la latence pour les utilisateurs des deux régions. En règle générale, cette méthode est plus facile à mettre en œuvre si les données stockées par l’application sont spécifiques de certains utilisateurs et elle ne nécessite pas de réplication des données entre divers comptes de stockage.  Pour une distribution du contenu à grande échelle, un CDN est recommandé. Pour plus d’informations à ce sujet, voir la section suivante.  

### <a name="subheading5"></a>Distribution de contenu
Il arrive qu’une application doive diffuser le même contenu vers plusieurs utilisateurs situés au sein d’une même région ou dans des régions différentes. Il peut s’agir, par exemple, d’une vidéo de démonstration d’un produit utilisée sur la page d’accueil d’un site web. Dans ce cas, vous devez utiliser un réseau de distribution de contenu (CDN), tel que le CDN Azure, qui utilise le stockage Azure comme origine des données. Contrairement à un compte Azure Storage qui existe dans une seule région et qui ne peut pas diffuser de contenu avec une faible latence vers d’autres régions, le CDN Azure utilise des serveurs dans plusieurs centres de données répartis dans le monde entier. De plus, un CDN peut généralement prendre en charge des limites de sortie bien plus élevées qu’un compte de stockage unique.  

Pour plus d’informations sur le CDN Azure, voir la page [CDN Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Utilisation de SAP et de CORS
Lorsque vous devez autoriser du code, tel que JavaScript, dans le navigateur Web d’un utilisateur ou une application pour téléphone mobile afin d’accéder à des données dans le stockage Azure, une méthode consiste à utiliser une application dans un rôle Web en tant que proxy : l’appareil de l’utilisateur s’authentifie avec le rôle Web, qui à son tour s’authentifie avec le service de stockage. Vous évitez ainsi d’exposer vos clés de compte de stockage sur des appareils non sécurisés. Cependant, cela place une surcharge importante sur le rôle web, dans la mesure où toutes les données transférées entre l’appareil de l’utilisateur et le service de stockage doivent transiter par ce rôle. Vous pouvez éviter d’utiliser un rôle web comme proxy pour le service de stockage en utilisant des signatures d’accès partagé (SAP), combinées parfois à des en-têtes CORS (Partage des ressources cross-origin). Grâce au modèle SAP, vous pouvez autoriser l’appareil de votre utilisateur à adresser directement des demandes à un service de stockage par le biais d’un jeton à accès limité. Par exemple, si un utilisateur souhaite télécharger une photo vers votre application, votre rôle web peut générer et envoyer à l’appareil de cet utilisateur un jeton SAP qui accordera des autorisations en écriture sur un conteneur ou un objet blob spécifique au cours des 30 prochaines minutes (au terme desquelles le jeton SAP expirera).

En règle générale, un navigateur n’autorise pas le code JavaScript d’une page hébergée par un site web sur un domaine à effectuer des opérations spécifiques telles que « PUT » sur un autre domaine. Par exemple, si vous hébergez un rôle web à l’adresse « contosomarketing.cloudapp.net » et que vous souhaitez utiliser du code JavaScript côté client pour télécharger un objet blob vers votre compte de stockage à l’adresse « contosoproducts.blob.core.windows.net », la stratégie « same origin policy » du navigateur interdit cette opération. CORS est une fonctionnalité de navigateur qui autorise le domaine cible (dans ce cas, le compte de stockage) à indiquer au navigateur qu’il fait confiance aux demandes en provenance du domaine source (dans ce cas, le rôle web).  

Ces deux technologies vous aident à éviter toute charge inutile (ainsi que les goulots d’étranglement) au niveau de votre application web.  

#### <a name="useful-resources"></a>Ressources utiles
Pour plus d’informations sur SAP, voir la page [Signatures d’accès partagé, partie 1 : présentation du modèle SAP](../storage-dotnet-shared-access-signature-part-1.md)  

Pour plus d’informations sur CORS, consultez [Prise en charge du service Partage des ressources cross-origin (CORS) pour les services Azure Storage](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Mise en cache
#### <a name="subheading7"></a>Récupération de données
En règle générale, il est préférable de récupérer des données d’un service à une seule reprise plutôt que deux fois. Prenons l’exemple d’une application web MVC exécutée dans un rôle web qui a déjà récupéré un objet blob de 50 Mo du service de stockage afin de le diffuser comme contenu à un utilisateur. L’application pourra, par la suite, récupérer ce même objet blob chaque fois qu’un utilisateur en fera la demande ou le mettre en cache sur un disque local et réutiliser cette version mise en cache pour les demandes ultérieures de l’utilisateur. De plus, lorsqu’un utilisateur demandera les données, l’application pourra émettre une commande GET avec un en-tête conditionnel pour l’heure de modification, évitant ainsi la récupération de l’intégralité de l’objet blob s’il n’a pas été modifié. Vous pouvez appliquer ce même schéma à l’utilisation des entités de table.  

Dans certains cas, vous pouvez déterminer que votre application part du principe que l’objet blob reste valide pendant une courte période après sa récupération, et que, au cours de cette période, elle ne doit pas vérifier si l’objet blob a été modifié.

Les données de configuration, de recherche et d’autres données toujours utilisées par l’application constituent de parfaits candidats pour la mise en cache.  

Pour savoir comment faire en sorte que les propriétés d’un objet blob détectent la date de la dernière modification à l’aide de .NET, consultez [Configuration et récupération des propriétés et des métadonnées](../blobs/storage-properties-metadata.md). Pour plus d’informations sur les téléchargements conditionnels, consultez [Spécification d’en-têtes conditionnels pour les opérations de Service Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Téléchargement de données par lots
Dans certains scénarios d’application, vous pouvez agréger des données en local, puis les télécharger périodiquement dans un lot au lieu de les télécharger immédiatement une à une. Par exemple, une application web peut conserver un fichier journal des activités : l’application peut soit télécharger, sous la forme d’une entité de table, les détails de chaque activité lorsqu’elle se produit (ce qui implique de nombreuses opérations de stockage), soit enregistrer les détails de l’activité dans un fichier journal local, puis les télécharger régulièrement vers un objet blob sous la forme d’un fichier délimité. Si chaque entrée du journal a une taille de 1 Ko, vous pouvez en télécharger des milliers au cours d’une seule transaction « Put Blob » (vous pouvez télécharger un objet d’une taille maximale de 64 Mo au cours d’une seule transaction). Bien sûr, si l’ordinateur local tombe en panne avant le téléchargement, vous pouvez perdre des données de journal : le développeur de l’application doit penser à la possibilité de défaillances de périphérique client ou de téléchargement.  Si les données d’activité doivent être téléchargées pour un intervalle de temps (et pas seulement pour une seule activité), il est préférable d’utiliser des objets blob plutôt que des tables.

### <a name="net-configuration"></a>Configuration .NET
Si vous utilisez .NET Framework, vous trouverez dans cette section plusieurs paramètres de configuration rapide que vous pouvez appliquer pour améliorer sensiblement les performances.  Si vous utilisez un autre langage, vérifiez si des concepts similaires y sont associés.  

#### <a name="subheading9"></a>Augmentation de la limite de connexions par défaut
Dans .NET, le code suivant augmente la limite de connexions par défaut (qui est généralement de 2 dans un environnement client ou de 10 dans un environnement de serveur) à 100. En règle générale, vous devez définir la valeur sur environ le nombre de threads utilisés par votre application.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Vous devez définir la limite de connexions avant d’ouvrir une connexion.  

Pour les autres langages de programmation, voir la documentation correspondante pour savoir comment définir la limite de connexions.  

Pour plus d’informations, consultez le billet de blog [Services web : connexions simultanées](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Augmentation du nombre minimum de threads du pool de threads en cas d’utilisation de code synchrone avec Async Tasks
Ce code augmente le nombre minimum de threads du pool de threads :  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Pour plus d’informations, consultez [Méthode ThreadPool.SetMinThreads](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Utilisation du nettoyage de la mémoire de .NET 4.5
Utilisez .NET 4.5 ou version ultérieure pour que l’application cliente tire parti des améliorations de la fonctionnalité de nettoyage de la mémoire du serveur sur le plan des performances.

Pour plus d'informations, consultez l'article [Présentation des améliorations des performances de .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Parallélisme illimité
Le parallélisme peut améliorer sensiblement les performances. Soyez toutefois prudent lorsque vous utilisez le parallélisme illimité (nombre de threads et/ou de demandes parallèles illimité) pour charger ou télécharger des données, ou lors de l’utilisation de plusieurs rôles de travail pour accéder à plusieurs partitions (conteneurs, files d’attente ou partitions de table) dans le même compte de stockage ou à plusieurs éléments d’une même partition. Si vous optez pour un parallélisme illimité, votre application peut dépasser les capacités de l’appareil client ou les objectifs d’extensibilité du compte de stockage, ce qui se traduit par des temps de latence plus importants et par une limitation.  

### <a name="subheading13"></a>Outils et bibliothèques clientes de stockage
Utilisez toujours la version la plus récente des outils et bibliothèques clientes fournis par Microsoft. Au moment de la rédaction du présent document, des bibliothèques clientes étaient disponibles pour .NET, Windows Phone, Windows Runtime, Java et C++, et des bibliothèques en version préliminaire sont également disponibles pour d’autres langages. Microsoft a, en outre, publié des cmdlets PowerShell et des commandes de l’interface de ligne de commande, utilisables avec Azure Storage. Microsoft s’attelle au développement de ces outils dans une optique de performances, veille à leur mise à jour continue avec les versions de service les plus récentes et s’assure qu’ils répondent, en interne, à la majorité des pratiques éprouvées en termes de performances.  

### <a name="retries"></a>Nouvelle tentatives
#### <a name="subheading14"></a>Limitation/Serveur occupé
Dans certains cas, il se peut que le service de stockage limite votre application ou qu’il soit simplement incapable de répondre à la demande en raison d’une situation temporaire, et renvoie alors un message « 503 Serveur occupé » ou « 500 Délai d’expiration de l’opération ».  Cela peut se produire si votre application s’approche de l’un des objectifs d’extensibilité ou si le système rééquilibre vos données partitionnées pour permettre un débit plus élevé.  L’application cliente doit généralement réessayer l’opération qui provoque cette erreur : retenter la même demande ultérieurement peut réussir. Cependant, si le service de stockage limite votre application en raison d’un dépassement des objectifs d’extensibilité, ou si le service n’a pas été en mesure de répondre à la demande pour une autre raison, effectuer des nouvelles tentatives agressives ne fait généralement qu’aggraver le problème. C’est pourquoi il est conseillé d’opter pour une interruption exponentielle (il s’agit du comportement par défaut des bibliothèques clientes). Votre application peut, par exemple, effectuer une nouvelle tentative après 2 secondes, puis 4 secondes, 10 secondes, 30 secondes avant d’abandonner complètement. Cela se traduit par un allégement sensible de la charge de l’application sur le service, au lieu d’aggraver le problème.  

Les erreurs de connectivité ne sont pas dues à une limitation et sont généralement temporaires. Dès lors, de nouvelles tentatives peuvent être effectuées immédiatement.  

#### <a name="subheading15"></a>Erreurs non renouvelables
Les bibliothèques clientes peuvent faire la distinction entre les erreurs renouvelables et celles qui ne le sont pas. Cependant, si vous écrivez votre propre code par rapport à l’API REST de stockage, n’oubliez pas certaines erreurs non renouvelables : par exemple, une réponse 400 (Bad Request) indique que l’application cliente a envoyé une demande qui n’a pas pu être traitée car elle n’était pas au format attendu. Renvoyer cette demande générera à chaque fois la même réponse. La renouveler ne sert donc à rien ! Si vous écrivez votre propre code pour l’API REST de stockage, vous devez connaître la signification des codes d’erreur et, le cas échéant, la méthode de renouvellement à appliquer pour chacun d’eux.  

#### <a name="useful-resources"></a>Ressources utiles
Pour plus d’informations sur les codes d’erreur de stockage, voir la page [Codes d’état et codes d’erreur](http://msdn.microsoft.com/library/azure/dd179382.aspx) sur le site web Microsoft Azure.  

## <a name="blobs"></a>Objets blob
Outre les pratiques éprouvées pour [Tous les services](#allservices) décrites précédemment, les pratiques ci-dessous s'appliquent spécifiquement au service BLOB.  

### <a name="blob-specific-scalability-targets"></a>Objectifs d’extensibilité propres aux objets blob
#### <a name="subheading46"></a>Plusieurs clients accédant simultanément à un seul objet
Si vous avez un grand nombre de clients qui accèdent simultanément à un seul objet, vous devrez prendre en compte les objectifs d’évolutivité de compte de stockage et par objet. Le nombre exact de clients qui peuvent accéder à un objet unique varie en fonction de facteurs tels que le nombre de clients demandant l’accès à l’objet simultanément, la taille de l’objet, les conditions réseau, etc.

Si l’objet peut être distribué via un CDN, comme les images ou les vidéos provenant d’un site web, vous devez utiliser un CDN. Voir [ici](#subheading5).

Dans d’autres scénarios tels que les simulations scientifiques où les données sont confidentielles, vous disposez de deux options. La première consiste à échelonner l’accès de votre charge de travail de façon à ce que l’objet soit accessible sur une période de temps au lieu d’un accès simultané. Sinon, vous pouvez copier temporairement l’objet sur plusieurs comptes de stockage pour améliorer le nombre total d’E/S par objet et sur les comptes de stockage. Dans un test limité, nous avons constaté qu’environ 25 machines virtuelles pouvaient télécharger simultanément un objet blob de 100 Go (chaque machine virtuelle téléchargeait en parallèle à l’aide de 32 threads). Si vous avez 100 clients devant accéder à l’objet, copiez-le dans un deuxième compte de stockage et attribuez l’accès au premier objet blob aux 50 premières machines virtuelles, puis les 50 machines virtuelles suivantes accèdent au deuxième objet blob. Les résultats varient selon le comportement de vos applications. Nous vous conseillons de tester ceci pendant la conception. 

#### <a name="subheading16"></a>Bande passante et opérations par objet blob
Le débit maximal en lecture ou en écriture sur un objet blob unique est de 60 Mo/seconde (soit environ 480 Mbits/s), ce qui est supérieur aux capacités de nombreux réseaux côté client (y compris la carte réseau physique qui équipe l’appareil client). De plus, un seul objet blob prend en charge plus de 500 demandes par seconde. Si vous risquez de dépasser ces limites lorsque plusieurs clients doivent lire le même objet blob, il est conseillé d’utiliser un CDN pour distribuer l’objet blob.  

Pour plus d’informations sur le débit cible pour les objets blob, consultez [Objectifs de performance et d’extensibilité d’Azure Storage](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copie et déplacement d’objets blob
#### <a name="subheading17"></a>Copie d’un objet blob
L’API REST de stockage version 2012-02-12 a introduit la fonctionnalité utile de copier des objets blob entre les comptes : une application cliente peut indiquer au service de stockage de copier un objet blob d’une autre source (éventuellement dans un compte de stockage différent), puis de laisser au service le soin d’effectuer la copie de manière asynchrone. Cela peut réduire sensiblement la bande passante requise pour l’application lorsque vous faites migrer des données depuis d’autres comptes de stockage, dans la mesure où vous ne devez pas envoyer et télécharger les données.  

Il convient cependant de tenir compte du fait que lorsque vous effectuez une copie entre des comptes de stockage, vous ne pouvez pas savoir avec certitude quand l’opération prendra fin. Si votre application doit procéder à une copie rapide d’un objet blob sous votre supervision, il peut être préférable de le copier en le téléchargeant sur une machine virtuelle, puis en le transférant vers la destination.  Pour que cette opération se déroule dans les meilleures conditions possibles, assurez-vous que la copie est effectuée par une machine virtuelle exécutée dans la même région Azure, sans quoi les conditions réseau affecteront plus que probablement les performances de copie.  Vous pouvez, en outre, surveiller la progression d’une copie asynchrone via un programme.  

Nous attirons votre attention sur le fait que les copies effectuées au sein d’un même compte de stockage sont généralement plus rapides.  

Pour plus d’informations, consultez [Copie d’un objet blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Utilisation d’AzCopy
L’équipe Stockage Azure a développé un outil en ligne de commande baptisé « AzCopy », destiné à faciliter le transfert en bloc de nombreux objets blob entre des comptes de stockage.  Cet outil est optimisé pour ce scénario et peut générer des taux de transfert élevés.  Il est vivement conseillé de l’utiliser pour les opérations de chargement, de téléchargement et de copie en bloc. Pour plus d’informations et pour accéder au téléchargement, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Service Azure Import/Export
Pour les très gros volumes de données (plus de 1 To), Azure Storage propose le service Import/Export qui permet de transférer et de télécharger des données à partir d’un stockage d’objets blob en expédiant des disques durs.  Vous pouvez copier vos données sur un disque dur et l’envoyer à Microsoft en vue du transfert des données, ou envoyer un disque dur vierge à Microsoft en vue de leur téléchargement.  Pour plus d'informations, consultez [Utilisation du service Import/Export Microsoft Azure pour transférer des données vers le stockage d'objets blob](../storage-import-export-service.md).  Cela peut se révéler bien plus pratique que de transférer un tel volume de données sur le réseau.  

### <a name="subheading20"></a>Utilisation de métadonnées
Le service BLOB prend en charge les demandes HEAD, lesquelles peuvent inclure des métadonnées sur un objet blob. Par exemple, si votre application doit extraire les données EXIF d’une photo, elle peut récupérer la photo en question, puis procéder à l’extraction. Pour économiser la bande passante et améliorer les performances, votre application peut stocker les données EXIF dans les métadonnées de l’objet blob lorsque l’application a téléchargé la photo : vous pouvez ensuite récupérer les données EXIF dans les métadonnées à l’aide d’une simple demande HEAD, ce qui permet d’économiser beaucoup de bande passante et de temps de traitement, nécessaires pour extraire les données EXIF chaque fois que l’objet blob est lu. Cela s’avère particulièrement utile lorsque vous avez simplement besoin des métadonnées, et non de tout le contenu d’un objet blob.  Un maximum de 8 Ko de métadonnées peut être stocké par objet blob (le service refusera les demandes de stockage supérieures). Dès lors, si vos données ne respectent pas cette taille, vous ne pourrez pas utiliser cette méthode.  

Pour savoir comment récupérer les métadonnées d’un objet blob à l’aide de .NET, consultez [Configuration et récupération des propriétés et des métadonnées](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Téléchargement rapide
Pour télécharger les objets blob rapidement, la première question est : téléchargez-vous un blob ou plusieurs ?  Utilisez les instructions ci-dessous pour déterminer la méthode correcte en fonction de votre scénario.  

#### <a name="subheading21"></a>Téléchargement rapide d’un objet blob volumineux
Pour télécharger rapidement un seul objet blob de grande taille, votre application cliente doit télécharger ses blocs ou pages en parallèle (en tenant compte des objectifs d’extensibilité des différents objets blob et du compte de stockage dans son ensemble).  Notez que les bibliothèques clientes de stockage RTM fournies par Microsoft (.NET, Java) offrent cette possibilité.  Pour chacune des bibliothèques, utilisez l’objet ou la propriété indiqué ci-dessous pour définir le niveau de simultanéité :  

* .NET : définissez ParallelOperationThreadCount sur un objet BlobRequestOptions à utiliser.
* Java/Android : utilisez BlobRequestOptions.setConcurrentRequestCount()
* Node.js : utilisez parallelOperationThreadCount sur les options de demande ou sur le service BLOB.
* C++ : utilisez la méthode blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Téléchargement rapide de nombreux objets blob
Pour télécharger rapidement de nombreux objets blob, effectuez cette opération en parallèle. Cela s’avère plus rapide que de télécharger des objets blob individuels avec des téléchargements de blocs parallèles, dans la mesure où le transfert est réparti entre plusieurs partitions du service de stockage. Dans le cas d’un objet blob unique, le débit pris en charge est seulement de 60 Mo/seconde (environ 480 Mbits/s). Au moment de la rédaction du présent document, un compte LRS basé sur US prenait en charge un débit de 20 Gbit/s en entrée, soit bien plus que le débit pris en charge par un objet blob individuel.  [AzCopy](#subheading18) effectue des téléchargements en parallèle et son utilisation est recommandée pour ce scénario.  

### <a name="subheading23"></a>Choix du type d’objet blob approprié
Azure Storage prend en charge deux types d’objet blob : *de pages* et *de blocs*. Pour un scénario d’utilisation donné, le type d’objet blob choisi affecte les performances et l’extensibilité de la solution. Les objets blob de blocs sont utiles si vous souhaitez télécharger efficacement de grandes quantités de données : par exemple, une application cliente a besoin de télécharger des photos ou vidéos sur le stockage d’objets blob. Les objets blob de pages sont appropriées si l’application doit effectuer des écritures aléatoires sur les données : par exemple, les disques durs virtuels d’Azure sont stockés en tant qu’objets blob de pages.  

Pour plus d’informations, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tables
Outre les pratiques éprouvées pour [Tous les services](#allservices) décrites précédemment, les pratiques ci-dessous s'appliquent spécifiquement au service de Table.  

### <a name="subheading24"></a>Objectifs d’extensibilité propres aux tables
Outre les limitations de bande passante d’un compte de stockage complet, les tables possèdent leur propre limite d’extensibilité.  Notez que le système équilibre la charge à mesure que le trafic augmente. Cependant, en cas de salves de trafic, il peut s’avérer impossible d’obtenir immédiatement ce volume de débit.  En cas de pic de trafic, une limitation et/ou des délais d’attente risquent de se produire tandis que le service de stockage décharge automatiquement votre table.  Une accélération progressive offre généralement de meilleurs résultats, dans la mesure où cela donne au système le temps d’équilibrer la charge de manière appropriée.  

#### <a name="entities-per-second-account"></a>Entités par seconde (compte)
S’agissant de l’accès aux tables, la limite d’extensibilité est de 20 000 entités (d’une taille individuelle de 1 Ko) par seconde pour un compte.  En règle générale, chaque entité insérée, mise à jour, supprimée ou analysée est comptabilisée.  Une insertion par lots composée de 100 entités compte donc pour 100 entités.  De même, une requête qui analyse 1 000 entités et en renvoie 5 compte pour 1 000 entités.  

#### <a name="entities-per-second-partition"></a>Entités par seconde (partition)
Dans une partition unique, l’objectif d’extensibilité pour l’accès aux tables est de 2 000 entités (d’une taille individuelle de 1 Ko) par seconde, en utilisant le même mode de calcul que celui décrit dans la section précédente.  

### <a name="configuration"></a>Configuration
Cette section décrit les paramètres de configuration rapide que vous pouvez utiliser pour améliorer sensiblement les performances du service de Table :  

#### <a name="subheading25"></a>Utilisation de JSON
Depuis la version 2013-08-15 du service de stockage, le service de Table prend en charge l’utilisation de JSON plutôt que le format AtomPub XML pour transférer des données de table. Cela permet de réduire la taille de la charge utile de quelque 75 % et d’améliorer sensiblement les performances de votre application.

Pour plus d’informations, consultez [Tables Microsoft Azure : présentation de JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) et [Format de charge utile pour les opérations du service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Désactivation de Nagle
L’algorithme de Nagle est utilisé à grande échelle sur les réseaux TCP/IP en vue d’améliorer les performances du réseau. Cependant, il n’est pas idéal dans toutes les situations (c’est le cas, par exemple, dans les environnements très interactifs). Pour le stockage Azure, l’algorithme de Nagle a un impact négatif sur les performances des demandes adressées aux services de table et de file d’attente. Vous devez donc le désactiver si cela s’avère possible.  

Pour plus d’informations, voir le billet de blog [Algorithme Nagle et petites demandes : des rapports peu amicaux](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) qui explique les problèmes d’interaction entre l’algorithme de Nagle et les demandes de table et de file d’attente. Vous y apprendrez également comment le désactiver dans votre application cliente.  

### <a name="schema"></a>Schéma
Le mode de représentation et d’interrogation de vos données constitue le principal facteur ayant une incidence sur les performances du service de Table. Bien que chaque application soit différente, cette section énumère quelques pratiques générales concernant les points suivants :  

* Conception de tables
* Requêtes efficaces
* Mises à jour de données efficaces  

#### <a name="subheading27"></a>Tables et partitions
Les tables sont divisées en partitions. Toutes les entités stockées dans une partition partagent la même clé de partition et sont associées à une clé de ligne pour les identifier dans cette partition. Les partitions offrent des avantages, mais elles s’accompagnent également de limites d’extensibilité.  

* Avantages : vous pouvez mettre à jour des entités d’une même partition au cours d’une seule transaction atomique par lots pouvant contenir jusqu’à 100 opérations de stockage distinctes (taille totale limite de 4 Mo). En partant du principe que le même nombre d’entités doit être récupéré, vous pouvez également interroger plus efficacement les données d’une seule partition que celles qui couvrent plusieurs partitions (vous trouverez d’autres conseils sur l’interrogation des données de table dans la suite de ce document).
* Limite d’extensibilité : l’accès aux entités stockées dans une seule partition ne peut pas faire l’objet d’un équilibrage de la charge, car les partitions prennent en charge les transactions atomiques par lots. C’est pourquoi l’objectif d’extensibilité d’une partition de table individuelle est inférieur à celui du service de Table dans son ensemble.  

Compte tenu des caractéristiques des tables et des partitions, il est conseillé d’adopter les principes de conception suivants :  

* Les données que votre application cliente a fréquemment mises à jour ou interrogées dans la même unité de travail logique doivent être situées dans la même partition.  Cela peut être dû au fait que votre application agrège les écritures ou que vous souhaitiez tirer parti d’opérations atomiques par lots.  Ajoutons encore que les données d’une seule partition peuvent être interrogées plus efficacement dans une seule requête que les données de plusieurs partitions.
* Les données que votre application cliente n’insère pas, ne met pas à jour ou n’interroge pas dans la même unité de travail logique (requête unique ou mise à jour par lots) doivent être situées dans des partitions distinctes.  Notez que le nombre de clés de partition dans une seule table n’est pas limité. Le fait qu’il y ait plusieurs millions de clés de partition ne constitue donc pas un problème et n’a aucune incidence sur les performances.  Par exemple, si votre application est un site web populaire auquel les utilisateurs doivent se connecter, il peut être judicieux de choisir l’ID utilisateur comme clé de partition.  

#### <a name="hot-partitions"></a>Partitions actives
On appelle « partition active » une partition qui reçoit un pourcentage disproportionné du trafic vers un compte et qui ne peut pas faire l’objet d’un équilibrage de la charge, car il s’agit d’une partition unique.  En règle générale, la création des partitions actives s’effectue de l’une des façons suivantes :  

##### <a name="subheading28"></a>Modèles « Ajouter après uniquement » et « Ajouter avant uniquement »
Avec le modèle « Ajouter après uniquement », l’intégralité (ou la grande majorité) du trafic à destination d’une clé de partition donnée augmente et diminue en fonction de l’heure.  Par exemple, si votre application utilise la date du jour comme clé de partition pour les données de journal.  Par conséquent, toutes les insertions sont placées dans la dernière partition de votre table et le système ne peut pas équilibrer la charge, car l’ensemble des écritures va en fin de table.  Si le trafic à destination de cette partition dépasse l’objectif d’extensibilité au niveau de la partition, cela se traduit par une limitation.  Il est préférable de s’assurer que le trafic est envoyé vers plusieurs partitions, afin de permettre l’équilibrage de la charge des demandes sur votre table.  

##### <a name="subheading29"></a>Données à trafic élevé
Si votre schéma de partitionnement donne lieu à une seule partition qui comporte simplement les données qui sont beaucoup plus utilisées que d’autres partitions, le phénomène de limitation risque également de se présenter à mesure que cette partition unique s’approche de son objectif d’extensibilité.  Il est conseillé de faire en sorte que votre schéma de partitionnement ne génère pas une partition unique qui s’approche des objectifs d’extensibilité.  

#### <a name="querying"></a>Interrogation
Cette section décrit les pratiques éprouvées concernant l’interrogation du service de Table.  

##### <a name="subheading30"></a>Étendue de requête
Pour spécifier la plage des entités à interroger, vous pouvez procéder de plusieurs façons.  Vous trouverez, ci-dessous, une brève description de chaque méthode.  

En règle générale, il est conseillé d’éviter les analyses (requêtes d’une taille supérieure à une seule entité). Cependant, si vous devez procéder de la sorte, tâchez d’organiser vos données de façon à ce que les analyses les récupèrent sans qu’il faille examiner ou renvoyer de grandes quantités d’entités dont vous n’avez pas besoin.  

###### <a name="point-queries"></a>Requêtes de point
Une requête de point récupère exactement une entité. Pour ce faire, elle spécifie la clé de partition et la clé de ligne de l’entité à récupérer. Ces requêtes s’avèrent particulièrement efficaces et il est conseillé de les utiliser autant que possible.  

###### <a name="partition-queries"></a>Requêtes de partition
Une requête de partition récupère un jeu de données qui partagent une clé de partition commune. En règle générale, cette requête spécifie une plage de valeurs de clé de ligne ou une plage de valeurs pour une propriété d’entité, en plus d’une clé de partition. Les requêtes de partition sont moins efficaces que les requêtes de point et doivent être utilisées avec modération.  

###### <a name="table-queries"></a>Requêtes de table
Une requête de table récupère un jeu d’entités ne partageant pas une clé de partition commune. Les requêtes de ce type ne sont pas efficaces et il est conseillé de les éviter dans la mesure du possible.  

##### <a name="subheading31"></a>Densité des requêtes
S’agissant de l’efficacité des requêtes, un autre facteur important est le nombre d’entités renvoyées par rapport au nombre d’entités analysées pour obtenir le jeu renvoyé. Si votre application effectue une requête de table avec un filtre pour une valeur de propriété partagée par seulement 1 % des données, la requête analyse 100 entités pour chaque entité renvoyée. Les objectifs d’extensibilité de table abordés précédemment sont liés au nombre d’entités analysées et non au nombre d’entités retournées : une densité de requête faible peut facilement générer un service de table qui limite votre application, car il convient d’analyser de nombreuses entités pour récupérer l’entité que vous recherchez.  Pour savoir comment éviter ce problème, consultez la section traitant de la [dénormalisation](#subheading34) ci-après.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitation du volume de données renvoyé
###### <a name="subheading32"></a>Filtrage
Lorsque vous savez qu’une requête va renvoyer des entités dont vous n’avez pas besoin dans l’application cliente, pensez à utiliser un filtre afin de réduire la taille du jeu renvoyé. Bien que les entités non renvoyées au client soient comptabilisées dans les limites d’extensibilité, les performances de votre application s’en trouveront améliorées, compte tenu de la taille réduite de charge utile du réseau et de la réduction du nombre d’entités traitées par votre application cliente.  Voir la remarque ci-dessus concernant la [densité des requêtes](#subheading31). Les objectifs d’extensibilité faisant référence au nombre d’entités analysées, il se peut qu’une requête qui exclut de nombreuses entités donne toujours lieu à une limitation, même si le nombre d’entités renvoyées est faible.  

###### <a name="subheading33"></a>Projection
Si votre application cliente nécessite seulement un jeu limité de propriétés des entités de votre table, vous pouvez utiliser la projection pour limiter la taille du jeu de données renvoyé. Comme c’est le cas avec le filtrage, cela permet de réduire la charge réseau et le traitement du client.  

##### <a name="subheading34"></a>Dénormalisation
Contrairement à l’utilisation des bases de données relationnelles, les pratiques éprouvées pour interroger efficacement des données de table entraînent leur dénormalisation. Cette opération consiste à dupliquer les mêmes données dans plusieurs entités (une pour chaque clé utilisable pour rechercher les données) afin de réduire le nombre d’entités qu’une requête doit analyser pour rechercher les données dont le client a besoin, plutôt que d’analyser de grandes quantités d’entités pour rechercher les données dont votre application a besoin.  Sur un site web de commerce électronique, par exemple, vous pouvez rechercher une commande en fonction de l’ID du client (rechercher les commandes de ce client) et de la date (rechercher les commandes passées à une date donnée).  Dans le stockage de tables, il est préférable de stocker l’entité (ou une référence à celle-ci) à deux reprises : une fois avec le nom de la table, la clé de partition et la clé de ligne afin de faciliter la recherche en fonction de l’ID de client, et une fois pour faciliter la recherche en fonction de la date.  

#### <a name="insertupdatedelete"></a>Insertion/Mise à jour/Suppression
Cette section décrit les pratiques éprouvées concernant la modification des entités stockées dans le service de Table.  

##### <a name="subheading35"></a>Traitement par lot
Dans Azure Storage, les transactions par lots sont connues sous le nom de transactions de groupe d’entités (ETG) ; toutes les opérations d’une ETG doivent être effectuées dans une partition unique d’une seule table. Lorsque cela s’avère possible, utilisez des ETG pour effectuer des opérations d’insertion, de mise à jour et de suppression par lots. Cela réduit le nombre d’allers-retours entre votre application cliente et le serveur, ainsi que le nombre de transactions facturables (une ETG est comptabilisée comme une seule transaction à des fins de facturation et peut contenir 100 opérations de stockage), et autorise les mises à jour atomiques (dans une ETG, toutes les opérations réussissent ou échouent). L’utilisation des ETG peut apporter des avantages non négligeables dans les environnements où les temps de latence sont élevés, tels que les appareils mobiles.  

##### <a name="subheading36"></a>Opération Upsert
Lorsque cela s'avère possible, il est conseillé d'utiliser des opérations de table **Upsert** . Il existe deux types d’opération **Upsert** ; tous deux peuvent se révéler plus efficaces que les opérations **Insert** et **Update** classiques :  

* **InsertOrMerge** : utilisez cette opération lorsque vous souhaitez télécharger un sous-ensemble des propriétés de l’entité, mais ne savez pas si cette dernière existe déjà. Si elle existe, cet appel met à jour les propriétés incluses dans l’opération **Upsert** et laisse toutes les propriétés existantes en l’état. Si elle n’existe pas, cet appel insère la nouvelle entité. Cela revient à utiliser la projection dans une requête, en ce sens que vous devez simplement télécharger les propriétés qui sont modifiées.
* **InsertOrReplace** : utilisez cette opération lorsque vous souhaitez télécharger une toute nouvelle entité, mais ne savez pas si cette dernière existe déjà. Ne l’utilisez que lorsque vous n’avez aucun doute quant à la qualité de la nouvelle entité téléchargée, car elle écrase complètement l’ancienne entité. Vous souhaitez, par exemple, mettre à jour l’entité qui stocke l’emplacement actuel d’un utilisateur et ce, que l’application ait déjà stocké ou non des données d’emplacement pour cet utilisateur ; la nouvelle entité d’emplacement est complète et vous n’avez besoin d’aucune information d’une entité précédente.

##### <a name="subheading37"></a>Stockage de séries de données dans une seule entité
Parfois, une application stocke une série de données fréquemment nécessaires pour tout récupérer à la fois : par exemple, une application peut suivre l’utilisation du processeur au fil du temps pour tracer un graphique propagée des données à partir des dernières 24 heures. Une méthode consiste à disposer d’une entité de table par heure, chaque entité représentant alors une heure donnée et stockant l’utilisation du processeur au cours de cette période. Pour représenter ces données sur un graphique, l’application doit récupérer les entités qui contiennent les données des 24 dernières heures.  

Sinon, votre application peut stocker l'utilisation du processeur pour chaque heure sous la forme d'une propriété distincte d'une entité unique : pour mettre à jour chaque heure, votre application peut utiliser un seul appel **InsertOrMerge Upsert** pour mettre à jour la valeur de la dernière heure. Pour représenter les données sur un graphique, l’application doit récupérer une seule entité au lieu de 24, générant ainsi une requête très efficace (reportez-vous à la section traitant de l’ [étendue de requête](#subheading30)ci-dessus).

##### <a name="subheading38"></a>Stockage de données structurées dans des objets blob
Les données structurées donnent parfois l’impression qu’elles devraient être placées dans les tables. Cependant, les plages d’entités sont toujours récupérées ensemble et peuvent être insérées par lots.  À cet égard, un fichier journal constitue un parfait exemple.  Dans ce cas, vous pouvez regrouper plusieurs minutes de journalisation et les insérer. Vous récupérez alors plusieurs minutes de journalisation à la fois.  Dans une optique de performances, il est préférable d’utiliser des objets blob plutôt que des tables, dans la mesure où vous pouvez réduire de manière significative le nombre d’objets écrits/renvoyés, ainsi que, généralement, le nombre de demandes qui doivent être effectuées.  

## <a name="queues"></a>Files d’attente
Outre les pratiques éprouvées pour [Tous les services](#allservices) décrites précédemment, les pratiques ci-dessous s'appliquent spécifiquement au service de file d’attente.  

### <a name="subheading39"></a>Limites d’extensibilité
Une seule file d’attente peut traiter environ 2 000 messages (1 Ko chacun) par seconde (chaque AddMessage, GetMessage et DeleteMessage compte pour un message). Si cela est insuffisant pour votre application, utilisez plusieurs files d’attente et répartissez les messages entre elles.  

consultez les objectifs d’extensibilité actuels dans [Objectifs de performance et d’extensibilité d’Azure Storage](storage-scalability-targets.md).  

### <a name="subheading40"></a>Désactivation de Nagle
Voir la section relative à la configuration de table qui traite de l’algorithme Nagle ; en règle générale, cet algorithme dégrade les performances des demandes de file d’attente et, de ce fait, vous devez le désactiver.  

### <a name="subheading41"></a>Taille des messages
Les performances et l’extensibilité des files d’attente diminuent quand la taille de message augmente. Dans un message, placez uniquement les informations dont le destinataire a besoin.  

### <a name="subheading42"></a>Récupération par lots
Vous pouvez récupérer jusqu’à 32 messages d’une file d’attente en une seule opération. Cela contribue à réduire le nombre d’allers-retours avec l’application cliente, ce qui s’avère particulièrement utile pour les environnements où les temps de latence sont élevés, tels que les appareils mobiles.  

### <a name="subheading43"></a>Intervalle d'interrogation de file d'attente
La plupart des applications interrogent une file d’attente pour les messages, ce qui peut représenter l’une des plus grandes sources de transactions pour cette application. Sélectionnez votre intervalle d’interrogation avec soin : une interrogation trop fréquente pourrait entraîner un rapprochement des objectifs d’extensibilité pour la file d’attente. Toutefois, à 200 000 transactions à 0,01 $ (au moment de la rédaction), un seul processeur interrogeant une fois par seconde pendant un mois reviendrait à moins de 15 cents. Le prix n’est donc généralement pas un facteur qui affecte le choix de l’intervalle d’interrogation.  

Pour plus d’informations relatives au coût, consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Vous pouvez utiliser l'opération **UpdateMessage** pour augmenter le délai d'expiration de l'invisibilité ou pour mettre à jour les informations d'état d'un message. Bien que l'opération **UpdateMessage** soit particulièrement puissante, n'oubliez pas que chacune d'elles est comptabilisée dans le cadre de l'objectif d'évolutivité. Cependant, cela peut constituer une méthode beaucoup plus efficace qu’un flux de travail qui transmet une tâche d’une file d’attente à la suivante, une fois chaque étape terminée. L’utilisation de l’opération **UpdateMessage** permet à votre application d’enregistrer l’état de la tâche dans le message, puis de poursuivre le traitement, au lieu de replacer à chaque fois le message en file d’attente pour l’étape suivante.  

Pour plus d’informations, voir l’article [Modification du contenu d’un message en file d’attente](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Architecture de l’application
Il est conseillé d’utiliser des files d’attente pour rendre l’architecture de votre application extensible. Vous trouverez, dans les listes suivantes, les méthodes applicables pour accroître l’extensibilité de votre application :  

* Vous pouvez utiliser des files d’attente pour créer des journaux de travaux en souffrance à traiter et éliminer des charges de travail de votre application. Vous pouvez, par exemple, placer en file d’attente des demandes d’utilisateurs portant sur l’exécution de tâches exigeant d’importantes ressources processeur, telles que le redimensionnement d’images téléchargées.
* Vous pouvez utiliser des files d’attente pour dissocier des parties de votre application, de manière à pouvoir les faire évoluer séparément. Un serveur web frontal peut, par exemple, placer les résultats d’une enquête en file d’attente en vue de les stocker et de les analyser ultérieurement. Vous pouvez ajouter des instances du rôle de travail afin de traiter les données de file d’attente suivant les besoins.  

## <a name="conclusion"></a>Conclusion
Dans cet article, nous avons passé en revue quelques-unes des pratiques utilisées le plus couramment pour optimiser les performances lors de l’utilisation d’Azure Storage. Nous invitons tous les développeurs d’applications à évaluer chacune d’elles et à prendre en compte les recommandations énoncées afin de bénéficier de performances optimales pour les applications qui utilisent Azure Storage.