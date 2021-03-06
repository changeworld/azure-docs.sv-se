---
title: Ange marketplace-avbildningar för ett labb i Azure Lab Services
description: Den här artikeln visar hur du anger Marketplace-avbildningar som labbskaparen kan använda för att skapa labb i ett labbkonto i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257714"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Ange Marketplace-avbildningar som är tillgängliga för labbuppgiftsförfattare
Som labbkontoägare kan du ange Marketplace-avbildningar som labbskapare kan använda för att skapa labb i labbkontot. 

## <a name="select-images-available-for-labs"></a>Välj bilder som är tillgängliga för laboratorier
Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan så att endast aktiverade/inaktiverade bilder visas genom att välja alternativet **Endast**/aktiverat**inaktiverat endast** i listrutan högst upp. 
    
![Sida för Marketplace-avbildningar](../media/tutorial-setup-lab-account/marketplace-images-page.png)

De Marketplace-avbildningar som visas i listan är bara de filer som uppfyller följande villkor:
    
- Skapar en enskild virtuell dator.
- Använder Azure Resource Manager för att etablera virtuella datorer
- Kräver inte att du köper en extra licensieringsplan

## <a name="disable-images-for-a-lab"></a>Inaktivera bilder för ett labb 
Om du vill inaktivera en enda bild för ett labb väljer du **... (ellips)** i den sista kolumnen och välj **Inaktivera bild**. 

![Inaktivera en avbildning](../media/tutorial-setup-lab-account/disable-one-image.png) 

Du kan också markera kryssrutan före bildnamnet och välja **Inaktivera markerade bilder** i verktygsfältet. 

Om du vill inaktivera flera bilder samtidigt markerar du kryssrutor före bildnamnen och väljer **Inaktivera markerade bilder** i verktygsfältet. 

![Inaktivera flera avbildningar](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Aktivera avbildningar för ett labb
Om du vill aktivera en inaktiverad bild väljer du **... (ellips)** i den sista kolumnen och välj **Aktivera bild**. Du kan också markera kryssrutan före bildnamnet och välja **Aktivera markerade bilder** i verktygsfältet. 

Om du vill inaktivera flera bilder samtidigt markerar du kryssrutor före bildnamnen och väljer **Aktivera markerade bilder** i verktygsfältet. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Aktivera bilder när labbet skapas
Du kan aktivera fler avbildningar när labbet skapas: 

1. Logga in på [Azure Lab Services-webbplatsen](https://labs.azure.com) med hjälp av autentiseringsuppgifter för **labbkontoägare**
2. Välj standardavbildning för virtuella datorer eller nedpilen. 
3. Välj **Aktivera fler bildalternativ**. 

    ![Aktivera fler bildalternativ](../media/specify-marketplace-images/enable-more-images-menu.png)
4. Följ instruktionerna i föregående avsnitt för att aktivera de bilder du väljer. 
5. Du kan behöva stänga det **nya labbfönstret** och öppna det igen för att se bilderna du valde i föregående steg. 



## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare får du tillgång till klassrumslabb](how-to-use-classroom-lab.md)
