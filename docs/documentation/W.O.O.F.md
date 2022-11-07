github: https://github.com/11mods/woof
description: Adds new variants of wolves based on biomes, has an API that allows custom variants and goals to be made.

# Wolves Of Other Furs

Since W.O.O.F 3.0.0, an addon API has been introduced which allows you to create your own variants and add your own goals into the wolves' AI.

## Installation

![](https://maven.mineblock11.dev/api/badge/latest/releases/mine/block/woof/?color=8c3e39&name=Latest%20Maven)

Add the following to your build.gradle

```groovy
repositories {
    maven {
        name "mineblockMaven"
        url "https://maven.mineblock11.dev/releases"
    }
}

dependencies {
    // Do not "include" Wolves Of Other Furs, it will not work.
    modImplementation "mine.block:woof:${project.woof_version}"
}
```

Make sure to add `woof_version` to your gradle.properties.

## Adding A Custom Variant

You can add a custom variant by calling `WoofAPI.registerWolfVariant(Identifier, Function<RegistryEntry<Biome>, Boolean>)`, this will return a `Variant` record that has been registered to the variant registry.

The `Function<RegistryEntry<Biome>, Boolean>` acts as a biome predicate, where the variant should spawn and be found.

Example usage of `WoofAPI.registerWolfVariant(Identifier, Function<RegistryEntry<Biome>, Boolean>)`:

```java
// The "woof:skeleton" variant will spawn only in the soul sand valley biome.
WoofAPI.registerWolfVariant(new Identifier("woof", "skeleton"), (biome) -> biome.matchesId(new Identifier("soul_sand_valley")));

// This variant will never spawn unless using commands.
WoofAPI.registerWolfVariant(new Identifier("woof", "impossible"), (biome) -> false);
```

The variant of the wolf can be found in the `variant` tag of the wolf entity NBT, useful for map-makers.

When you register a variant, W.O.O.F expects the following textures:

- `assets/<namespace>/textures/woof/<path>/untamed.png`
- `assets/<namespace>/textures/woof/<path>/tamed.png`
- `assets/<namespace>/textures/woof/<path>/angry.png`

Where `<namespace>` is the namespace of the ID used to register the variant, and `<path>` is the path of the ID used to register the variant.

Eg: `woof:skeleton`'s angry texture would be found at: `assets/woof/textures/woof/skeleton/angry.png`

## Adding A Custom Goal

You can add a custom goal in the `WoofDogGoalCallback` event.

Example usage:

```java
// This is how the event is used by W.O.O.F itself.
WoofDogGoalCallback.EVENT.register((goalSelector, wolfEntity) -> {
    goalSelector.add(7, new DogSitOnBlockGoal(wolfEntity, 0.8));
    goalSelector.add(4, new DogEatOutBowlGoal(wolfEntity, 0.9));
});
```

## Releasing Addons

You are free to release addons on Curseforge and Modrinth, but you must do the following:

- Link back to Wolves Of Other Furs
- Credit `mineblock11` as the original creator of Wolves Of Other Furs
- Make sure you don't break the original mod!