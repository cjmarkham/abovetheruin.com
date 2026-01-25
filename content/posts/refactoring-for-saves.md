+++
date = '2026-01-25T16:20:00Z'
title = 'Refactoring for Saving'
+++

Up until now, I've been having fun creating systems. However, when it came to
wiring them up, I was a bit cavalier with how that was done. I did try to separate 
concerns as much as possible, having controllers to do the logic while renderers would
listen to events that they emitted.

However, if we take the `PlayerController` as an example, it had a lot of references on
it for UI. Things like the dialogue wrapper, conversation box etc. For this vertical slice, 
it wasn't too bad. However, I'm at the stage now where I need to move the player to a new scene.

The player itself is marked as `DontDestroyOnLoad`, meaning they can persist through scene changes.
After all, I don't want a different player in each scene. This also means that anything it relied on,
such as the UI elements that were referenced, need to also be persisted.

With all of these things persisted, it means I can send the player to a new scene and everything should just work.

However, if the player goes back to the original scene, we end up with duplicates, and even another player. 
This is because they existed in the scene before runtime, and we've just loaded that level again. So we end up with 
the pre-placed player, and the one we persisted.

So, I created a "bootstrap" scene. This scene holds all of the things that need to be persisted. All of the different UI canvases,
managers, some controllers etc. All of that is held in a parent `GameObject` and marked as `DontDestroyOnLoad`.
As soon as the game starts, that scene is loaded and I immediately start loading the actual starting scene.
Once that has loaded, the `GameRoot` script will spawn the player, I no longer rely on the player being in the scene before runtime.

After that, there's a few bits and pieces that need to be wired up, like renderers. This is where the `UiRoot` comes in. 
This also sits in the Bootstrap scene and contains references to all the needed UI components such as dialogue, inventory etc.
The `GameRoot` emits an event when the player is spawned, and the `UiRoot` subscribes to it, binding the different presenters that need it.
For example, the `HealthController` is bound to the `HealthPresenter`, `InventoryScreenCoordinator` to the player etc.

```csharp
public void OnEnable() {
  gameRoot.playerManager.PlayerChanged += OnPlayerChanged;
}

public void OnDisable() {
  gameRoot.playerManager.PlayerChanged -= OnPlayerChanged;
}

private void OnPlayerChanged(PlayerController pc) {
  healthPresenter.Bind(pc.healthController);
  inventoryScreen.BindPlayer(pc.inventoryController, pc.combatController);
  currencyPresenter.Bind(pc.currencyController);
}
```

A lot of things that held references when they shouldn't needed to be refactored.
It was a war of attrition, refactoring, playing the game and fixing the errors one by one.
But I'm glad I decided to do it now, rather than later. Scene transitions are working now, which was the last
big hurdle before I could put everything together.

I've also got part of the save/load system in place. Because when you leave a scene, it needs to be saved. I need to store 
which objects have been placed in the scene, then I need to load them once you go back to that scene.
To solve this, I created a `SceneState`, which will eventually be used by the main save/load module by having a list of 
`SceneState` and then writing to a JSON file. I'll do another post about that when I've completed it.
 
I'm sure there's still something lingering, some reference that I didn't move or one that I didn't set. 
But I'll find those as I start wiring everything up in to a playable slice.
