# Room 1010: Kendo's Gun Shop

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/kendo-gun-shop-overworld-room1010.png" />
</div>

After escaping the zombies on the street in the beginning of the game in room 1000, the player will enter Kendo Gun Shop, which is shown as room 1010 in the above image. The owner of the store Robert Kendo will point a gun at the player, but realize the player is a human and instead locks the door of the gun shop to prevent anyone else from entering. However, the zombies will eventually find a way into the shop by smashing the glass. The player will have to leave the shop and proceed to room 1020, which is the back alley.

In this article, we will be running the original Resident Evil 2 and the open source version OpenBiohazard2. The open source version has partially implemented the game, which means some features such as zombies won't show up. The open source version will render the areas for debugging and log the script statements in the output, which makes it much easier for us to understand how the game works internally.

The script viewer will also be used to understand the code. As mentioned in the script viewer article, in every room, the game will run the initial script "init.scd" when loading the room and run "main.scd" after the room is loaded. Only some part of "main.scd" will be run as part of initialization. The functions will be referred to by a numerical id, based on the order in which they appear in the file. "init.scd" only has one function "Function 0" and for this room, "main.scd" will have functions 0 to 34.

When the player interacts with a trigger area, some function "main.scd" will be executed, which determines the game events. The trigger area will be referred to as an AOT. There are multiple types of AOT.
1. AOT type 0 does nothing.
2. AOT type 1 is for the area around the door that lets the player enter or exit.
3. AOT type 2 is for the area around the items or static objects, e.g. ammo.
4. AOT type 3 is unknown.
5. AOT type 4 is for messages that show up when the player interacts with an object.
6. AOT type 5 is for triggers. When a player steps on this area, a function in the main script will be executed.

## Room initialization

### Init script

The initial script "init.scd" will run. AOT 0 and 1 will be initialized for the doors, one for the entrance and one for the exit respectively.

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/kendo-gun-shop-room1010-init-script.png" />
</div>

<br>

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/kendo-gun-shop-room1010-doors.png" />
</div>

The red blocks are the collision entities, which would be used for the walls and counters. The light blue rectangle represents the trigger area and the blue area represents the door trigger. Leon will enter Kendo's Gun Shop at AOT 0 on the top right and exit AOT 1 on the bottom right.

### Main script

ScriptThread 0 will run starting function 0. Functions 2 and 4 are called indirectly using gosub().

1. AOT 13-19 and 21 will be initialized with type 4. The purpose is to display messages when the player uses the action button next to a location.
2. AOT 8 and 9 will be initialized as items with type 2. Objects will be initialized with id 1 and 2. These objects correspond to handgun ammo.
3. AOT 2 and 4 will be initialized with type 0. They won't have any effect now, but will be activated later.
5. AOT 3, 5, 6 will be initialized with type 5 as trigger areas. Stepping on these areas will execute script code.
6. AOT 0 was reset from a door AOT and assigned type 5. AOT 0 corresponds to the entrance area and leaving through the entrance will execute script code.
7. Dynamic entity 0 initialized using SceEmSet() with id 72 (Kendo). Dynamic entities 1, 2, 3, 4 initialized using SceEmSet() with id 31 (zombies)
8. AOT 11 initialized as an item with type 2, but the item is unknown.

At end of running ScriptThread 0, we have initialized the following:
1. AOT initialized (AOT 0-6, 8, 9, 11, 13-19, 21)
2. Static objects 1, 2, which correspond to ammo
3. Dynamic entities 0-4. Entity 0 corresponds to Kendo and entities 1-4 correspond to zombies.

The image below shows what is initialized:

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/kendo-gun-shop-room1010-step1.png" />
</div>

In this article, we will mostly focus on AOT 0-6, which are used to trigger the cutscenes. The remaining script threads will set some variables.

1. ScriptThread 1 runs starting function 1 and calls function 3 to set bit array values. The purpose is unknown.
2. ScriptThread 2 runs starting function 5 and calls function 6 four times to run MemberSet() for zombies. The purpose is unknown.
3. ScriptThread 3 runs starting function 8. The camera view is changed to camera 5 and the player position is set to (X=-15312, Y=0, Z=-13362).

No more script threads will run until the player steps into a trigger zone, which will trigger the cutscene with Kendo.

## Cutscene with Kendo

The cutscene will trigger when you try to leave the store through the entrance (AOT 0) or when you try to move inside the store and step on a trigger area (AOT 5 or AOT 6). For every trigger area, the script will be run by a EvtExec() call to function 22. An EvtExec() call in the script code will start a new script thread and execute any code that was defined for that function.

