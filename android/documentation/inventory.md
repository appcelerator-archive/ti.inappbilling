# Ti.InAppBilling.Inventory

## Description

Represents an in-app billing inventory. An Inventory Object can be obtained by running a `queryInventory` and listening for a `queryinventorycomplete` event, the inventory will be a property of the event object for a successful query.

## Methods

### boolean hasDetails(productId)

Returns true if the inventory has details for the _productId_ passed in.

* __productId__[string]: The product's id

### object getDetails(productId)

Returns an object with details for the _productId_ passed in.

The object returned will have the following properties:

* __productId__[string]: The product's id.
* __type__[string]: The product's type (ITEM_TYPE_INAPP or ITEM_TYPE_SUBSCRIPTION).
* __price__[string]: The product's price.
* __price_amount_micros__[string]: The product's price amount micros.
* __price_currency_code__[string]: The product's price currency code.
* __title__[string]: The product's title.
* __description__[string]: The product's description.

### boolean hasPurchase(productId)

Returns true if the inventory has a [Ti.InAppBilling.Purchase][] for the _productId_ passed in.

* __productId__[string]: The product's id

### [Ti.InAppBilling.Purchase][] getPurchase(productId)

Returns the [Ti.InAppBilling.Purchase][] for the _productId_ passed in or null if none exist for that _productId_.

* __productId__[string]: The product's id

### void erasePurchase(productId)

Erase a purchase (locally) from the inventory, given its _productId_. This just modifies the Inventory locally and has no effect on the server. This is useful when you have an existing Inventory object which you know to be up to date, and you have just consumed an item successfully, which means that erasing its purchase data from the Inventory you already have is quicker than querying for a new Inventory.

* __productId__[string]: The product's id

[Ti.InAppBilling.Purchase]: purchase.html
