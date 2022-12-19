github: https://github.com/11mods/mine11lib
description: Mineblock's Repetitive Utilities - A library mod used by my mods with methods that are used repeatedly or in repetition such as datagen providers.

# MRU

MRU (formerly known as mine11lib) is a utility library for repetitive tasks.

The following features are available:

- Packet Helpers
- Texture Data Generation
- Image Filters

### Installation

![](https://maven.mineblock11.dev/api/badge/latest/releases/mine/block/MRU/?color=8c3e39&name=Latest%20Maven)

Add the following to your build.gradle

```groovy
repositories {
    maven {
        name "mineblockMaven"
        url "https://maven.mineblock11.dev/releases"
    }
}

dependencies {
    // Do not "include" mine11lib, it will not work.
    modImplementation "mine.block:MRU:${project.MRU_version}"
}
```

## Packet Helpers

mine11lib wraps the Fabric Networking API's custom packet system into neat classes you can use:

- `CustomC2SPacket`: A packet that is sent from the client and recieved on the server.
- `CustomS2CPacket`: A packet that is sent from the server and recieved on the client.
- `AbstractPacket`: A custom packet that may be useful for other networking stacks.

Simply extend these classes and implement the methods to create your packets.

To register your packets, you can either call `.register()` on the packet class, or chuck it into the registry provided by mine11lib:

```java
var customPacket = new MyC2SPacket();
Registry.register(MinelibRegistry.CUSTOM_PACKETS, new Identifier("mymod", customPacket.getID(), customPacket);
```

## Texture Data Generation

If you want to use texture datagen, you can extend the `FabricTextureProvider` class.

mine11lib uses Scrimage instead of AWT to prevent crashes on MacOS.

## Image Filters

There are some filters you can use alongside the texture datagen:

- `ColorSwapFilter`: Swaps colors in an image.


