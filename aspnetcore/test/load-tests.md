---
title: ASP.NET Tests de charge/stress de base
author: Jeremy-Meng
description: Renseignez-vous sur plusieurs outils et approches notables pour les tests de charge et les tests de résistance ASP.NET les applications Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664688"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Tests de charge/stress de base

Les tests de charge et les tests de résistance sont importants pour s’assurer qu’une application Web est performante et évolutive. Leurs objectifs sont différents même s’ils partagent souvent des tests similaires.

**Test de charge** &ndash; Si l’application peut gérer une charge spécifiée d’utilisateurs pour un certain scénario tout en satisfaisant l’objectif de réponse. L’application est exécutée dans des conditions normales.

**Tests de stress** &ndash; Testez la stabilité de l’application lorsque vous courez dans des conditions extrêmes, souvent pendant une longue période de temps. Les tests placent une charge utilisateur élevée, soit des pics ou une augmentation progressive de la charge, sur l’application, ou ils limitent les ressources informatiques de l’application.

Les tests de résistance déterminent si une application stressée peut se remettre d’une défaillance et revenir gracieusement au comportement attendu. Sous le stress, l’application n’est pas exécutée dans des conditions normales.

Visual Studio 2019 est la dernière version de Visual Studio avec des fonctionnalités de test de charge. Pour les clients nécessitant des outils de test de charge à l’avenir, nous recommandons d’autres outils, tels que Apache JMeter, Akamai CloudTest et BlazeMeter. Pour plus d’informations, voir le [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Outils Visual Studio

Visual Studio permet aux utilisateurs de créer, de développer et de déboiffer des tests de performance web et de charge. Une option est disponible pour créer des tests en enregistrant des actions dans un navigateur Web.

Pour plus d’informations sur la façon de créer, configurer et exécuter un projet de test de chargement à l’aide de Visual Studio 2017, voir [Quickstart: Créer un projet de test de charge](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Les tests de chargement peuvent être configurés pour s’exécuter sur place ou s’exécuter dans le cloud à l’aide d’Azure DevOps.

## <a name="third-party-tools"></a>Outils tiers

La liste suivante contient des outils de performance Web tiers avec divers ensembles de fonctionnalités :

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6 (en)](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling (en)](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

