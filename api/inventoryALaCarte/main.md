# InventoryALaCarte

Having a way of doing menu with ease what inevitable, so here it is ! This API provide a easy way to do menu (like in hypixel with inventory and item) thought YML files, register clickable event and even have a little logic gate system.

## contents

## Getting started

If you want to use this API you first need to know how to setup it. Unlike other API like cattamand who are just some class file this API is a plugin with listener, config file and other plugin related behavior. To use it simply add it as a dependency in Intellij in CompileOnly, then in your server you'll need to have inventoryALaCarte as a jar in your plugins folder. You can find the latest compiled verison [there]().

## Your first menu

Sooo let's getting started, the all point of InventoryALaCarte is that it's easy to create a menu directly from a YML file. here's an exemple : 

```yml
title :
  title : "§ftitle !"
type : "generic"
rows : 6
```

### Type

Menu yml just require one field to work : `type : <layout>`
This field define wich layout will be used by the menu, like chest, dispenser or event stonecutter ! (In the exemple the generic layout is used wich require one additionnal field called raw)

see [here](api/inventoryALaCarte/layout.md) the current list of available layout 

### Title

you can give a custom title to your menu with the title field : 

```yml
title:
  title: <your title>
```
> **NOTE** : title can be formated by using [formatting codes](https://minecraft.wiki/w/Formatting_codes)

You may think having a title categorie just to have one title field in it is stupid but this is because formatting code are deprecated (see [slicelime quote](https://bugs.mojang.com/browse/MC/issues/MC-190605)) so in the future formatting will be through the title catgorie.

### Registering the menu

Now that we have a simple menu well we don't have it in game we just have a yml file. To register a yml file as a menu and use it in you plugin you'll need to do those step : 

#### import the menu factory

```java
import me.titruc.inventoryALaCarte.menu.menuLoader.MenuFactory;
```
in order to register a menu the MenuFactory class is available. This class handle menu creation.

#### create a menu

you can create a menu from a `String` path or from a `File`

```java
MenuHolder menu1 = MenuFactory.createFromPath(menuPath);
MenuHolder menu2 = MenuFactory.createFromPath(menuFile);
```
