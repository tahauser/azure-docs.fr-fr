---
title: didacticiel Unity Roll a Ball
description: Étapes de création du jeu Unity Roll a Ball classique, qui est une condition préalable à tous les didacticiels Unity sur Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 52d5962645b1408fdba61ec1bf4e4f682b80905b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a id="unity-roll-a-ball"></a>Créer un jeu Unity Roll a Ball
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce didacticiel est une version légèrement modifiée du [didacticiel Unity Roll a Ball](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)et en présente les étapes principales. Cet exemple de jeu consiste en un objet « Player » sphérique contrôlé par l’utilisateur de l’application, et l’objectif du jeu et de « collecter » des objets en les heurtant avec l’objet Player. Cela suppose une connaissance de base de l’environnement de Unity Editor. Si vous rencontrez des problèmes, reportez-vous au didacticiel complet. 

### <a name="setting-up-the-game"></a>Configuration du jeu
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Ouvrez **Unity Editor** et cliquez sur **New**. 
   
    ![][51] 
2. Indiquez un **nom de projet** & **emplacement**, sélectionnez **3D** et cliquez sur **Create project**.
   
    ![][52]
3. Enregistrer la scène par défaut que vous venez de créer dans le cadre du nouveau projet sous **MiniGame** dans un nouveau dossier **\_Scenes** sous le dossier **Assets** :
   
    ![][53]
4. Créez un objet **3D Object -> Plane** en tant que champ de jeu et renommez cet objet Plane en **Ground**.
   
    ![][1]
5. Réinitialisez le composant de transformation de cet objet **Ground** afin qu’il se trouve à l’origine. 
   
    ![][3]
6. Décochez la case **Show Grid** du **menu Gizmos** pour l’objet **Ground**.
   
    ![][4]
7. Mettez à jour le composant **Scale** pour l’objet **Ground** avec les valeurs [X = 2,Y = 1,Z = 2]. 
   
    ![][5]
8. Ajouter un nouvel objet **3D Object -> Sphere** au projet, puis renommez cet objet Sphere en **Player**. 
   
    ![][6]
9. Sélectionnez l’objet **Player** et cliquez sur **Reset Transform** comme pour l’objet Plane. 
10. Mettez à jour le composant **Transform -> Position -> Y Coordinate** pour l’objet Player avec la valeur 0,5.  
    
    ![][7]
11. Créez un nouveau dossier appelé **Materials** dans le projet où nous allons créer le matériau pour colorier l’objet Player. 
12. Créez un **matériau** appelé **Background** dans ce dossier. 
    
    ![][8]
13. Mettez à jour la couleur du matériau en mettant à jour la propriété **Albedo** de celui-ci. Vous pouvez sélectionner les valeurs RVB [0,32,64]. 
    
    ![][9]
14. Faites glisser ce matériau dans la vue de la scène pour appliquer la couleur à l’objet **Ground** . 
    
    ![][10]
15. Pour terminer, mettez à jour **Transform -> Rotation -> Y** avec la valeur 60 sur l’objet Directional Light pour la clarté. 
    
    ![][12]

### <a name="moving-the-player"></a>Déplacement du de l’objet Player
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Ajoutez un composant **RigidBody** à l’objet **Player**. 
   
    ![][13]
2. Créez un dossier appelé **Scripts** dans le projet. 
3. Cliquez sur **Add Component-> New Script -> C# Script**. Nommez-le **PlayerController**, puis cliquez sur **Create and Add**. Cette opération crée et attache un script à l’objet Player.  
   
    ![][14]
4. Déplacez ce script dans le dossier **Scripts** du projet. 
5. Ouvrez le script pour le modifier dans l’éditeur de script de votre choix, mettez à jour le code de script avec le code suivant et enregistrez-le. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Notez que le script ci-dessus utilise une propriété **Speed** . Dans Unity Editor, mettez à jour la propriété Speed avec la valeur 10.  
   
    ![][15]
7. Appuyez sur **Play** dans Unity Editor. Vous devez maintenant être en mesure de contrôler la balle à l’aide du clavier, et elle doit pivoter et se déplacer. 

### <a name="moving-the-camera"></a>Déplacement de la caméra
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) et relient l’objet **Main Camera** à l’objet **Player**. 

1. Mettez à jour **Transform.Position** avec les valeurs X = 0, Y = 10,5, Z = -10.  
2. Mettez à jour **Transform.Rotation** avec les valeurs X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Ajoutez un script appelé **CameraController** à **MainCamera** et déplacez-le dans le dossier Scripts. 
   
    ![][17]
4. Ouvrez le script pour le modifier et ajoutez-y le code suivant :
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Dans l’environnement Unity, faites glisser la variable Player dans l’emplacement Player pour l’objet Main Camera afin de les associer l’un à l’autre. 
   
    ![][18]
