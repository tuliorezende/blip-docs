## Client notifications


### Sending notifications

> The following sample show how to send a notification after connection has been stablished:

```javascript
client.connect()
    .then((session) => {
        // Sending "received" notification
        var notification = {
            id: 'ef16284d-09b2-4d91-8220-74008f3a5788',
            to: '553199990000@0mn.io',
            event: Lime.NotificationEvent.RECEIVED
        };
        client.sendNotification(notification);
    });

// Using await keyword
let session = await client.connect();
let notification = {
    id: 'ef16284d-09b2-4d91-8220-74008f3a5788',
    to: '553199990000@0mn.io',
    event: Lime.NotificationEvent.RECEIVED
};

client.sendNotification(notification);
```

```csharp

public class PlainTextMessageReceiver : IMessageReceiver
{
    private readonly ISender _sender;

    public PlainTextMessageReceiver(IMessagingHubSender sender)
    {
        _sender = sender;
    }

    public async Task ReceiveAsync(Message message, CancellationToken cancellationToken)
    {
        // Write the received message to the console
        Console.WriteLine(message.Content.ToString());
        // Responds to the received message
        _sender.SendMessageAsync("Hi. I just received your message!", message.From, cancellationToken);

        var notification = new Notification
        {
            Id = message.Id,
            To = message.From,
            Event = Event.Consumed
        };

        await _sender.SendNotificationAsync(notification, cancellationToken);
    }
}
```

```http
POST https://msging.net/notifications HTTP/1.1
Content-Type: application/json
Authorization: Key bWVzc2FnaW5naHViQHRha2VuZXQuY29tLmJyOjEyMzQ=
Content-Length: 131

{
    "id": "99cf454e-f25d-4ebd-831f-e48a1c612cd4",
    "from": "551100001111@0mn.io/4ac58r6e3",
    "event": "consumed"
}
```

#### Webhook
In order to correctly show the message history is important that the chatbots send notifications of messages processed to originator clients.

For each message processed is important send a notification with the consumed event. In case of problems the chatbot must send a notification with the failed event. The request must contain a authorization header (Authorization) with Key type, as showed on chatbot settings.

For instance, imagine that the received message from example above (whit id `99cf454e-f25d-4ebd-831f-e48a1c612cd4`) was processed with success. The code bellow show a complete notification request including the headers and the body request.

#### C\# and JavaScript
It's possible send notifications and messages only after sessions has been stablished.


### Receiving notifications

> The next sample show how to add notification receiver with filter to `received` event type:

```javascript
client.addNotificationReceiver("received", function(notification) {
  // Process received notifications
});

// Using expression lambda
client.addNotificationReceiver(() => true, function(message) {
  // Process received notifications
});
```

```csharp
public class ConsumedNotificationReceiver : INotificationReceiver
{
    public async Task ReceiveAsync(Notification notification, CancellationToken cancellationToken)
    {
        // Write the received notification to the console
        Console.WriteLine(notification.ToString());
    }
}
```

#### JavaScript

All messages sent to the chatbot are redirected to registered receivers of messages and notifications. You also can define filters to each receiver.

#### C#     

The receipt of messages and notifications is done using the interaces IMessageReceiver and INotificationReceiver respectively.

Some important notes:

Before the ReceiveAsync method be executed, a notification of Event.Received type is automatically sent to originator of message.
After ReceiveAsync method be executed, if no one exception occur, a notification of type Event.Consumed is automatically sent to originator of message.
If some exception occur on ReceiveAsync method, a notificação of type Event.Failed is automatically sent to originator of message.

The notifcations are fire-and-forget and if occur some exception on ReceiveAsync, this fail will be ignored.

Note: Remember to register all implementations of INotificationReceiver and IMessageReceiver on application.json file. For more informations check the Configuring section.

#### Webhook

All notifications will be delivered on configured chatbot notification URL. Each notification contains the status of messages. Observe that the notifications are sent by the clients, informing if received or not some message.

A sample of notification is presented bellow. This notification will be deliverd as a HTTP POST request on the chatbot notification URL.