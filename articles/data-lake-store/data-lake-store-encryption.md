---
title: Chiffrement dans Azure Data Lake Store | Microsoft Docs
description: Le chiffrement dans Azure Data Lake Store vous permet de protéger vos données, de mettre en œuvre des stratégies de sécurité d’entreprise et de répondre aux exigences de conformité réglementaire. Cet article fournit une vue d’ensemble de la conception et présente certains aspects techniques de la mise en œuvre.
services: data-lake-store
documentationcenter: ''
author: esung22
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 53d5f413f58cea7bc8eab081d46eff2ab83e7ecb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Chiffrement des données dans Azure Data Lake Store

Le chiffrement dans Azure Data Lake Store vous permet de protéger vos données, de mettre en œuvre des stratégies de sécurité d’entreprise et de répondre aux exigences de conformité réglementaire. Cet article fournit une vue d’ensemble de la conception et présente certains aspects techniques de la mise en œuvre.

Data Lake Store prend en charge le chiffrement des données au repos et en transit. Pour les données au repos, Data Lake Store s’appuie sur un chiffrement transparent « activé par défaut ». Voici, plus en détails, ce que ces termes signifient :

* **Activé par défaut** : lorsque vous créez un nouveau compte Data Lake Store, le paramètre par défaut active le chiffrement. Ainsi, les données stockées dans Data Lake Store sont toujours chiffrées avant d’être stockées sur un support permanent. Ce comportement s’applique à toutes les données et ne peut pas être modifié après la création d’un compte.
* **Transparent** : Data Lake Store chiffre automatiquement les données avant le stockage permanent et déchiffre les données avant la récupération. Le chiffrement est configuré et géré au niveau Data Lake Store par un administrateur. Aucune modification n’est apportée aux API d’accès aux données. Aucune modification n’est donc requise dans les applications et services qui interagissent avec Data Lake Store en raison du chiffrement.

Les données en transit (ou données en mouvement) sont également toujours chiffrées dans Data Lake Store. Outre le chiffrement des données avant le stockage sur un support permanent, les données sont également toujours sécurisées en transit à l’aide du protocole HTTPS. HTTPS est le seul protocole pris en charge pour les interfaces REST Data Lake Store. Le diagramme suivant illustre le mode de chiffrement des données dans Data Lake Store :

