+++
date = '2026-01-14T07:20:00Z'
title = 'Removing Actions from Dialogue'
+++

The dialogue system has gotten a bit too complex and is doing too much. 
When I wrote the system, which was the first one I wrote, I envisioned dialogue with actions.
Those actions could trigger before text is rendered or when the interact button was pressed. 
Actions would be ran sequentially. For example in the JSON below, the NPC would move down 1 tile, then up 1 tile.
There was also an action to move the camera, which followed the same schema. 

```json
{
  "default": {
    "portrait": "angry",
    "actionTrigger": "afterContinue",
    "actions": [
      {
        "type": "moveNPC",
        "targetRelativePosition": {
          "y": -1
        }
      },
      {
        "type": "moveNPC",
        "targetRelativePosition": {
          "y": 1
        }
      }
    ]
  }
}
```

However, this is limited. There was no way to move the camera along with the NPC, since all actions run sequentially.
So I had the idea of replacing this with action "phases". Each phase held a list of actions. These would be ran sequentially, or in parallel,
depending on a "runInParallel" flag in the JSON.
I think this could work, and solve the limitations of the current implementation. However, when I was looking through the code and planning
how to do this, I realised that the dialogue system was doing several things:
- Dialogue and choices
- Action orchestration
- Injected nodes - When you get an item, it injects a node to say "You got 99x Carrots", before proceeding with normal dialog
- World state mutations - Open shop, accept quest

I realised that I was building a cutscene system and making the dialogue system control it. This makes the dialogue system more complex, for basic dialogue,
while also lacking in some features that a cutscene system would have, such as hiding NPCs, handling actions for multiple NPCs etc.

So, for now, I've removed the concept of actions from dialogue. I'll bring them back, but they will be part of the larger cutscene system.
