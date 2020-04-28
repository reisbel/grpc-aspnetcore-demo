# grpc-aspnetcore-demo

How to create a .NET Core gRPC client and an ASP.NET Core gRPC Server.

## gRPC Service

Creates a new gRPC service

```bash
dotnet new grpc -o GrpcGreeter
```

Examine the project files

```note
GrpcGreeter project files:
greet.proto – The Protos/greet.proto file defines the Greeter gRPC and is used to generate the gRPC server assets. For more information, see Introduction to gRPC.
Services folder: Contains the implementation of the Greeter service.
appSettings.json – Contains configuration data, such as protocol used by Kestrel. For more information, see Configuration in ASP.NET Core.
Program.cs – Contains the entry point for the gRPC service. For more information, see .NET Generic Host.
Startup.cs – Contains code that configures app behavior. For more information, see App startup.
```

## gRPC Client

Create the gRPC client in a .NET console app

```bash
dotnet new console -o GrpcGreeterClient
```

Install required packages

```bash
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

Create a Protos folder in the gRPC client project.

Copy the Protos\greet.proto file from the gRPC Greeter service to the gRPC client project.

Edit the GrpcGreeterClient.csproj project file and add an item group with a <Protobuf> element that refers to the greet.proto file:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

Create the Greeter client

Build the project to create the types in the GrpcGreeter namespace. The GrpcGreeter types are generated automatically by the build process.

Update the gRPC client Program.cs file with the following code:

```c#

using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5000) must match the port of the gRPC server.
            using var channel = GrpcChannel.ForAddress("http://localhost:5000");
            var client =  new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

## Test the gRPC client with the gRPC Greeter service

Start the Greeter service.

```bash
➜  GrpcGreeter git:(master) ✗ dotnet run
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Users/reisbel/rep/github-workspace/grpc-aspnetcore-demo/GrpcGreeter
```

Start the client.

```bash
➜  GrpcGreeterClient git:(master) ✗ dotnet run
Hello World!
```

## References

[Tutorial: Create a gRPC client and server in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/tutorials/grpc/grpc-start?view=aspnetcore-3.1&tabs=visual-studio-code)

## License

MIT - See [LICENSE](LICENSE) for more information.
