---
title: "Application Java nécessitant beaucoup de ressources système sur une machine virtuelle | Microsoft Docs"
description: "Apprenez à créer une machine virtuelle Azure qui exécute une application de calcul intensif Java qu'une autre application Java peut surveiller."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ccccdf58fbb84605bc5dff29d870b373134f1f97
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Exécution d'une tâche nécessitant beaucoup de ressources en langage Java sur une machine virtuelle
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Azure permet d'utiliser une machine virtuelle pour gérer les tâches nécessitant beaucoup de ressources. Par exemple, une machine virtuelle peut gérer des tâches et fournir des résultats à des ordinateurs clients ou à des applications mobiles. Après avoir lu cet article, vous serez en mesure de créer une machine virtuelle exécutée sur une application Java nécessitant beaucoup de ressources et pouvant être surveillée par une autre application Java.

Ce didacticiel part du principe que vous savez créer des applications console Java, importer des bibliothèques dans votre application Java et générer une archive Java (JAR). Aucune connaissance de Microsoft Azure n'est nécessaire.

Vous apprendrez à effectuer les opérations suivantes :

* créer une machine virtuelle déjà dotée d'un kit de développement Java (JDK) ;
* vous connecter à distance à votre machine virtuelle ;
* création d'un espace de noms Service Bus ;
* créer une application Java exécutant une tâche qui nécessite beaucoup de ressources ;
* créer une application Java surveillant la progression de la tâche qui nécessite beaucoup de ressources ;
* exécuter les applications Java ;
* arrêter les applications Java.

Dans le cadre de ce didacticiel, la tâche nécessitant beaucoup de ressources repose sur le problème du voyageur de commerce. Vous trouverez ci-dessous un exemple d'application Java qui exécute la tâche nécessitant beaucoup de ressources.

![Résolution du problème du voyageur de commerce][solver_output]

Vous trouverez ci-dessous un exemple d'application Java qui surveille la tâche nécessitant beaucoup de ressources :

