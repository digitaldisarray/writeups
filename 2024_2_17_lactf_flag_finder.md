# 2024 LACTF rev/flag-finder

Speak with the almighty flag. Perhaps if you find an item it likes, it will be willing to share some of its knowledge? I'm sure it's in the room somewhere... Note: Enter the flag using all lowercase. M1/M2 Macs must use Rosetta.

Files: .zip for each game architecture

## Solution

When you launch the game there are NPCs standing around that will want a specific item. There is also a flag NPC that wants a special item. However, the flag NPC doesn't accept any of the items that you give it.

Looking at the game files it appears that it is a GameMaker game, which is fair to assume because of the data.win file.

After googling and lots of trial and error, we find a tool that is able to decompile/decode the data.win file called [UndertaleModTool](https://github.com/UnderminersTeam/UndertaleModTool).  

With undertale mod tool we can open the data.win and see the code for the flag:
![Screenshot of UndertaleModTool showing code for the flag](https://github.com/digitaldisarray/writeups/blob/main/img/flagfinder/flagCode.png?raw=true)
In the screenshot, we can see that it wants an item with an ID of 20  
What if we could just change the item that the flag NPC wants to be an item that we can actually obtain? Lets look at the code for an NPC that we have succesfully given an item to:
![Screenshot of UnderTaleModTool showing code for the teacher](https://github.com/digitaldisarray/writeups/blob/main/img/flagfinder/teacherCode.png?raw=true)
The teacher wants an apple item in the game, which looks like it has an ID of 21. So, if we just change the flag's desired item id to be 21, we can give it the apple and get the flag. Thankfully UndertaleModTool lets you just edit the code and export a new data.win file.  

Now lets run the game with the new data.win and get the flag!  
![In game screenshot showing the flag](https://github.com/digitaldisarray/writeups/blob/main/img/flagfinder/flag.png?raw=true)

After giving the apple to the flag NPC, the flag is reavealed: lactf{k3y_to_my_h34rt}  

Quick Summary:
 - UndertaleModTool
 - Edit code for: gml_Object_object_flag01_PreCreate_0
 - Set its required item to 21 (the apple)
 - Run game with new data.win file
 - Give apple to the flag NPC
 - Flag is revealed: lactf{k3y_to_my_h34rt}
