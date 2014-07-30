# Ti.InAppBilling.Purchase

## Description

Represents an in-app billing purchase.

## Properties

### type [string] (read-only)

A string representing the type of purchase (ITEM_TYPE_INAPP or ITEM_TYPE_SUBSCRIPTION);

### orderId [string] (read-only)

A unique order identifier for the transaction. This corresponds to the Google Wallet Order ID.

### packageName [string] (read-only)

The application package from which the purchase originated.

### productId [string] (read-only)

The item's product identifier. Every item has a product ID, which you must specify in the application's product list on the Google Play Developer Console.

### purchaseTime [number] (read-only)

The time the product was purchased, in milliseconds since the epoch (Jan 1, 1970).

### purchaseState [number] (read-only)

The state of the purchase (PURCHASE_STATE_PURCHASED, PURCHASE_STATE_CANCELED, or PURCHASE_STATE_REFUNDED).

### developerPayload [string] (read-only)

A developer-specified string that contains supplemental information about an order. You can specify a value for this field when you make a `purchase` request.

### token [string] (read-only)

A token that uniquely identifies a purchase for a given item and user pair.

