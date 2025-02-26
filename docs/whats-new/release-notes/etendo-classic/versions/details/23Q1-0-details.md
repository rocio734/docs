---
title: Details 23Q1.0
---
## EPL-574
### Upgrade Gradle Version to 7.3.2

**Issue Description**
The current version of Gradle used in the koodu_software/int_etendo_base, GitHub - etendosoftware/etendo_core: Etendo Classic source code  and koodu_software/etendo_gradle_plugin  needs to be updated to version 7.3.2 to take advantage of its improved performance and features. However, manual upgrades may not be feasible in all environments, and some systems may need to be updated manually to support the new version of Gradle. Furthermore, the upgrade may introduce changes to the project's behavior, and it is essential to test these changes thoroughly to minimize the risk of unexpected issues in production.

  
## EPL-531
### Undo post/unpost button

**Issue Description**
Delete Post/Unpost button and undo changes on accounting status column  from Goods Receipt, Goods Shipments, Physical Inventory, Transaction Tab from Financial Account, Payment In, Payment Out, Purchase Invoice and Sales Invoice windows from Core  Module.

**Solution Design**
- Activate the Core module.
- Then export the changes that always brings the module and create a patch with the diff that we obtained.
- Perform the following steps for each of the windows implicit in the description
- Copy the Display logic of the Post/Unpost button in Post.
- Delete the Post/Unpost button.
- Change in Post the display length, the description and the Help/Comment, uncheck Read Only, Show in grid view and Show in status bar.
- In the Column change the Reference to Button and the Reference Search Key to All_Posted Status, the Application Element to Posted-Posted, the description, the Help/Comment and the Length in some cases.
- Activate the Synchronize Terminology.

## EPL-535
### Add hooks in the AddPaymentActionHandler

**Issue Description**

Move the process payment that is process definition to action in order to be able to concatenate actions in the future. 

**Solution Design**

- Move to action
- Check that the behavior of the process has not been modified.
- Check if you can discriminate by window to allow multiple payments by WS
- Allow multiple selection when calling the process from a WS with the corresponding flag processByWebService
- Add the pre and post hooks that were implemented in the feature/EPL-537 branch

- Change the class that extends the process to Action, implement the action method, make some superior methods to transform the data and call the original method for the cases of 1 single payment either from the windows or by WS, I will also add a validation so that it is not allowed to call the process with more than one record selected unless it has the flag mentioned above to call by ws and process multiple payments.By WS only multiple payments will be able to be processed, the payments will have to be created previously with their respective details, etc.By WS you can create and process a payment passing the correct json.

URL: http://localhost:8080/etendo/sws/com.smf.securewebservices.kernel/org.openbravo.client.kernel?processId=9BED7889E1034FE68BD85D5D16857320&_action=org.openbravo.advpaymentmngt.actionHandler.
AddPaymentActionHandler

```
{"recordIds":["69A235939B474190B9559A31933A303C"],
  "_params":{
    "fin_payment_id":"69A235939B474190B9559A31933A303C",
    "ad_org_id":"E443A31992CB4635AFCAEABE7183CE85",
    "issotrx":false,
    "document_action":"25889363327D44FC851D97EA78F6BCFC",
    "c_currency_id":"102",
    "received_from":"858B90C7AF0A4533863EEC65437382BF",
    "actual_payment":"100",
    "payment_date":"2023-01-10",
    "difference":null,
    "conversion_rate":null,
    "converted_amount":null,
    "reference_no":"",
    "credit_to_use":{"_selection":[]},
    "order_invoice":{"_selection":[]},
    "glitem":{"_allRows":[]}
  },
"_entityName":"FIN_Payment"
}

To process multiple payments, you should send the following json

{"recordIds":["69A235939B474190B9559A31933A303C","123E1CF82DA4443EBE34F858409CDC0B"],

"document_action":"25889363327D44FC851D97EA78F6BCFC",
"processByWebService":true,
"_entityName": "FIN_Payment"
}
```

- If the payment is not well created, it will give error.
- Also modify the message capture to process multiple payments, and a commit will be made for each payment so that in case one payment fails it will not rollback all the payments.

## EPL-537
### Add hooks in the AddPaymentActionHandler
**Issue Description**
Create a possibility to implement hooks of pre and post payment to add validation or any develop before payment start the processing and after finish the process payment

**Solution Design**
- Create a interface called PaymentProcessHook with two methods called preProcess and posProcess this methods will have a JSONObjets as a parameter and return the same type
- Create an abstract class called PaymentProcessOrderHook with a method called getPriority and return an int
- Modify the class AddPaymentActionHandler and add the execution of this hooks when start the process and when finish the process

## EPL-529
### Add hooks in the process shipments
**Issue Description**
Allow hook of pre and post shipments, This functionanily add a hooks to implement for partners o customer

**Solution Design**
- Create a new interface called ProcessShipmentHook in org.openbravo.advpaymentmngt module
- Create a util called ProcessShipmentUtil copy from ProcessOrderUtil and do all necessary changes
- Change the method processShipment in ProcessShiment java and call to ProcessShipmentUtil to process the shipment.









