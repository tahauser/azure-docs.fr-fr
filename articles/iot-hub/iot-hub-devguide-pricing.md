---
title: Présentation des tarifs Azure IoT Hub | Microsoft Docs
description: 'Guide du développeur : informations concernant le fonctionnement des mesures et des tarifs IoT Hub, avec des exemples.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 390e917990586642e55913a69873b1707e371de3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-hub-pricing-information"></a>Informations sur les tarifs Azure IoT Hub

La [tarification d’Azure IoT Hub][lnk-pricing] fournit des informations générales sur les différentes références SKU et les tarifs d’IoT Hub. Cet article contient des informations supplémentaires sur la manière dont les différentes fonctionnalités d’IoT Hub sont mesurées en tant que messages par IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Frais par opération

| Opération | Informations de facturation | 
| --------- | ------------------- |
| Opérations du registre d’identité <br/> (créer, récupérer, répertorier, mettre à jour, supprimer) | Non facturé. |
| Messages appareil-à-cloud | Les messages envoyés avec succès sont facturés en blocs de 4 Ko une fois entrés dans IoT Hub. Par exemple, un message de 6 Ko est facturé 2 messages. |
| Messages Cloud vers appareil | Les messages envoyés avec succès sont facturés en blocs de 4 Ko ; par exemple, un message de 6 Ko est facturé 2 messages. |
| Chargements de fichiers | Le transfert de fichiers vers Stockage Azure n’est pas mesuré par IoT Hub. Les messages de lancement et de complétion du transfert de fichiers sont facturés en tant que messages, par incréments de 4 Ko. Par exemple, le transfert d’un fichier de 10 Mo est facturé deux messages, en plus du coût de Stockage Azure. |
| Méthodes directes | Les demandes réussies de méthodes sont facturées par blocs de 4 Ko, tandis que les réponses comportant des corps non vides sont elles aussi facturées par blocs de 4 Ko, en tant que messages supplémentaires. Les demandes dirigées vers des appareils déconnectés sont facturées en tant que messages, par blocs de 4 Ko. Par exemple, une méthode avec un corps de 6 Ko qui génère une réponse sans corps de l’appareil est facturée comme deux messages. Une méthode avec un corps de 6 Ko qui génère une réponse de 1 Ko de l’appareil est facturée comme deux messages pour la demande, plus un autre message pour la réponse. |
| Lectures de jumeaux d’appareil | Les lectures de jumeaux d’appareil de l’appareil et du back-end de la solution sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un jumeau d’appareil de 6 Ko est facturée comme 12 messages. |
| Mises à jour de jumeaux d’appareil (balises et propriétés) | Les mises à jour de jumeaux d’appareil à partir de l’appareil et du back-end de la solution sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un jumeau d’appareil de 6 Ko est facturée comme 12 messages. |
| Requêtes de représentations d’appareil | Les requêtes sont facturées en tant que messages, en fonction de la taille du résultat, par blocs de 512 octets. |
| Opérations de travaux <br/> (créer, mettre à jour, répertorier, supprimer) | Non facturé. |
| Opérations de travaux par appareil | Les opérations de travaux (comme les mises à jour de jumeaux d’appareil et les méthodes) sont facturées normalement. Par exemple, un travail entraînant 1 000 appels de méthode avec des demandes de 1 Ko et des réponses à corps vide est facturé comme 1 000 messages. |

> [!NOTE]
> L’ensemble des tailles sont calculées en fonction de la taille en octets de la charge utile (le tramage de protocole est ignoré). Pour les messages qui ont des propriétés et un corps, la taille est calculée de manière agnostique du point de vue du protocole. Pour en savoir plus, consultez le [Guide du développeur de messages IoT Hub][lnk-message-size].

## <a name="example-1"></a>Exemple 1

Un appareil transmet un message appareil-à-cloud de 1 Ko par minute vers IoT Hub ; ce message est ensuite lu par Azure Stream Analytics. Le backend de la solution appelle une méthode (avec une charge utile de 512 octets) sur l’appareil toutes les 10 minutes afin de déclencher une action spécifique. L’appareil répond à la méthode, avec un résultat de 200 octets.

L’appareil consomme :

* Un message * 60 minutes * 24 heures = 1440 messages par jour pour les messages appareil-à-cloud.
* Deux demande plus réponse * 6 fois par heure * 24 heures = 288 messages pour les méthodes.

Ce calcul donne un total de 1 728 messages par jour.

## <a name="example-2"></a>Exemple n° 2

Chaque heure, un appareil envoie un message appareil-à-cloud de 100 Ko. Il met également à jour son jumeau d’appareil avec des charges utiles de 1 Ko toutes les quatre heures. Le back-end de la solution, une fois par jour, lit le jumeau d’appareil de 14 Ko et le met à jour avec des charges utiles de 512 octets afin de modifier les configurations.

L’appareil consomme :

* 25 (100 Ko / 4 Ko) messages * 24 heures pour les messages appareil-à-cloud.
* Deux messages (1 Ko / 0,5 Ko) * six fois par jour pour les mises à jour de jumeau d’appareil.

Ce calcul donne un total de 612 messages par jour.

Le backend de la solution consomme 28 messages (14 Ko / 0,5 Ko) pour lire le jumeau d’appareil, plus un message pour le mettre à jour, ce qui donne un total de 29 messages.

Au total, l’appareil et le back-end de la solution consomment 641 messages par jour.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
