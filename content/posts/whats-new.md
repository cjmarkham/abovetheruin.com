+++
date = '2026-02-16T08:00:00Z'
title = 'What's new?
+++

It's been a while since I posted an update, so I thought now would be a good time to 
let you all know why that is.

Other than the full time job that I have, I've been working on **Above The Ruin** every day on my way to 
release a playabe demo.

During this time, a lot of changes have happened. Most recently (yesterday in fact), I implemented
a save and load system. I already had something in place for saving and loading stuff for scene
transitions, but I didn't have anything to save the entire game state, in such a way that
you could load a game rather than starting a new one each time.
Now, you can save the game, and the state is saved as a JSON file, including things like game state and the state
of every placed item, including things like the inventory of that item.

I've also completely changed the grid and interaction behaviour. The last version worked, but
was over complicated and had a few problems.
It used to have two methods of interaction. The player would do a ray cast in front of them, which would be 
used to interact with "interactables", such as placed items or NPCs. If the ray reported no hit, I'd then 
defer to the grid and ask it if there was anything in this cell that I could interact with (such as a plot of farm land).
I would then also query if we had a tool equipped, and try to use that.

Being a 2D game, I wasn't happy with the usage of physics to interact, but also wasn't happy with the different paths.
I also had 3 grids, one for placeable items, one for autotiling, and one for autotiling overlay (watered farm plots).

I've completely changed all of this now. There's one grid, with a class that has methods such as `Register`. When an 
item is placed, it calls this `Register` method to store its cell and what it is. The same thing will happen for NPCs
once I add movement to them. When they move, and their cell changes, they'll register their new cell with the grid.

On interaction, I get the cell in front of the player (based on facing direction) and ask what's in this cell.

Another thing I implemented is cutscenes. These are JSON files, with a list of action groups. Actions within each group are
ran in parallel, but action groups themselves are ran sequentially. This enables things such as panning the camera and moving the player in
one action group, then triggering dialogue in another, after the panning and movement have completed.
Cutscenes have the ability to also give items, start quests and teleport the player.

Here's an example of the JSON for a cutscene:

```json
{
  "id": "bridge_shout",
  "skippable": true,
  "actionGroups": [
    [
      {
        "type": "start_dialogue",
        "npc_name": "Stranger",
        "dialogue": [
          {
            "text": "Hellooooo! You there!"
          }
        ]
      }
    ],
    [
      {
        "type": "set_player_facing",
        "value": "right"
      }
    ],
    [
      {
        "type": "start_dialogue",
        "npc_name": "Stranger",
        "dialogue": [{
            "text": "Come over here, I hate shouting."
        }]
      }
    ],
    [
      {
        "type": "move_player",
        "target": {
          "x": 0,
          "y": -2
        }
      },
      {
        "type": "move_camera",
        "target": {
          "x": 0,
          "y": -3
        }
      }
    ],
    [
      {
        "type": "move_player",
        "target": {
          "x": 5,
          "y": 0
        }
      },
      {
        "type": "move_camera",
        "target": {
          "x": 8,
          "y": 0
        }
      }
    ],
    [
      {
        "type": "start_dialogue",
        "npc_name": "Stranger",
        "dialogue": [
          {
            "text": "I haven't seen you around here before, when did you get here?"
          },
          {
            "text": "...Not a talker ey? (Looks like it's gonna be one of those games)"
          },
          {
            "text": "I've been gathering some supplies for a trip, but the stairwell here is blocked."
          },
          {
            "text": "We could rebuild this bridge if we had some wood, then I could use your stairwell."
          },
          {
            "text": "Of course, I'd give you some supplies for helping me out."
          },
          {
            "text": "Go and gather some wood. You'll probably find loads of the stuff scattered on the roof. Bring it back here when you're done."
          }
        ]
      }
    ],
    [
      {
        "type": "start_quest",
        "id": "gather_wood"
      }
    ],
    [
      {
        "type": "move_camera",
        "target": {
          "x": -7,
          "y": 0
        }
      }
    ]
  ]
}
```

These cutscenes are also triggered when the player leaves their house for the first time of the day. If they don't leave that day,
the cutscene will play the next day.

```json
{
  "days": {
    "10": ["BridgeShout"]
  },
  "quests": {
    "gather_wood": "GatheredBridgeWood"
  }
}
```

Each day can have a number of cutscenes, which will all be played sequentially.

Other than all of that, there's been some "business as usual" things, such as bug fixes, pixel art improvements etc.

With a little more polish, and some more gameplay added, I should be able to release a video of the vertical slice :) 
