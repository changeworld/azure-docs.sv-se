---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187265"
---
## <a name="scenario"></a>Scenario
En virtuell dator med ett enda nätverkskort skapas och ansluts till ett virtuellt nätverk. Den virtuella datorn kräver tre olika *privata* IP-adresser och två *offentliga* IP-adresser. IP-adresserna tilldelas följande IP-konfigurationer:

* **IPConfig-1:** Tilldelar en *statisk* privat IP-adress och en *statisk* offentlig IP-adress.
* **IPConfig-2:** Tilldelar en *statisk* privat IP-adress och en *statisk* offentlig IP-adress.
* **IPConfig-3:** Tilldelar en *statisk* privat IP-adress och ingen offentlig IP-adress.
  
    ![Flera IP-adresser](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

IP-konfigurationerna är associerade till nätverkskortet när nätverkskortet skapas och nätverkskortet är kopplat till den virtuella datorn när den virtuella datorn skapas. De typer av IP-adresser som används för scenariot är för illustration. Du kan tilldela den IP-adress och de tilldelningstyper du behöver.

> [!NOTE]
> Även om stegen i den här artikeln tilldelar alla IP-konfigurationer till ett enda nätverkskort, kan du också tilldela flera IP-konfigurationer till alla nätverkskort i en virtuell dator med flera NIC.Though the steps in this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations to any NIC in a multi-NIC VM. Om du vill veta hur du skapar en virtuell dator med flera nätverkskort läser du artikeln [Skapa en virtuell dator med flera nätverkskort.](../articles/virtual-machines/windows/multiple-nics.md)