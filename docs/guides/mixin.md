author_name: StarL0stGaming
author_img: https://github.com/StarL0st.png
difficulty: intermediate
description: Learn how to make use of Mixin to print "Hello World!" in the console by creating a simple mixin that runs when the main menu is opened.
                

# Mixins

In this guide you will learn how to make use of Mixin, a tool that is very useful in certain cases when modding.

This guide should work for all new MC versions, due to the way mixins work.


## Prerequisites

Basic Java knowledge and a bit of experience on Minecraft modding is needed, you can check the [Weather Totems Guide](https://docs.mineblock11.dev/guides/totem/) to get started on modding basics.

A quick re-cap of the needed things:

- Basic Java Knowledge
- A bit of modding experience, as Mixins are not something quite easy to understand
- An already existing project, or the will to setup a new one

Intellij IDEA and the Minecraft Dev plugin are recommemded, specially the McDev plugin, as it will help a lot on setting up mixins.

You should also checkout the official documentation on mixins:
https://github.com/SpongePowered/Mixin/wiki

## So, what are mixins?

Mixins are a tool that allow you to inject your own code inside Minecraft's code, this way you can add things to already existing things, like the main screen menu or the player inventory.

Mixins work by modifying the bytecode thats generated from mc's code, and adding your owns code bytecode into that part of the code.

## Getting Started

First, we need to create our `mixin` package, this is the package that will have all of our mixins.

After you have made the `mixin` package, we need to tell Mixin where to look for it, so go into your `resources` directory (where your fabric.mod.json file is) and look for `yourmodid.mixins.json`, that's where we will tell Mixin what our mixin directory is AND the mixins we will add inside it.

Now look for the `"package"` field and add your mixin package inside it, just like this:

```json
{
  "required": true,
  "package": "your.packagename.mymod.mixin", // <-- Put your package's directory here
  "compatibilityLevel": "JAVA_17",
  "injectors": {
    "defaultRequire": 1
  },
  "client": [

  ], 
  "mixins": [

  ]
}
```

After you have set your package directory on the `"package"` field, we are all ready to start doing some mixin!

## Hello World Mixin

To start things off we are going to print a simple `"Hello World"` message when the main menu is initialized.

First, we need to create the class that will hold our code. Go to your `mixin` directory and create a `TitleScreenMixin` file (You can always name this files as you want, but it is recommended to name then after the class you are mixin to so you dont confuse yourself).

We now need to add a `@Mixin` annotation, this will tell Mixin which class you are mixin. This `@Mixin` annotation takes a class parameter, which is just the class name we are mixin into followed by a `.class`, this tells java that this is a class reference, and not an instance.

Your class should look like this so far:
```java
@Mixin(TitleScreen.class)
public class TitleScreenMixin {

}
```

After creating your class, go into your `<yourmodid>.mixins.json` and look for the `client` array and add the name of your class (If you have the McDev plugin in Intellij IDEA you will have autocompletion for this)

**NOTE:**`There are different types of arrays where one would add their mixin references, it is important to choose the right one because it can cause problems`
```md
There are three types of arrays:

- `mixins` is for mixins to classes that are present in both server and client.
- `client` is for mixins to classes that are present only in the client.
- `server` is for mixins to classes that are present only in the server.

For example, if I added my `TitleScreenMixin` (Whichs mixins to `TitleScreen`) reference to the `server` or `mixins` array, 
there would be problems because `TitleScreen` is only available in the client, and it isn't on the server or in both.
```

Now, its time to do the dirty work.

#### But what about the injection things?

Mixin provides several ways to modify the code you are modifying, but the one we will be focusing on today is `Inject`.

This little friend allows us to inject into diferent stages of the method.

There are several stages you can mixin into, but the ones you will probably need the most are:

-   `HEAD`: Code is ran at the top of the method, before everything else.
-   `INVOKE`: Code is ran just after the method that was set as the target is ran.
-   `RETURN`: Code is ran before every return value.
-   `TAIL`: Code is ran before the final return value.

You can check all the Injection Point References (Yes, thats how they are called) [here](https://github.com/SpongePowered/Mixin/wiki/Injection-Point-Reference).

For now we will be using the `HEAD` Injection Point Reference.

`Inject` is a method annotation so we first need to create a method which will hold this annotation and the code we will be using.

There are several recommendations to follow when creating this method, mainly to avoid future confusing and to ensure at least a bit of mod compatibility:

- Make your mixin methods private. Why? I don't know, just do it. (TODO: FIX)
- Prefix your method names with your modid, that way, in the highly hipotetic but very likely case that another mod has the same method name it won't "collide"
  with each other and just act like another method.

Now that you have made your method, its time to add the annotation.

The `@Inject` annotation is composed from several things, but the main 2 things you will need are:

- The `method` parameter, this is a string, which has the method signature you are mixin into, McDev autocompletes this by only typing the method name.
- The `@At` annotation, this specifies the location you want to mixin into.
   
The `@At` annotation itself is composed from several things, the main parts being:

- `value`, this holds the Injection Point Reference to be used.
- `target`, this tells Mixin what part you are referencing to.

Please note that the `target` parameter may not be used in certain cases, as it is not needed. For example
if you utilize the `HEAD` Injection Point you will not need it as you will be already targeting the top of the method.

So now that we know what composes the `@Inject` annotation we are ready to start doing some work.

#### Wait! That isn't a class!

To start, we need to add the `@Inject` annotation to our method. Remember that it is composed 
of a `method` parameter and a `at` parameter, this `at` parameter contains the `@At` annotation

We will be targeting the `TitleScreen`'s `init` method so our code will run when that method is ran.

The `@Inject` annotation should look like this:

```java
    @Inject(method = "init", at = @At(value = "HEAD"))
```

I recommend you write this annotation manually and not copy it, as mcdev will have to parse the method signature

Add this annotation to your method and make sure no errors are thrown, the method should look like this:

```java
    @Inject(method = "init", at = @At(value = "HEAD"))
    private void yourmodid_helloworld(CallbackInfo ci) {
      //HELLO? DO YOU HEAR ME?
    }
```

Now we are ready to write our code, you may ignore the `CallbackInfo` parameter for now, we'll talk about that in a future guide.

You may aswell do more things when mixin into this method, but since you have just started doing this I recommend you stick to what we are doing for now.

To print our message we will be using our mod's logger, it is a common practice to make a static instance in your mod's main entrypoint. If you
dont have it already, you can add this to your mod's main entrypoint:

```java
public static final Logger LOGGER = LoggerFactory.getLogger("yourmodid");
```

Make sure that the `Logger` and `LoggerFactory` classes are imported from the `org.slf4j` package.

We can now use this instance to print our message to the console.

```java
    @Inject(method = "init", at = @At(value = "HEAD"))
    private void yourmodid_helloworld(CallbackInfo ci) {
      LOGGER.info("Hello World!");
    }
```

And we are done!, when you launch mc you should see our Hello World! message on the console, you just need to look a little bit for it.


## What's next?

Now that you understand the basics or mixin, it's time to go ahead and try to do more complicated things.

We will now try to add our own button the main menu, to do this, we will need to have a deeper understanding on how mixins work.

I strongly recommend you go checkout the [official Mixin documentation](https://github.com/SpongePowered/Mixin/wiki/Introduction-to-Mixins---Understanding-Mixin-Architecture) on how they work.

After you have read that, it's time to do some work.

#### Injection 2: Electric Bogaloo 

Yes, we will, in this case since we are not changing any of the existing buttons on the menu we don't need to use
some of the other fancy things mixin provides.

We are only going to change the method we are mixin into.

It is extremely important you know what to do to know where you want your mixin to be in, so, we are going to dive into Minecraft's code!

You can access the code in two different ways, you can either do middle click in a class reference to a class in Minecrafts's code (even in your own code) or 
you can look for the sources in the External Libraries block at the bottom of the project panel.

Once you are inside your desired class's code, you can start looking for the method you want to target.

For this, it is very important you plan ahead your mixins so you know what to look for. In our case, we are going to add a new button
to the main menu, so we need to look where are this buttons initialized, in `TitleScreen`'s case this "widgets" are initialized on the
`initWidgetsNormal` method, so thats where we are going to mixin.

We now need to change our method target in our `@Inject` annotation.

```java
    @Inject(method = "initWidgetsNormal", at = @At(value = "HEAD"))
    private void yourmodid_addButton(CallbackInfo ci) {
      
    }
```

Note that I removed the line that prints "Hello World!" and I changed the method's name.

Now its time to do the real work.

#### But I don't like him mom!

Remember when I told you to check how mixins work? Well if you haven't you will absolutely need to check that now.

If you have, good, we can continue now. What we need to add a button to a screen it's on the `Screen` class, alright
but how do we access it?

We will need to extend the `Screen` class to be able to access those methods, and the reason is quite simple.

Your code isn't exactly injected into the method you are mixin into, but instead Mixin adds a call to your method at the position you 
asked it to do so.

So technically our code is injected into the target class, as a separete method, and then a call to that method is added where you asked for,
so in our case, since `TitleScreen` also extends `Screen` our code will end up having access to the method we need, but when coding it, it doesnt.

And we need to make the compiler happy, so what do we do? We extend the class which has the methods we need.

This will allow us to access the methods we need while also making the compiler happy and avoiding annoying errors. Remember
that this can also be applied to different cases, not only this one.

I'm not going to go in detail on to how to make a "button", you can always check mc's code on how it's done.

To add a "widget" we need to call the `addDrawableChild` method, this method takes a `Element` as a parameter, the `Element` can be
a `TexturedButtonWidget` or a `ButtonWidget`, we will be using the `ButtonWidget` one.

The `ButtonWidget` class takes several parameters, including the x and y pos, the width and lenght of the button, a `Text` instance and a `pressAction`, 
this can also be a lambda.

This button we are going to add is the same lenght as most of the buttons in the main menu, and should be a little bit over the `Singleplayer` button.

Your method should look like this:

```java
    @Inject(method = "initWidgetsNormal", at = @At(value = "HEAD"))
    private void andromedaproject_hello(CallbackInfo ci) {
        //this method add's the button to our screen
        this.addDrawableChild(new ButtonWidget(this.width / 2 - 50, 1, 200, 20, new LiteralText("hello?"), (buttonWidget -> {
            //this sets the client's current screen to the world selection screen
            this.client.setScreen(new SelectWorldScreen(this));
        })));
    }
```

Feel free to play with all the parameters as you may like. Note that you can also include your own logic in the `pressAction` lambda.

So now that we have added our button, it's time to see if it worked!

As you can see, the button is actually quite high in the screen, but that's the fun of doing this type of things, moving and
changing values to see which one does it correctly.

![](/images/mixin/menu_button.png)

With some moving of values we can make it be at least a little bit nicer.

![](/images/mixin/second_try.png)

My method ended up looking like this:

```java
    @Inject(method = "initWidgetsNormal", at = @At(value = "HEAD"))
    private void andromedaproject_hello(CallbackInfo ci) {
        //this method add's the button to our screen
        this.addDrawableChild(new ButtonWidget(this.width / 2 - 100, 25, 200, 20, new LiteralText("hello?"), (buttonWidget -> {
            //this sets the client's current screen to the world selection screen
            this.client.setScreen(new SelectWorldScreen(this));
        })));
    }
```