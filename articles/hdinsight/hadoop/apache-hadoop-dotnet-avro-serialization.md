---
title: "Sérialisation des données dans Hadoop - Microsoft Avro Library - Azure | Microsoft Docs"
description: "Découvrez comment sérialiser et désérialiser des données dans Hadoop sur HDInsight à l’aide de Microsoft Avro Library pour les conserver dans une mémoire, une base de données ou un fichier."
keywords: avro,hadoop avro
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 5bb2ee2b9b838cc9feca60eca6b2c721ca58ed45
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Sérialisation des données dans Hadoop avec Microsoft Avro Library

>[!NOTE]
>Le SDK Avro n’est plus pris en charge par Microsoft. La bibliothèque est prise en charge par la communauté open source. Les sources de la bibliothèque se trouvent dans [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Cette rubrique illustre comment utiliser la bibliothèque [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) afin de sérialiser des objets et d’autres structures de données dans des flux pour les conserver dans une mémoire, une base de données ou un fichier. Elle montre également comment les désérialiser pour récupérer les objets d’origine.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
La bibliothèque <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> met en œuvre le système de sérialisation des données Apache Avro pour l’environnement Microsoft.NET. Apache Avro fournit un format compact d'échange des données binaires pour la sérialisation. Elle utilise le format <a href="http://www.json.org" target="_blank">JSON</a> pour définir un schéma sans langage spécifié qui assure l’interopérabilité des langages. Les données sérialisées dans un langage peuvent être lues dans un autre langage. Les langages C, C++, C#, Java, PHP, Python et Ruby sont actuellement pris en charge. Vous pouvez trouver des informations détaillées sur ce format dans la <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Spécification Apache Avro</a>. 

>[!NOTE]
>Microsoft Avro Library ne prend pas en charge la partie RPC (appels de procédure distante) de cette spécification.
>

La représentation sérialisée d’un objet dans le système Avro est composée de deux parties : schéma et valeur réelle. Le schéma Avro décrit le modèle de données indépendant du langage des données sérialisées avec JSON. Il est présenté côte à côte avec une représentation binaire des données. Le fait de séparer le schéma de la représentation binaire permet l’écriture de chaque objet sans surcharge par valeur, ce qui permet d’accélérer la sérialisation et de réduire la représentation.

## <a name="the-hadoop-scenario"></a>Scénario Hadoop
Le format de sérialisation Apache Avro est largement utilisé dans Azure HDInsight et dans d’autres environnements Apache Hadoop. Avro offre un moyen pratique pour représenter des structures de données complexes dans une tâche Hadoop MapReduce. Le format des fichiers Avro (fichier conteneur d’objets Avro) a été conçu pour prendre en charge le modèle de programmation MapReduce distribué. La fonctionnalité clé qui permet la distribution est la possibilité de fractionner les fichiers : il est ainsi possible de rechercher un point quelconque dans un fichier et de commencer la lecture à partir d'un bloc particulier.

## <a name="serialization-in-avro-library"></a>Sérialisation dans Avro Library
La bibliothèque .NET pour Avro prend en charge deux types de sérialisations d'objets :

* **Réflexion** - Le schéma JSON des types est automatiquement généré à partir des attributs de contrat de données des types .NET à sérialiser.
* **Enregistrement générique** - Un schéma JSON est spécifié de manière explicite dans un enregistrement représenté par la classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) lorsqu’aucun type .NET n’est présent pour décrire le schéma des données à sérialiser.

Lorsque le schéma de données est connu par l'enregistreur et le lecteur du flux, les données peuvent être envoyées sans le schéma associé. Si un fichier conteneur d’objets Avro est utilisé, le schéma est stocké dans le fichier. D’autres paramètres, tels que le codec utilisé pour la compression des données, peuvent être spécifiés. Ces scénarios sont présentés plus en détail et illustrés dans les exemples de code ci-après :

