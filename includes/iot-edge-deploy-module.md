Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Un module IoT Edge est un package exécutable implémenté en tant que conteneur. Dans cette section, vous déployez un module qui génère des données de télémétrie pour votre appareil simulé. 

1. Accédez à votre hub IoT dans le portail Azure.
1. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**.
1. Sélectionnez **Ajouter un module IoT Edge**.
1. Dans le champ **Nom**, entrez `tempSensor`. 
1. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Laissez les autres paramètres inchangés et sélectionnez **Enregistrer**.

   ![Enregistrer le module IoT Edge après avoir entré le nom et l’URI de l’image](./media/iot-edge-deploy-module/name-image.png)

1. De retour à l’étape **Ajouter des modules**, sélectionnez **Suivant**.
1. À l’étape **Spécifier des itinéraires**, sélectionnez **Suivant**.
1. À l’étape **Vérifier le modèle**, sélectionnez **Envoyer**.
1. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**. Vous devez voir le nouveau module tempSensor en cours d’exécution avec le runtime IoT Edge. 

   ![Afficher tempSensor dans la liste des modules déployés][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png