---
title: Granska faktureringsdata för Azure-företagsregistrering med REST API
description: Lär dig hur du använder Azure REST-API:er för att granska faktureringsinformation för företagsregistrering.
author: lleonard-msft
ms.service: cost-management-billing
ms.topic: article
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 4d42a9cfa15b532a98b632331f592d7e51bc09f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202870"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Granska fakturering för företagsregistrering med hjälp av REST-API:er

Rapporterings-API:er i Azure hjälper dig att granska och hantera dina Azure-kostnader.

I den här artikeln lär du dig att hämta faktureringsinformation som är associerad med faktureringskonton, avdelningskonton eller Enterprise-avtalsregistreringskonton (EA) med hjälp av Azure REST:erna.

## <a name="individual-account-billing"></a>Fakturering för enskilt konto

Så här hämtar du användningsinformation för konton på en avdelning:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametern `{billingAccountId}` är obligatorisk och ska innehålla ID:t för kontot.

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella faktureringsperioden. Av prestandaskäl returnerar synkrona anrop information för den senaste månaden.  Du kan även anropa [API:et asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data för 36 månader.


## <a name="response"></a>Svar  

Statuskoden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerade kostnader för kontot.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortat; en fullständig beskrivning av varje svarsfält och felhantering finns i [Hämta användningsinformation för ett faktureringskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy).

## <a name="department-billing"></a>Avdelningsfakturering

Hämta aggregerad användningsinformation för alla konton på en avdelning.

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametern `{departmentId}` är obligatorisk och ska innehålla ID:t för avdelningen i registreringskontot.

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella faktureringsperioden. Av prestandaskäl returnerar synkrona anrop information för den senaste månaden.  Du kan även anropa [API:et asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data för 36 månader.

### <a name="response"></a>Svar  

Statuskoden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerad användningsinformation samt kostnader för en viss faktureringsperiod och faktura-ID för avdelningen.


I följande exempel visas utdata från REST-API:et för avdelning `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortat; en fullständig beskrivning av varje svarsfält och felhantering finns i [Hämta användningsinformation för en avdelning](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy).

## <a name="enrollment-account-billing"></a>Fakturering för registreringskonto

Hämta aggregerad användningsinformation för registreringskontot.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametern `{enrollmentAccountId}` är obligatorisk och ska innehålla ID:t för registreringskontot.

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella faktureringsperioden. Av prestandaskäl returnerar synkrona anrop information för den senaste månaden.  Du kan även anropa [API:et asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data för 36 månader.

### <a name="response"></a>Svar  

Statuskoden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerad användningsinformation samt kostnader för en viss faktureringsperiod och faktura-ID för avdelningen.

I följande exempel visas utdata från REST-API:et för företagsregistrering `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```

Det här exemplet är förkortat; en fullständig beskrivning av varje svarsfält och felhantering finns i [Hämta användningsinformation för ett registreringskonto](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy).

## <a name="next-steps"></a>Nästa steg
- Granska [Översikt över företagsrapportering](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersöka [REST API för företagsfakturering](https://docs.microsoft.com/rest/api/billing/)   
- [Komma igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
