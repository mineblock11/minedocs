author_name: Ace
author_img: https://github.com/ang-xd.png
difficulty: easy
description: Use Fabric's Networking API to send messages from the client to the server.

# Networking

In this guide, you will learn how to use Fabric's networking API to send messages from the client to the server.

This guide is made for 1.19.x, it is not guaranteed to work below 1.19.x

## Prerequisites
You will need basic knowledge of Java to begin this guide. If you do not know how to program in Java with Gradle, please check out these beginner guides before continuing:

- [Java Tutorial](https://www.w3schools.com/java/)
- [Gradle: Building A Java App](https://www.baeldung.com/gradle-building-a-java-app)

You will obviously need IntelliJ IDEA, the Minecraft Dev plugin.

[You should also checkout the official wiki page on networking](https://fabricmc.net/wiki/tutorial:networking)

## Getting Started
We will be using the [Keybinding](https://docs.mineblock11.dev/guides/keybinding/) guide as a start for our project. For this guide, we will be setting the player on fire using a keybind and simple C2S (client to server) packet. Your client initializer should look something like this
```java
public class ExampleModClient implements ClientModInitializer {

    @Override
    public void onInitializeClient() {
        KeyBinding keyBinding = new KeyBinding("Example", InputUtil.GLFW_KEY_M, "Example Mod");
        KeyBindingHelper.registerKeyBinding(keyBinding);

        ClientTickEvents.END_CLIENT_TICK.register(client -> {
             if (client.world == null)
                 return;

            if (keyBinding.isPressed) {
                System.out.println("Hello, World!");
            }
        });
    }

}
```

## Sending the packet
Add the following code to your keybind listener (right under where we print "Hello, World!")
```java
ClientPlayNetworking.send(new Identifier("<modid>", "setonfire"), PacketByteBufs.empty());
```
Currently, this will send a packet to the server named "setonfire". If you try this currently, nothing will happen. That is because the server is just recieving a packet, not handling it.

## Handling the packet
Now, we will head on over to your main class. This is where we will be handling the "setonfire" packet. Start off by adding this code to your onInitialize() method
```java
ServerPlayNetworking.registerGlobalReceiver(new Identifier("<modid>", "setonfire"), (server, player, handler, buf, responseSender) -> {
    // Packet handling
});
```
Inside the handler, we will set the player who sent the packet on fire, just like this: 
```java
ServerPlayNetworking.registerGlobalReceiver(newIdentifier("<modid>", "setonfire"), (server, player, handler, buf, responseSender) -> {
    // If we received data, it would be read and saved in a variable here
    server.execute(() -> { // All server related code goes here
        // Data sent through packets can not be read inside server code
        player.setOnFireFor(3); // Set the player on fire for 3 seconds
    });
});
```
If you test this ingame, it should work and set your client on fire when you press the keybind!

## Sending data through the packet
Say you want to send some data such as a string from client to server, this can actually be done fairly easy!
Let's start by editing our packet sender,   
```java
ClientPlayNetworking.send(new Identifier("<modid>", "setonfire"),
    PacketByteBufs.create().writeString("Hello server!"));
```
This time instead of sending PacketByteBufs.empty(), we are creating it and writing a string! That should be enough for the sender. Let's get to handling. 
```java
ServerPlayNetworking.registerGlobalReceiver(new Identifier("<modid>", "setonfire"), (server,player, handler, buf, responseSender) -> {
    String message = buf.readString(); // Read the message
    server.execute(() -> { // All server related code goes here
        player.setOnFireFor(3); // Set the player on fire for 3 seconds
        System.out.println(message); // Print the message sent from the client
    });
});
```
This code will read the message sent from the client ("Hello server!") and print it out (on the server)