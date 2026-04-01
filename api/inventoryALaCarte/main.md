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
//create a menu from a String, the string contain a path
MenuHolder menu1 = MenuFactory.createFromPath(menuPath);
//create a menu from a File, the file contain the YML loaded file
MenuHolder menu2 = MenuFactory.createFromPath(menuFile);
```

those methods return a MenuHolder, wich are basicly your menu YML translated into a java object. Those where the simplest step to create a menu now we can dive in more precise aspect.

Oh and also MenuHolder don't have a constructeur who take a string or a path, for maintability mainly and other reason don't ask

## YML Format

### Introduction

has we've seen into the first menu, it's easy to create a menu from a YML file, but you need to know how to create those YML correclty.

With InventoryALaCarte you can create a menu, manage item in them and what click on those item do.

### Type

as we saw whe a menu is declared he need a type declared by the *type* field

`type : <layout>`

To this day there is only one layout available, but this will change dont worry *trust*

#### generic

The generic layout is the one used for chest for exemple, you have *n*  rows of 9 slots

this layout require another argument called rows wich is the number *n* of rows that the menu will have

```yml
type : "generic"
rows : 6
#this menu will have 6 rows of 9 slots
```

### Title

As we also saw in the exemple, you can have a title like this : 

```yml
title:
  title: <your title>
```

> **NOTE** : title can be formated by using [formatting codes](https://minecraft.wiki/w/Formatting_codes)

You may think having a title categorie just to have one title field in it is stupid but this is because formatting code are deprecated (see [slicelime quote](https://bugs.mojang.com/browse/MC/issues/MC-190605)) so in the future formatting will be through the title catgorie.

### Items

What is a menu without item in it ? to add item in the menu you can use the *items* category. here's an exemple :

```yml
items:
  test:
    slot: 0
    material: OAK_LOG
    lore:
      - "§610$"
    amount: 16
    click:
      - statement:
          if:
            conditions:
              - type: "shopNStore:has_enouth_money"
                currency: "wildWestShopCookie"
                amount: 10
                logic: AND
                not: false
              - type: "is_op"
                logic: OR
                not: false
            actions:
              - action: "shopNStore:give_from_material"
                parameter:
                  material: OAK_LOG
                  amount: 16
              - action: "shopNStore:remove_money"
                parameter:
                  currency: "wildWestShopCookie"
                  amount: 10
          else:
            actions:
              - action: "send_message"
                parameter:
                  text: "§4You don't have enough money to buy that!"
      - action: "close"
```

as you can see items is the most complexe part. so let me explain.

#### add an item

the items field is a list of elements. All of those element have 5 fields

> **Note** : item can have less than 5 fields

- ##### slot

    slot is an integer, it's the id of the slot where the item will be displayed.

- ##### material

    material is the item type, the item will be determine by
    ```java
    Material.valueOf((String) rawItem.get("material"));
    ```
    (rawItem is the current YML items categorie)

- ##### lore

    the name can't be more clear, it's the lore of the item, can be a list, can be colored by [formated text](https://minecraft.wiki/w/Formatting_codes)

- ##### amount

    The quatity of the displayed item 

- ##### click

    this part tell what the heck the plugin is suppose to do when the item is clicked, we'll see that in a minute.

### Clickable event

so a menu need action, to do that you need to have in your item the *click* field. The *click* field is a list of action or statement.

#### Action

Action are simple, it just tell an action to execute and is parameters they are structure like this : 

```yml
- action: "shopNStore:give_from_material"
  parameter:
    material: OAK_LOG
    amount: 16
```
all parameter are specific to an action, so you need to know wich action require what.

#### statement

Statement are basicly condition tree that execute action depending on some condition. they are structure like this:

```yml
- statement:
    if:
      conditions:
        - type: "shopNStore:has_enouth_money"
          currency: "wildWestShopCookie"
          amount: 10
          logic: AND
          not: false
        - type: "is_op"
          logic: OR
          not: false
      actions:
        - action: "shopNStore:give_from_material"
          parameter:
            material: OAK_LOG
            amount: 16
        - action: "shopNStore:remove_money"
          parameter:
            currency: "wildWestShopCookie"
            amount: 10
    else:
      actions:
        - action: "send_message"
          parameter:
            text: "§4You don't have enough money to buy that!"
```

##### if

Statement have one required field : *if*, in there you put two field : *conditions* and *actions*. Conditions tell on wich condition the action will be execute. 

###### condition

Condition is structure as a liste of type, type is the ClickableCondition name : 

```yml
conditions:
  - type: "shopNStore:has_enouth_money"
    currency: "wildWestShopCookie"
    amount: 10
    logic: AND
    not: false
  - type: "is_op"
    logic: OR
    not: false
```

as you can see there is some type, some parameters for the condition and two field called *logic* and *not*

**not** : if true you're condition will be treated as !condition

**logic** tell the logic to use, each element of the list will be test in order, so if you want you can add some logic like AND, NAND, OR, NOR

##### else if

Statement can have as much else if field as you wish. they work like if

##### else

No need to specifie any condition, only the action field is required.
