# Helo orders - Webhooks

This article describes how to setup webhooks for receiving order updates (for orders you get through the HeloProtocol app).

> Simplicity is the ultimate sophistication
> -Leonardo da Vinci

There are 3 simple steps to follow:
1. **Webhook URL** - Email the webhook url to tech@heloprotocol.in and we'll configure it in our backend
2. **Staged order notifications** - STAGED orders are unpaid orders which are captured before the customer completes the payment. These can be mostly ignored.
3. **Payment notifications** - an order is PLACED once the payment is successful. This is a confirmed order that you should process.

## Payloads
**STAGED order - webhook payload**


```javascript
{
  "event": "order-created",
  "order": {
    "id": "9730",
    "userId": "visitor:1",
    "sellerUserId": "visitor:2",
    "userLocation": {
      "lat": 12.923494699999992,
      "lng": 77.6851069,
      "address": "Adarsh Palm Retreat, Bellandur, Bengaluru, Karnataka, India"
    },
    "shoppingCart": {
      "title": "Product List",
      "allSku": [
        {
          "title": "Sharbati Wheat Flour",
          "subHeading": "4.5 KG per Packet",
          "price": 266,
          "skuId": "item-2",
        },
        {
          "title": "Sharbati Wheat Flour",
          "subHeading": "3KG per Packet",
          "price": 177,
          "skuId": "item-55"
        },
        {
          "title": "FINE Gram Flour (Besan) Flour",
          "subHeading": "per kg",
          "price": 110,
          "skuId": "item-6",
        }
      ],
      "cart": [
        {
          "skuId": "item-2",
          "quantity": 1
        },
        {
          "skuId": "item-55",
          "quantity": 1
        },
        {
          "skuId": "item-6",
          "quantity": 1
        }
      ]
    },
    "amount": 553,
    "orderId": "5680-1604881796902",
    "timestamp": "1604881796902",
    "billDetails": {
      "cartAmount": 553,
      "deliveryCharges": 0,
      "gst": 0,
      "paymentGatewayFee": 1,
      "borneBySeller": false,
      "finalAmount": 554,
      "couponCode": "",
      "discountAmount": 0
    },
    "status": "STAGED",
    "statusAndTs": [
      {
        "status": "STAGED",
        "timestamp": "1604881796902"
      }
    ],
    "metadata": {
      "linkId": "delivery"
    },
    "deliverySlot": "",
    "comments": "",
    "customerPhone": "9009009009",
    "customerName": "Customer name - Test",
    "pickupLocation": {
      "lat": 12.923494699999992,
      "lng": 77.6851069,
      "address": "Adarsh Palm Retreat, Bellandur, Bengaluru, Karnataka, India"
    },
    "sellerPhone": "9009009009",
    "sellerName": "Seller name - Test",
    "finalAmount": 554,
    "mode": "VIRTUAL_UPI",
    "success": true,
    "transactionId": "payu-11564976601",
    "paymentRequestId": "11708"
  }
}

```


**PLACED order - webhook payload**

```javascript
{
  "event": "order-paid",
  "order": {
    "id": "9730",
    "userId": "visitor:1",
    "sellerUserId": "visitor:2",
    "userLocation": {
      "lat": 12.923494699999992,
      "lng": 77.6851069,
      "address": "Adarsh Palm Retreat, Bellandur, Bengaluru, Karnataka, India"
    },
    "shoppingCart": {
      "title": "Product List",
      "allSku": [
        {
          "title": "Sharbati Wheat Flour",
          "subHeading": "4.5 KG per Packet",
          "price": 266,
          "skuId": "item-2",
        },
        {
          "title": "Sharbati Wheat Flour",
          "subHeading": "3KG per Packet",
          "price": 177,
          "skuId": "item-55"
        },
        {
          "title": "FINE Gram Flour (Besan) Flour",
          "subHeading": "per kg",
          "price": 110,
          "skuId": "item-6",
        }
      ],
      "cart": [
        {
          "skuId": "item-2",
          "quantity": 1
        },
        {
          "skuId": "item-55",
          "quantity": 1
        },
        {
          "skuId": "item-6",
          "quantity": 1
        }
      ]
    },
    "amount": 553,
    "orderId": "5680-1604881796902",
    "timestamp": "1604881796902",
    "billDetails": {
      "cartAmount": 553,
      "deliveryCharges": 0,
      "gst": 0,
      "paymentGatewayFee": 1,
      "borneBySeller": false,
      "finalAmount": 554,
      "couponCode": "",
      "discountAmount": 0
    },
    "status": "PLACED",
    "statusAndTs": [
      {
        "status": "STAGED",
        "timestamp": "1604881796902"
      },
      {
        "status": "PLACED",
        "timestamp": 1604881843229
      }
    ],
    "metadata": {
      "linkId": "delivery"
    },
    "deliverySlot": "",
    "comments": "",
    "customerPhone": "9009009009",
    "customerName": "Customer name - Test",
    "pickupLocation": {
      "lat": 12.923494699999992,
      "lng": 77.6851069,
      "address": "Adarsh Palm Retreat, Bellandur, Bengaluru, Karnataka, India"
    },
    "sellerPhone": "9009009009",
    "sellerName": "Seller name - Test",
    "finalAmount": 554,
    "mode": "VIRTUAL_UPI",
    "success": true,
    "transactionId": "payu-11564976601",
    "paymentRequestId": "11708"
  }
}

```
