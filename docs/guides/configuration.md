author_name: mineblock11
author_img: https://github.com/11mods.png
difficulty: easy
description: Make your mod configurable using owo-lib.

# Configuration

Configuration allows users to customize their experience with your mod, it also allows modpack developers to tailor your mod to fit into the theme of their pack. Configurability is a must if you want to expand your mod into new directions.

This guide will help you through creating configuration using `owo-lib`.

## Installation

First, add the following to your build.gradle

```groovy
repositories {
    maven { 
        name "Wisp Forest"
        url 'https://maven.wispforest.io'
    }
}

// ...

dependencies {
    modImplementation "io.wispforest:owo-lib:${project.owo_version}"
    include "io.wispforest:owo-sentinel:${project.owo_version}"
    annotationProcessor "io.wispforest:owo-sentinel:${project.owo_version}"
}
```

Make sure to add `owo_version` to your `build.gradle`, the latest [can be found here.](https://github.com/wisp-forest/owo-lib/releases/)

## Creating your config "model"

This model is processed by `owo-lib`'s annotation processor into a wrapper class that can be used by your code.

```java
@Config(name = "modid", wrapperName = "ModIDConfig")
public class ModIDModel {
    
}
```

__**Make sure to replace any instances of `ModID` with your mod's ID!**__

Once we've created the model, build your mod to run the annotation processor.

## Accessing the wrapper

To access the wrapper, we can add the following field to our `ModInitializer` entrypoint:

```java
public static final ModIDConfig CONFIG = ModIDConfig.createAndLoad();
```

This creates an instance of the config we can access.

## Adding Config Values

Adding config values is simple, just add a field to the `ModIDModel` we previously created:

```java
@Config(name = "modid", wrapperName = "ModIDConfig")
public class ModIDModel {
    public int userFavouriteNumber = 16;
    public boolean userLikesMod = false;
}
```

Build your project to regenerate the config wrapper.

When you add a config value, you will need to add it to your language files:

- `text.config.<config name>.option.<value name>`

In the case of `userFavouriteNumber`, the translation would end up looking something like this:

```json
{
    "text.config.modid.option.userFavouriteNumber": "Favourite Number"
}
```

## Accessing Config Values

To access config values, we can simply use the instance of the config wrapper we created in our `ModInitializer` entrypoint.

Lets say we want to get the `userFavouriteNumber` field in our config and change it to `69420`

```java
// This will get the `userFavouriteNumber` field.
CONFIG.userFavouriteNumber();

// This will set the `userFavouriteNumber` field to `69420`
CONFIG.userFavouriteNumber(69420);
```

## Mod Menu Integration

To get your config automatically showing in Mod Menu, you can add the following annotation to `ModIDModel`:

```java
@Modmenu(modId = "modid")
```

## Notes

That's pretty much all there is to know! A more in-depth guide can be found on the [`owo-lib` documentation found here.](https://docs.wispforest.io/owo/config/)