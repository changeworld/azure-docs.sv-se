---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371793"
---
Följande krav måste uppfyllas för att en anordningstunnel ska kunna upprättas:

* Enheten måste vara en domän ansluten dator som kör Windows 10 Enterprise eller Education version 1809 eller senare.
* Tunneln kan bara konfigureras för den inbyggda VPN-lösningen i Windows och upprättas med IKEv2 med autentisering av datorcertifikat.
* Endast en enhetstunnel kan konfigureras per enhet.

1. Installera klientcertifikat på Windows 10-klienten med hjälp av [punkt-till-plats-VPN-klientartikeln.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) Certifikatet måste finnas i arkivet Lokal dator.
1. Skapa en VPN-profil och konfigurera enhetstunneln i kontexten för local system-kontot med hjälp av [dessa instruktioner](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Konfigurationsexempel för enhetstunnel

När du har konfigurerat den virtuella nätverksgatewayen och installerat klientcertifikatet i arkivet Lokal dator på Windows 10-klienten använder du följande exempel för att konfigurera en klientenhetstunnel:

1. Kopiera följande text och spara den som ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopiera följande text och spara den som ***VPNProfile.xml*** i samma mapp som **devicecert.ps1**. Redigera följande text så att den matchar din miljö.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Ladda ner **PsExec** från [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) och extrahera filerna till **C:\PSTools**.
1. Starta PowerShell från en admin CMD-prompt genom att köra:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. I PowerShell växlar du till mappen där **devicecert.ps1** och **VPNProfile.xml** finns och kör följande kommando:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest (MachineCert)](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Kör **rasphone**.

   ![Rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Leta efter **MachineCertTest-posten** och klicka på **Anslut**.

   ![Anslut](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Om anslutningen lyckas startar du om datorn. Tunneln ansluts automatiskt.
