---
title: "Chiffrement des données au repos de Microsoft Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du chiffrement des données au repos de Microsoft Azure, avec ses fonctionnalités globales et des considérations générales."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: b02afa77ce99f576fed76b398642ba3f3ce2ba98
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-encryption-at-rest"></a>Chiffrement des données au repos d’Azure
Il existe plusieurs outils dans Microsoft Azure pour protéger les données en fonction des besoins de sécurité et de conformité de votre entreprise. Ce document porte sur les points suivants :
- Protection des données au repos sur Microsoft Azure
- Présentation des différents composants impliqués dans la mise en œuvre de la protection des données
- Examen des avantages et des inconvénients des principales approches de protection de la gestion 

Le chiffrement au repos est une exigence de sécurité courante. Un avantage de Microsoft Azure est que les organisations peuvent réaliser le chiffrement au repos sans devoir supporter les coûts d’implémentation et de gestion, ni le risque d’une solution de gestion des clés personnalisée. Les organisations ont la possibilité de laisser Azure gérer complètement le chiffrement au repos. En outre, les organisations ont différentes options pour gérer étroitement le chiffrement ou les clés de chiffrement.

## <a name="what-is-encryption-at-rest"></a>Qu’est-ce que le chiffrement au repos ?
Le chiffrement au repos fait référence à l’encodage chiffré (chiffrement) des données quand elles sont stockées. La conception du chiffrement au repos dans Azure utilise le chiffrement symétrique pour chiffrer et déchiffrer rapidement de grandes quantités de données selon un modèle conceptuel simple :

- Une clé de chiffrement symétrique est utilisée pour chiffrer les données au fil de leur stockage 
- La clé de chiffrement est utilisée pour déchiffrer ces données au fil de leur préparation à une utilisation en mémoire
- Les données peuvent être partitionnées, et des clés différentes peuvent être utilisées pour chaque partition
- Les clés doivent être stockées à un emplacement sécurisé, avec des stratégies de contrôle d’accès limitant l’accès à certaines identités et journalisant l’utilisation des clés. Les clés de chiffrement de données sont souvent chiffrées avec un chiffrement asymétrique pour limiter davantage l’accès (ceci est présenté dans *Hiérarchie des clés* plus loin dans cet article)

Les éléments courants principaux du chiffrement au repos sont décrits ci-dessus. Dans la pratique, les scénarios de gestion et de contrôle des clés, ainsi que les garanties de scalabilité et de disponibilité, nécessitent des mécanismes supplémentaires. Les concepts et les composants du chiffrement des données au repos de Microsoft Azure sont décrits ci-dessous.

## <a name="the-purpose-of-encryption-at-rest"></a>Objectif du chiffrement au repos
Le chiffrement au repos est destiné à fournir une protection des données pour les données au repos (comme décrit ci-dessus). Les attaques contre les données au repos sont notamment des tentatives d’obtenir un accès physique au matériel sur lequel les données sont stockées puis de compromettre les données qui y sont contenues. Dans une telle attaque, le disque dur d’un serveur peut avoir fait l’objet d’une mauvaise manipulation lors de la maintenance, permettant à un attaquant de retirer le disque dur. Plus tard, cet attaquant peut placer le disque dur dans un ordinateur qu’il contrôle pour tenter d’accéder aux données. 

Le chiffrement au repos est conçu pour empêcher l’attaquant d’accéder aux données non chiffrées en garantissant que les données sont chiffrées quand elles sont sur le disque. Si un attaquant devait obtenir un disque dur avec des données ainsi chiffrées et sans accès aux clés de chiffrement, il ne pourrait compromettre les données que très difficilement. Dans un tel scénario, un attaquant devrait tenter des attaques sur des données chiffrées, ce qui est bien plus complexe et gourmand en ressources qu’accéder à des données non chiffrées sur un disque dur. Pour cette raison, le chiffrement au repos est fortement recommandé et constitue une exigence de haute priorité pour de nombreuses organisations. 