![Client du problème du voyageur de commerce][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Création d’une machine virtuelle
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Créer une ressource**, sur **Compute**, sur **Virtual machine**, puis sur **From Gallery**.
3. Dans la boîte de dialogue **Sélectionner une image de machine virtuelle**, sélectionnez **Windows Server 2012 JDK 7**.
   Notez que **Windows Server 2012 JDK 6** est disponible si vous ne pouvez pas exécuter certaines de vos applications héritées dans JDK 7.
4. Cliquez sur **Suivant**.
5. Dans la boîte de dialogue **Configuration de la machine virtuelle** :
   1. Entrez un nom pour la machine virtuelle.
   2. Entrez la taille de la machine virtuelle.
   3. Entrez un nom pour l'administrateur dans le champ **Nom d'utilisateur** . Notez le nom et le mot de passe que vous allez entrer, car vous les utiliserez pour vous connecter à distance à votre machine virtuelle.
   4. Entrez un mot de passe dans le champ **Nouveau mot de passe**, puis entrez-le de nouveau dans le champ **Confirmer**. Il s'agit du mot de passe du compte Administrateur.
   5. Cliquez sur **Suivant**.
6. Dans la boîte de dialogue **Configuration de la machine virtuelle** suivante :
   1. Pour le **Service cloud**, utilisez le paramètre par défaut **Créer un nouveau service cloud**.
   2. La valeur du **Nom du cloud Service DNS** doit être unique sur cloudapp.net. Si nécessaire, modifiez cette valeur afin qu'Azure indique qu'elle est unique.
   3. Indiquez une région, un groupe d'affinités ou un réseau virtuel. Dans le cadre de ce didacticiel, indiquez une région comme **Bretagne**.
   4. Pour **Storage Account**, sélectionnez **Use an automatically generated storage account**.
   5. Pour **Availability Set**, sélectionnez **(None)**.
   6. Cliquez sur **Suivant**.
7. Dans la dernière boîte de dialogue **Configuration de la machine virtuelle** :
   1. Validez les entrées de points de terminaison par défaut.
   2. Cliquez sur **Terminé**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Connexion distante à votre machine virtuelle
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle à laquelle vous voulez vous connecter.
4. Cliquez sur **Connecter**.
5. Répondez aux invites pour vous connecter à la machine virtuelle. Lorsque vous y êtes invité, entrez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

Notez que la fonctionnalité Azure Service Bus requiert l'installation du certificat racine Baltimore CyberTrust dans le magasin **cacerts** de votre environnement JRE. Ce certificat est automatiquement inclus dans l'environnement JRE (Java Runtime Environment) utilisé par ce didacticiel. Si vous ne disposez pas de ce certificat dans le magasin **cacerts** de votre environnement JRE, consultez la rubrique [Ajout d’un certificat au magasin de certificats d’autorité de certification Java][add_ca_cert] pour plus d’informations sur l’ajout de celui-ci (et sur l’affichage des certificats de votre magasin cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus
Pour commencer à utiliser les files d'attente Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. En bas du volet de navigation gauche du Portail Azure, cliquez sur **Service Bus, Access Control et Cache**.
3. Dans le volet supérieur gauche du Portail Azure, cliquez sur le nœud **Service Bus**, puis sur le bouton **Nouveau**.  
   ![Capture d'écran du nœud Service Bus][svc_bus_node]
4. Dans la boîte de dialogue **Créer un espace de noms de service**, entrez un **Espace de noms**, puis vérifiez qu’il est unique en cliquant sur le bouton **Vérifier la disponibilité**.  
   ![Capture d'écran Créer un espace de noms][create_namespace]
5. Après avoir vérifié que le nom de l'espace de noms est disponible, choisissez le pays ou la région où votre espace de noms sera hébergé, puis cliquez sur le bouton **Créer un espace de noms** .  
   
   L’espace de noms que vous avez créé apparaît alors dans le portail Azure. Son activation peut prendre un peu de temps. Attendez que l’état **Actif** apparaisse avant de passer à l’étape suivante.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obtention d'informations d'identification de gestion par défaut pour l'espace de noms
Pour pouvoir effectuer des opérations de gestion telles que la création d'une file d'attente sur le nouvel espace de noms, vous devez obtenir les informations d'identification de gestion associées.

1. Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour afficher la liste des espaces de noms disponibles.
   ![Capture d'écran Available Namespaces][avail_namespaces]
2. Sélectionnez l’espace de noms que vous venez de créer dans la liste affichée.
   ![Capture d’écran Liste d’espaces de noms][namespace_list]
3. Le volet **Propriétés** de droite répertorie les propriétés du nouvel espace de noms.
   ![Capture d'écran du volet Propriétés][properties_pane]
4. La **Clé par défaut** est masquée. Cliquez sur le bouton **Afficher** pour afficher les informations d'identification de sécurité.
   ![Capture d'écran Clé par défaut][default_key]
5. Notez **l’Émetteur par défaut** et la **Clé par défaut**, car vous devrez utiliser ces informations ci-dessous pour accomplir les opérations relatives à l’espace de noms.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Création d'une application Java exécutant une tâche qui nécessite beaucoup de ressources
1. Sur votre ordinateur de développement (qui n'est pas forcément celui où se trouve la machine virtuelle que vous avez créée), téléchargez le [Kit de développement logiciel (SDK) Azure pour Java](https://azure.microsoft.com/develop/java/).
2. Créez une application console Java à l'aide de l'exemple de code disponible à la fin de cette section. Dans le cadre de ce didacticiel, nous utiliserons le nom de fichier Java **TSPSolver.java** . Modifiez les espaces réservés **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** et **your\_service\_bus\_key** pour utiliser respectivement vos valeurs Service Bus **Espace de noms**, **Émetteur par défaut** et **Clé par défaut**.
3. Après le codage, exportez l'application dans une archive Java exécutable (JAR) et créez un package contenant les bibliothèques requises dans le fichier JAR généré. Dans le cadre de ce didacticiel, nous utiliserons le nom **TSPSolver.jar** pour désigner le fichier JAR généré.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Création d'une application Java surveillant la progression de la tâche qui nécessite beaucoup de ressources
1. Sur votre ordinateur de développement, créez une application console Java à l'aide de l'exemple de code disponible à la fin de cette section. Dans le cadre de ce didacticiel, nous utiliserons le nom **TSPClient.java** pour désigner le fichier Java. Comme indiqué plus haut, modifiez les espaces réservés **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** et **your\_service\_bus\_key** pour utiliser respectivement vos valeurs Service Bus **Espace de noms**, **Émetteur par défaut** et **Clé par défaut**.
2. Exportez l'application dans un fichier JAR exécutable et créez un package contenant les bibliothèques requises dans le fichier JAR généré. Dans le cadre de ce didacticiel, nous utiliserons le nom **TSPClient.jar** pour désigner le fichier JAR généré.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Exécution des applications Java
Exécutez l'application nécessitant beaucoup de ressources pour créer la file d'attente, puis pour résoudre le problème du voyageur de commerce afin d'ajouter le meilleur itinéraire actuel à la file d'attente Service Bus. Pendant (ou après) l'exécution de l'application nécessitant beaucoup de ressources, exécutez le client pour afficher les résultats de la file d'attente Service Bus.

### <a name="to-run-the-compute-intensive-application"></a>Exécution de l'application nécessitant beaucoup de ressources
1. Connectez-vous à votre machine virtuelle.
2. Créez un dossier où vous exécuterez votre application. Par exemple, **C:\TSP**.
3. Copiez **TSPSolver.jar** sous **C:\TSP**.
4. Créez un fichier intitulé **C:\TSP\cities.txt** avec le contenu ci-dessous.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Depuis une invite de commandes, accédez au répertoire c:\TSP.
6. Vérifiez que le dossier Bin de JRE se trouve dans la variable d'environnement PATH.
7. Vous devez créer la file d'attente Service Bus avant d'exécuter les permutations de solveur TSP. Exécutez la commande ci-dessous pour créer la file d'attente de bus de services.
   
        java -jar TSPSolver.jar createqueue
8. Maintenant que la file d’attente est créée, vous pouvez exécuter les permutations de solveur TSP. Par exemple, exécutez la commande suivante afin d’exécuter le solveur pour 8 villes.
   
        java -jar TSPSolver.jar 8
   
   Si vous n'entrez aucun nombre, l'exécution portera sur 10 villes. Dès que le solveur trouve les itinéraires les plus courts, il les ajoute à la file d’attente.

> [!NOTE]
> Plus le nombre spécifié est élevé, plus l’exécution du solveur est longue. Par exemple, une exécution portant sur 14 villes peut prendre quelques minutes, et une exécution portant sur 15 villes peut prendre des heures. Au-delà de 16 villes, l'exécution peut prendre des jours (voire des semaines, des mois et des années). Cette lenteur est due à la hausse rapide du nombre de permutations évaluées par le solveur à mesure que le nombre de villes augmente.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Exécution de la surveillance de l'application cliente
1. Connectez-vous à l'ordinateur où vous exécuterez l'application cliente. Il ne doit pas nécessairement s'agir de l'ordinateur qui exécute l'application **TSPSolver** .
2. Créez un dossier où vous exécuterez votre application. Par exemple, **C:\TSP**.
3. Copiez **TSPClient.jar** sous **C:\TSP**,
4. Vérifiez que le dossier Bin de JRE se trouve dans la variable d'environnement PATH.
5. Depuis une invite de commandes, accédez au répertoire c:\TSP.
6. Exécutez la commande ci-dessous.
   
        java -jar TSPClient.jar
   
    Vous pouvez éventuellement spécifier le nombre de minutes de veille entre les vérifications de la file d’attente via un argument de ligne de commande. La période de veille par défaut de la vérification de la file d'attente est de 3 minutes et elle est appliquée si aucun argument de ligne de commande n'est transmis à **TSPClient**. Si vous souhaitez utiliser une autre valeur pour l’intervalle de veille (une minute, par exemple), exécutez la commande suivante :
   
        java -jar TSPClient.jar 1
   
    Le client s'exécutera jusqu'à ce qu'il voie le message de file d'attente « Terminé ». Notez que si vous exécutez plusieurs occurrences du solveur sans exécuter le client, vous serez peut-être amené à exécuter le client plusieurs fois pour vider entièrement la file d'attente. Vous pouvez également supprimer la file d’attente puis la recréer. Pour supprimer la file d’attente, exécutez la commande **TSPSolver** (et non **TSPClient**) ci-dessous.
   
        java -jar TSPSolver.jar deletequeue
   
    Le solveur s’exécutera jusqu’à ce qu’il ait examiné tous les itinéraires.

## <a name="how-to-stop-the-java-applications"></a>Arrêt des applications Java
Pour quitter les applications solveur et cliente avant la fin normale, vous pouvez appuyer sur **Ctrl+C** .

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
