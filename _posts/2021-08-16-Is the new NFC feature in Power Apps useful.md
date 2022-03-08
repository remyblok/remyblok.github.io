---
title: Is the new NFC feature in Power Apps useful?
tags: ["Power Platform", "Power Apps", "NFC"]
coauthors:
  - name: Joop Melchers 
    linkedin: https://www.linkedin.com/in/joopmelchers/
---
NFC stands for `Near Field Communication` and, as the name implies, it enables short-range communication between compatible devices and tags. Tags store data in a very small package. Tags do not require any power to work. Most NFC tags are in the form of stickers or cards.

The following NFC use cases are very recognizable:
- NFC for Mobile Payments.  
  Convenient: The convenience of Mobile payment is one of the greatest advantages of this technology. You can just hold your card against the device, you do no longer need to swipe it.

- NFC in Asset Tracking.  
  For example, asset return: NFC asset tracking system gives innovative ways to ensure that the item taken from the asset store is returned or not.

- NFC for Building security.  
  Seamless: High level of NFC encryption allows an organization to employ it as a security system as it will provide an ID of the person entering and exiting the premises.

In this post I will introduce what NFC functionality Microsoft introduced in Power Apps and an example how this can be used. Next to this, I will discuss the current limitations.
<!--more-->
Microsoft introduced the new NFC feature in June 2021 and makes it very easy to use this functionality in a Power App.

## Quick example
Create a new phone Canvas App in your [maker portal](https://make.powerapps.com/). Add a button on Screen 1 and add the following code in the button onSelect event.

```javascript
Set(varNFC, ReadNFC())
```

Save the App and run it on your phone and press the button and the App will prompt you to scan the NFC tag. After a successful scan the following information is available in your Power App:

| Column | Type | Description |
|:--|:--|:--|
| RTD | Text | The tag's Record Type Definition (RTD). Only `RTD_TEXT` and `RTD_URI` are supported at this time. |
| TNF | Number | The tag's Type Name Format (TNF). Only `TNF_WELL_KNOWN` is supported at this time. |
| Text | Text | The text payload of the NFC tag if RTD is `RTD_TEXT`, blank otherwise. |
| URI | Hyperlink | The URI payload of the NFC tag if RTD is `RTD_URI`, blank otherwise. |

## Possible applications
Product information: You can store the identifier of a product as text on the NFC tag that is attached to the item. As soon as the Power App scans the tag, the App can use the information on the tag. For example, to get information from you ERP to show product information or track maintenance history.

## Limitations
The reader only reads the first record on the NFC tag that is either of the type Text or URI, any other data on the NFC tag is ignored. Therefore it is not possible to read more complex information like records or other types of information. For example, if product information like ingredients is stored on a tag this can currently not be used. Or an NFC business card that stores the contact information like name, address, email in a record, this cannot be read by the Power Apps NFC reader.

## Conclusion
The current NFC implementation in Power Apps makes it very easy to build Apps that use this functionality. However, make sure the requirements match the current features. I expect that Microsoft will extend the possibilities of the NFC reader in future releases to make it even more useful.