Dans certains cas, le chiffrement au repos est nécessaire pour les besoins de l’organisation en matière de gouvernance et de conformité des données. Les réglementations publiques et de l’industrie, comme HIPAA, PCI et FedRAMP définissent des protections spécifiques quant aux exigences de protection et de chiffrement des données. Pour la plupart de ces réglementations, le chiffrement au repos est une mesure obligatoire nécessaire à la conformité de la protection et de la gestion des données. 

En plus des obligations réglementaires et de conformité, le chiffrement au repos doit être perçu comme une fonctionnalité de défense en profondeur des plateformes. Alors que Microsoft fournit une plateforme conforme pour les services, les applications et les données, une sécurité physique et des équipements complète, et le contrôle et l’audit des accès aux données , il est important de fournir des mesures de sécurité supplémentaires qui se recouvrent en cas d’échec de l’une des mesures de sécurité. Le chiffrement au repos fournit un mécanisme de défense supplémentaire de cet ordre.

Microsoft s’efforce d’offrir des options de chiffrement au repos dans les services cloud, et de fournir aux clients une facilité de gestion des clés de chiffrement et l’accès à des journaux montrant quand les clés de chiffrement sont utilisées. En outre, Microsoft travaille à cet objectif que toutes les données des clients soient par défaut chiffrées au repos.

## <a name="azure-encryption-at-rest-components"></a>Composants du chiffrement au repos d’Azure

Comme décrit précédemment, l’objectif du chiffrement au repos est que les données stockées sur disque soient chiffrées avec une clé de chiffrement secrète. Pour atteindre cet objectif de clés sécurisées, il est nécessaire de disposer d’un système permettant la création, le stockage, le contrôle d’accès et la gestion des clés de chiffrement. Bien que des détails puissent varier, les implémentations du chiffrement au repos des services Azure peuvent être décrites selon les termes des concepts ci-dessous, qui sont illustrés dans le diagramme suivant.

