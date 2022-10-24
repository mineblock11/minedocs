author_name: mineblock11
author_img: https://github.com/mineblock11.png
difficulty: easy
description: Learn how to make interactive items by creating totems that change the weather of the world.

# Weather Totems

In this guide, you will learn how to create interactive items.

This guide is made for 1.19.x, it is not guaranteed to work below 1.19.x

Completed code can be found on GitHub: [11mods/weather-totems](https://github.com/11mods/weather-totems)

## Prerequisites

You will need basic knowledge of Java to begin this guide. If you do not know how to program in Java with Gradle, please check out these beginner guides before continuing:

- [Java Tutorial](https://www.w3schools.com/java/)
- [Gradle: Building A Java App](https://www.baeldung.com/gradle-building-a-java-app)

You will obviously need IntelliJ IDEA, the Minecraft Dev plugin.

## Getting Started

First, create a new Fabric Mod project in the `Create Project` menu of IntelliJ, fill out the details.

Once your project has completed generating, we can start with creating our items, and the shrine system.

Lets create a `WeatherTotem` class that extends `Item` like so:

```java
public class WeatherTotem extends Item {
    public WeatherTotemItem(Settings settings) {
        super(settings);
    }
}
```

Right now, this item is completely and utterly useless. What do we want to make the totem do?

**Planning your mods is probably more important than actually creating them. If you don't think about how your mod works beforehand, [things can get messy...](https://github.com/11mods/FabricGlass/blob/60716be110ac08e30db2fb2c5812f46ef2b4d9f4/src/main/java/mine/block/glass/blocks/entity/ProjectionBlockBase.java#L150-L265)**

So, we want our totems to:

- Change the weather instantly.
- Be one time use.

## Creating The Totems

Let's make our totems change the weather!

First of all, we need to assign weather to a totem item, to do this, we can use an enum:

```java
public enum WeatherType {
    RAIN,
    CLEAR,
    STORM
}
```

Now we've created the enum, we can store it in the `WeatherTotem` class, this will represent what weather the totem should change the world to.

```java
public class WeatherTotem extends Item {
    // The type of weather to change to when the totem is used.
    private final WeatherType type;

    public WeatherTotemItem(Settings settings, WeatherType type) {
        super(settings);
        this.type = type;
    }
}
```

It's best to comment what your code does, as it makes it easier to understand to you in the future, and other people.

Now we're storing the weather type, lets make the totem do something!

#### Totem Usage

In Minecraft, you can create an interactable item by extending the `use(...)` method, so, in the `WeatherTotem` class, we'll extend that method for our own usage.

```java
    @Override
    public TypedActionResult<ItemStack> use(World world, PlayerEntity user, Hand hand) {
    
        // ...
    }
```

`use(...)` requires a `TypedActionResult` to be returned - it tells the game what it should do with the item, destroy it, duplicate it etc.

In this case, we want to destroy the item, as it should only be used once.

We'll use `TypedActionResult.consume(...)` to destroy the item once its used.

```java
    @Override
    public TypedActionResult<ItemStack> use(World world, PlayerEntity user, Hand hand) {
        ItemStack totemItemStack = user.getStackInHand(hand);
        return TypedActionResult.consume(totemItemStack);
    }
```

Alright, the totem now disappears when its used, we need to change the weather now!

We have to change the weather on the server, because all game logic is ran on the server. We can check whether the `use(...)` method is being ran on the server or not by checking if `world.isClient` is false.

```java
    @Override
    public TypedActionResult<ItemStack> use(World world, PlayerEntity user, Hand hand) {

        if(!world.isClient) {
            // This code is only ran on the server!
        }

        ItemStack totemItemStack = user.getStackInHand(hand);
        return TypedActionResult.consume(totemItemStack);
    }
```

To change the weather, we must access the `world`'s properties and change the weather ticks. Due to the fact that this code is being ran on the server, we can cast `world` to the `ServerWorld` class to access the properties properly.

```java
if(!world.isClient) {
    // This code is only ran on the server!
    ServerWorld serverWorld = (ServerWorld) world;
    WorldProperties worldProperties = serverWorld.getLevelProperties();
}
```

`WorldProperties` doesn't have the methods to change the weather though! So we have to check if it's an instance of `LevelProperties`, which contains the methods to change the weather. We can use `instanceof` in an `if` statement to do this:

```java
// This code is only ran on the server!
ServerWorld serverWorld = (ServerWorld) world;
WorldProperties worldProperties = serverWorld.getLevelProperties();
            
if(worldProperties instanceof LevelProperties levelProperties) {
    // We'll change the weather in this bit.
}
```

#### Changing The Weather

We have to check what type the totem is to change the weather:

```java
if(worldProperties instanceof LevelProperties levelProperties) {
    if(this.type == WeatherType.CLEAR) {
        levelProperties.setRainTime(0);
        levelProperties.setThunderTime(0);
        levelProperties.setRaining(false);
        levelProperties.setThundering(false);
    }
    if(this.type == WeatherType.RAIN) {
        // We need to set the weather to rain.
        levelProperties.setRainTime(4800); // This is in ticks. Change it if you want. 20 ticks = 1 second so 4800 ticks = 4 minutes (240 seconds)
        levelProperties.setRaining(true);
    }
    if(this.type == WeatherType.THUNDER) {
        levelProperties.setRainTime(4800);
        levelProperties.setRaining(true);
        levelProperties.setThundering(true);
        levelProperties.setThunderTime(4800);
    }
}
```

The logic is now done, all we need to do is register the totems.

### Registering Totems

We can create a new class, and register our items there:

```java
public class TotemItems {
    public static void initialize() {
        // Register all the totem types possible.
        register("thunder_totem", new WeatherTotemItem(new FabricItemSettings().group(ItemGroup.TOOLS), WeatherType.THUNDER));
        register("rain_totem", new WeatherTotemItem(new FabricItemSettings().group(ItemGroup.TOOLS), WeatherType.RAIN));
        register("clear_totem", new WeatherTotemItem(new FabricItemSettings().group(ItemGroup.TOOLS), WeatherType.CLEAR));
    }

    public static Item register(String id, Item item) {
        // Register an item to the registry.
        return Registry.register(Registry.ITEM, new Identifier("weathertotems", id), item);
    }
}
```

We now need to call `TotemItems.initialize()` from our `ModEntrypoint`:

```java
@Override
public void onInitialize() {
    TotemItems.initialize();
}
```

Now, when we go in-game, we should have three totem items available from the tools tab of the creative inventory. They should change the weather on use.

![](/images/totem/rain-totem.gif)
## Textures, Models, Art? Oh my!

You'll need some textures for your totems before continuing, i've made some up for you here: *or you can make your own!*

<div id="totem-textures" class="modal">
  <div class="modal-background"></div>
  <div class="modal-content">
    <div class="container box">
      <p class="subtitle"><i class="fa fa-download" aria-hidden="true"></i> Download Assets</p>
      <p class="content">
        By downloading these assets from minedocs, you agree to respect the 
        <a href="https://creativecommons.org/licenses/by-nd/4.0/">Creative Commons Attribution-NoDerivatives 4.0 International License</a>
      </p>
      <a class="button is-primary" href="/assets/totem-assets.zip" target="_blank">Download totem-assets.zip</a>
    </div>
  </div>

  <button class="modal-close is-large" aria-label="close"></button>
</div>

<button class="js-modal-trigger button is-large is-primary" data-target="totem-textures">
  Download Assets
</button>

### Textures

Place the textures in the `totem-assets.zip` or the textures you have created in the `resources/assets/<modid>/textures/item` folder.

### Models

We'll now need to create some models, for the totems, we can extend the `item/generated` model provided by vanilla Minecraft.

This is `thunder_totem.json`, make sure to make models for the other totems as well! The JSON file name should match the path of the item ID. Place these json models in `resources/assets/<modid>/models/item`

*`weathertotems:thunder_totem` = `thunder_totem.json`*
```json
{
    "parent": "item/generated",
    "textures": {
        "layer0": "weathertotems:item/thunder_totem"
    }
}
```

<hr>

Once you've created all the models, they should appear in-game:

![Totem Models Ingame](/images/totem/totem-models-ingame.png)

<!-- ### Recipes

We have no way to craft the totems! Meaning they aren't accessible from survival Minecraft. Again, we'll plan our recipes:

- The clear totem should be crafted using a Totem Of Undying, and an empty bucket - eg: the rainy, thundery weather is collected in the bucket, or something along the lines of that.
- The thunder totem should be crafted using a Totem Of Undying and a lightning rod, as that just seems plausible.
- The rain totem should be crafted using a Totem Of Undying and a bucket of water.

You can always change the required items if you want. -->