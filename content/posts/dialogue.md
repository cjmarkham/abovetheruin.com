+++
date = '2026-01-10T06:00:00Z'
title = "Dialogue - What do I say?"
+++

The first system I designed when I started **Above the Ruin** was the dialogue system.
I wanted something that would make it easy to write dialogue, but also easy to extend for potential modders.

The first part was how to load in the dialogue. I went with JSON files as this is easier to manage, structured and can be extended.
```json
{
  "dialog": {
    "16_spring": {
      "text": "So, {playerName}...",
      "nextNode": "nextOne"
    },
    "nextOne": {
      "portrait": "angry",
      "actions": [{
        "type": "moveNPC",
        "targetRelativePosition": {
          "y": -1
        }
      }],
      "text": "It's day {dayNumber} of {season}. Are you sure you want to go down in to the tower?",
      "choices": [{
        "text": "Yes",
        "nextNode": "nextOne_yes"
      }, {
        "text": "No",
        "nextNode": "nextOne_no"
      }]
    },
    ...
```
Each object in the JSON represents a line of dialogue, with several options depending on the NPC and the conversation.

But first was how to decide which dialogue "node" to choose when interacting with an NPC.
I originally thought about having conditions inside of each dialogue node. 
```json
{
  "conditions": {
    "day": {
      "op": "eq",
      "value": 12
    }
  }
}
```
In the above example, the dialogue node would be selected if the current day was 12.
This comes from my software engineering background and databases, and that's exactly how I'd need to treat this JSON file.
If I wanted to see which dialogue node was needed, I'd need to loop through every node and see if the conditions can be satisfied.
For a relatively small game like **Above the Ruin**, it wouldn't be too bad. But I still didn't like it.

So the next option was "candidates". As you can see in the screenshot, the key of certain nodes is in a specific format, for example `16_Spring`.
The idea behind this is that the conditions are added to the keys. I then build a list of candidates, based on things like the current day, season, time etc.
```c#
string[] candidates = {
  $"{ctx.hour}_{ctx.day}_{ctx.season}",
  $"{ctx.day}_{ctx.season}",
  ctx.day.ToString()
};
```
This means that I only need to check for a match in the keys.
```c#
foreach (string candidate in candidates) {
  if (file.dialog.ContainsKey(candidate)) {
```

There's a lot more to the dialogue system, like actions and choices, but I'll leave that for another post. 
