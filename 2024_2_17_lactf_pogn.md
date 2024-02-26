# 2024 LACTF web/pogn

Pogn in mong.

Links: (link to game website)
Files: .zip containing source code

## Solution

This challenge was a pong game that ran in the browser  
You could control your paddle with your mouse and the opponent's paddle was just set to the y value of the ball  
The flag was delivered when the ball made it past the opponent's paddle  

Our initial thoughts were to send a really really small decimal for our velocity which would break the normalization function and in turn make the ball move so fast that it skips over colliding with the opponent's paddle.  
However, before we were able to get that working, we solved the challenge by leaving the game running, switching tabs, and leaving for a bit to go get some water. When we came back we had won the game and gotten the flag.  
We think this happened because the tab was out of focus so perhaps the network communication got messed up and that in turn broke the collisiono detection for the ball.

The intended solution was to use `ws.send(JSON.stringify([1, [[0, 0], [0, 0]]]))` which works because "when the server tries to normalize the [0, 0], it gets a NaN and the win check results in usr winning"