In Resident Evil 2, the game uses prerendered backgrounds and each room will have multiple cameras. Based on the zone the player is in, the game will determine which camera and prerendered background to show. During the cutscene, the camera and prerendered background will automatically be changed to show different views of a scene. There are sleep calls to control the timing of each camera view. At the end of the cutscene, the camera will focus back on the player to make it easy for the player to know when they can regain control of their character.

ScriptThread 0 runs starting function 22.

1. AOT 0, 5, 6 get reset to type 0. These areas previously were used to trigger the cutscene, but setting these areas to type 0 ensures the cutscene is only triggered once.
2. Camera view changed to camera 10. Zooms out from player and shows both Leon and Kendo.
3. Camera view changed to camera 11. Focus is on Kendo, who raises his shotgun.
4. Camera view changed to camera 5. Focus is on player.
5. Camera view changed to camera 10. Shows both player and Kendo.
6. Camera view changed to camera 11. Focus is on Kendo, who lowers his shotgun.
7. EvtExec() called for functions 23, 25, 27 to control player and Kendo animations.
8. Camera view changed to camera 5. Focus is on player. Kendo walks over, locks the door, and talks to the player.
9. EvtExec() called for functions 26, 24, 9 to control player and Kendo animations.
10. Camera changed to camera 0. It is zoomed out and different from the previous views. Player can now move.
11. AOT 0 and 4 are reset. They are assigned to type 5 and will trigger function 28. This area seems to trigger when the player tries to leave through the entrance and Kendo will tell the player to stay inside.

In the image below, you can see the result of the changes:

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/kendo-gun-shop-room1010-step2.png" />
</div>

When we compare OpenBiohazard2 to the original, we can see the timing mostly matches, except for the last camera view where Kendo locks the door.

**OpenBiohazard2**

<div style="display:inline-block;">
<video width="800" height="600" controls>
<source src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/video/kendo-gun-shop-openbiohazard2-kendo.mp4" type="video/mp4" />
</video>
</div>

**Original Resident Evil 2**

<div style="display:inline-block;">
<video width="800" height="600" controls>
<source src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/video/kendo-gun-shop-original-kendo.mp4" type="video/mp4" type="video/mp4" />
</video>
</div>

## The zombies break the glass

When the player moves to the back of the store to pick up ammo, they will step on AOT 3, which will call function 14, but won't trigger an immediate reaction. Function 14 will set AOT 2 to be type 5. When the player tries to leave the store or move closer to Kendo, the player will step on AOT 2 and trigger function 10.

Function 10 will be run.
1. AOT 2 and 4 will be reset to type 0.
2. The position of 4 zombies is initialized. These are the zombies that will break the glass and attack Kendo.
3. EvtExec() will be called for functions 13 and 30.
    * In function 13, there are a large number of SceEsprOn() class that are run, which seems like the glass shattering.
    * In function 30, the limbs of one of work set (3, 0) (Kendo) will move, which is when Kendo is fighting the zombies. In function 30, there is a EvtExec() call to function 31. In function 31, there are more sprite calls and a EvtExec() call to function 32. This part seems to correspond with the zombies attacking Kendo.
4. AOT 0 and 4 will be reset to type 4. These two areas previously were triggered when Kendo was alive and Kendo would tell the player to stay inside. However, since Kendo is dead, there is no need for this trigger.
5. AOT 18 will be reset to type 5 and trigger function 29. The purpose of this function is unknown.

When we compare OpenBiohazard2 to the original, we can see the timing matches. The cutscene takes around 10 seconds, but the horror effect is missing from OpenBiohazard2.

**OpenBiohazard2**

<div style="display:inline-block;">
<video width="800" height="600" controls>
<source src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/video/kendo-gun-shop-openbiohazard2-zombie.mp4" type="video/mp4" />
</video>
</div>

**Original Resident Evil 2**

<div style="display:inline-block;">
<video width="800" height="600" controls>
<source src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/video/kendo-gun-shop-original-zombie.mp4" type="video/mp4" type="video/mp4" />
</video>
</div>

## Further Improvements

OpenBiohazard2 has implemented the cutscenes and triggers correctly, but there are still parts of the game that haven't been implemented.

Tasks needed to match the original game:
* Show the models and animations for Kendo and the zombies
* Allow the user to pick up ammo and add to the inventory.
* Show the shotgun that Kendo drops after getting attacked by zombies
* Play the background music for this room