## <a name="install-avro-library"></a>Installation d’Avro Library
Les éléments suivants sont requis avant d’installer la bibliothèque :

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (version 6.0.4 ou ultérieure)

Notez que la dépendance Newtonsoft.Json.dll est également téléchargée automatiquement lors de l’installation de la bibliothèque Microsoft Avro Library. Cette procédure est indiquée dans la section suivante :

La bibliothèque Microsoft Avro Library est distribuée en tant que package NuGet pouvant être installé à partir de Visual Studio via la procédure suivante :

1. Sélectionnez l'onglet **Projet** -> **Gérer les packages NuGet...**
2. Recherchez « Microsoft.Hadoop.Avro » dans la zone **Recherche en ligne** .
3. Cliquez sur le bouton **Installer** en regard de **Microsoft Azure HDInsight Avro Library**.

Notez que la dépendance Newtonsoft.Json.dll (>= .6.0.4) est également téléchargée automatiquement avec Microsoft Avro Library.

Le code source Microsoft Avro Library est disponible dans [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Compiler des schémas à l'aide d’Avro Library
La bibliothèque Microsoft Avro Library contient un utilitaire de génération de code qui permet de créer automatiquement des types C# basés sur le schéma JSON défini précédemment. L’utilitaire de génération de code n’est pas distribué comme un code exécutable binaire, mais peut être créé sans difficulté via la procédure suivante :

1. Téléchargez le fichier .zip avec la dernière version du code source du Kit de développement logiciel (SDK) HDInsight sur <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Kit de développement logiciel (SDK) Microsoft .NET pour Hadoop</a>. (Cliquez sur l’icône **Télécharger**, pas sur l’onglet **Téléchargements**.)
2. Extrayez le Kit de développement logiciel (SDK) HDInsight dans un répertoire sur l’ordinateur où .NET Framework 4 a été installé et qui est connecté à Internet afin de télécharger les packages NuGet de dépendance nécessaires. Nous supposons ci-après que le code source est extrait dans C:\SDK.
3. Accédez au dossier C:\SDK\src\Microsoft.Hadoop.Avro.Tools et exécutez build.bat. (Le fichier appelle MSBuild à partir de la distribution 32 bits de .NET Framework. Si vous voulez utiliser la version 64 bits, modifiez build.bat en suivant les commentaires à l’intérieur du fichier.) Assurez-vous que la génération est réussie. (Sur certains systèmes, MSBuild peut générer des avertissements. Ceux-ci n’affectent pas l’utilitaire tant qu’il n’existe aucune erreur de génération.)
4. L’utilitaire compilé se trouve à l’emplacement suivant : C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Pour vous familiariser avec la syntaxe de la ligne de commande, exécutez la commande suivante depuis le dossier où se trouve l’utilitaire de génération de code : `Microsoft.Hadoop.Avro.Tools help /c:codegen`.

Pour tester l’utilitaire, vous pouvez générer des classes C# à partir du fichier de schéma JSON de l’exemple fourni avec le code source. Exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Cette commande est censée créer deux fichiers C# dans le répertoire actif : SensorData.cs et Location.cs.

Pour comprendre la logique utilisée par l’utilitaire de génération de code lorsque le schéma JSON est converti en types C#, reportez-vous au fichier GenerationVerification.feature, situé à l’emplacement suivant : C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Les espaces de noms sont extraits du schéma JSON suivant la logique décrite dans le fichier mentionné au paragraphe précédent. Les espaces de noms extraits du schéma sont prioritaires sur tout ce qui est fourni avec le paramètre /n sur la ligne de commande de l’utilitaire. Si vous voulez remplacer les espaces de noms contenus dans le schéma, utilisez le paramètre /nf. Par exemple, pour changer tous les espaces de noms du schéma SampleJSONSchema.avsc en my.own.nspace, exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>À propos des exemples
Les six exemples fournis dans cette rubrique illustrent chacun un scénario différent pris en charge par Microsoft Avro Library. Microsoft Avro Library est conçu pour fonctionner avec n'importe quel flux. Dans ces exemples, les données sont manipulées à l’aide de flux de mémoire plutôt que de flux de fichiers ou de bases de données pour des questions de simplicité et de cohérence. L’approche au sein d’un environnement de production dépend des exigences du scénario, de la source et du volume des données, des contraintes en matière de performances et d’autres facteurs.

Les deux premiers exemples montrent comment sérialiser et désérialiser des données dans des mémoires tampons de flux de mémoire à l’aide de la réflexion et d’enregistrements génériques. Le schéma dans ces deux cas est supposé être partagé entre les lecteurs et les enregistreurs hors bande.

Les troisième et quatrième exemples montrent comment sérialiser et désérialiser des données avec les fichiers conteneurs d’objets Avro. Lorsque les données sont stockées dans un fichier conteneur Avro, leur schéma est toujours stocké avec, car il doit être partagé pour la désérialisation.

L’échantillon contenant les quatre premiers exemples peut être téléchargé sur le site des <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">exemples de code Azure</a> .

Le cinquième exemple montre comment utiliser un codec de compression personnalisé pour les fichiers conteneurs d’objets Avro. Un échantillon contenant le code de cet exemple peut être téléchargé sur le site des <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">exemples de code Azure</a> .

Le sixième exemple montre comment utiliser la sérialisation Avro pour télécharger des données dans le stockage d’objets blob Azure, puis les analyser à l’aide de Hive avec un cluster HDInsight (Hadoop). Vous pouvez le télécharger sur le site <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">exemples de code Azure</a> .

Les liens suivants renvoient aux six exemples que nous avons examinés dans la rubrique :

* <a href="#Scenario1">**Sérialisation avec réflexion**</a> - Le schéma JSON pour les types à sérialiser est automatiquement généré à partir des attributs de contrat de données.
* <a href="#Scenario2">**Sérialisation avec enregistrement générique**</a> - Le schéma JSON est spécifié de manière explicite dans un enregistrement lorsqu’aucun type .NET n’est disponible pour la réflexion.
* <a href="#Scenario3">**Sérialisation à l’aide de fichiers conteneurs d’objets avec réflexion**</a> - Le schéma JSON est automatiquement créé et partagé en même temps que les données sérialisées, via un fichier conteneur d’objets Avro.
* <a href="#Scenario4">**Sérialisation à l’aide de fichiers conteneurs d’objets avec enregistrement générique**</a> - Le schéma JSON est spécifié de manière explicite avant la sérialisation et partagé en même temps que les données sérialisées, via un fichier conteneur d’objets Avro.
* <a href="#Scenario5">**Sérialisation à l’aide de fichiers conteneurs d’objets avec un codec de compression personnalisé**</a> - L’exemple montre comment créer un fichier conteneur d’objets Avro avec une implémentation .NET personnalisée du codec de compression de données Deflate.
* <a href="#Scenario6">**Utilisation d’Avro pour télécharger des données pour le service Microsoft Azure HDInsight**</a> - L’exemple illustre comment la sérialisation Avro interagit avec le service HDInsight. Un abonnement Azure actif et l’accès à un cluster Azure HDInsight sont requis pour exécuter cet exemple.

## <a name="Scenario1"></a>Exemple 1 : sérialisation avec réflexion
Le schéma JSON des types peut être généré automatiquement par la bibliothèque Microsoft Avro Library via la réflexion, à partir des attributs de contrat de données des objets C# à sérialiser. La bibliothèque Microsoft Avro Library crée un [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) pour identifier les champs à sérialiser.

Dans cet exemple, les objets (classe **SensorData** avec struct **Location** de membre) sont sérialisés dans un flux de mémoire qui est à son tour désérialisé. Le résultat est ensuite comparé avec l’instance d’origine pour vérifier que l’objet **SensorData** récupéré est identique à l’original.

Le schéma présenté dans cet exemple étant supposé partagé entre les lecteurs et les enregistreurs, le format de conteneur d'objet Avro n'est pas requis. Pour un exemple de sérialisation et de désérialisation de données dans des mémoires tampons à l’aide de la réflexion avec le format de conteneur d’objet lorsque le schéma doit être partagé avec les données, consultez le scénario relatif à la <a href="#Scenario3">Sérialisation à l’aide de fichiers conteneurs d’objets avec réflexion</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Exemple 2 : sérialisation avec enregistrement générique
Un schéma JSON peut être spécifié de manière explicite dans un enregistrement générique lorsque la réflexion ne peut pas être utilisée, car les données ne peuvent pas être représentées à l’aide de classes .NET avec un contrat de données. Cette méthode est plus lente que celle qui utilise la réflexion. Dans ce cas, le schéma des données peut également être dynamique, c’est-à-dire inconnu au moment de la compilation. Voici un exemple de ce type de scénario dynamique : des données représentées sous forme de fichiers CSV dont le schéma est inconnu tant qu’il n’a pas été transformé en format Avro au moment de l’exécution.

Cet exemple montre comment créer et utiliser un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) pour spécifier explicitement un schéma JSON, comment le remplir avec les données, puis comment le sérialiser/désérialiser. Le résultat est ensuite comparé avec l’instance d’origine pour confirmer que l’enregistrement récupéré est identique à l’original.

