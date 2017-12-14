---
title: "Envoyer des événements vers Azure Event Hubs avec C | Microsoft Docs"
description: "Envoyer des événements vers Azure Event Hubs avec C"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: sethm
ms.openlocfilehash: 2b714c5de96a8fb7ed66a30c62daaa38b84fdc5b
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Envoyer des événements vers Azure Event Hubs avec C

## <a name="introduction"></a>Introduction
Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectées dans un concentrateur d’événements, les données peuvent être transformées et stockées à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la [Vue d’ensemble des concentrateurs d’événements][Vue d’ensemble des concentrateurs d’événements].

Ce didacticiel explique comment envoyer des événements à un hub d’événements à l’aide d’une application console en C. Pour en savoir plus sur la réception d’événements, cliquez sur le langage de réception approprié dans la table des matières de gauche.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un environnement de développement en C. Ce didacticiel présuppose que la pile GCC est sur une machine virtuelle Linux Azure dotée du système d’exploitation Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Envoi de messages vers Event Hubs
Cette section explique comment écrire une application en C pour envoyer des événements à votre hub d’événements. Le code utilise la bibliothèque Proton AMQP du [projet Apache Qpid](http://qpid.apache.org/). Cette approche est similaire à l’utilisation de rubriques et de files d’attente Service Bus avec AMQP en partant du langage C comme indiqué dans [cet exemple](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Pour plus d’informations, consultez la [Documentation Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Dans la [page Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), suivez les instructions d’installation de Qpid Proton correspondant à votre environnement.
2. Pour compiler la bibliothèque Proton, installez les packages suivants :
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Téléchargez la [bibliothèque Qpid Proton](http://qpid.apache.org/proton/index.html) et effectuez son extraction, par exemple :
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Créez un répertoire de build, compilez-le et installez-le :
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Dans le répertoire de travail, créez un fichier nommé **sender.c** avec le code suivant. N’oubliez pas de remplacer les valeurs pour votre nom/clé SAS, votre nom de hub d’événements et votre espace de noms. Vous devez également remplacer une version codée URL de la clé pour le **SendRule** précédemment créé. Vous pouvez la coder par URL [ici](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compilez le fichier (en supposant que **gcc**est défini) :
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ce code utilise une fenêtre sortante de 1 pour forcer l’envoi des messages dès que possible. Il est recommandé que votre application essaie d’envoyer les messages par lot pour augmenter le débit. Consultez la [page Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) pour plus d’informations sur l’utilisation de la bibliothèque Qpid Proton dans l’ensemble des environnements et à partir des plateformes pour lesquelles des liaisons sont fournies (actuellement Perl, PHP, Python et Ruby).


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
