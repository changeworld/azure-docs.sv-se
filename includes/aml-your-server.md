---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485941"
---
1. Installera Azure Machine [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Learning SDK för python

1. Skapa en [Azure Machine Learning-arbetsyta](../articles/machine-learning/how-to-manage-workspace.md).

1. Skriv en [konfigurationsfilfil](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```bash
    jupyter notebook
    ```