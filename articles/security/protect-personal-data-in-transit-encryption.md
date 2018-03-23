---
title: Protéger les données personnelles en transit avec un chiffrement dans Azure | Microsoft Docs
description: des informations sur le chiffrement Azure pour protéger des données personnelles peuvent être utiles afin d’être en conformité avec le Règlement général de protection des données (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 6975358d40206a497a53de16731d16ef374db905
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>technologies de chiffrement Azure : Protéger les données personnelles en transit avec un chiffrement

Cet article vous aide à comprendre et à utiliser des technologies de chiffrement Azure pour sécuriser les données en transit. La protection de la confidentialité des données personnelles pendant qu’elles transitent sur le réseau constitue un élément essentiel de la stratégie de sécurité de défense en profondeur multicouche. Le chiffrement en transit est conçu pour empêcher tout attaquant qui intercepte des transmissions de pouvoir en afficher ou utiliser les données. Les informations contenues dans cet article peuvent contribuer aux efforts de conformité avec le Règlement général sur la protection des données (RGPD) d’une organisation.

## <a name="scenario"></a>Scénario

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée, Adriatique et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni. 

La compagnie utilise Microsoft Azure pour stocker les données d’entreprise dans le cloud. Ces dernières incluent des informations d’identification personnelle telles que les noms, les adresses, les numéros de téléphone et les informations de carte de crédit de sa base de clients globale. Elles incluent également des informations de ressources humaines traditionnelles telles que les adresses, les numéros de téléphone, les numéros d’identification fiscale, etc. des employés de toutes les agences de la compagnie. La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense qui inclut des informations personnelles pour effectuer le suivi des relations avec les clients actuels et anciens.

Les données personnelles des clients sont entrées dans la base de données à partir des agences distantes de la compagnie et des agents de voyage répartis dans le monde entier. Les documents contenant des informations sur les clients sont transférés via le réseau vers le stockage Azure.

## <a name="problem-statement"></a>Définition du problème

La compagnie doit protéger la confidentialité des données personnelles de ses clients et employés quand elles transitent vers et depuis les services Azure.

## <a name="company-goal"></a>Objectif de l’entreprise

L’objectif de la compagnie consiste à garantir que les données personnelles sont chiffrées quand elles sont hors disque. Si des personnes non autorisées accèdent aux données personnelles hors disque, celles-ci doivent être sous forme illisible. L’application du chiffrement doit être simple, ou entièrement transparent, pour les utilisateurs et les administrateurs.

## <a name="solutions"></a>solutions

Les services Azure proposent plusieurs outils et technologies pour vous aider à protéger les données personnelles en transit.

### <a name="azure-storage"></a>Stockage Azure

Les données stockées dans le cloud doivent se déplacer à partir du client, qui peut être physiquement situé n’importe où dans le monde, vers le centre de données Azure. Quand ces données sont récupérées par des utilisateurs, elles se déplacent à nouveau, dans la direction opposée. Les données en transit sur l’Internet public courent toujours un risque d’interception par des attaquants. Il est important de protéger la confidentialité des données personnelles en utilisant un chiffrement au niveau du transport pour les sécuriser lors de leur déplacement entre deux emplacements.

Le protocole HTTPS fournit un canal de communication sécurisé et chiffré sur Internet. HTTPS doit être utilisé pour accéder aux objets dans le stockage Azure et lors de l’appel d’API REST. Vous mettez en place l’utilisation du protocole HTTPS quand vous utilisez des [signatures d’accès partagé](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAP) pour déléguer l’accès aux objets du stockage Azure. Il existe deux types de SAP : SAP de service et SAP de compte.

#### <a name="how-do-i-construct-a-service-sas"></a>Comment construire une SAP de service ?

Une SAP de service délègue l’accès à une ressource d’un seul des services de stockage (service BLOB, de File d’attente, de Table ou de fichiers). Pour construire une SAP de service, effectuez les opérations suivantes :

1. Spécifier le champ de version signée

2. Spécifier la ressource signée (service BLOB et service de fichiers uniquement)

3. Spécifier les paramètres de requête pour remplacer les en-têtes de réponse (service BLOB et service de fichiers uniquement)

4. Spécifiez le nom de table (service de Table uniquement)

5. Spécifier la stratégie d’accès

6. Spécifier l’intervalle de validité de signature

8. Spécifier des autorisations

9. Spécifier l’adresse IP ou la plage d’adresses IP

10. Spécifier le protocole HTTP

11. Spécifier les plages d’accès de Table

12. Spécifier l’identificateur signé

13. Spécifier la signature

Pour plus d’instructions, consultez [Construction d’une SAP de service](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Comment construire une SAP de compte ?

Une SAP de compte délègue l’accès aux ressources dans un ou plusieurs des services de stockage. Vous pouvez également déléguer l'accès aux opérations de lecture, d’écriture et de suppression sur les conteneurs d'objets blob, les tables, les files d'attente et les partages de fichiers qui ne sont pas autorisées avec une SAP de service. La construction d’une SAP de compte est similaire à celle d’une SAP de service. Pour obtenir des instructions détaillées, consultez [Construction d’une SAP de compte.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Comment mettre en place le protocole HTTPS lors de l’appel d’API REST ?

Pour mettre en place l’utilisation du protocole HTTPS lorsque vous appelez des API REST pour accéder à des objets dans les comptes de stockage, vous pouvez activer l’option Transfert sécurisé requis pour le compte de stockage. 

1. Dans le portail Azure, sélectionnez **Créer un compte de stockage**, ou pour un compte de stockage existant, sélectionnez **Paramètres**, puis **Configuration**.

2. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

![Création d’un compte de stockage](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Pour obtenir des instructions plus précises, notamment sur la manière d’activer l’option Transfert sécurisé requis par programme, consultez [Exiger un transfert sécurisé](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Comment chiffrer des données dans Stockage Fichier Azure ?

Pour chiffrer des données en transit avec [Stockage Fichier Azure](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), vous pouvez utiliser SMB 3.x avec Windows 8, 8.1 et 10, Windows Server 2012 R2 et Windows Server 2016. Lorsque vous utilisez le service Azure Files, toute connexion sans chiffrement échoue si l’option Transfert sécurisé requis est activée. Cela inclut des scénarios utilisant SMB 2.1, SMB 3.0 sans chiffrement, et certaines versions du client SMB Linux.

#### <a name="azure-client-side-encryption"></a>Chiffrement côté client Azure

Une autre possibilité pour protéger des données personnelles pendant leur transfert entre une application cliente et Stockage Azure consiste à utiliser un [chiffrement côté client](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Les données sont chiffrées avant d’être transférées dans Stockage Azure, et quand vous récupérez les données depuis Stockage Azure, elles sont déchiffrées après leur réception côté client.

### <a name="azure-site-to-site-vpn"></a>VPN de site à site Azure

Un moyen efficace de protéger les données personnelles en transit entre un réseau d’entreprise ou un utilisateur et le réseau virtuel Azure consiste à utiliser un réseau privé virtuel (VPN) [de site à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou [de point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Une connexion VPN crée un tunnel chiffré sécurisé sur Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Comment créer une connexion VPN de site à site ?

Une connexion VPN de site à site connecte plusieurs utilisateurs sur le réseau d’entreprise à Azure. Pour créer une connexion de site à site dans le portail Azure, procédez comme suit :

1. Créez un réseau virtuel.

2. Spécifiez un serveur DNS.

3. Créez le sous-réseau de passerelle.

4. Créez la passerelle VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Créez la passerelle réseau locale.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Configurez votre appareil VPN.

7. Créez la connexion VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Vérifiez la connexion VPN.

Pour obtenir des instructions plus précises sur la création d’une connexion de site à site dans le portail Azure, consultez [Créer une connexion de site à site dans le portail Azure.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Comment créer une connexion VPN de point à site ?

Un VPN de point à site crée une connexion sécurisée entre un ordinateur client individuel et un réseau virtuel. Cette connexion s’avère utile quand vous voulez vous connecter à Azure depuis un emplacement distant, par exemple depuis votre domicile, un hôtel ou un centre de conférence. Pour créer une connexion de point à site dans le portail Azure :

1. Créez un réseau virtuel.

2. Ajoutez un sous-réseau de passerelle.

3. Spécifiez un serveur DNS. (facultatif)

4. Créez une passerelle de réseau virtuel.

5. Générez des certificats.

6. Ajoutez le pool d’adresses des clients.

7. Chargez les données de certificat public du certificat racine.

8. Générez et installez le package de configuration du client VPN.

9. Installez un certificat client exporté.

10. Connexion à Azure.

11. Vérifiez votre connexion.

Pour obtenir des instructions plus précises, consultez [Configurer une connexion de point à site à un réseau virtuel à l’aide d’une authentification par certificat : portail Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

Microsoft vous recommande de toujours utiliser les protocoles SSL/TLS pour échanger des données entre les différents emplacements. Les organisations qui choisissent d’utiliser [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) pour déplacer de grands jeux de données via une liaison réseau étendu à haut débit dédiée peuvent également chiffrer les données au niveau de l’application à l’aide des protocoles SSL/TLS ou d’autres protocoles pour une protection accrue.

### <a name="encryption-by-default"></a>Chiffrement par défaut

Microsoft utilise un chiffrement pour protéger les données en transit entre les clients et services cloud Azure. Si vous interagissez avec Azure Storage via le portail Azure, toutes les transactions se produisent via HTTPS.

Le [protocole TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) est celui que les centres de données Microsoft tentent de négocier avec les systèmes clients qui se connectent aux services cloud de Microsoft. TLS fournit une authentification forte, la confidentialité et l’intégrité des messages (activation de la détection de falsification, l’interception et la falsification des messages), l’interopérabilité, la flexibilité, la facilité de déploiement et l’utilisation des algorithmes.

[PFS (Perfect Forward Secrecy)](https://en.wikipedia.org/wiki/Forward_secrecy) est aussi utilisé pour que chaque connexion entre les systèmes clients des clients et les services cloud de Microsoft utilisent des clés uniques. Les connexions aux services cloud de Microsoft exploitent également les longueurs de clé de chiffrement de type RSA 2 048 bits. La combinaison des longueurs de clé RSA 2 048 bits, TLS et PFS rend beaucoup plus difficile l’interception et l’accès aux données en transit entre les clients et les services cloud de Microsoft.

Les données en transit sont toujours chiffrées dans [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Outre le chiffrement des données avant le stockage sur un support permanent, les données sont également toujours sécurisées en transit à l’aide du protocole HTTPS. HTTPS est le seul protocole pris en charge pour les interfaces REST Data Lake Store.

## <a name="summary"></a>Résumé

La compagnie peut atteindre son objectif de protection des données personnelles et la confidentialité de ces données en mettant en place des connexions HTTPS à Stockage Azure, en utilisant des signatures d’accès partagé et en activant l’option Transfert sécurisé requis sur les comptes de stockage. Elle peut aussi protéger les données personnelles en utilisant des connexions SMB 3.0 et en mettant en place un chiffrement côté client. Les connexions VPN de site à site depuis le réseau d’entreprise vers le réseau virtuel Azure et les connexions VPN de point à site depuis des utilisateurs individuels créent un tunnel sécurisé par lequel les données personnelles peuvent voyager en toute sécurité. Les pratiques de chiffrement par défaut de Microsoft renforce encore la protection de la confidentialité des données personnelles.

## <a name="next-steps"></a>Étapes suivantes

- [Bonnes pratiques Azure en matière de chiffrement et de sécurité des données](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Planification et conception de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [FAQ sur la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Acheter et configurer un certificat SSL pour votre service Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
