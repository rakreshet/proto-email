Throughout this course we will be building a chat program (like Slack, only better!). Today we'll start with the (gRpc)
interface and the basic actors that will operate behind the scenes - leaving the hard problems of persistence,
clustering, etc. for later.

This way we will get to know the ideas behind the actor model and Akka's implementation of it, and also Kit, Traiana's
micro-service infrastructure.

Task:
-----
Create a server using the provided Protocol Buffers API (see `chatter.proto`), that supports the following:

* Clients can `login`. Successful logins should return a token to the client, that it can use in further communications
  with the server.
* Once logged in, the client can use the `chat` API, that will open a bi-directional stream for the client and server to
  communicate over - the client can send commands to the server, and the server can send responses to the client.
* The client can send the following commands to the server:
    1. Join or leave a channel.
    2. Post a message to a (joined) channel. This should result in the message being sent from the server to _all_
       clients that have joined that channel.

To that end, start by generating the gRpc server interface (using the Gradle `generateProto` task). Next, start
creating the actors that implement all the logic and interact with the gRpc code.

Walk-through:
--------------------
* Set up Spring Boot and the gRpc server (See the hello-service demo on Stash for reference):
    - Write a `ChatterServer` "main class" that starts the Spring application and annotate it with `@SpringBootApplication`.
    - Write a `ChatterServiceImpl` "stub" class that extends the generated `ChatterService` class. Annotate it with `@Service`.
    - Write a `ChatterServiceBindable` class that extends `BindableService`. Annotate it with `@GrpcService` and have
      the `ChatterServiceImpl` reference injected to it. There is no need for this class if you generated the service in
      Java - just add the `@GrpcService` annotation to your `ChatterServiceImpl`.
    - Run your server. Try to connect to it with a client.
* Write a `LoginActor` class that handles login requests. It can either work from a hard-coded set of usernames/passwords
  or simply allow any user to log-in without checking the password. The returned tokens should be unpredictable, so that
  Moty can't just forge it and post messages in your name.
* In your `ChatterServiceImpl`, create an `ActorSystem` and a `LoginActor`, and connect them to the actual API. Use
  Akka's `ask` pattern to easily obtain a `Future`, or create a `Promise`/`CompletableFuture` and complete it in the
  `Actor`.
* Test that the client can now log in to your server.
* Write a `ChannelActor` that represents a specific channel. It should manage a list of clients that have joined the
  channel and when one of them posts a message should initiate publishing it to the entire list.
* Write a `ChannelManagerActor` that acts as a router between the service and the `ChannelActor`s. The actor should:
    - Handle commands coming in from users by verifying their token (and obtaining the user's nickname), and forwarding
      the command to the appropriate `ChannelActor`.
    - Handle notifications coming from the 'ChannelActor' and distribute them to the appropriate clients.
* Write a `ChatStreamActor` that will be responsible for managing the gRPC streams created by `chat` API calls.
* Connect the `ChatStreamActor` to the `chat` API. Don't forget to handle the streams' failure/completion.
* One final test to see that everything works!

Bonus:
------
* Add support for listing clients connected to the server, clients connected to a channel, and list of channels.
* Notify everyone in a channel when a user joins/leaves the channel.
* Add support for "direct messages" - messages from one client to another that don't belong to a channel.
* Unit tests: Use the Akka Testkit to test your actors
