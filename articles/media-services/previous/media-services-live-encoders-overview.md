---
title: Konfigurera lokala kodare när du använder Azure Media Services för att skapa flerbitriga strömmar | Microsoft-dokument
description: I det här avsnittet visas lokala live-kodare som du kan använda för att fånga dina livehändelser och skicka en enda bitrate live stream till AMS-kanaler (som är aktiverat för live-kodning) för vidare bearbetning. Avsnittet länkar till självstudier som visar hur du konfigurerar listade kodare.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 71a31228602ef161158eaa05c80d50f65de98a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133275"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Konfigurera lokala kodare när du använder Azure Media Services för att skapa flerbitratströmmar
I det här avsnittet visas lokala live-kodare som du kan använda för att fånga dina livehändelser och skicka en enda bitrate live stream till AMS-kanaler (som är aktiverat för live-kodning) för vidare bearbetning. Avsnittet länkar också till självstudier som visar hur du konfigurerar listade kodare.

> [!NOTE]
> Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.

## <a name="haivision-kb-encoder"></a>Haivision KB-kodare
Information om hur du [konfigurerar Haivision KB Encoder-kodaren](https://www.haivision.com/products/kb-series/) för att skicka en enda bithastighets direktuppspelning till en AMS-kanal finns i [Konfigurera Haivision KB-kodare](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Information om hur du konfigurerar [Telestream Wirecast-kodaren](https://www.telestream.net/wirecast/overview.htm) för att skicka en enda bithastighets direktuppspelning till en AMS-kanal finns i [Konfigurera Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NyaTek TriCaster
Information om hur du [konfigurerar Tricaster-kodaren](https://newtek.com/products/tricaster-40.html) för att skicka en enda bithastighets direktuppspelning till en AMS-kanal finns i [Konfigurera Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Mer information finns i [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

[Livestreaming med Azure Media Services för att skapa multibitrate-strömmar](media-services-manage-live-encoder-enabled-channels.md).

