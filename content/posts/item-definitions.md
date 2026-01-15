+++
date = '2026-01-15T06:10:00Z'
title = 'Item Definitions'
+++

With a lot of items in **Above the Ruin**, it's important that I get them right. They should be easy to create and easy to extend.
Items can range from something like carrots, to water collectors equipped with a tarp funnel. Each one can have several different
properties like being able to consume them or place them in the world and finding a way nice way to do this was tricky.

The first iteration of items was a simple `ItemDefinition` Scriptable Object. Each definition contains base properties such as an 
id, name and description. Then they also have properties depending on their use. For example, a carrot may be consumed and it may heal
the player's HP. Then, a farm plot may be placed in the world and can be walked on.
```c#
[CreateAssetMenu(menuName = "Items/Item Definition")]
class ItemDefinition : ScriptableObject {
  public string id;
  public string name;
  public string description;
  public bool canPlace;
  public bool canWalkOn;
  public bool canConsume;
  public int hpHealed;
}
```

As you can see, even with just those few configurations, the `ItemDefinition` gets messy and does far too much.

As I was adding producers, items that can be placed and produce another item over time, I decided to tackle this complexity.

The way I've done this is to have a base `ItemDefinition` which can be extended with modules. Each module holds configuration
for its intended purpose. For example, here's a `ConsumableDefinition`:

```c#
[CreateAssetMenu(menuName = "Items/Modules/Consumable")]
public class ConsumableModule : ItemModule {
  [Header("Whether consuming this restores health")]
  public bool doesRestoreHealth;

  [Header("How much health is restored on consumption")]
  public int amountOfHealthRestored;
}
```

So far, this only tells us whether this item restores health on consumption. This can be extended though for other things such as stat boosts.

The `ItemDefinition` is then given a list of modules:

```c#
[SerializeField] private ItemModule[] modules;
```

And then I can see if an item definition contains a specific module with a `TryGet` method;

```c#
public bool TryGet<T>(out T module) where T : ItemModule {
  foreach (ItemModule m in modules) {
    if (m is not T t) {
      continue;
    }

    module = t;
    return true;
  }

  module = null;
  return false;
}

# Example usage
definition.TryGet<ConsumableModule>(out var consumableModule)
```

There are a few other different modules, such as:
- `Placeable` - Defines the prefab to be placed
- `Sellable` - Defines how much the item sells for
- `Usable` - Defines if the item has limited uses
- `Producer` - Defines the item and amount that's produced and over what time length

I'm sure there'll be a few more definitions that get added, but this modular system makes it very easy to add them, and add new items.

![Item Definition](/images/item-definition.png)
