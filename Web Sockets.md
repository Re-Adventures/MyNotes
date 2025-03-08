# Web Socket

* Websocket is a full duplex connection

* Specification is mentioned in [RFC 6455](https://www.rfc-editor.org/rfc/rfc6455.html)

# Fleck

* *Implementation of WebSockets for C#*

* We are going to use this package for implementing web sockets. Install this from NuGet package manager


# Implementing web sockets

* In the `Program.cs` file, remove everything & write the following code

```c#
using Fleck;

// Creating the web socket server.
// We can specify any url on which we want to host the web-socket server
var webSocketServer = new WebSocketServer("ws://0.0.0.0:9090");

// Initialization stuff for web apis
WebApplication.CreateBuilder(args).Build().Run();
```

* Now we need to start the web socket server

```c#
// Starting the web socket server
webSocketServer.Start(ws =>
{
    // Even handler for when there is a connection received from the client
    ws.OnOpen = () =>
    {
        Console.WriteLine("Connection opened");
    };

    // Setting up a on message event handler
    ws.OnMessage = (message) =>
    {
        Console.WriteLine(message);
    };
});
```

* Now we can run the code & our web socket server will be hosted on **ws://0.0.0.0:9090**

* For testing this we need to do this with postman
* In postman click on File -> New -> WebSocket

* Enter the websocket url & first click on connect. Once the connection is successful, we can send & receive messages to/from the server.

* Enter some message on the **Message** tab & click on **Send**. Our message will be logged in to the console.

## Sending message from the server

* We can send messages to the client as & when needed

```c#
webSocketServer.Start(ws => {
    ws.OnMessage = (message) => {
        // Sending message to the client
        ws.Send($"Hello there. You typed: {message}");

        ...
    }
})
```

# Implementing a chat server using websockets (Pub-Sub method)

* Create a new project

* In Program.cs

```c#
using WebSockets;

new WebSocketHandler().SetupWebSockets();

WebApplication.CreateBuilder(args).Build().Run();
```

* In `WebSockets.cs`

```c#
using Fleck;

namespace WebSockets
{
    public class WebSocketHandler
    {
        /// <summary>
        /// Will hold a list of connections that are active 
        /// </summary>
        public List<IWebSocketConnection> Connections = [];

        public void SetupWebSockets()
        {
            // Creating the web socket server.
            // We can specify any url on which we want to host the
            // web-socket server
            var webSocketServer = new WebSocketServer("ws://0.0.0.0:9090");

            // Starting the web socket server
            webSocketServer.Start(ws =>
            {
                // Even handler for when there is a connection received from
                // the client
                ws.OnOpen = () => { this.WebSocketConnectionOpener(ws); };

                // Setting up a on message event handler
                ws.OnMessage = (msg) => { WebSocketOnMessageHandler(ws, msg); };

                ws.OnClose = () => { WebSocketOnCloseHandler(ws) };
            });
        }

        /// <summary>
        /// Handler for when a client connects to the server
        /// </summary>
        /// <param name="connection"></param>
        private void WebSocketConnectionOpener(IWebSocketConnection connection)
        {
            Connections.Add(connection);
        }

        /// <summary>
        /// WebSocket on message received handler
        /// </summary>
        /// <param name="connection"></param>
        /// <param name="message"></param>
        private void WebSocketOnMessageHandler(IWebSocketConnection connection, string message)
        {
            // Broadcast the message to each & every available connection
            foreach (var conn in Connections)
            {
                if (conn == connection)
                {
                    continue;
                }

                conn.Send($"Message received: {message}");
            }
        }

        /// <summary>
        /// Method for handling connection drops
        /// </summary>
        /// <param name="ws"></param>
        private void WebSocketOnCloseHandler(IWebSocketConnection ws)
        {
            // Removing the connection from our active connections list
            Connections.Remove(ws);

            WebSocketOnMessageHandler(ws, "Some user dropped out of chat server");
        }

    }
}
```

# Useful dotnet commands

1. Creating solution file `dotnet new sln`
2. Creating api template file `dotnet new webapi`
3. Adding project to solution `dotnet sln add filename.csproj`