![Composants](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

L’emplacement de stockage des clés de chiffrement et le contrôle d’accès à ces clés sont fondamentaux pour un modèle de chiffrement au repos. Les clés doivent être hautement sécurisées, mais gérables par des utilisateurs spécifiés et disponibles pour des services spécifiques. Pour les services Azure, Azure Key Vault est la solution de stockage des clés recommandée et offre une expérience de gestion commune entre les services. Les clés sont stockées et gérées dans des coffres de clés, et l’accès à un coffre de clés peut être donné à des utilisateurs ou à des services. Azure Key Vault prend en charge la création de clés par les clients ou l’importation de clés des clients pour les utiliser dans des scénarios où les clés de chiffrement sont gérées par ces clients.

### <a name="azure-active-directory"></a>Azure Active Directory

Les autorisations d’utiliser les clés stockées dans Azure Key Vault, pour les gérer ou pour y accéder pour les chiffrer et les déchiffrer dans le cadre du chiffrement au repos, peuvent être données à des comptes Azure Active Directory. 

### <a name="key-hierarchy"></a>Hiérarchie des clés

Plusieurs clés de chiffrement sont utilisées dans une implémentation du chiffrement au repos. Un chiffrement asymétrique est utile pour établir la confiance, et une authentification est nécessaire pour l’accès et la gestion des clés. Un chiffrement symétrique est plus efficace pour le chiffrement et le déchiffrement en bloc, permettant un chiffrement plus fort et de meilleures performances. En outre, limiter l’utilisation d’une seule clé de chiffrement réduit le risque que la clé soit compromise et le coût du rechiffrement quand une clé doit être remplacée. Pour tirer parti des avantages du chiffrement symétrique et asymétrique, et pour limiter l’utilisation et l’exposition d’une seule clé, les modèles de chiffrement au repos d’Azure utilisent une hiérarchie de clés constituée des types de clés suivants :

- **Clé de chiffrement des données** : une clé symétrique AES256 utilisée pour chiffrer une partition ou un bloc de données.  Une même ressource peut avoir plusieurs partitions et de nombreuses clés de chiffrement des données. Le chiffrement de chaque bloc de données avec une clé différente rend les attaques d’analyse du chiffrement plus difficiles. L’accès aux clés de chiffrement des données est nécessaire au fournisseur de ressources ou à l’instance d’application qui chiffre et déchiffre un bloc spécifique. Quand une clé de chiffrement des données est remplacée par une nouvelle clé, seules les données du bloc qui y est associé doivent être rechiffrées avec la nouvelle clé.
- **Clé de chiffrement des clés** : une clé de chiffrement asymétrique utilisée pour chiffrer les clés de chiffrement des données. L’utilisation d’une clé de chiffrement des clés permet le chiffrement et le contrôle des clés de chiffrement des données elles-mêmes. L’entité qui a accès à la clé de chiffrement des clés peut être différente de l’entité qui a besoin de la clé de chiffrement des données. Ceci permet à une entité de négocier l’accès à la clé de chiffrement des données dans le but de garantir un accès limité de chaque clé de chiffrement des données à une partition spécifique. Comme la clé de chiffrement des clés est nécessaire pour déchiffrer les clés de chiffrement des données, la clé de chiffrement des clés est dès lors le point unique par lequel les clés de chiffrement des données peuvent être supprimées en supprimant la clé de chiffrement des clés.

Les clés de chiffrement des données chiffrées avec des clés de chiffrement des clés sont stockées séparément, et seule une entité ayant accès à la clé de chiffrement des clés peut obtenir les clés de chiffrement des données chiffrées avec cette clé. Différents modèles de stockage des clés sont pris en charge. Nous allons décrire chaque modèle plus en détail plus loin, dans la section suivante.

## <a name="data-encryption-models"></a>Modèles de chiffrement des données

Comprendre les différents modèles de chiffrement, ainsi que leurs avantages et inconvénients, est essentiel pour comprendre comment les différents fournisseurs de ressources dans Azure implémentent le chiffrement au repos. Ces définitions sont partagées par tous les fournisseurs de ressources dans Azure, ce qui garantit un langage et une taxonomie communs. 

Il existe trois scénarios de chiffrement côté serveur :

- Chiffrement côté serveur à l’aide de clés gérées par le service
    - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
    - Microsoft gère les clés
    - Fonctionnalité cloud complète

- Chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault
    - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
    - Le client contrôle les clés via Azure Key Vault
    - Fonctionnalité cloud complète

- Chiffrement côté serveur à l’aide de clés gérées par le client sur du matériel contrôlé par le client
    - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
    - Le client contrôle les clés sur du matériel contrôlé par le client
    - Fonctionnalité cloud complète

Pour le chiffrement côté client, considérez les éléments suivants :

- Les services Azure ne peuvent pas voir les données déchiffrées
- Les clients gèrent et stockent les clés localement (ou dans d’autres magasins sécurisés). Les clés ne sont pas disponibles pour les services Azure
- Fonctionnalité cloud réduite

Les modèles de chiffrement pris en charge dans Azure se divisent en deux groupes principaux : « Chiffrement client » et « Chiffrement côté serveur », comme indiqué précédemment. Notez que, indépendamment du modèle de chiffrement au repos utilisé, les services Azure recommandent toujours l’utilisation d’un transport sécurisé, comme TLS ou HTTPS. Ainsi, le chiffrement du transport doit être traité par le protocole de transport et il ne doit pas être un facteur majeur pour déterminer le modèle de chiffrement au repos à utiliser.

### <a name="client-encryption-model"></a>Modèle de chiffrement client

Le modèle de chiffrement client fait référence au chiffrement qui est effectué en dehors du fournisseur de ressources ou d’Azure par le service ou l’application appelante. Le chiffrement peut être effectué par l’application de service dans Azure, ou par une application s’exécutant dans le centre de données du client. Dans les deux cas, quand vous tirez parti de ce modèle de chiffrement, le fournisseur de ressources Azure reçoit un objet blob chiffré des données sans possibilité de déchiffrer les données en aucune façon ou d’avoir accès aux clés de chiffrement. Dans ce modèle, la gestion des clés est effectuée par le service ou l’application appelant et est opaque pour le service Azure.

![Client](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modèle de chiffrement côté serveur

Les modèles de chiffrement côté serveur se réfèrent au chiffrement qui est effectué par le service Azure. Dans ce modèle, le fournisseur de ressources effectue les opérations de chiffrement et de déchiffrement. Par exemple, le stockage Azure peut recevoir des données dans des opérations en texte brut, et effectue le chiffrement et le déchiffrement en interne. Le fournisseur de ressources peut utiliser des clés de chiffrement qui sont gérées par Microsoft ou par le client, selon la configuration fournie. 

![Serveur](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modèles de gestion des clés de chiffrement côté serveur

Chacun des modèles de chiffrement au repos côté serveur a des particularités distinctives dans la gestion des clés. Il s’agit notamment de l’endroit et de la façon dont les clés de chiffrement sont créées et stockées, ainsi que des modèles d’accès et des procédures de rotation des clés. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Chiffrement côté serveur à l’aide de clés gérées par le service

Pour de nombreux clients, l’exigence principale est de garantir que les données sont chiffrées quand elles sont au repos. Le chiffrement côté serveur à l’aide de clés gérées par le service autorise ce modèle en permettant aux clients de marquer la ressource spécifique (compte de stockage, base de données SQL, etc.) pour le chiffrement et de laisser tous les aspects de la gestion des clés, comme l’émission, la sauvegarde et la rotation des clés, à Microsoft. La plupart des services Azure prenant en charge le chiffrement au repos prennent généralement en charge ce modèle de déchargement de la gestion des clés de chiffrement sur Azure. Le fournisseur de ressources Azure crée les clés, les place dans un stockage sécurisé et les récupère quand c’est nécessaire. Cela signifie que le service a un accès complet aux clés et qu’il a un contrôle total sur la gestion du cycle de vie des informations d’identification.

![géré](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Le chiffrement côté serveur à l’aide de clés gérées le service répond ainsi rapidement au besoin d’avoir un chiffrement au repos avec une charge de travail peu importante au niveau du client. Quand il est disponible, un client ouvre en général le portail Azure pour l’abonnement cible et le fournisseur de ressources, et il coche une case indiquant qu’il veut que les données soient chiffrées. Dans certains gestionnaires de ressources, le chiffrement côté serveur avec des clés gérées par le service est activé par défaut. 

Le chiffrement côté serveur avec des clés gérées par Microsoft implique que le service ait un accès complet pour stocker et gérer les clés. Si certains clients peuvent souhaiter gérer les clés en pensant qu’ils peuvent assurer une meilleure sécurité, le coût et le risque associés à une solution de stockage de clés personnalisée doivent être considérés lors de l’évaluation de ce modèle. Dans de nombreux cas, une organisation peut déterminer que les contraintes sur les ressources ou les risques d’une solution locale peuvent être supérieurs au risque lié à une gestion dans le cloud des clés de chiffrement au repos.  Cependant, ce modèle peut ne pas suffire pour les organisations qui ont des exigences quant au contrôle de la création ou du cycle de vie des clés de chiffrement, ou qui veulent que les personnes gérant les clés de chiffrement d’un service soient différentes de celles qui gèrent le service (par exemple une séparation entre la gestion des clés et le modèle de gestion global pour le service).

##### <a name="key-access"></a>Accès aux clés

Quand le chiffrement côté serveur avec des clés gérées par le service est utilisé, la création, le stockage et l’accès du service aux clés sont tous gérés par le service. En règle générale, les principaux fournisseurs de ressources Azure stockent les clés de chiffrement des données dans un magasin qui est proche des données, et qui est disponible et accessible rapidement, alors que les clés de chiffrement des clés sont stockées dans un magasin interne sécurisé.

**Avantages**

- Simplicité de la configuration
- Microsoft gère la rotation, la sauvegarde et la redondance des clés
- Le client ne doit pas faire face au coût associé à l’implémentation ni au risque d’un schéma de gestion des clés personnalisé.

**Inconvénients**

- Pas de contrôle du client sur les clés de chiffrement (spécification, cycle de vie, révocation des clés, etc.)
- Pas de possibilité de séparer la gestion de clés du modèle de gestion global pour le service

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault 

Pour les scénarios où l’exigence est de chiffrer les données au repos et de contrôler les clés de chiffrement, les clients peuvent utiliser le chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault. Certains services peuvent stocker seulement la clé de chiffrement des clés racine dans Azure Key Vault et stocker la clé de chiffrement des données chiffrée à un emplacement interne plus proche des données. Dans ce scénario, les clients peuvent apporter leurs propres clés au coffre de clés (BYOK, Bring Your Own Key), ou en générer de nouvelles et les utiliser pour chiffrer les ressources souhaitées. Quand le fournisseur de ressources effectue les opérations de chiffrement et de déchiffrement, il utilise la clé configurée comme clé racine pour toutes les opérations de chiffrement. 

##### <a name="key-access"></a>Accès aux clés

Le modèle de chiffrement côté serveur avec des clés gérées par le client dans Azure Key Vault implique que le service accède aux clés pour chiffrer et déchiffrer quand c’est nécessaire. Les clés de chiffrement au repos sont rendues accessibles à un service via une stratégie de contrôle d’accès. Cette stratégie accorde à cette identité de service un accès pour recevoir la clé. Un service Azure s’exécutant pour le compte d’un abonnement associé peut être configuré avec une identité dans cet abonnement. Le service peut effectuer l’authentification Azure Active Directory et recevoir un jeton d’authentification en s’identifiant lui-même comme étant ce service agissant pour le compte de l’abonnement. Ce jeton peut ensuite être présenté au coffre de clés pour obtenir une clé à laquelle l’accès lui a été donné.

Pour les opérations utilisant des clés de chiffrement, une identité de service peut être autorisée à accéder aux opérations suivantes : déchiffrer, chiffrer, unwrapKey, wrapKey, vérifier, signer, obtenir, répertorier, mettre à jour, créer, importer, supprimer, sauvegarder et restaurer.

Pour obtenir une clé à utiliser dans le chiffrement ou le déchiffrement des données au repos, l’identité de service pour le compte duquel l’instance du service Resource Manager s’exécutera doit avoir les autorisations UnwrapKey (pour obtenir la clé de déchiffrement) et WrapKey (pour insérer une clé dans le coffre de clés lors de la création d’une nouvelle clé).


>[!NOTE] 
>Pour plus d’informations sur les autorisations de Key Vault, consultez la page consacrée à la sécurisation de votre coffre de clés dans la [documentation d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Avantages**

- Contrôle total sur les clés utilisées : les clés de chiffrement sont gérées dans le coffre de clés du client, sous le contrôle du client.
- Possibilité de chiffrer de plusieurs services en un seul service maître
- Possibilité de séparer la gestion de clés du modèle de gestion global pour le service
- Possibilité de définir l’emplacement du service et des clés dans des régions différentes

**Inconvénients**

- Le client a l’entière responsabilité de la gestion de l’accès aux clés
- Le client a l’entière responsabilité de la gestion du cycle de vie des clés
- Charge de travail supplémentaire pour l’installation et la configuration

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Chiffrement côté serveur à l’aide de clés gérées par le service sur du matériel contrôlé par le client

Pour les scénarios où l’exigence est de chiffrer les données au repos et de gérer les clés dans un référentiel propriétaire en dehors du contrôle de Microsoft, certains services Azure permettent d’utiliser le modèle de gestion des clés HYOK (Host Your Own Key). Dans ce modèle, le service doit récupérer la clé auprès d’un site externe, et les garanties de disponibilité et de performances sont donc affectées, et la configuration est plus complexe. En outre, comme le service n’a pas accès à la clé de chiffrement des données pendant les opérations de chiffrement et de déchiffrement, les garanties de sécurité globale de ce modèle sont similaires à celle du modèle où les clés sont gérées par le client dans Azure Key Vault.  Par conséquent, ce modèle n’est pas approprié pour la plupart des organisations, sauf si elles ont des exigences spécifiques de gestion des clés qui le nécessitent. En raison de ces limitations, la plupart des services Azure ne gèrent pas le chiffrement côté serveur à l’aide de clés gérées par le serveur dans le matériel contrôlé par le client.

##### <a name="key-access"></a>Accès aux clés

Quand le chiffrement côté serveur à l’aide de clés gérées par le service dans du matériel contrôlé par le client est utilisé, les clés sont conservées sur un système configuré par le client. Les services Azure qui prennent en charge ce modèle fournissent un moyen d’établir une connexion sécurisée avec un magasin de clés fourni par le client.

**Avantages**

- Contrôle total sur la clé racine utilisée : les clés de chiffrement sont gérées par un magasin fourni par le client
- Possibilité de chiffrer de plusieurs services en un seul service maître
- Possibilité de séparer la gestion de clés du modèle de gestion global pour le service
- Possibilité de définir l’emplacement du service et des clés dans des régions différentes

**Inconvénients**

- Entière responsabilité pour le stockage, la sécurité, les performances et la disponibilité des clés
- Entière responsabilité de la gestion de l’accès aux clés
- Entière responsabilité de la gestion du cycle de vie des clés
- Coûts considérables d’installation, de configuration et de maintenance
- Dépendance accrue de la disponibilité du réseau entre le centre de données du client et les centres de données Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Chiffrement au repos dans les services cloud Microsoft

Les services cloud Microsoft sont utilisés dans chacun des trois modèles cloud : IaaS, PaaS, SaaS. Voici des exemples de la façon dont ils s’adaptent sur chaque modèle :

- Services logiciels, appelés SaaS ou Software as a Server, qui ont des applications fournies par le cloud, comme Office 365.
- Services de plateforme, dont les clients tirent parti sur le cloud dans leurs applications, en utilisant le cloud pour des fonctionnalités comme le stockage, l’analytique et Service Bus.
- Services d’infrastructure, appelés IaaS ou Infrastructure as a Service, dans lesquels le client déploie des systèmes d’exploitation et des applications qui sont hébergés dans le cloud, et tirant éventuellement parti d’autres services cloud.

### <a name="encryption-at-rest-for-saas-customers"></a>Chiffrement au repos pour les clients SaaS

Les clients SaaS (Software as a Service) ont généralement le chiffrement au repos activé ou disponible dans chaque service. Office 365 propose plusieurs options permettant aux clients de vérifier ou d’activer le chiffrement au repos. Pour plus d’informations sur les services Office 365, consultez Technologies de chiffrement des données pour Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Chiffrement au repos pour les clients PaaS

Les données des clients PaaS (Platform as a Service) se trouvent généralement dans un environnement d’exécution d’applications et un des fournisseurs de ressources Azure est utilisé pour stocker les données du client. Pour voir les options de chiffrement au repos disponibles, consultez le tableau ci-dessous pour les plateformes de stockage et d’applications que vous utilisez. Quand elles sont prises en charge, des liens vers des instructions sur l’activation du chiffrement au repos sont présents pour chaque fournisseur de ressources. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Chiffrement au repos pour les clients IaaS

Les clients IaaS (Infrastructure as a Service) peuvent utiliser différents services et applications. Les services IaaS peuvent activer le chiffrement au repos dans leurs machines virtuelles et leurs disques durs virtuels hébergés dans Azure en utilisant Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Stockage chiffré

Comme pour PaaS, les solutions IaaS peuvent tirer parti d’autres services Azure qui stockent les données chiffrées au repos. Dans ce cas, vous pouvez activer la prise en charge du chiffrement au repos telle qu’elle est fournie par chaque service Azure utilisé. Le tableau ci-dessous énumère les principales plateformes de stockage, de services et d’applications, avec le modèle de chiffrement au repos pris en charge. Quand il est pris en charge, des liens vers des instructions sur l’activation du chiffrement au repos sont présents. 

#### <a name="encrypted-compute"></a>Calcul chiffré

Une solution complète de chiffrement au repos nécessite que les données ne soient jamais stockées sous une forme non chiffrée. Lors de leur utilisation, sur un serveur qui charge les données en mémoire, ces données peuvent être stockées localement de différentes façons, notamment dans le fichier d’échange Windows, dans un vidage sur incident et dans la journalisation que l’application peut effectuer. Pour garantir que ces données sont chiffrées au repos, les applications IaaS peuvent utiliser Azure Disk Encryption sur une machine virtuelle (Windows ou Linux) et sur un disque virtuel IaaS Azure. 

#### <a name="custom-encryption-at-rest"></a>Chiffrement au repos personnalisé

Il est recommandé que, chaque fois que c’est possible, les applications IaaS tirent parti des options d’Azure Disk Encryption et du chiffrement au repos fournies par les services Azure utilisés. Dans certains cas, comme en présence d’exigences de chiffrement irrégulières ou d’un stockage qui n’est pas basé sur Azure, le développeur d’une application IaaS peut être amené à implémenter lui-même le chiffrement au repos. Les développeurs de solutions IaaS peuvent mieux s’intégrer à la gestion d’Azure et répondre aux attentes des clients en tirant parti de certains composants Azure. En particulier, les développeurs doivent utiliser le service Azure Key Vault pour fournir un stockage des clés sécurisé, ainsi qu’offrir à leurs clients des options de gestion des clés cohérentes avec celles de la plupart des services de plateforme Azure. En outre, les solutions personnalisées doivent utiliser des identités de service gérées par Azure pour permettre aux comptes de service d’accéder aux clés de chiffrement. Pour plus d’informations sur Azure Key Vault et les identités de service gérées, les développeurs peuvent consulter leurs SDK respectifs.

## <a name="azure-resource-providers-encryption-model-support"></a>Prise en charge du modèle de chiffrement des fournisseurs de ressources Azure

Les services Microsoft Azure prennent chacun en charge un ou plusieurs modèles de chiffrement au repos. Cependant, pour certains services, un ou plusieurs des modèles de chiffrement peuvent ne pas être applicables. En outre, les services peuvent prendre en charge ces scénarios selon des planifications différentes. Cette section décrit la prise en charge du chiffrement au repos au moment de la rédaction de ce document pour chacun des principaux services de stockage de données Azure.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Tout client utilisant les fonctionnalités IaaS d’Azure peut effectuer le chiffrement au repos pour ses machines virtuelles et ses disques IaaS via Azure Disk Encryption. Pour plus d’informations sur Azure Disk Encryption, consultez la [documentation d’Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Stockage Azure

Les services Azure Stockage Blob et Stockage Fichier prennent en charge le chiffrement au repos pour les scénarios avec chiffrement côté serveur, ainsi que les données chiffrées par le client (chiffrement côté client).

- Côté serveur : les clients utilisant Azure Stockage Blob peuvent activer le chiffrement au repos sur chaque compte de ressource de stockage Azure. Une fois activé, le chiffrement côté serveur s’effectue de façon transparente pour l’application. Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/storage-service-encryption).
- Côté client : le chiffrement côté client des objets blob Azure est pris en charge. Lors de l’utilisation du chiffrement côté client, les clients chiffrent les données et les chargent sous la forme d’un objet blob chiffré. La gestion des clés est effectuée par le client. Pour plus d’informations, consultez [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="sql-azure"></a>SQL Azure

Azure SQL Database prend actuellement en charge le chiffrement au repos pour les scénarios de chiffrement côté service géré par Microsoft et côté client.

La prise en charge du chiffrement côté serveur est actuellement fournie par la fonctionnalité SQL nommée Transparent Data Encryption. Une fois qu’un client Azure SQL Database active Transparent Data Encryption, les clés sont créées et gérées automatiquement pour lui. Le chiffrement au repos peut être activé au niveau de la base de données et au niveau du serveur. À compter de juin 2017, [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) est activé par défaut sur les bases de données nouvellement créées.

Le chiffrement côté client des données d’Azure SQL Database est pris en charge via la fonctionnalité [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted utilise une clé qui est créée et stockée par le client. Les clients peuvent stocker la clé principale dans un magasin de certificats Windows, dans Azure Key Vault ou dans un module de sécurité matériel local. Avec SQL Server Management Studio, les utilisateurs SQL choisissent quelle clé ils veulent utiliser pour quelle colonne.

|                                  |                |                     | **Modèle de chiffrement**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Client** |
|                                  | **Gestion des clés** | **Clé gérée par le service** | **Gérée par le client dans un coffre de clés** | **Gérée par le client localement** |        |
| **Stockage et bases de données**            |                |                     |                              |                              |        |
| Disque (IaaS)                      |                | -                   | Oui                          | Oui*                         | -      |
| SQL Server (IaaS)                |                | Oui                 | Oui                          | Oui                          | Oui    |
| Azure SQL Database (PaaS)                 |                | Oui                 | VERSION PRÉLIMINAIRE                      | -                            | Oui    |
| Stockage Azure (Objets blob de blocs/pages) |                | Oui                 | VERSION PRÉLIMINAIRE                      | -                            | Oui    |
| Stockage Azure (Fichiers)            |                | Oui                 | -                            | -                            | -      |
| Stockage Azure (Tables, Files d’attente)   |                | -                   | -                            | -                            | Oui    |
| Cosmos DB (Document DB)          |                | Oui                 | -                            | -                            | -      |
| StorSimple                       |                | Oui                 | -                            | -                            | Oui    |
| Sauvegarde                           |                | -                   | -                            | -                            | Oui    |
| **Décisionnel &amp; Analytique**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Oui                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | VERSION PRÉLIMINAIRE                      | -                            | -      |
| Azure Stream Analytics           |                | Oui                 | -                            | -                            | -      |
| HDInsights (Stockage Blob Azure)  |                | Oui                 | -                            | -                            | -      |
| HDInsights (Stockage Data Lake)   |                | Oui                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Oui                 | Oui                          | -                            | -      |
| Azure Data Catalog               |                | Oui                 | -                            | -                            | -      |
| Power BI                         |                | Oui                 | -                            | -                            | -      |
| **Services IoT**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Oui    |
| Service Bus                      |                | Oui (niveau Premium)              | -                            | -                            | Oui    |
| Event Hubs                       |                | Oui             | -                            | -                            | -      |


## <a name="conclusion"></a>Conclusion

La protection des données des clients stockées au sein des services Azure est d’une importance capitale pour Microsoft. Tous les services hébergés par Azure doivent à terme fournir des options de chiffrement au repos. Les services fondamentaux, comme Stockage Azure, Azure SQL Database, et les services clés de décisionnel et d’analytique, offrent déjà des options de chiffrement au repos. Certains de ces services prennent en charge les clés contrôlées par le client ou le chiffrement côté client, ainsi que les clés et le chiffrement gérés par le service. Les services Microsoft Azure étendent considérablement la disponibilité du chiffrement au repos, et la disponibilité en préversion puis générale de nouvelles options est planifiée dans les mois à venir.

