---
title: Couverture du géocodage dans Azure Location Based Services | Microsoft Docs
description: Découvrez la couverture du géocodage dans Azure Location Based Services
services: location-based-services
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b2ae0f2cb8eba6ca42b3c82458d1fadf4ea93cdf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-location-based-services---geocoding-coverage"></a>Azure Location Based Services - Couverture du géocodage
Azure Location Based Services (LBS) fournit des informations de géocodage détaillées pour la recherche d’adresses, de lieux et autres entités géographiques dans le monde. Ceci est important pour les clients qui veulent savoir où ils peuvent et ne peuvent pas s’attendre à trouver divers niveaux de fidélité lors de la recherche d’adresses dans Search Service. Lors de la recherche de régions avec une couverture basse fidélité, vous ne pouvez pas vous attendre à des résultats de recherche d’un niveau de confiance moindre. Le tableau suivant répertorie les informations de couverture pour le géocodage de recherche Azure LBS.

| Région             | Points d’adresse<sup>1</sup>|Numéros de résidence<sup>2</sup>| Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| **Amérique**                                            |                 |                |              |            |                    |
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarctique                                          |                 |                |              |      ✓     |          ✓         |
| Antigua-et-Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentine                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Les Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbade                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudes                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Saint-Eustache et Saba|                 |                |              |      ✓     |          ✓         |
| Brésil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Caïmans (îles)                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chili                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Îles Clipperton                                   |                 |                |              |      ✓     |                    |
| Colombie                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominique                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Équateur                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Îles Malouines                                    |                 |                |              |      ✓     |          ✓         |
| Guyane française                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenade                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Haïti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaïque                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexique                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pérou                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Kitts-et-Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Lucie                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre et Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Vincent-et-les-Grenadines                    |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Géorgie du Sud et îles Sandwich du Sud        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinité-et-Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Îles mineures éloignées des États-Unis                |                 |                |              |      ✓     |          ✓         |
| États-Unis d’Amérique                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Îles Vierges - britanniques                            |                 |                |              |      ✓     |          ✓         |
| Îles Vierges - États-Unis                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Asie-Pacifique**                                        |                 |                |              |            |                    |
| Samoa américaines                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhoutan                                              |                 |                |              |      ✓     |          ✓         |
| Territoire britannique de l'océan Indien                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambodge                                            |                 |                |              |      ✓     |          ✓         |
| Chine |                 |                |              |      ✓     |          ✓         |
| Christmas (île)                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Îles Cocos (Keeling)|                 |                |              |      ✓     |          ✓         |
| Comores|                 |                |              |      ✓     |          ✓         |
| Cook (îles)                                        |                 |                |              |      ✓     |          ✓         |
| Fidji |                 |                |              |      ✓     |          ✓         |
| Polynésie française                                    |                 |                |              |      ✓     |          ✓         |
| Heard et McDonald (îles)                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Japon                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaisie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésie |                 |                |              |      ✓     |          ✓         |
| Mongolie                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Népal|                 |                |              |      ✓     |          ✓         |
| Nouvelle-Calédonie                                       |                 |                |              |      ✓     |          ✓         |
| Nouvelle-Zélande                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk (île)                                      |                 |                |              |      ✓     |          ✓         |
| Corée du Nord                                         |                 |                |              |      ✓     |          ✓         |
| Îles Marianne du Nord                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Papouasie Nouvelle Guinée                                    |                 |                |              |      ✓     |          ✓         |
| Îles Paracels                                     |                 |                |              |      ✓     |                    |
| Philippines                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Îles Senkaku                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapour                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomon (îles)                                     |                 |                |              |      ✓     |          ✓         |
| Corée du Sud                                         |                 |                |              |      ✓     |          ✓         |
| Îles Kouriles                                     |        ✓        |                |              |      ✓     |          ✓         |
| Îles Spratleys                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taïwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thaïlande                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turques-et-Caïques (îles)                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Viêt Nam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis et Futuna|                 |                |              |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Europe**                                              |                 |                |              |            |                    |
| Albanie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorre                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménie                                             |                 |                |              |      ✓     |          ✓         |
| Autriche                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaïdjan                                          |                 |                |              |      ✓     |          ✓         |
| Belgique                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnie-Herzégovine                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bulgarie                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Biélorussie|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croatie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chypre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| République tchèque                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danemark                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Féroé (îles)                                       |                 |                |              |      ✓     |          ✓         |
| Finlande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| France                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Géorgie                                             |                 |                |              |      ✓     |          ✓         |
| Allemagne                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grèce                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenland                                           |                 |                |              |      ✓     |          ✓         |
| Guernesey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hongrie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Islande                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlande                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italie                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakhstan                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirghizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettonie                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituanie                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxembourg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macédoine (Ex-République yougoslave de)                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malte                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Monténégro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Pays-bas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvège                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pologne                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Roumanie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Fédération de Russie                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Marin                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbie                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovaquie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovénie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espagne                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suède                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suisse                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadjikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turquie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkménistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraine                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Royaume-Uni                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ouzbékistan                                          |                 |                |              |      ✓     |          ✓         |
| Cité du Vatican                                        |                 |                |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Moyen-Orient et Afrique**                                  |                 |                |              |            |                    |
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahreïn                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bénin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet (île)                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Cameroun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cap-Vert|                 |                |       ✓      |      ✓     |          ✓         |
| République centrafricaine                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tchad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| République démocratique du Congo                        |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Égypte                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guinée équatoriale, République de                      |                 |                |       ✓      |      ✓     |          ✓         |
| Érythrée                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Éthiopie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Terres australes françaises|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambie                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israël                                              |                 |                |              |      ✓     |          ✓         |
| Jordanie                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Koweït                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libye|                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldives |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall (îles) |                 |                |              |      ✓     |          ✓         |
| Mauritanie                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurice (île)                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroc                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| La Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Hélène                                        |                 |                |              |      ✓     |          ✓         |
| Arabie Saoudite                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Sénégal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalie                                             |                 |                |              |      ✓     |          ✓         |
| Afrique du Sud                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Soudan du Sud                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Soudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrie                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé-et-Príncipe, République démocratique de       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Ouganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Émirats Arabes Unis                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cisjordanie                                           |                 |                |              |      ✓     |          ✓         |
| Yémen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |


<br>
<br>




|Termes|Définitions|
|--- |--- |
|**<sup>1</sup> - Address Point**|Dérivés de l’index Point d’adresse dans la recherche en ligne. Ce champ est uniquement pris en charge lorsque la Recherche et le Géocodage sont pris en charge.|
|**<sup>2</sup> - Numéro de résidence**|Dérivés de l’index Interpolation d’adresse dans la recherche en ligne. Ce champ est uniquement pris en charge lorsque la Recherche et le Géocodage sont pris en charge.|
|**<sup>3</sup> - Code affecté par l’utilisateur**|Recherche spécifique code en ligne, pas un code officiel ISO 3166-1.|