Le schéma présenté dans cet exemple étant supposé partagé entre les lecteurs et les enregistreurs, le format de conteneur d'objet Avro n'est pas requis. Pour un exemple de sérialisation et de désérialisation de données dans des mémoires tampons à l’aide d’un enregistrement générique avec le format de conteneur d’objet lorsque le schéma doit être inclus avec les données sérialisées, consultez l’exemple de <a href="#Scenario4">Sérialisation à l’aide de fichiers conteneurs d’objets avec enregistrement générique</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemple 3 : sérialisation à l'aide de fichiers conteneurs d'objets et sérialisation avec réflexion
Cet exemple est similaire au scénario du <a href="#Scenario1"> premier exemple</a> où le schéma est spécifié implicitement avec la réflexion. La différence est que, dans le présent exemple, le schéma n’est pas supposé être connu du lecteur qui le désérialise. Les objets **SensorData** à sérialiser et leurs schémas associés spécifiés de manière implicite sont stockés dans un fichier conteneur d’objet représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx).

Dans cet exemple, les données sont sérialisées avec [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) et désérialisées avec [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Le résultat est ensuite comparé aux instances d’origine afin d’en vérifier l’identité.

Les données du fichier conteneur d’objet sont compressées à l’aide du codec de compression [**Deflate**][deflate-100] par défaut issu de .NET Framework 4. Consultez le <a href="#Scenario5">cinquième exemple</a> de cette rubrique pour savoir comment utiliser une version ultérieure et plus récente du codec de compression [**Deflate**][deflate-110] disponible dans .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemple 4 : sérialisation à l'aide de fichiers conteneurs d'objets et sérialisation avec enregistrement générique
Cet exemple est similaire au scénario du <a href="#Scenario2"> deuxième exemple</a> où le schéma est spécifié explicitement avec le format JSON. La différence est que, dans le présent exemple, le schéma n’est pas supposé être connu du lecteur qui le désérialise.

Le jeu de données de test est collecté dans une liste d’objets [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) via un schéma JSON défini explicitement, puis stocké dans un fichier conteneur d’objet représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). Ce fichier conteneur crée un enregistreur qui permet de sérialiser les données décompressées dans un flux de mémoire qui est ensuite enregistré dans un fichier. Le paramètre [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilisé pour créer le lecteur spécifie que ces données ne sont pas compressées.

Les données sont ensuite lues à partir du fichier et désérialisées dans une collection d'objets. Cette dernière est ensuite comparée à la liste initiale d'enregistrements Avro pour confirmer qu'ils sont identiques.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemple 5: sérialisation à l'aide de fichiers conteneurs d'objets avec un codec de compression personnalisé
Le cinquième exemple montre comment utiliser un codec de compression personnalisé pour les fichiers conteneurs d’objets Avro. Un échantillon contenant le code de cet exemple peut être téléchargé sur le site des [exemples de code Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) .

La [Spécification Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) autorise l'utilisation d'un codec de compression facultatif (outre les codecs **Null** et **Deflate** par défaut). Cet exemple n’implémente pas un nouveau codec tel que Snappy (mentionné comme codec facultatif pris en charge dans la [Spécification Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Il montre comment utiliser l’implémentation .NET Framework 4.5 du codec [**Deflate**][deflate-110], qui offre un algorithme de compression, basé sur la bibliothèque de compression [zlib](http://zlib.net/), plus efficace que celui de la version par défaut .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemple 6 : utilisation d'Avro pour télécharger des données pour le service Microsoft Azure HDInsight
Le sixième exemple illustre quelques techniques de programmation liées à l’interaction avec le service Azure HDInsight. Un échantillon contenant le code de cet exemple peut être téléchargé sur le site des [exemples de code Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) .

L’exemple effectue les tâches suivantes :

* Établit la connexion à un cluster existant du service HDInsight.
* Sérialise plusieurs fichiers CSV et télécharge le résultat dans le stockage d'objets blob Azure. (Les fichiers CSV sont distribués avec l’exemple et représentent un extrait des données historiques de la bourse AMEX distribuées par [Infochimps](http://www.infochimps.com/) pour la période 1970-2010. L’exemple lit les données du fichier CSV, convertit les enregistrements en instances de classe **Stock**, puis les sérialise en utilisant la réflexion. La définition du type Stock est créée à partir d’un schéma JSON à l’aide de l’utilitaire de génération de code Microsoft Avro Library.
* Crée une nouvelle table externe nommée **Stocks** dans Hive et la lie aux données téléchargées à l’étape précédente.
* Exécute une requête avec Hive sur la table **Stocks** .

En outre, l'exemple exécute une procédure de nettoyage avant et après avoir effectué des opérations majeures. Pendant le nettoyage, l’ensemble des dossiers et des données d’objets blob Azure associés sont supprimés, ainsi que la table Hive. Vous pouvez également appeler la procédure de nettoyage à partir de la ligne de commande de l’exemple.

L'exemple nécessite les éléments suivants :

* Un abonnement Microsoft Azure actif et son ID d’abonnement.
* Un certificat de gestion pour l’abonnement avec la clé privée correspondante. Le certificat doit être installé dans le stockage privé de l’utilisateur actuel sur l’ordinateur utilisé pour exécuter l’exemple.
* Un cluster HDInsight actif.
* Un compte Stockage Azure lié au cluster HDInsight par les conditions préalables précédentes, ainsi que la clé d’accès primaire ou secondaire correspondante.

Toutes les informations des conditions préalables doivent être entrées dans le fichier de configuration de l’exemple avant que ce dernier ne soit exécuté. Il existe deux façons d'effectuer cette opération :

* Modifier le fichier app.config dans le répertoire racine de l’exemple, puis créer l’exemple.
* D’abord créer l’exemple, puis modifier AvroHDISample.exe.config dans le répertoire de build.

Dans les deux cas, toutes les modifications doivent être effectuées dans la section des paramètres **<appSettings>**. Suivez les commentaires dans le fichier.
L’exemple est exécuté à partir de la ligne de commande avec la commande suivante (où le fichier .zip avec l’exemple était censé avoir été extrait sous C:\AvroHDISample ; sinon, utilisez le chemin de fichier approprié) :

    AvroHDISample run C:\AvroHDISample\Data

Pour nettoyer le cluster, exécutez la commande suivante :

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
