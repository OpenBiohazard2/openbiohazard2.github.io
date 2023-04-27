# Room 1010: Kendo's Gun Shop

After escaping the zombines on the street in the beginning of the game, the player will enter Kendo Gun Shop. The owner of the store Robert Kendo will point a gun at the player, but realize the player is a human and instead locks the door of the gun shop to prevent anyone else from entering. However, the zombies will eventually find a way into the shop by smashing the glass.

As mentioned earlier, in every room, the game will run the initial script "init.scd" when loading the room and run "main.scd" after the room is loaded. Only some part of "main.scd" will be run as part of initialization.

When the player interacts with a trigger area, some function "main.scd" will be executed, which determines the game events. The trigger area will be referred to as an AOT. There are multiple types of AOT.
1. AOT type 0 does nothing.
2. AOT type 1 is for the area around the door that lets the player enter or exit.
3. AOT type 2 is for the area around the items or static objects, e.g. ammo.
4. AOT type 3 and 4 are unknown.
5. AOT type 5 is for triggers. When a player steps on this area, a function in the main script will be executed.

The entities will be identified by the a pair of numbers known as the work set id. The player's work set is (1, 0), Kendo is (3, 0), and the four zombies are (3, 1), (3, 2), (3, 3), (3, 4). The zombies are initialized in the beginning of the code, but will only appear when they break into the store.

## Room initialization

The initial script "init.scd" will run. AOT 0 and 1 will be initialized for the doors, one for the entrance and one for the exit.

ScriptThread 0 will run starting function 0. Function 0 calls function 2. Function 2 will call function 4.

1. AOT 13-19 and 21 will be initialized with type 4.
2. AOT 8 and 9 will be initialized as items with type 2. Objects will be initialized with id 1 and 2. These objects correspond to handgun ammo.
3. AOT 2 and 4 will be initialized with type 0.
5. AOT 3, 5, 6 will be initialized with type 5 as trigger areas.
6. AOT 0 was reset and assigned type 5.
7. Entity 0 initialized using SceEmSet with id 72 (Kendo)
8. Entities 1, 2, 3, 4 initialized using SceEmSet with id 31 (zombies)
9. AOT 11 initialized as an item with type 2, but the item is unknown.

At end of running ScriptThread 0, we have a lot of AOT initialized (AOT 0-6, 8, 9, 11, 13-19, 21), static objects 1, 2 initialized, and dynamic entities 0-4 initialized.

1. ScriptThread 1 runs starting function 1 and calls function 3. This thread initializes the bit array (6, 62) to 1, (5, 4) to 1, (8, 15) to 1.
2. ScriptThread 2 runs starting function 5 and calls function 6 four times to run member set for zombies.
3. ScriptThread 3 runs starting function 8. The camera view is changed to camera 5 and the player position is set to (X=-15312, Y=0, Z=-13362).

No more script threads will run until the player steps into a trigger zone, which will trigger the cutscene with Kendo.

## Cutscene with Kendo

The cutscene will trigger when you try to leave the store through AOT 0 or when you try to move inside the store and step on AOT 5 or AOT 6. In any case, there will be an EvtExec call to function 22.

ScriptThread 0 runs starting function 22. There are sleep calls to control the timing of each camera view.

1. AOT 0, 5, 6 get reset to type 0. The AOT can't trigger anything.
2. Camera view changed to camera 10. Zooms out from player and shows both Leon and Kendo.
3. Camera view changed to camera 11. Focus is on Kendo, who raises his shotgun.
4. Camera view changed to camera 5. Focus is on player.
5. Camera view changed to camera 10. Shows both player and Kendo.
6. Camera view changed to camera 11. Focus is on Kendo, who lowers his shotgun.
7. EvtExec called for functions 23, 25, 27.
    1. Function 23 controls work set (1, 0) (player)
    2. Function 25 controls work set (3, 0) (Kendo)
    3. Function 27 controls work set (1, 0) (player)
8. Camera view changed to camera 5. Focus is on player. Kendo walks over, locks the door, and talks to the player.
9. EvtExec called for functions 26, 24, 9.
    1. Function 26 controls work set (3, 0) (Kendo)
    2. Function 24 controls work set (1, 0) (player)
    3. Function 9 controls work set (3, 0) (Kendo)
10. Camera changed to camera 0. It is zoomed out and different from the previous views. Player can now move.
11. AOT 0 and 4 are reset. They are assigned to type 5 and will trigger function 28.
12. EvtExec call for function 19. Function 19 has gosub call to function 21.

## The zombies break the glass

When the player moves to the back of the store to pick up ammo, they will step on AOT 3, which will call function 14, but won't trigger an immediate reaction. Function 14 will set AOT 2 to be type 5. When the player tries to leave the store or move closer to Kendo, the player will step on AOT 2 and trigger function 10.

Function 10 will be run.
1. AOT 2 and 4 will be reset to type 0.
2. The position of 4 zombies is initialized for work set (3, 1), (3, 2), (3, 3), (3, 4). These are the zombies that will break the glass and attack Kendo.
3. EvtExec will be called for functions 13 and 30.
4. AOT 0 and 4 will be reset to type 4. AOT 18 will be reset to type 5 and trigger function 29.

In function 13, there are a large number of SceEsprOn() class that are run, which seems like the glass shattering.

In function 30, the limbs of one of work set (3, 0) (Kendo) will move, which is when Kendo is fighting the zombies. In function 30, there is a EvtExec() call to function 31. In function 31, there are more sprite calls and a EvtExec call to function 32. This part seems to correspond with the zombies attacking Kendo.
