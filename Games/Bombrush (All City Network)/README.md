All City Network is a mod for the game Bombrush Cyberfunk that turns the game into an online multiplayer game.


## Debug Menu

*-STATUS: Not Fixed*

**- BUG:** All City Network's debug menu can be accessed by normal users through various exploits, giving those users the ability to 
join any game lobby that they want. This can be done by editing packets sent out by the client, or through patching the debug flag in All City Network

**- FIX:** The easiest fix would be to make the Join Lobby feature in the debug menu restricted in the same fashion other options are, like the ban menu.

--------------------------------------

## Broken GRACE Packets

*-STATUS: Not Fixed*

**- BUG:** There are no filters on GRACE Packets, allowing a bad actor to send packets to insta win through crafting a custom packet saying they just
tagged X amount of spots.

**- FIX:** Add a filter that checks how many spots are actually in the race to begin with, and another filter that acts a sane rate limit for how many spots
a client can say they tagged in one go.

--------------------------------------

## NaN remote crash

*STATUS: Not Fixed*

**- BUG:** A bad actor can pick a target by teleporting to them, then they can set various local values to NaN in their client, causing the target's game to crash when their client tries to render the bad actor. 

**EXTRA INFO ON HOW:** Bombrush, and Freesoul for that matter, seem to be leaving this out in the open due to them assuming the unity engine will handle it, however unity starts to fail on that front when it comes to rendering and or doing physics. BombrushMP doesn't have any special number checks when taking data from one client, sending it to the server, and also when sending those packets out to other players. The exploit is in DisguiseScale , CurrentAnimationTime , Velocity, and also can come from the a bad actor's player model's legs, however it's hard to pin point exactly what causes it due to NaN infecting other values. Regardless of not being able to pin point the exact cause, setting all 3 of those values (DisguiseScale , CurrentAnimationTime , Velocity) alongside triggering the server to pick up those values to send out will cause remote crashes almost every time for whoever tries to render it. 

**- FIX:** Add a filter when sending and or accepting packets that they have safe values.

--------------------------------------

## Unity Max Float Value Nuke

*STATUS: Not Fixed*

**- BUG:** Teleporting to a bad actor who has a speed of (3.402823 * 10^38), and a location of (3.402823 * 10^38), (3.402823 * 10^38), (3.402823 * 10^38) will
cause the victim's game to crash or lock up at best. 

**EXTRA INFO ON HOW:** When things like speed and location float values reach  \(3.402823 \times 10^{38}\), the engine starts to forget numbers past the decimal.
Setting your speed and location to all be that max float value causes your game to freak out, but nothing you can't easily fix by reloading the map. When the victim tries to teleport to you though, it breaks the victim's game in a way where it either crashes, or they are forced to close the game on their own. Bombrush, and Freesoul for that matter, seem to be leaving this out in the open due to them assuming the unity engine will handle it, however unity starts to fail on that front when it comes to rendering and or doing physics. 

**- FIX:** Add a filter when attempting to spectate and or teleport that ensures the player you are teleporting has safe values.

--------------------------------------

## /GetAuth Command Leak

*-STATUS: Fixed*

**- BUG:** The command /GetAuth allows admins to get the discord profile and description of any player using their ID, but there are no checks to ensure
the player using the /GetAuth command is actually an admin, allowing anyone to grab the personal and admin made infomation on any player.

**- The Code Behind The Vuln:**
> // BRCServer.cs, Line 564 ---------------
case "getauth":
            {
                var parsedArgs = CommandUtility.ParseArgs(message, 1);
                if (string.IsNullOrWhiteSpace(parsedArgs[0])) break;
                if (ushort.TryParse(parsedArgs[0], out var result))
                {
                    if (Players.TryGetValue(result, out var resultPly))
                    {
                        if (resultPly.ClientState != null)
                        {
                            // Sends the Discord ID back to the player who typed the command!
                            SendMessageToPlayer($"<color=yellow>Auth Description for Player {result}: {resultPly.ClientState.User.Description}", player);
                        }
                    }
                }
            }
            break;
--------------------------------------

## Auth Keys In Raw Text

*-STATUS: FIXED In Version 0.17.0*

**- BUG:** All City Network would send it's auth keys from the clients in raw text/http, allowing anyone on the same network with a packet reading 
tool to get other's auth keys

**- FIX:** Hash the keys before sending them
