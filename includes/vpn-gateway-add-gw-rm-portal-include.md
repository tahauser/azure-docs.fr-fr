---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c5952422bb4faec196a5a54b9f18cf9700988aad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
1. Dans le portail, sur le côté gauche, cliquez sur **+**, puis tapez « passerelle de réseau virtuel » dans la recherche. Recherchez **passerelle de réseau virtuel** dans la zone de recherche et cliquez sur l’entrée. Sur la page de la **Passerelle de réseau virtuel**, cliquez sur **Créer** en bas de la page pour ouvrir la page **Créer une passerelle de réseau virtuel**.
2. Sur la page **Créer une passerelle de réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

  ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Champs de la page Créer une passerelle de réseau virtuel")
3. Dans la page **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

  - **Nom** : nommez votre passerelle. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
  - **Type de passerelle** : sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**. 
  - Dans **Type de réseau privé virtuel** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
  - **Référence** : sélectionnez la référence de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Emplacement** : vous devrez peut-être faire défiler pour afficher l’emplacement. Renseignez le champ **Emplacement** de manière à ce qu’il pointe vers l’emplacement où se trouve votre réseau virtuel. Si l’emplacement ne pointe pas vers la région où se trouve votre réseau virtuel, quand vous sélectionnerez un réseau virtuel à l’étape suivante, il n’apparaîtra pas dans la liste déroulante.
  - **Réseau virtuel** : choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur **Réseau virtuel** pour ouvrir la page Choisir un réseau virtuel. Sélectionnez le réseau virtuel. Si vous ne voyez pas votre réseau virtuel, assurez-vous que le champ Emplacement pointe sur la région dans laquelle se trouve votre réseau virtuel.
  - **Plage d’adresses de sous-réseau de la passerelle** : ce paramètre n’apparaît que si vous n’avez pas encore créé de sous-réseau de passerelle pour votre réseau virtuel. Si vous avez déjà créé un sous-réseau de passerelle valide, ce paramètre n’est pas affiché.
  - **Première configuration IP** : la page Choisir une adresse IP publique crée un objet d’adresse IP publique qui est associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

    - Tout d’abord, cliquez sur **Créer une configuration IP de passerelle** pour ouvrir la page Choisir une adresse IP publique, puis cliquez sur **+ Créer** pour ouvrir la page Créer une adresse IP publique.
    - Ensuite, renseignez le champ **Nom** pour votre adresse IP publique. Laissez la référence SKU définie sur **De base**, sauf si vous devez modifier cette valeur pour une raison spécifique, puis cliquez sur **OK** en bas de la page pour enregistrer vos modifications.

      ![Créer l’adresse IP publique](./media/vpn-gateway-add-gw-s2s-rm-portal-include/gwip.png "Créer PIP")

4. Vérifiez les paramètres. Si vous souhaitez que votre passerelle apparaisse sur le tableau de bord, vous pouvez sélectionner **Épingler au tableau de bord** en bas de la page. 
5. Cliquez sur **Créer** pour créer la passerelle VPN. Les paramètres sont validés et la vignette Déploiement d’une passerelle de réseau virtuel s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté. Vous pouvez cliquer sur l’appareil connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.