# Helo Chat API

This article describes how to use Helo Chat and build very interesting use cases on top of it.

> Simplicity is the ultimate sophistication
> -Leonardo da Vinci

There are 3 key actors in the system you need to know of:
1. **Helo chat server** - the server serving and storing the data, making it all possible.
2. **Helo chat UI** - Web and App - beautiful presentation layer for humans
3. **Helo user chat clients** - the ones you write as bots. This is where you build ***intelligence* !!**

The architecture is based on Websocket communication and a watermark based diff communication to reduce bytes transferred over the wire. Simply put, get what changed from the last time you synced.

Commands are sent to the server over the same websocket connection. Some basic commands that you will end up using:
1. **New message** - Send a new message to a group / chat
2. **New group** - Create a new group
3. **Add event listener** - listen to specific events like new messages in a group / user updates etc.
4. ...

## Groups
The basic unit of communication that encapsulates a series of messages.
Whether its a group of people chatting together, or a private chat with a user, every message is sent to a group.


## Messages
The pretty text, image, video messages and custom task lists / shopping carts you want to share with other users.



## Working example
Now that we have a handle on the basic terminology, we will write a simple bot that sends a `Hi` message and a shopping cart to a user.


We use the Helo Chat API library from the [Helo Staging server](https://staging.heloprotocol.in/assets/chat.js) and make a `HeloChatClient` which enables communication the server.
We use a `LocalDataStore` which enables storing data locally so you can get diffs easily. You can provide your own custom implementation, but we suggest start with the one provided.


Creating the client is simple:
```javascript
const { LocalDataStore, HeloChatClient } = window.Helo || {};

const userId = USER_TEST_1;
const localDataStore = await LocalDataStore.create(userId);
const client = await HeloChatClient({ userId, localDataStore, apiKey: API_KEY, authMethod: 'v1' });

```

Here `API_KEY` is the key you've obtained for experimentation (Email us to get yours: support@heloprotocol.in).


Once the client is created and connected, you can listen to new messages in a group / chat. The listener expects an `on()` method where it will send updates :

```javascript
// Listen to new messages
const listenerId = client.addEventListener({ newMessagesForGroupId: groupId }, this);

on = async ({ homeView, message }) => {
    console.log('[Listener] on: ', { homeView, message });
};
```

NOTE: To listen to new messages in a chat, you need the groupID for the chat. Get it using this API:

```javascript
// Get the group id to be able to chat with the user.
const groupId = await client.getGroupIdForChat(USER_GAGAN, true);
```


Now that we have the listener setup, we can send messages to the group based on what the other user types. Let's send a `Hi`.
```javascript
// Send a Hi
await client.newMessage(groupId, { type: 'TEXT', text: 'Hi' });
```

It's that simple 🙂


## Send complex messages
The real power of Helo chat is that it allows you to create custom message types with beautiful presentation layers and simple storage layer. Here's a shopping list message

![](../assets/images/shopping-cart-ex1.png)

How do you send this message, you define the schema, the UI layer. Here's the code for sending it:

```javascript
  // Send a custom shopping cart
  const shoppingCart = {
    allSku: FOOD_SKU_LIST,
    cart: FOOD_CART_SKU,
    title: 'Shopping list',
    metadata: [{ key: 'deliveryDate', value: '23 April' }, { key: 'deliveryTime', value: '3 pm' }, { key: 'cookId', value: 'supply:352' }],
  };
  await client.newMessage(groupId, { type: 'SHOPPING_CART', shoppingCart });

const FOOD_SKU_LIST = [{
    photo: 'https://cafedelites.com/wp-content/uploads/2019/01/Butter-Chicken-IMAGE-64.jpg',
    title: 'Butter chicken -Half',
    subHeading: 'less spicy please',
    price: 150,
    skuId: 'item1',
}, {
    photo: 'https://www.whiskaffair.com/wp-content/uploads/2016/02/Achari-Paneer-Tikka-4-500x500.jpg',
    title: 'Achari Paneer Tikka',
    subHeading: '',
    price: 120,
    skuId: 'item2',
}, {
    photo: 'https://i.pinimg.com/originals/58/7b/90/587b90c4d849849a6c3fa9fda9555a0a.jpg',
    title: 'Tandoori roti',
    subHeading: 'garam',
    price: 15,
    skuId: 'item3',
}, {
    photo: 'https://www.sandyathome.com/wp-content/uploads/2017/10/jal-jeera-recipe.png',
    title: 'Jal jeera',
    subHeading: 'Teekha',
    price: 70,
    skuId: 'item4',
}];
const FOOD_CART_SKU = [
    { skuId: 'item1', quantity: 3 },
    { skuId: 'item2', quantity: 2 },
    { skuId: 'item3', quantity: 4 },
    { skuId: 'item4', quantity: 1 },
];
```


---

## Full code for reference
See the full working example:

```javascript
export default class App extends React.Component {
    componentDidMount() {
        const aScript = document.createElement('script');
        aScript.type = 'text/javascript';
        aScript.src = SCRIPT_SRC;

        document.head.appendChild(aScript);
        aScript.onload = this.onLoad;
    }

    onLoad = async () => {
        console.log('Loaded: ', window.Helo);
        const { LocalDataStore, HeloChatClient } = window.Helo || {};

        const userId = USER_TEST_1;
        const localDataStore = await LocalDataStore.create(userId);
        const client = await HeloChatClient({ userId, localDataStore, apiKey: API_KEY, authMethod: AUTH_METHOD });

        // Get the group id to be able to chat with the user.
        const groupId = await client.getGroupIdForChat(USER_GAGAN, true);

        // Listen to new messages
        const listenerId = client.addEventListener({ newMessagesForGroupId: groupId }, this);

        // Send a Hi
        await client.newMessage(groupId, { type: 'TEXT', text: 'Hi' });

        // Send a custom shopping cart
        const shoppingCart = {
            allSku: FOOD_SKU_LIST,
            cart: FOOD_CART_SKU,
            title: 'Shopping list',
            metadata: [{ key: 'deliveryDate', value: '23 April' }, { key: 'deliveryTime', value: '3 pm' }, { key: 'cookId', value: 'supply:352' }],
        };
        await client.newMessage(groupId, { type: 'SHOPPING_CART', shoppingCart });

        //  Wait 5 seconds, remove the event listener and close the client when done
        await new Promise(resolve => setTimeout(resolve, 5000));
        client.removeEventListener(listenerId);
        client.close();
    };

    on = async ({ homeView, message }) => {
        console.log('[Listener] on: ', { homeView, message });
    };

    render() {
        return (<div>Hi</div>);
    }
}

const FOOD_SKU_LIST = [{
    photo: 'https://cafedelites.com/wp-content/uploads/2019/01/Butter-Chicken-IMAGE-64.jpg',
    title: 'Butter chicken -Half',
    subHeading: 'less spicy please',
    price: 150,
    skuId: 'item1',
}, {
    photo: 'https://www.whiskaffair.com/wp-content/uploads/2016/02/Achari-Paneer-Tikka-4-500x500.jpg',
    title: 'Achari Paneer Tikka',
    subHeading: '',
    price: 120,
    skuId: 'item2',
}, {
    photo: 'https://i.pinimg.com/originals/58/7b/90/587b90c4d849849a6c3fa9fda9555a0a.jpg',
    title: 'Tandoori roti',
    subHeading: 'garam',
    price: 15,
    skuId: 'item3',
}, {
    photo: 'https://www.sandyathome.com/wp-content/uploads/2017/10/jal-jeera-recipe.png',
    title: 'Jal jeera',
    subHeading: 'Teekha',
    price: 70,
    skuId: 'item4',
}];
const FOOD_CART_SKU = [
    { skuId: 'item1', quantity: 3 },
    { skuId: 'item2', quantity: 2 },
    { skuId: 'item3', quantity: 4 },
    { skuId: 'item4', quantity: 1 },
];

const SCRIPT_SRC = 'https://staging.heloprotocol.in/assets/chat.js';
const API_KEY = '<Your key here>';
const AUTH_METHOD = 'v1';
const USER_TEST_1 = 'visitor:6';
const USER_TEST_2 = 'visitor:7';
const USER_VEDANT = 'visitor:2';
const USER_GAGAN = 'visitor:1';

```

