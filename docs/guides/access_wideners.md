author_name: mineblock11
author_img: https://github.com/11mods.png
difficulty: intermediate
description: Access methods, classes and fields in Minecraft's code that are protected, final or private!

# Access Wideners

> Access wideners provide a way to loosen the access limits of classes, methods or fields.
> - [Fabric Wiki](https://fabricmc.net/wiki/tutorial:accesswideners)

Access Wideners are what they say on the tin, they widen the access to classes, methods or fields. They can be compared to Forge's Access Transformers.

## Creating your Access Widener file.

Access Wideners are stored in `src/main/resources`, for this example, we'll create one called `modid.accesswidener`.

To make Fabric Loader, and Fabric Loom recognize it's existance, we must add it to `fabric.mod.json` and `build.gradle`.

```json
{
    "accessWidener": "modid.accesswidener"
}
```

```groovy
loom {
    accessWidenerPath = file("src/main/resources/modid.accesswidener")
}
```

In this example, we will use the `named` namespace. So the top of the access widener file will look like the following:

```python
accessWidener	v2	named # The general format is `accessWidener {version} {namespace}`
```

## Adding wideners.

Instead of having to type the entries out by hand we can use [Linkie](https://linkie.shedaniel.me/mappings) to generate them for us!

When using Linkie, make sure you have "Do Not Translate" `3`, the correct Minecraft version `2` and the correct mappings selected `1`, or it will generate invalid access widener entries for your project.

![](/images/access_wideners/linkie.png)

*Work smarter, not harder.*

### Step 1: Copy the reference.

In IntelliJ IDEA, you can copy a method, class or field reference by right clicking on the target and selecting `Copy / Paste Special` -> `Copy Reference`

![](/images/access_wideners/copy_reference.png)

This should copy the reference to the clipboard.

In this example, I will be widening the `RegistryEntry.Reference.Type` enum, since it is private. I have copied the reference, it is as follows:

```java
net.minecraft.util.registry.RegistryEntry.Reference.Type
```

### Step 2: Paste into Linkie

After pasting the reference into Linkie, it should appear at the top, with relevant info on its intermediary and obfuscated names.

![](/images/access_wideners/paste_reference.png)

We only need the `AW`, which stands for, *you guessed it!*, access widener.

In this case, it is the following:

```
accessible class net/minecraft/util/registry/RegistryEntry$Reference$Type
```

### Step 3: Paste into your Access Widener file.

Once Linkie has provided you with the generated access widener entry, you can simply just paste it into your access widener file. In my case, my file now looks like this:

```python
accessWidener	v2	named # The general format is `accessWidener {version} {namespace}`
accessible class net/minecraft/util/registry/RegistryEntry$Reference$Type
```

### Step 4: Build/Resync Gradle!

You can either build or resync gradle to make Loom generate new sources with your applied access widener. Once it has finished syncing or building, your entries should have been applied. Using my example, we can see the `RegistryEntry.Reference.Type` enum is now publicly accessible!

![](/images/access_wideners/applied.png)

