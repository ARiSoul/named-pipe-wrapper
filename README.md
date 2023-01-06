# Named Pipe Wrapper for .NET 4.8 (no conflicts found with .Net 7 usage in Windows SO)

A simple, easy to use, strongly-typed wrapper around .NET named pipes.

# THIS IS A FORK

Credits to the original creator acdvorak. He has a *Nuget Package* available in (https://www.nuget.org/packages/NamedPipeWrapper/).
As far as I could test, despite this being made with .Net 4.0, it works with no problems with .Net 7. In my version, it's upgraded to .Net 4.8.
It cannot be upgraded to .Net Standard 2.0 to be compatible with most .Net Frameworks due to the reference to System.IO.Pipes.Security, that doesn't work with that version. That being said, most probably it only works with Windows SO.

# Features

*  Create named pipe servers that can handle multiple client connections simultaneously.
*  Send strongly-typed messages between clients and servers: any serializable .NET object can be sent over a pipe and will be automatically serialized/deserialized, including cyclical references and complex object graphs.
*  Messages are sent and received asynchronously on a separate background thread and marshalled back to the calling thread (typically the UI).
*  Supports large messages - up to 300 MiB.

# Requirements

Requires .NET 4.8.

# Usage

Server:

```csharp
var server = new NamedPipeServer<SomeClass>("MyServerPipe");

server.ClientConnected += delegate(NamedPipeConnection<SomeClass> conn)
    {
        Console.WriteLine("Client {0} is now connected!", conn.Id);
        conn.PushMessage(new SomeClass { Text: "Welcome!" });
    };

server.ClientMessage += delegate(NamedPipeConnection<SomeClass> conn, SomeClass message)
    {
        Console.WriteLine("Client {0} says: {1}", conn.Id, message.Text);
    };

// Start up the server asynchronously and begin listening for connections.
// This method will return immediately while the server runs in a separate background thread.
server.Start();

// ...
```

Client:

```csharp
var client = new NamedPipeClient<SomeClass>("MyServerPipe");

client.ServerMessage += delegate(NamedPipeConnection<SomeClass> conn, SomeClass message)
    {
        Console.WriteLine("Server says: {0}", message.Text);
    };

// Start up the client asynchronously and connect to the specified server pipe.
// This method will return immediately while the client runs in a separate background thread.
client.Start();

// ...
```

# MIT License

Named Pipe Wrapper for .NET is licensed under the [MIT license](LICENSE.txt).
