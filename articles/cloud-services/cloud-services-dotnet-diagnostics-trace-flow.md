---
title: Assurer le suivi du flux dans une application Cloud Services avec Diagnostics Azure | Microsoft Docs
description: "Ajouter des messages de suivi à une application Azure pour aider au débogage, à la mesure des performances, à la surveillance, à l’analyse du trafic et bien plus encore."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: adegeo
ms.openlocfilehash: 7551ae2e3a7c5f3ff6010bf34e7600da1028d67f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Assurer le suivi du flux dans une application Cloud Services avec Diagnostics Azure
Le suivi est un moyen de surveiller l’exécution de votre application pendant son exécution . Vous pouvez utiliser les classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) et [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) pour enregistrer des informations relatives aux erreurs et à l’exécution des applications dans des journaux, des fichiers texte ou d’autres périphériques pour une analyse ultérieure. Pour plus d’informations sur le suivi, consultez [Applications de suivi et instrumentation](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Utilisez les instructions et commutateurs de suivi
Mettez en œuvre le suivi dans votre application Cloud Services en ajoutant [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) à la configuration d’application et en lançant des appels vers System.Diagnostics.Trace ou vers System.Diagnostics.Debug dans votre code d’application. Utilisez le fichier de configuration *app.config* pour les rôles de travail et le fichier *web.config* pour les rôles web. Lorsque vous créez un nouveau service hébergé à l’aide d’un modèle Visual Studio, Azure Diagnostics est automatiquement ajouté au projet et DiagnosticMonitorTraceListener est ajouté au fichier de configuration approprié pour les rôles que vous ajoutez.

Pour plus d’informations sur le placement des instructions de suivi, consultez [Procédure : ajouter des instructions de suivi au Code d’application](https://msdn.microsoft.com/library/zd83saa2.aspx).

En plaçant des [Commutateurs de suivi](https://msdn.microsoft.com/library/3at424ac.aspx) dans votre code, vous pouvez contrôler le traçage et son importance. Vous pouvez ainsi surveiller l’état de votre application dans un environnement de production, ce qui est particulièrement important dans une application qui utilise plusieurs composants s’exécutant sur plusieurs ordinateurs. Pour plus d’informations, consultez la rubrique [Procédure : configuration de commutateurs de trace](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurer l’écouteur de suivi dans une application Azure
Trace, Debug et TraceSource nécessitent que vous définissiez des « écouteurs » pour recueillir et enregistrer les messages qui sont envoyés. Les écouteurs recueillent, stockent et acheminent les messages de suivi. Ils orientent la sortie de suivi vers une cible appropriée, par exemple un journal, une fenêtre ou un fichier texte. Azure Diagnostics utilise la classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Avant d’exécuter la procédure suivante, vous devez initialiser le moniteur de diagnostic Azure. Pour ce faire, voir [Activation des diagnostics dans Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Notez que si vous utilisez les modèles fournis par Visual Studio, la configuration de l’écouteur est automatiquement ajoutée pour vous.

### <a name="add-a-trace-listener"></a>Ajouter un écouteur de suivi
1. Ouvrez le fichier web.config ou app.config correspondant à votre rôle.
2. Ajoutez le code suivant au fichier. Modifiez l’attribut de version pour utiliser le numéro de version de l’assembly que vous référencez. La version d’assembly ne change pas nécessairement avec chaque version du Kit de développement logiciel (SDK) Azure sauf s’il existe des mises à jour.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Assurez-vous de disposer d’une référence de projet à l’assembly Microsoft.WindowsAzure.Diagnostics. Mettre à jour le numéro de version dans le document xml ci-dessus pour correspondre à la version de l’assembly Microsoft.WindowsAzure.Diagnostics référencé.
   > 
   > 
3. Enregistrez le fichier de configuration.

Pour plus d’informations sur les écouteurs, consultez [Suivi des écouteurs](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Une fois les opérations destinées à ajouter l’écouteur terminées, vous pouvez ajouter des instructions de suivi à votre code.

### <a name="to-add-trace-statement-to-your-code"></a>Pour ajouter des instructions de suivi à votre code
1. Ouvrez un fichier source pour votre application. Par exemple, le fichier <RoleName>.cs pour le rôle de travail ou le rôle web.
2. Ajoutez le code suivant à l’aide d’une instruction s’il n’a pas été encore ajouté :
    ```
        using System.Diagnostics;
    ```
3. Ajoutez les instructions de suivi à l’endroit où vous souhaitez capturer des informations sur l’état de votre application. Vous pouvez utiliser différentes méthodes pour mettre en forme la sortie de l’instruction de suivi. Pour plus d’informations, consultez [Procédure : Ajout d’instructions de suivi au code d’application](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Enregistrez le fichier source.

