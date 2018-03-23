---
title: Développer des opérateurs U-SQL définis par l’utilisateur | Microsoft Docs
description: 'Apprenez à développer des opérateurs définis par l’utilisateur pour les utiliser et les réutiliser dans des travaux Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 7c0b9c193f8f1c3a3043824186e337f942ebfd56
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Développer des opérateurs U-SQL définis par l’utilisateur
Apprenez à développer des opérateurs définis par l’utilisateur pour traiter les données d’un travail U-SQL.

Pour obtenir des instructions concernant le développement des assemblys à usage général pour U-SQL, consultez [Développement d’assemblys U-SQL pour les travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Définir et utiliser un opérateur défini par l’utilisateur dans U-SQL
**Pour créer et soumettre un travail U-SQL**

1. Dans Visual Studio, sélectionnez **Fichier > Nouveau > Projet > Projet U-SQL**.
2. Cliquez sur **OK**. Visual Studio crée une solution avec un fichier Script.usql.
3. À partir de **l’Explorateur de solutions**, développez Script.usql, puis double-cliquez sur **Script.usql.cs**.
4. Collez le code suivant dans le fichier :

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Ouvrez **Script.usql** et collez le script U-SQL suivant :

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Spécifiez le compte Data Lake Analytics, la base de données et le schéma.
8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer le script**.
9. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Soumettre le script**.
10. Si vous ne vous êtes pas connecté à votre abonnement Azure, vous serez invité à entrer vos informations d’identification de compte Azure.
11. Cliquez sur **Envoyer**. Les résultats de l’envoi et le lien vers le travail sont disponibles dans la fenêtre Résultats quand l’envoi est terminé.
12. Cliquez sur le bouton **Actualiser** pour afficher le dernier état du travail et actualiser l’écran.

**Pour afficher la sortie**

1. Dans **l’Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**, puis cliquez avec le bouton droit sur le stockage par défaut et sur **Explorer**.
2. Développez des exemples, des sorties, puis double-cliquez sur **Drivers.csv**.

## <a name="see-also"></a>Voir aussi
* [Extension des expressions U-SQL avec le code utilisateur](https://msdn.microsoft.com/en-us/library/azure/mt621316.aspx)
* [Utiliser les outils Data Lake pour Visual Studio pour le développement d’applications U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