![Diagramme du chiffrement des données dans Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Configuration du chiffrement avec Data Lake Store

Le chiffrement pour Data Lake Store est configuré lors de la création de compte et est toujours activé par défaut. Vous pouvez gérer les clés vous-même ou autoriser Data Lake Store à les gérer pour vous (option par défaut).

Pour plus d’informations, consultez [Prise en main](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Fonctionnement du chiffrement dans Data Lake Store

Les informations suivantes traitent de la gestion des clés de chiffrement principales, et décrivent les trois types de clés que vous pouvez utiliser dans le chiffrement des données pour Data Lake Store.

### <a name="master-encryption-keys"></a>Clés de chiffrement principales

Data Lake Store propose deux modes de gestion des clés de chiffrement principales (MEK). À ce stade, supposons que la clé de chiffrement principale est la clé de niveau supérieur. Un accès à la clé de chiffrement principale est requis pour déchiffrer les données stockées dans Data Lake Store.

Les deux modes de gestion de la clé de chiffrement principale sont les suivants :

*   Clés gérées par le service
*   Clés gérées par le client

Dans ces deux modes, la clé de chiffrement principale est sécurisée en la stockant dans Azure Key Vault. Key Vault est un service hautement sécurisé entièrement géré sur Azure qui peut être utilisé pour protéger les clés cryptographiques. Pour plus d’informations, consultez [Key Vault](https://azure.microsoft.com/services/key-vault).

Voici une brève comparaison des fonctionnalités fournies par les deux modes de gestion des MEK.

|  | Clés gérées par le service | Clés gérées par le client |
| --- | --- | --- |
|Comment les données sont stockées ?|Toujours chiffrées avant d’être stockées.|Toujours chiffrées avant d’être stockées.|
|Où est stockée la clé de chiffrement principale ?|Key Vault|Key Vault|
|Des clés de chiffrement sont-elles stockées en dehors de Key Vault ? |Non |Non |
|La MEK peut-elle être récupérée dans Key Vault ?|Non. Une fois la MEK stockée dans Key Vault, elle peut uniquement être utilisée pour le chiffrement et le déchiffrement.|Non. Une fois la MEK stockée dans Key Vault, elle peut uniquement être utilisée pour le chiffrement et le déchiffrement.|
|Qui possède l’instance Key Vault et la MEK ?|Service Data Lake Store|Vous possédez l’instance Key Vault, qui appartient à votre propre abonnement Azure. La MEK dans Key Vault peut être gérée par logiciel ou matériel.|
|Pouvez-vous révoquer l’accès à la MEK pour le service Data Lake Store ?|Non |Oui. Vous pouvez gérer des listes de contrôle d’accès sur Key Vault et supprimer des entrées de contrôle d’accès à l’identité de service pour le service Data Lake Store.|
|Pouvez-vous supprimer définitivement la MEK ?|Non |Oui. Si le client supprime la MEK de Key Vault, les données dans le compte Data Lake Store ne peuvent pas être déchiffrées par quiconque, y compris le service Data Lake Store. <br><br> Si vous avez explicitement sauvegardé la MEK avant de la supprimer de Key Vault, elle peut être restaurée et les données peuvent ensuite être récupérées. Toutefois, si vous n’avez pas sauvegardé la MEK avant de la supprimer de Key Vault, les données dans le compte Data Lake Store ne peuvent jamais être déchiffrées ultérieurement.|


Hormis la différence entre l’utilisateur qui gère la MEK et l’instance Key Vault dans lequel elle se trouve, les autres éléments de conception sont les mêmes pour les deux modes.

Il est important de se rappeler ce qui suit lors du choix du mode des clés de chiffrement principales :

*   Vous pouvez choisir d’utiliser des clés gérées par le client ou des clés gérées par le service au moment de la configuration d’un compte Data Lake Store.
*   Une fois qu’un compte Data Lake Store a été configuré, le mode ne peut pas être modifié.

### <a name="encryption-and-decryption-of-data"></a>Chiffrement et déchiffrement des données

Trois types de clés sont utilisées dans la conception du chiffrement des données. Le tableau suivant présente une synthèse :

| Clé                   | Abréviation | Associée à | Emplacement de stockage                             | type       | Notes                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Clé de chiffrement principale | MEK          | Compte Data Lake Store | Key Vault                              | Asymétrique | Peut être gérée par Data Lake Store ou par vous.                                                              |
| Clé de chiffrement des données   | DEK          | Compte Data Lake Store | Stockage permanent, géré par le service Data Lake Store | Symétrique  | La DEK est chiffrée par la MEK. La DEK chiffrée est stockée sur un support permanent. |
| Clé de chiffrement de bloc  | BEK          | Un bloc de données | Aucun                                         | Symétrique  | La BEK est dérivée de la DEK et du bloc de données.                                                      |

Le schéma suivant illustre ces concepts :

![Clés dans le chiffrement des données](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algorithme lorsqu’un fichier doit être déchiffré :
1.  Vérifiez si la DEK du compte Data Lake Store est mise en cache et prête à être utilisée.
    - Si ce n’est pas le cas, lisez alors la DEK chiffrée à partir d’un stockage permanent et envoyez-la vers Key Vault pour être déchiffrée. Mettez la DEK déchiffrée en mémoire cache. Elle est maintenant prête à utiliser.
2.  Pour chaque bloc de données du fichier :
    - Lire le bloc de données chiffré sur un stockage permanent.
    - Générer la BEK à partir de la DEK et du bloc de données chiffré.
    - Utiliser la BEK pour déchiffrer des données.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algorithme lorsqu’un bloc de données doit être chiffré :
1.  Vérifiez si la DEK du compte Data Lake Store est mise en cache et prête à être utilisée.
    - Si ce n’est pas le cas, lisez alors la DEK chiffrée à partir d’un stockage permanent et envoyez-la vers Key Vault pour être déchiffrée. Mettez la DEK déchiffrée en mémoire cache. Elle est maintenant prête à utiliser.
2.  Générez une BEK unique pour le bloc de données à partir de la DEK.
3.  Chiffrez le bloc de données avec la BEK à l’aide du chiffrement AES-256.
4.  Stockez le bloc de données chiffré sur un stockage permanent.

> [!NOTE] 
> La clé de chiffrement est toujours stockée chiffrée par la MEK, soit sur un média persistant ou via une mise en cache en mémoire.

## <a name="key-rotation"></a>Rotation des clés

Lorsque vous utilisez des clés gérées par le client, vous pouvez effectuer une rotation de la MEK. Pour savoir comment configurer un compte Data Lake Store avec des clés gérées par le client, consultez [Prise en main](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Prérequis


Lorsque vous avez configuré le compte Data Lake Store, vous avez choisi d’utiliser vos propres clés. Cette option ne peut pas être modifiée une fois le compte créé. Les étapes ci-dessous supposent que vous utilisez des clés gérées par le client (c’est-à-dire que vous avez choisi vos propres clés dans Key Vault).

Notez que si vous utilisez les options par défaut pour le chiffrement, vos données seront toujours chiffrées à l’aide des clés gérées par Data Lake Store. Dans cette option, vous ne pouvez pas effectuer une rotation des clés, car elles sont gérées par Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Comment effectuer une rotation de la MEK dans Data Lake Store

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Accédez à l’instance Key Vault dans laquelle sont stockées vos clés associées à votre compte Data Lake Store, puis sélectionnez des clés. Sélectionner **Clés**.

    ![Capture d’écran de Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Sélectionnez la clé associée à votre compte Data Lake Store et créez une nouvelle version de cette clé. Notez que pour le moment Data Lake Store prend uniquement en charge la rotation des clés vers une nouvelle version d’une clé. Il ne prend en charge la rotation vers une autre clé.

   ![Capture d’écran de la fenêtre Clés, avec Nouvelle version en surbrillance](./media/data-lake-store-encryption/keynewversion.png)

4.  Accédez au compte de stockage Data Lake Store et sélectionnez **Chiffrement**.

    ![Capture d’écran de la fenêtre du compte de stockage Data Lake Store, avec Chiffrement en surbrillance](./media/data-lake-store-encryption/select-encryption.png)

5.  Un message vous informe qu’une nouvelle version de clé de la clé est disponible. Cliquez sur **Rotation de clé** pour mettre à jour la clé vers la nouvelle version.

    ![Capture d’écran de la fenêtre Data Lake Store avec le message et Rotation de clé en surbrillance](./media/data-lake-store-encryption/rotatekey.png)

Cette opération doit prendre moins de deux minutes et aucune interruption de service due à la rotation des clés n’est attendue. Une fois l’opération terminée, la nouvelle version de la clé est utilisée.
