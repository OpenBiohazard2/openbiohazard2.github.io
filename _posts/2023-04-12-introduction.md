## Introduction

OpenBiohazard2 was developed as an alternative open source software to the classic Resident Evil 2 PC version. The original Resident Evil 2 was launched in 1998 for Playstation 1. The game is a survival horror game that uses pre-rendered backgrounds. Biohazard 2 is the Japanese name for Resident Evil 2 and these names will be used interchangeably in this document.

When the original Resident Evil 2 was ported to PC, the application was designed for Windows 98. However, the newer computer operating systems, Windows 7 and above, can't run the game directly. Currently to run Resident Evil 2 on the PC, the user has to download the Sourcenext version, which is an updated 2006 PC version of Resident Evil 2, and download a custom DLL developed by a 3rd party developer in order to get the game working. In the long run, it won't be sustainable to have to keep modifying a DLL to patch a closed source program.

The other alternatives to running Resident Evil 2 is to download an emulator and run one of the various ports for the game, since the game was ported to N64 and Gamecube.

OpenBiohazard2 is written in the Go programming languages and uses OpenGL for the graphics. In the latest build of the game, the user is able to load the rooms in the game and walk around inside. For most levels, if there is a door, the player can access other parts of the game by going through the door. Some of the scripted game events will run when the player enters a trigger zone.

However, the game is still unplayable for multiple reasons. There are no zombies or puzzles to solve, which are needed for the game story to progress. One of the biggest challenges is reverse engineering the original code, which has been decoded to a certain extent by the modding community, but there is still not enough information on the rest of the code. There is enough knowledge to develop a standalone mod for the game, but not enough to rewrite the entire game in a different programming language.

Since each room in the game is defined by the scripts in the room, the next step is to figure out how each function in the scripting system works. I have been working on a GUI to display the room script in a cleaner format. There is still a lot of remaining work to be done before the project reaches a playable state.
