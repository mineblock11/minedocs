author_name: Deftu
author_img: https://github.com/Deftu.png
difficulty: easy
description: Learn how to work with keybindings by implementing a mod which prints "Hello, World!" when the M key is pressed.

# Keybindings

In this guide, you will learn how to create keybindings configurable via Minecraft's controls menu.

This guide is made for 1.18.x and above, there is no guarantee it will work below 1.18.x.

## Prerequisites

You will need basic knowledge of Java to begin this guide. If you do not know how to program in Java with Gradle, please check out these beginner guides before continuing:

- [Java Tutorial](https://www.w3schools.com/java/)
- [Gradle: Building A Java App](https://www.baeldung.com/gradle-building-a-java-app)

You will obviously need IntelliJ IDEA, the Minecraft Dev plugin.

## Getting Started

First, create a new Fabric Mod project in the `Create Project` menu of IntelliJ, fill out the details.

Once your project has completed generating, we can start with creating mod initializer. Do keep in mind that keybindings obviously only interact via the client. If you want server functionality, you'll need to send packets to the server via the client. Right now, this mod will do absolutely nothing. Now would be about the time to start thinking about what you want your keybinding to actually do... In this guide, we'll be making it print out "Hello, World!".

## Creating the Keybinding

The most important thing we have to do here is actually create our keybinding, or else we can't interact with it!

The list of parameters in the Keybinding constructor are as follows:

- The name of the keybinding itself, shown in the controls menu.
- The category the keybinding is listed under in the controls menu.
- The default key or button the keybinding uses.

The possible keys can be found as constants inside both the `GLFW` and `InputUtil` classes. It's recommended to use the `InputUtil` class out of the two, though.

```java
public class ExampleModClient implements ClientModInitializer {

    @Override
    public void onInitializeClient() {
        KeyBinding keyBinding = new KeyBinding("Example", InputUtil.GLFW_KEY_M, "Example Mod");
    }

}
```

## Registering your Keybinding

For this step, we will **need** Fabric API, as it has utilities we will use inside of it's keybinding API.

To register our Keybindings, we'll need to use the `KeyBindingHelper`. This can be done as follows:

```java
public class ExampleModClient implements ClientModInitializer {

    @Override
    public void onInitializeClient() {
        KeyBinding keyBinding = new KeyBinding("Example", InputUtil.GLFW_KEY_M, "Example Mod");
        KeyBindingHelper.registerKeyBinding(keyBinding);
    }

}
```

All this does is add our keybinding to the Minecraft controls menu. This is not necessarily a required step, though it's very important for users to be able to configure your keybinding.

## Interacting with your Keybinding

To interact with your keybind, there are two options:

- Check for interactions every client tick (expected to be ~20 times per second)
- Mixin into the keyboard handler and only check when a keypress is detected

As to keep things simple, we are only going to be going with option 1, as it is the standard. It is far less reliable if your keybinding logic is sensitive and can only be ran once, but it works for most use-cases.

To get started here, we're going to need to register a client tick event listener via **Fabric API**.

```java
public class ExampleModClient implements ClientModInitializer {

    @Override
    public void onInitializeClient() {
        KeyBinding keyBinding = new KeyBinding("Example", InputUtil.GLFW_KEY_M, "Example Mod");
        KeyBindingHelper.registerKeyBinding(keyBinding);

        ClientTickEvents.END_CLIENT_TICK.register(client -> {
        });
    }

}
```

We've now registered a client tick event listener. This is necessary just so we can check if the keybinding has been pressed. This effectively also functions as handling it being held down. Keep this in mind when writing your logic.

Now, to actually handle our keybinding itself, we can do as follows:

```java
public class ExampleModClient implements ClientModInitializer {

    @Override
    public void onInitializeClient() {
        KeyBinding keyBinding = new KeyBinding("Example", InputUtil.GLFW_KEY_M, "Example Mod");
        KeyBindingHelper.registerKeyBinding(keyBinding);

        ClientTickEvents.END_CLIENT_TICK.register(client -> {
            // Uncomment this if your keybindings should only be interacted with within a world.
            // if (client.world == null)
            //     return;

            if (keyBinding.isPressed) {
                System.out.println("Hello, World!");
            }
        });
    }

}
```
