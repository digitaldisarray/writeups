# DiceCTF 2024 rev/dicequest writeup
#### Description:
Try 2024's hottest game so far - DiceQuest! Can you survive the onslaught? Custom sprites made by Gold

note: the flag matches the regex `dice{[a-z_]+}`
#### Files (.zip archive):
 - dicequest: ELF binary x86-64
 - ./assets/: A folder with some sprites
## Step one: finding out what to do
Upon running the game we are greeted with a player which we can move with WASD, small blue dice which give us points upon collision, as well as a larger blue dice.
![Game.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Game.png?raw=true)

Upon collision with the larger blue dice we are given a shop menu where we can buy some abilities with our hard earned points.
![Shop.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Shop.png?raw=true)
The "Tame Dagon" item is particularly interesting, but how could we possibly collect that many tokens? If only there was a tool that we could use to edit our games memory.  
  
What does the game mean by "Tame the dragon army" anyways? Well, after standing still in the game for a big a swarm of dragons come out of nowhere and attack you. I guess if we survive the dragons we will get the flag.
## Step two: scanmem & GameConqueror
After some quick googling to find a tool that can help us cheat in linux games, a tool called scanmem and its GUI frontend, GameConqueror show up.   
Link: https://github.com/scanmem/scanmem  

After installing GameConqueror we can attach it to the game process and start scanning it's memory! Lets scan for our current score, which is 0. And to make sure the dragons don't get us we can pause the game while we are scanning with the command "kill -STOP game_pid"  
![Scan.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Scan.png?raw=true)  
After the first scan for every int32 with the value of zero in the games memory, we came up with just over 203 million results. Lets try to narrow it down by unfreezing the game (kill -CONT game_pid) and collecting some points.  
![Scan2.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Scan2.png?raw=true)  
After collecting two dice for ten points we can scan again. Except, instead of just scanning the entire process again, GameConqueror will search only the parts of memory that were equal to zero before. And after collecting more points we only come across 292 places in memory that were once zero, but are now ten. So lets collect 5 more points and then scan again.  
![Edited.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Edited.png?raw=true)  
After scanning for 15 we found just one memory address that was once 0, then 10, and now 15. So we can safely assume this is our point counter. To edit it we double click it to bring it down into the variable list and then double click the value to add some zeros to the end. Now lets unfreeze the game and try to buy the "Tame Dragon" item.  

![LotsPoints.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/LotsPoints.png?raw=true)  
Upon unfreezing, the value hasn't changed however thats probably because the number in the UI is different than the int we modified, so when we collect 5 more points it changes and shows the updated/correct value.  
  
After buying "Tame Dragon" and waiting for them to show up, they no longer attacked me and instead started flying outward and started forming odd shapes... that spelled out the flag!
![Flag.png](https://github.com/digitaldisarray/writeups/blob/main/img/dicequest/Flag.png?raw=true)  
Its harder to read in a still image, but the dragons shape out: DICE{YOUR_FLAG_IS_NOT_IN_ANOTHER_CASTLE} however, we do know from the challenge description that it will be in all lower case, so the flag is dice{your_flag_is_not_in_another_castle}

## Hindsight
This felt like a cheesed solution since it was so quick to solve but the creator confirmed it was the intended solution.