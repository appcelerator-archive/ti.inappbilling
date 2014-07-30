# Ti.InAppBilling Module

## Description

The InAppBilling module allows you to access the Android In-App Billing mechanism. 

## Dependencies

* This module requires Titanium SDK 3.2.2.GA or later.
* This module must be tested on a device, testing on an emulator will not work.
* Your test device should be running on Android SDK Version 2.2 (API level 8) or higher, and have Google Play client Version 3.9.16 or higher installed.

## In-app Billing Resources

* [In-app Billing Version 3 documentation](http://developer.android.com/google/play/billing/api.html)
* [Add Your Application to the Developer Console](http://developer.android.com/training/in-app-billing/preparing-iab-app.html#AddToDevConsole)
* [Specify In-app Products in Google Play](http://developer.android.com/training/in-app-billing/list-iab-products.html)
* [Testing Your In-app Billing Application](http://developer.android.com/training/in-app-billing/test-iab-app.html)

## Getting Started

View the [Using Titanium Modules](http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules) document for instructions on getting
started with using this module in your application.

## Important Notes

### Initializing the module

The `startSetup` method must be called and the `setupcomplete` event must have a RESULT_OK `responseCode` before calling any other module methods. If a RESULT_OK `responseCode` is returned then in-app billing v3 is supported. To check if subscription are supported use the `subscriptionsSupported` method.

### Unmanaged Products

Unmanaged products behave differently if you are using in-app billing v3 rather than in-app billing v2. In in-app billing v3, Unmanaged products are treated as Managed products and will need to be explicitly consumed.

### Testing

The app must be signed to make a purchase, either test or real. Only static responses do not require your app to be signed. The easiest way to do this is to package the app for production using Titanium/Appcelerator Studio and installing that app on your test device. Note that the apk uploaded to the developer console must be signed as well.

### Testing with static responses

It is possible to test using [static responses from Google Play][TestingStatic]. Testing static responses for product ids "android.test.canceled" and "android.test.refunded" will produce a IAB_RESULT_UNKNOWN_ERROR `responseCode`, this is expected behavior. Using "android.test.purchased" and "android.test.item_unavailable" will produce expected results.

### Verify the Signature

The signature of received purchases will be verified automatically. If there is a signature verification failure, the `responseCode` of the associated event will be IAB_RESULT_VERIFICATION_FAILED.

### Verify the Developer Payload

It is important to verify that the developer payload of the purchase is correct. It will be
the same one that you sent when initiating the purchase.

**WARNING:** Locally generating a random string when starting a purchase and
verifying it here might seem like a good approach, but this will fail in the
case where the user purchases an item on one device and then uses your app on
a different device, because on the other device you will not have access to the
random string you originally generated.

So a good developer payload has these characteristics:

1. If two different users purchase an item, the payload is different between them,
   so that one user's purchase can't be replayed to another user.

2. The payload must be such that you can verify it even when the app wasn't the
   one who initiated the purchase flow (so that items purchased by the user on
   one device work on other devices owned by the user).

Using your own server to store and verify developer payloads across app
installations is recommended.

## Accessing the Module

Use `require` to access this module from JavaScript:

	var InAppBilling = require("ti.inappbilling");

The InAppBilling variable is a reference to the Module object.

## Breaking Changes

As of version 3.0.0 of this module, in-app billing v3 is supported. This upgrade caused a number of breaking changes. Do not use this module with older versions of the example application(s) as it will not work. Refer to the documentation and example application(s) for the current way to use the module.

## Methods

### void startSetup(args)
Starts the setup process for the module, the setup is complete when the `setupcomplete` event is fired. This method should be called before calling any other method and should only be called once. Calling this method results in a `setupcomplete` event.

* __args__[object]
	* __publicKey__[string]: Base64-encoded RSA public key, get this from the [Google Developer Console][GoogleDevConsole]
	* __debug__[boolean]: Used to print module debug logs in the console (options)(default: false).

#### Example
	InAppBilling.startSetup({
	    publicKey: "<< Public Key >>"
	});

### boolean subscriptionsSupported()
Check if subscriptions are supported on the current device.

#### Example
	InAppBilling.subscriptionsSupported();

### void queryInventory(args)
Queries the inventory. This will query all owned items from the server, as well as information on additional productIds. Calling this method results in a `queryinventorycomplete` event.

Calling this method with no arguments will retrieve all owned purchases. More items details can be retrieved by passing their productId even if they are not owned.

* __args__[object] (optional)
	* __queryDetails__[boolean]: Controls if details will be retrieved or not (optional)(default: true).
	* __moreItems__[string[]]: An array of productIds to be retrieved, will be ignored if queryDetails is false (optional).
	* __moreSubs__[string[]]: An array of subscription productIds to be retrieved, will be ignored if queryDetails is false (optional).

#### Example
	InAppBilling.queryInventory({
        moreItems: ['gas'],
        moreSubs: ['infinite_gas']
    });

### void purchase(args)
Initiate the UI flow for an in-app purchase. Call this method to initiate an in-app purchase, which will involve bringing up the Google Play screen. Calling this method results in a `purchasecomplete` event.

* __args__[object]
	* __productId__[string]: The productId of the product to be purchased.
	* __type__[string]: The type of product to be purchased (ITEM_TYPE_INAPP or ITEM_TYPE_SUBSCRIPTION).
	* __developerPayload__[string]: The payload that will be returned with the completed purchase (optional).

**Note:** It is important to verify the developer payload when it is returned in a [Ti.InAppBilling.Purchase][].

#### Example
	InAppBilling.purchase({
    	productId: 'gas',
    	type: InAppBilling.ITEM_TYPE_INAPP,
        developerPayload: '<< Developer Payload >>'
    });

### void consume(args)
Consumes a given in-app product. Consuming can only be done on an item that's owned, and as a result of consumption, the user will no longer own it. Calling this method results in a `consumecomplete` event.

* __args__[object]
	* __purchases__[[Ti.InAppBilling.Purchase][][]]: An Array of [Ti.InAppBilling.Purchase][]s

#### Example
	InAppBilling.consume({
        purchases: [purchaseToConsume]
    });

## Properties
  
### ITEM_TYPE_INAPP [string] (read-only)
Used to define the product type as a managed in-app product when purchasing a product.

### ITEM_TYPE_SUBSCRIPTION [string] (read-only)
Used to define the product type as a subscription when purchasing a product. Recurring monthly or annual billing product.

### PURCHASE_STATE_PURCHASED [int] (read-only)
Used to determine the state of a [Ti.InAppBilling.Purchase][].

### PURCHASE_STATE_CANCELED [int] (read-only)
Used to determine the state of a [Ti.InAppBilling.Purchase][].

### PURCHASE_STATE_REFUNDED [int] (read-only)
Used to determine the state of a [Ti.InAppBilling.Purchase][].

### RESULT_OK [int] (read-only)
Result code returned by Google Play when an operation was successful.

### RESULT_USER_CANCELED [int] (read-only)
Result code returned by Google Play when an operation was canceled by the user.

### RESULT_BILLING_UNAVAILABLE [int] (read-only)
Result code returned by Google Play when an operation fails due to billing being unavailable.

### RESULT_ITEM_UNAVAILABLE [int] (read-only)
Result code returned by Google Play when an operation fails due to the item being unavailable.

### RESULT_DEVELOPER_ERROR [int] (read-only)
Result code returned by Google Play when an operation fails due to a developer error.

### RESULT_ERROR [int] (read-only)
Result code returned by Google Play when an operation fails due to an unknown error.

### RESULT_ITEM_ALREADY_OWNED [int] (read-only)
Result code returned by Google Play when an operation fails due to the item already being owned.

### RESULT_ITEM_NOT_OWNED [int] (read-only)
Result code returned by Google Play when an operation fails due to the item not being owned.

### IAB_RESULT_REMOTE_EXCEPTION [int] (read-only)
Result code returned by the module when an operation fails due to a remote exception.

### IAB_RESULT_BAD_RESPONSE [int] (read-only)
Result code returned by the module when an operation fails due to a bad response.
    
### IAB_RESULT_VERIFICATION_FAILED [int] (read-only)
Result code returned by the module when an operation fails due to a verification error. This is related to verifying the signature of a purchase.

### IAB_RESULT_SEND_INTENT_FAILED [int] (read-only)
Result code returned by the module when an purchase fails due to a failure sending the intent.

### IAB_RESULT_UNKNOWN_PURCHASE_RESPONSE [int] (read-only)
Result code returned by the module when an purchase fails due to an unknown purchase response.

### IAB_RESULT_MISSING_TOKEN [int] (read-only)
Result code returned by the module when an consume fails due to a missing token.

### IAB_RESULT_UNKNOWN_ERROR [int] (read-only)
Result code returned by the module when an consume fails due to a missing token.

### IAB_RESULT_SUBSCRIPTIONS_NOT_AVAILABLE [int] (read-only)
Result code returned by the module when an operation fails due subscriptions not being available.

### IAB_RESULT_INVALID_CONSUMPTION [int] (read-only)
Result code returned by the module when an consume fails due to the consumption being invalid.

## Events

### setupcomplete
Occurs as a result of calling the `startSetup` method. Do not call any other module methods before this event comes back successful. 

The event object will have the following properties:

* __success__[boolean]: Convenience property will be true if the operation was successful.
* __responseCode__[int]: A result code indicating the result of the operation. For possible value, see constants starting with RESULT and IAB_RESULT.

### queryinventorycomplete
Occurs as a result of calling the `queryInventory` method.

The event object will have the following properties:

* __success__[boolean]: A convenience property that will be true if the operation was successful.
* __responseCode__[int]: A result code indicating the result of the operation. For possible value, see constants starting with RESULT and IAB_RESULT.
* __inventory__[[Ti.InAppBilling.Inventory][]]: The inventory of products returned by a successful inventory query.

### purchasecomplete
Occurs as a result of calling the `purchase` method.

The event object will have the following properties:

* __success__[boolean]: A convenience property that will be true if the operation was successful.
* __responseCode__[int]: A result code indicating the result of the operation. For possible value, see constants starting with RESULT and IAB_RESULT.
* __purchase__[[Ti.InAppBilling.Purchase][]]: The result of a successful purchase.

### consumecomplete
Occurs as a result of calling the `consume` method.

The event object will have the following properties:

* __success__[boolean]: A convenience property that will be true if the operation was successful.
* __responseCode__[int]: A result code indicating the result of the operation. For possible value, see constants starting with RESULT and IAB_RESULT.
* __purchase__[[Ti.InAppBilling.Purchase][]]: The [Ti.InAppBilling.Purchase][] associated with the event.	

# Usage

See the `example` application and Android documentation. 

## Author

Developed for Appcelerator by Logical Labs
Alexander Conway (Logical Labs) and Jon Alter

## Module History

View the [change log](changelog.html) for this module.

## Feedback and Support

Please direct all questions, feedback, and concerns to [info@appcelerator.com](mailto:info@appcelerator.com?subject=Android%20InAppBilling%20Module).

## License

Copyright(c) 2010-2014 by Appcelerator, Inc. All Rights Reserved. Please see the LICENSE file included in the distribution for further details.


[GoogleDevConsole]: https://play.google.com/apps/publish/
[TestingStatic]: http://developer.android.com/google/play/billing/billing_testing.html#billing-testing-static
[Ti.InAppBilling.Purchase]: purchase.html
[Ti.InAppBilling.Inventory]: inventory.html
