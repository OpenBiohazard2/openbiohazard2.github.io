# Development of the Script Viewer

## Motivation

The scripting system is the core logic for each room. The scripts are separate from the main executable, which also makes it easier for modders to code custom logic for their own room files. To understand how each room in the game works, you have to understand how the scripting logic works.

The scripting system is only partially reverse engineered. There is enough information for modders to create their own scripts, but we still don't have complete understanding of every scripting function and what each parameter does. To do further research into how the scripting system works, we need an easy way to render all the code in a human readable format.

The room data is stored in a .RDT file. The script data is one part of the RDT file and it is stored as a block of bytes. We consider the scripting data bytes as a .SCD file as if it was a separate file to isolate it from the rest of the .RDT file.

The script in the RDT file is originally stored as one block of hex values, but the ScriptViewer parses the data by splitting it into script commands, and combining script commands into functions using the EvtEnd opcode (0x01) as the separator.

* "init.scd" is the script that executes when the player initially enters a room and only executes once. This script is used for static events.

* "main.scd" is the script that will start running while the player is in the room as part of the main game loop. The entry points are function 0 and function 1. The script engine creates two events and runs each event separately on its own ScriptThread until the function exits, i.e. function 0 will run on ScriptThread0 and function 1 will run on ScriptThread1. These scripts can either spawn other events, which starts a separate ScriptThread, or call other functions on the same ScriptThread. The sub scripts are used for dynamic events.

There are existing tools to do output each function from the script into a separate text file, but there isn't a convenient way to display the script code in one application.

## First iteration: Go and Fyne

The script viewer was written in Go to reuse code from OpenBiohazard2, but only the scripting component was necessary. For the UI, I picked Fyne because it was convenient enough for this use case.

The initial requirements was to have a script viewer that could show the pseudocode and original bytecode side by side and allow me to switch between files in a single program. I was able to get a working version of this UI very quickly and it was able to replace the previous tool that I had to use. Each function would get its own file.

The left panel shows the original bytecode in hexadecimal, which is the same data that can be found if you open the RDT file in an hex editor and search for the sequence of bytes.

The right panel shows the corresponding pseudocode that contains a function name and its parameters. The first hex value in each row is the opcode and the subsequent hex values after the opcode are the function parameters. The opcode parameters are determined in advance by the scripting engine, and the parameter types can be 8 bit, 16 bit, or 32 bit values.

However, there were additional features that I needed, which couldn't be easily implemented in Fyne like syntax highlighting and UI tooltips.

Project Code: <https://github.com/OpenBiohazard2/Bio2ScriptViewer>

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/script-ide-v1.png" />
</div>

## Second iteration: Rust and egui

A newer version of the script viewer was written in Rust and egui. One of the challenges was having to rewrite all the logic to parse the original data files because the Go code couldn't be ported directly. In Go, I just needed to define the data structure of the file and the program could automatically read the entire struct from the file. In Rust, I had to manually read a stream of bytes and fill out the struct fields to get the data.

My initial plan was to implement a clone of the original UI in Go, but I decided to combine all the functions in main.scd into a single tab, instead of separate tabs. This made it easier to jump between different functions. I was also able to implement the additional features like syntax highlighting and UI tooltips much more easily in egui.

Project Code: <https://github.com/OpenBiohazard2/Bio2ScriptIde>

<div style="display:inline-block;">
<img src="https://raw.githubusercontent.com/OpenBiohazard2/openbiohazard2.github.io/main/assets/img/script-ide-v2.png" />
</div>

## Applications

With a working script viewer, we can use this to research each room in the game. OpenBiohazard2 has a working scripting system that can execute both the initial script (init.scd) and the main script (main.scd), but only some scripting functions have been implemented.
