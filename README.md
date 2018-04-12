# HOB-VPN-OSX2Win
A solution that fixes keyboard glitches when accessing a windows machine from a Mac over HOB VPN.

## Problem

When accessing a Windows machine over HOB VPN, some keys and key combinations will not work. The following list contains all combinations found so far and their solution status. If you find new ones, please expand the list.

|Key/Key Combination|Solution|
|---|---|
|z and y switched|solved|
|= not working|solved|
|< and > not working|solved|
|} not working|solved|
|Windows key|solved|
|^ and ° not working|**not solved**|

There are also some combinations that look like they don't work on first sight, but they actually do:

|Key/Key Combination|Out Of The Box Solution|
|---|---|
|@|Left_Control + Left_Option + q (Left_Control + Left_Option = AltGr)|
|€|Left_Control + Left_Option + e|

## Solution

Currently, the solution has been implemented for German keyboards (both on Mac and Win side) only, since the author only has these to disposal. If you can contribute with another languages, you're welcome to fork.

### Idea

The basic idea is pretty simple: use a key remapping software to fix switched keys (z and y) or keys that must "learn" a new functionality (Win key). I used Karabiner Elements (https://pqrs.org/osx/karabiner/) for OSX for this. I like the great configuration possibilities of it (see docs here: https://pqrs.org/osx/karabiner/json.html#complex_modifications-data-structure) as well as especially the fact that you can enable the remappings for specific software only. This saves me the hassle of switching profiles whenever I cmd+tab to the VPN connection or away from it.

Unfortunately, there are keys that don't just have a different meaning on the remote machine, but they never reach that machine. Pressing "Shift + 0" for example didn't print a wrong character (instead of the expected equal sign), it just didn't do anything. To fix these keys you can use AutoHotKey (https://autohotkey.com) for Windows, which allows you to remap keys on the remote machine (it can do a lot more, but we only need this functionality).

The way karabiner and AHK work together is this: we sacrifice the numpad keys and map the combinations that do not make it to the remote machine to a numpad key through karabiner. On the remote machine AHK remaps that numpad key to the desired character. This way "Shift + 0" is mapped to Numpad_0 on the Mac and then mapped from Numpad_0 to "=" on the remote machine.

Of course, the sacrificed numpad keys only make sense on a Macbook, where you don't have a numpad anyway. You are free to change the "bridging keys" to something different, but the solution here uses them.

# Implementation

The file HOB.json contains all the OSX mappings listed in the table above. After installing Karabiner Elements, copy this file to ~/.config/karabiner/assets/complex_modifications/HOB.json. Then you can open the karabiner preferences and switch to the "Complex Modifications" tab where you can import the rules defined in the json file. The rules are defined to only apply when the focused applications' name matches: ^net\\.java\\.openjdk\\.cmd$.

Some of these rules need AHK to run on the remote machine (as stated in their descriptions). The file HOB.ahk contains the mapping rules for the remote machine. You can compile them into a runnable exe using AutoHotKey or you can use the exe in this repository which has been compiled from this file. It's probably best to put the AHK exe into the startup list so it runs on every system (re)start.

**!! Warning !!**
If you also have analog access to the remote machine, remember that using the numpad on a hardware keyboard on-site will type wild characters (all our "bridging keys"), not the numbers you probably intended.
