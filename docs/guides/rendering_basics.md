author_name: Ace
author_img: https://github.com/ang-xd.png
difficulty: easy

# Rendering Basics

In this guide, you will learn how to render text on the screen using the TextRenderer and Fabric's HudRenderCallback

This guide is made for 1.19.x and above, there is no guarantee it will work below 1.19.x.

## Prerequisites

You will need basic knowledge of Java to begin this guide. If you do not know how to program in Java with Gradle, please check out these beginner guides before continuing:

- [Java Tutorial](https://www.w3schools.com/java/)
- [Gradle: Building A Java App](https://www.baeldung.com/gradle-building-a-java-app)

You will obviously need IntelliJ IDEA, the Minecraft Dev plugin.

## Getting Started

First, create a new Fabric Mod project in the `Create Project` menu of IntelliJ, fill out the details.

Once your project has completed generating, we can start in the client initializer class.

## Rendering text
We will be using Fabric's HudRenderCallback event to render on screen. This could also be done by using a mixin on `InGameHud`. We will use the event though. Add this to your onInitializeClient() method
```java
HudRenderCallback.EVENT.register((matrixStack, tickDelta) -> {
    TextRenderer textRenderer = MinecraftClient.getInstance().textRenderer;
    if(textRenderer != null)
        textRenderer.drawWithShadow(matrixStack, "Hello World!", 0, 0, 0xffffff);
});
```
This will render "Hello World" at the top left of your screen! If you want to draw a string without the shadow, use `textRenderer.draw` instead.