6. Si vous appuyez sur Play dans Unity Editor et que vous faites pivoter l’objet Player Ball, la caméra suit le mouvement.  

### <a name="setting-up-the-play-area"></a>Configuration de la zone Play
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Nous allons créer les murs (Walls) entourant le sol (Ground) afin que l’objet Player Ball ne sorte pas de la zone de jeu pendant son déplacement. 

1. Cliquez sur **Create -> Create Empty -> Game Object** et nommez-le **Walls**.
   
    ![][19]
2. Sous cet objet Walls, créez un objet **3D Object -> Cube** et nommez-le « West wall ». 
   
    ![][20]
3. Mettez à jour **Transform -> Position** et **Transform -> Scale** pour cet objet West Wall. 
   
    ![][21]
4. Dupliquer l’objet West Wall pour créer un objet **East wall** avec la position et l’échelle de transformation mises à jour. 
   
    ![][22]
5. Dupliquer l’objet East Wall pour créer un objet **North wall** avec la position et l’échelle de transformation mises à jour. 
   
    ![][23]
6. Dupliquer l’objet North Wall pour créer un objet **South wall** avec la position et l’échelle de transformation mises à jour. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Création d’objets pouvant être collectés
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Nous allons créer des objets attractifs qui formeront l’ensemble d’objets que l’objet Player Ball doit « collecter » en se heurtant à eux. 

1. Créez un **objet 3D Cube** et nommez-le Pickup. 
2. Ajustez **Transform -> Rotation** & **Transform -> Scale** pour l’objet Pickup. 
   
    ![][25]
3. Créez un **script C#** appelé **Rotator** et attachez-le à l’objet Pickup. Assurez-vous de placer le script dans le dossier Scripts. 
   
    ![][26]
4. Ouvrez ce script pour le modifier et mettez-le à jour comme suit : 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Appuyez à présent sur le mode Play dans Unity Editor : votre objet Pickup pivote sur son axe.
6. Créez un dossier appelé **Prefabs** 
   
    ![][27]
7. Faites glisser l’objet **Pickup** et placez-le dans le dossier Prefabs.
   
    ![][28]
8. Créez un **objet Empty Game** appelé **Pickups**. Réinitialisez sa position à l’origine, puis faites glisser l’objet Pickup sous cet objet de jeu.  
   
    ![][29]
9. Dupliquez l’objet **Pickup** et propagez-le sur l’objet **Ground** autour de l’objet **Player** en mettant à jour les valeurs **X et Z de Transform.Position** de manière appropriée. 
   
    ![][30]
10. Créez un **matériau** appelé **Pickup** et mettez-le à jour pour qu’il soit de couleur rouge en mettant à jour la **propriété Albedo** comme nous l’avons fait pour l’objet Ground. 
    
    ![][31]
11. Appliquez le matériau aux 4 objets Pickup.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Collecter les objets Pickup
Les étapes suivantes sont issues du [didacticiel Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Nous allons mettre à jour l’objet Player pour qu’il puisse « collecter » les objets Pickup en se heurtant à eux. 

1. Ouvrez le script **PlayerController** joint à l’objet Player pour le modifier et mettez-le à jour comme suit :  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Créez une **balise** appelée **Pick Up** (elle doit correspondre au contenu du script).  
   
    ![][33]
   
    ![][34]
3. Appliquez cette **balise** à l’objet Prefab Pickup. 
   
    ![][35]
4. Cochez la case **IsTrigger** pour l’objet Prefab.
   
    ![][36]
5. Ajoutez un corps rigide à l’objet Pickup Prefab. Pour optimiser les performances, nous allons mettre à jour le collider statique que nous avons utilisé en collider dynamique. 
   
    ![][37]
6. Enfin, vérifiez la propriété **IsKinematic** de l’objet Prefab. 
   
    ![][38]
7. Appuyez sur **Play** dans Unity Editor pour jouer à ce jeu **Roll a Ball** en déplaçant l’objet Player à l’aide des touches de votre clavier pour indiquer la direction. 

### <a name="updating-the-game-for-mobile-play"></a>Mise à jour du jeu pour y jouer sur un appareil mobile
Les sections ci-dessus marquent la fin du didacticiel de base d’Unity. Maintenant, nous allons modifier le jeu pour qu’il soit compatible avec un appareil mobile. Notez que nous avons utilisé le clavier jusqu’à présent pour tester le jeu. Nous allons maintenant le modifier pour être en mesure de contrôler l’objet Player à l’aide du mouvement du téléphone, par exemple, en utilisant l’accéléromètre en guise d’entrée. 

Ouvrez le script **PlayerController** pour le modifier et mettez à jour la méthode **FixedUpdate** pour utiliser le mouvement de l’accéléromètre pour déplacer l’objet Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Ce didacticiel a permis de créer un jeu de base avec Unity et vous pouvez le déployer sur l’appareil de votre choix pour y jouer. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













