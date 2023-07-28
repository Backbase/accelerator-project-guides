# SDK Navigator - Dive through the SDK interactively
#### Discussion

Sometimes documentation is a little bit lacking, specially when you don’t know where to look or it’s not updated. Having a place to easily check all of the journeys at a glance being always updated offers an unique way to interact with the SDK, specially if you don’t have prior code knowledge which then you’re not used to have any IDE’s built-in tool .

![](./sdk-navigator-dive-through-the-sdk-interactively-0.png)

#### The tool

Tap at the Backbase icon and the documentation window will show up, it contains all accessible configurations that are the ones that the developers have access and work with every single day. Navigate to wherever you need to find.

#### Tech behind it

This feature relies on the Mirror API, the app configuration is read recursively and processed in to iterable objects which are then shown to the use accordingly. A basic sort and grouping is also added to facilitate the user’s experience.