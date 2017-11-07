## Send Location

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Lime.Messaging.Contents;
using Lime.Protocol;
using Take.Blip.Client;

namespace MessageTypes
{
    public class OptionUserInputMessageReceiver : IMessageReceiver
    {
        private readonly ISender _sender;

        public OptionUserInputMessageReceiver(ISender sender)
        {
            _sender = sender;
        }
        public async Task ReceiveAsync(Message message, CancellationToken cancellationToken)
        {
            Document document = new Input
            {
                Label = new DocumentContainer
                {
                    Value = "Send your location please!"
                },
                Validation = new InputValidation
                {
                    Rule = InputValidationRule.Type,
                    Type = "application/vnd.lime.location+json"
                } 
            };

            await _sender.SendMessageAsync(document, message.From, cancellationToken);
        }
    }
}

```

You can send location by using [location](/#location)

Sending a location with latitude, longitude and altitude:

| Messenger                         | BLiPChat                                          |
|-----------------------------------|---------------------------------------------------|
| ![imagem](images/input_location_mssngr.png) | ![imagem](inputLocationBlipCHat.png)    |