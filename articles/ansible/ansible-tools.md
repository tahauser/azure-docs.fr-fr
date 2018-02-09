---
title: Outils pour utiliser Ansible avec Azure
description: Installer et utiliser des outils individuels pour Ansible avec Azure
ms.service: ansible
keywords: ansible, azure, devops, outils, code vs, visual studio code, extension
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Outils pour utiliser Ansible avec Azure

Les outils suivants facilitent l’utilisation d’Ansible et d’Azure et la rendent plus efficace.

## <a name="visual-studio-code-extension-for-ansible"></a>Extension de Visual Studio Code pour Ansible

[L’extension de Visual Studio Code pour Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) propose plusieurs fonctionnalités pour l’utilisation d’Ansible à de partir Visual Studio Code :

- La saisie automatique de directives, modules et plug-ins Ansible à partir de la documentation Ansible à mesure que vous tapez.
- L’affichage d’extraits de code lorsque vous cliquez sur &lt;Ctrl>&lt;Espace> pendant la création de vos playbooks Ansible.
- La mise en surbrillance de la syntaxe affiche votre code de playbook Ansible avec différentes couleurs et polices conformément à la syntaxe YAML.
- Des playbooks Ansible peuvent être exécutés à partir de la fenêtre de terminal Visual Studio Code.
    - (Windows uniquement) Ansible peut être exécuté à partir d’un conteneur Docker.
    - (Linux et macOS) Ansible peut être exécuté à partir d’un conteneur Docker ou à partir d’une installation Ansible locale. 
- Les playbooks Ansible peuvent être exécutés à partir d’Azure Cloud Shell.