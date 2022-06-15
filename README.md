# CyberSource 3-D Secure 2.0 API

## Related Documents

- [CyberSource Simple Order API for Java](https://github.com/Cybersource/cybersource-sdk-java) - github
- [CyberSource PHP Client](https://github.com/CyberSource/cybersource-sdk-php) - github
- [Simple order API](https://www.cybersource.com/en-us/support/technical-documentation/apis-and-integration.html#SimpleOrderAPI)
- [CyberSource Simple Order API Client](https://developer.cybersource.com/library/documentation/dev_guides/Simple_Order_API_Clients/Client_SDK_SO_API.pdf) - PDF
- [Credit Card Services Simple Order API](https://docs.cybersource.com/content/dam/new-documentation/documentation/en/credit-card/developer/migs/so/credit-card-services-so-migs.pdf) - PDF
- [Payer Authentication Simple Order API](https://docs.cybersource.com/content/dam/new-documentation/documentation/en/fraud-management/payer-auth/so/payer-auth-so.pdf) - PDF
- [API Field Reference Guides](https://docs.cybersource.com/en/reference/api-fields.html)

## Overview

![](https://straal.com/app/uploads/2021/05/3DS2_3.png)

ref: https://straal.com/what-is-3ds-2-0-thoughts-concerns-and-threats/

https://3dsecure2.com

- [Frictionless Flow](https://3dsecure2.com/frictionless-flow/)
- [Non-Payment Authentication](https://3dsecure2.com/non-payment-authentication/)
- [Native Mobile Integration](https://3dsecure2.com/native-mobile-integration/)

## Implementing Cardinal Cruise Direct Connection API Payer Authentication

### Step 1: Payer Authentication Setup Service

__Request__

```
billTo_city=Mountain View
billTo_country=US
billTo_email=test@yahoo.com
billTo_firstName=Tanya
billTo_lastName=Lee
billTo_postalCode=94043
billTo_state=CA
billTo_street1=1234 Gold Ave
card_accountNumber=XXXXXXXXXXXXXXXX
card_cardType=001
card_expirationMonth=12
card_expirationYear=2030
merchantID=patest
merchantReferenceCode=0001
payerAuthSetupService_run=true
```

__Response__

```
decision=ACCEPT
merchantReferenceCode=0001
payerAuththSetupReply_deviceDataCollectionURL=https://centinelapistag.cardinalcommerce.com/V1/Cruise/Collect
payerAuthSetupReply_reasonCode=100
payerAuthSetupReply_referenceID=f13fe5e0-9b47-4ea1-a03a-ec360f4d0f9f
payerAuthSetupReply_accessToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI1MDc4OTI0Mi0zYmEzLTRhZTItYWQwOS1kZjZkODk2NWQ5MjciLCJpYXQiOjE1OTgyOTk1MjQsImlzcyI6IjVkZDgzYmYwMGU0MjNkMTQ5OGRjYmFjYSIsImV4cCI6MTU5ODMwMzEyNCwiT3JnVW5pdElkIjoiNTVlZjNmMTBmNzIzYWE0MzFjOTliNWViIiwiUGF5bG9hZCI6eyJBQ1NVcmwiOiJodHRwczovLzBtZXJjaGFudGFjc3N0YWcuY2FyZGluYWxjb21tZXJjZS5jb20vTWVyY2hhbnRBQ1NXZWIvY3JlcS5qc3AiLCJQYXlsb2FkIjoiZXlKdFpYTnpZV2RsVkhsd1pTSTZJa05TWlhFaUxDSnRaWE56WVdkbFZtVnljMmx2YmlJNklqSXVNaTR3SWl3aWRHaHlaV1ZFVTFObGNuWmxjbFJ5WVc1elNVUWlPaUkzTkRNeVlUWXdNQzA0TXpNMkxUUm1PRGN0WVdKbE9TMDJObVkzTkRFM01EaGhNV1FpTENKaFkzTlVjbUZ1YzBsRUlqb2lPR0U1TkRkaU9ETXRNRFJpTkMwMFltVmlMV0V5WWpNdFpHTmpNV0UxWmprMFlURXlJaXdpWTJoaGJHeGxibWRsVjJsdVpHOTNVMmw2WlNJNklqQXlJbjAiLCJUcmFuc2FjdGlvbklkIjoiVEQ1b1MwbzFGQzY1cWF2MHhzeDAifSwiT2JqZWN0aWZ5UGF5bG9hZCI6dHJ1ZSwiUmV0dXJuVXJsIjoiaHR0cHM6Ly9leGFtcGxlLmNvbS9zdGVwLXVwLXJldHVybi11cmwuanNwIn0.8wZ8XhLgOIIRvgEUugvYrRAi-efavZTNM0tWInYLTfE
payerAuthSetupReply_reasonCode=100
requestID=5982993692286989203011
requestToken=AxjzbwSTRFa3h+A4wXZDABEBURwlqraRpAy7gDthk0kyro9JLIYA8AAA2wK2
```

__Important Response Fields__

- payerAuththSetupReply_deviceDataCollectionURL
- payerAuthSetupReply_accessToken

### Step 2: Device Data Collection Iframe

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>

<iframe id="cardinal_collection_iframe" name="collectionIframe" height="10" width="10" style="display: none;"></iframe>

<form id="cardinal_collection_form" method="POST" target="collectionIframe" action="{payerAuththSetupReply_deviceDataCollectionURL}">
    <input id="cardinal_collection_form_input" type="hidden" name="JWT" value="{payerAuthSetupReply_accessToken}">
</form>

</body>
<script>
    window.onload = function() {
        var cardinalCollectionForm = document.querySelector('#cardinal_collection_form');
        if (cardinalCollectionForm) cardinalCollectionForm.submit();
    }

    window.addEventListener('message', function(event) {
        if (event.origin === 'https://centinelapistag.cardinalcommerce.com') {
            console.log(event.data);
        }
    }, false);
</script>
</html>
```

### Step 3: Payer Authentication Check Enrollment Service

__Request__

```
billTo_city=Mountain View
billTo_country=US
billTo_email=test@yahoo.com
billTo_firstName=Tanya
billTo_lastName=Lee
billTo_postalCode=94043
billTo_state=CA
billTo_street1=1234 Gold Ave
card_accountNumber=XXXXXXXXXXXXXXXX
card_cardType=001
card_cvNumber=111
card_expirationMonth=12
card_expirationYear=2030
ccAuthService_run=true
merchantID=patest
merchantReferenceCode=0001
payerAuthEnrollService_referenceID=f13fe5e0-9b47-4ea1-a03a-ec360f4d0f9f
payerAuthEnrollService_returnURL=https://example.com/step-up-return-url.jsp
payerAuthEnrollService_run=true
purchaseTotals_currency=USD
purchaseTotals_grandTotalAmount=30.00
```

__Respose__

```
decision=REJECT
merchantReferenceCode=0001
payerAuthEnrollReply_accessToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI1MDc4OTI0Mi0zYmEzLTRhZTItYWQwOS1kZjZkODk2NWQ5MjciLCJpYXQiOjE1OTgyOTk1MjQsImlzcyI6IjVkZDgzYmYwMGU0MjNkMTQ5OGRjYmFjYSIsImV4cCI6MTU5ODMwMzEyNCwiT3JnVW5pdElkIjoiNTVlZjNmMTBmNzIzYWE0MzFjOTliNWViIiwiUGF5bG9hZCI6eyJBQ1NVcmwiOiJodHRwczovLzBtZXJjaGFudGFjc3N0YWcuY2FyZGluYWxjb21tZXJjZS5jb20vTWVyY2hhbnRBQ1NXZWIvY3JlcS5qc3AiLCJQYXlsb2FkIjoiZXlKdFpYTnpZV2RsVkhsd1pTSTZJa05TWlhFaUxDSnRaWE56WVdkbFZtVnljMmx2YmlJNklqSXVNaTR3SWl3aWRHaHlaV1ZFVTFObGNuWmxjbFJ5WVc1elNVUWlPaUkzTkRNeVlUWXdNQzA0TXpNMkxUUm1PRGN0WVdKbE9TMDJObVkzTkRFM01EaGhNV1FpTENKaFkzTlVjbUZ1YzBsRUlqb2lPR0U1TkRkaU9ETXRNRFJpTkMwMFltVmlMV0V5WWpNdFpHTmpNV0UxWmprMFlURXlJaXdpWTJoaGJHeGxibWRsVjJsdVpHOTNVMmw2WlNJNklqQXlJbjAiLCJUcmFuc2FjdGlvbklkIjoiVEQ1b1MwbzFGQzY1cWF2MHhzeDAifSwiT2JqZWN0aWZ5UGF5bG9hZCI6dHJ1ZSwiUmV0dXJuVXJsIjoiaHR0cHM6Ly9leGFtcGxlLmNvbS9zdGVwLXVwLXJldHVybi11cmwuanNwIn0.8wZ8XhLgOIIRvgEUugvYrRAi-efavZTNM0tWInYLTfE
payerAuthEnrollReply_acsTransactionID=8a947b83-04b4-4beb-a2b3-dcc1a5f94a12
payerAuthEnrollReply_acsURL=https://0merchantacsstag.cardinalcommerce.com/MerchantACSWeb/creq.jsp
payerAuthEnrollReply_authenticationTransactionID=TD5oS0o1FC65qav0xsx0
payerAuthEnrollReply_cardBin=40000000
payerAuthEnrollReply_cardTypeName=VISA
payerAuthEnrollReply_challengeRequired=false
payerAuthEnrollReply_directoryServerTransactionID=395fb036-cfc6-462b-b28d-d6ed7c970cdd
payerAuthEnrollReply_paReq=eyJtZXNzYWdlVHlwZSI6IkNSZXEiLCJtZXNzYWdlVmVyc2lvbiI6IjIuMi4wIiwidGhyZWVEU1NlcnZlclRyYW5zSUQiOiI3NDMyYTYwMC04MzM2LTRmODctYWJlOS02NmY3NDE3MDhhMWQiLCJhY3NUcmFuc0lEIjoiOGE5NDdiODMtMDRiNC00YmViLWEyYjMtZGNjMWE1Zjk0YTEyIiwiY2hhbGxlbmdlV2luZG93U2l6ZSI6IjAyIn0
payerAuthEnrollReply_reasonCode=475
payerAuthEnrollReply_specificationVersion=2.2.0
payerAuthEnrollReply_stepUpUrl=https://centinelapistag.cardinalcommerce.com/V2/Cruise/StepUp
payerAuthEnrollReply_threeDSServerTransactionID=7432a600-8336-4f87-abe9-66f741708a1d
payerAuthEnrollReply_veresEnrolled=Y
reasonCode=475
requestID=5982995245816268803007
requestToken=AxjzbwSTRFa9DM1xnUu/ABEBURwlqsQ5pAy7gDtXb0kyro9JLIYA8AAA2wK2
```

__Important Response Fields__

When you receive reason code 475, you also receive the following fields:

- payerAuthEnrollReply_stepUpUrl
- payerAuthEnrollReply_accessToken
- payerAuthEnrollReply_paReq

__Base64 encode value__

```
eyJtZXNzYWdlVHlwZSI6IkNSZXEiLCJtZXNzYWdlVmVyc2lvbiI6IjIuMi4wIiwidGhyZWVEU1NlcnZlclRyYW5zSUQiOiI3NDMyYTYwMC04MzM2LTRmODctYWJlOS02NmY3NDE3MDhhMWQiLCJhY3NUcmFuc0lEIjoiOGE5NDdiODMtMDRiNC00YmViLWEyYjMtZGNjMWE1Zjk0YTEyIiwiY2hhbGxlbmdlV2luZG93U2l6ZSI6IjAyIn0
```

This is an example for the decoded value

```json
{
  "messageType": "CReq",
  "messageVersion": "2.2.0",
  "threeDSServerTransID": "7432a600-8336-4f87-abe9-66f741708a1d",
  "acsTransID": "8a947b83-04b4-4beb-a2b3-dcc1a5f94a12",
  "challengeWindowSize": "02"
}
```

|challengeWindowSize| Iframe Dimensions (Width x Height)|
|---| ---|
|01| 250 x 400|
|02| 390 x 400|
|03| 500 x 600|
|04| 600 x 400|
|05| Full screen|


### Step 4: Step-Up IFrame

You need to perform this step only when Step 3 indicates step-up authentication is required.

```html
<iframe name="step-up-iframe" width="400" height="400"></iframe>
<form id="step-up-form" target="step-up-iframe" method="post" action="{payerAuthEnrollReply_stepUpUrl}">
    <input type="hidden" name="JWT" value="{payerAuthEnrollReply_accessToken}" />
    <input type="hidden" name="MD" value="optionally_include_custom_data_that_will_be_returned_as_is" />
</form>

<script>
    window.onload = function() {
        var stepUpForm = document.querySelector('#step-up-form');
        if (stepUpForm) stepUpForm.submit();
    }
</script>
```

### Step 5: Payer Authentication Validation Service

## Testing Payer Authentication

## Alternate Methods for Device Data Collection
