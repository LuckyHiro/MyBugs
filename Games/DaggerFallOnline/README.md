# DaggerFlaw Exploit Bug Report
*-STATUS: Not Fixed*

*-FOUND: 10-24-26*

This repo contains the write up the *DaggerFlaw* exploit in DaggerFall Online, alongside *DaggerFallen* which is a modded client cheating online saves using most of the same logic flaws that allow the *DaggerFlaw* exploit to work. Figuring out this exploit was just a fun side project as I wanted to understand where majorly AI led projects function under the hood, and we they might have failure points at, which in this case seems to be client trust, alongside trusting their own anti cheat rather than truly understanding the game itself. 

DaggerFlaw is an DFO exploit that allows you to easily edit your entire save file on their server using arbitrary state manipulation and a business logic flaw. The exploit is more of a whack a mole situation due to how DFO online was made, with it being "patched" while making this, but all that changed a small part of the commands, so until there is a major rewrite of the anti cheat and online systems, it will almost always be around in some fashion. 

- **Dagger Fall Online Source Code / Github Repo:** https://github.com/Lattymoy/daggerfall-js-source

# DFO's requested bug report info:
> - **"What happens, and what you expected to happen:"** Save editing, and that's what I expected
> - **"The steps that make it happen again:"** Listed Below
> - **"Where it happens:"** takes place online in game 
> - **"What it gives a player, or could give one:"** Anything I want really in terms of a save file.

## steps To Recreate:
>  - 0: Learn judge/anti cheat's large limits, and short times.
>  - 1: open dev tools
>  - 2: go to sources
>  - 3: ctrl + p
>  - 4: search for worldTick
>  - 5: copy the worldTick file name (has the id we need in it)
>  - 6: go to console
>  - 7: use the worldTick id to start crafting commands to turn the save file into an object you can edit in the console
>  - 8: Launching from a custom client allows you to get around the world tick part, and directly hit the save file

-----------

# DaggerFlaw Exploit Report:

<details>
  <summary><b>surfaces </b></summary>

  - **Client Version Doesn't Matter:** You can connect to the server using older versions of the client, which is most likely due to the amount updates pushed out by the agents running the project, as there's multiple new versions each day. The specific version I used for this was the newest at the time, version 0.416, which was the newest for roughly 9-10 hours.

- **.md files EVERYWHERE:** the repo of dagger fall online has a ton of .md files, which seem to act as the "notes" of the agents who work on it, with there even being various layers. I haven't dug through all of them, but it doesn't appear that the creator behind the project isn't deleting any of the md files, so there's a solid chance some important info is in there some where, like the various exploit aduit .md files.

- **Console Not Disabled:** The electron console is free for you to use, with very little restrictions

- **No Idle Timer:** There's no anti-afk/idle kick system in place, leaving the door open for easy automated commands.

- **Access To Save File Through Console:** you have full access to your local electron based saved file that the server is reading from. This requires that you grab your world tick ID from sources, and turn into an object you can call upon through the console, but other wise there's no security on it. Changes made to the save file are subjected to the "judge" and "bucket" systems that I explain below, but they are very easy to get around
> Command to open save file: globalThis.debugPlayer = (await import('dagger://game/assets/worldTick-XXXXXX.js')).e_;
</details>

<details>
  <summary><b>The Judge </b></summary>

  - DFO has a "judge" that is meant to act as the main anti cheat system, outside of the bucket system below. It has filters/checks for a lot of things, however the only check it does for items in your inventory is ensuring the item is legal, allowing you to spawn in any item, and duplicate any item you already have.

- You can also just fully max out your stats in one go, but they have to be within the legal limits (100 or 200)
> Max Stats Command: (async () => {
>  globalThis.debugPlayer = (await import('dagger://game/assets/worldTick-BqoqAdrV.js')).e_;
>  const dp = globalThis.debugPlayer;
>  if (!dp) { console.error('debugPlayer not found'); return; }
>  if (dp.stats && typeof dp.stats === 'object') {
>    for (const k of Object.keys(dp.stats)) {
>      dp.stats[k] = 100;
>    }
>    console.log('Stats maxed:', dp.stats);
>  } else {
>    console.warn('No stats object found');
>  }
>  if (Array.isArray(dp.skills)) {
>    dp.skills = dp.skills.map(() => 200);
>    console.log('Skills maxed:', dp.skills.length, 'skills set to 200');
>  } else {
>    console.warn('No skills array found');
>  }
>})();


- Spells are completely unchecked, so not only are you able to add what ever spells you want, you can also heavily edit the spell's given stats, like damage, timer, etc.


- Health, Magicka, and Fatigue are also missing checks, allowing you to give yourself god mode using a command that auto runs every second to change those values.
> Unless you are automating it, you can just use the save file command and manually edit the "health", "fatigue", & "magicka" values.
>
> Save File Command: globalThis.debugPlayer = (await import('dagger://game/assets/worldTick-XXXXXX.js')).e_;
</details>

<details>
  <summary><b>The Door </b></summary>

  - DFO also has a "bucket" system that the "judge" checks on to try and catch cheaters by adding limit on how much your character could earn in a given hour. The limit is based on your level, but the limit is almost always high. Fresh accounts (lvl 1-10) can start out with 10-20k gold per hour safely, but should have a much larger limit than that (hence why its safe regardless of it being a lot).

- After level 31, the limit is always 300k an hour, 1.8m per 6 hours. But until that level, I used a python script to figure out the limits for levels 1-30.
> - Bucket Limits Per Level:
>   - 5:  30,000 | 180,000
>   - 10: 60,000 | 360,000
>   - 20: 120,000 | 720,000
>   - 30: 200,000 | 1,200,000
>   - 31: 300,000 | 1,800,000

> Unless you are automating it, you can just use the save file command and manually edit "goldPieces"
</details>
-----------

# Modded Client based on DaggerFlaw PoC (Custom Compiled Client):

- Using some of the same issues that allows *DaggerFlaw exploit* to work, you can go much further than what daggerflaw allows, but it's not nearly as easy to setup. Daggerflaw works off of the worldTick id, as you can't directly reach the player save through the console in another way (that I know of). This, alongside various other things mainly in the world.js/worldScene.js scripts heavily limit what you can do with this exploit. However, since DFO is open source, alongside the server's anti cheat basically being a single fence in an open field, you can easily go edit the project and recompile it yourself to have almost direct access to anything that happens on your device with this game, which is almost everything.

- **Player Entity:** With a modded client, you can just directly reference the player entity, rather than just the world tick id. This allows to do everything we could do with the worldTickID/daggerflaw, and more as we access to the full player file, rather than just it's save file.

- **Mod Menu:** The first thing I did was turn the commands ive been using in the daggerflaw exploit, into actual buttons/toggles so I could automate them easier. To make this work, I just made a simple script that could be toggled with the 0/Zero key, and then I added the script in as an import in *"play/index.html"*. Afterwards though, I started adding functions to world.js and worldScene.js that would allow me to do entirely new things, like:
> - Auto roll for marks in the background until i reached the daily limit/20
> - Teleport to player houses, banks, and houses for sell
> - List/teleport to your houses (when in the town of your house)
> - Noclip forward
> - Auto house buyer
> - List houses for sell
> - Use Bank Menu anywhere indoors
> - Use Notice Board anywhere in a town
> - Add to stacks of items I already own
> - Use Any Aura

- And then of course, I was also able to get all of the same things daggerflaw allowed me to do, but now it was better and more automated.
> - Hourly gold drops that are right under your level's limit
> - Quick 10k/100k gold drops per 10/20 seconds
> - Remove 10k/100k gold
> - Auto level up per 6 minutes or 1 hour
> - Auto Health, fatigue, and Magicka
> - Delete/Clone/Repair Items
> - Max skills and attributes
> - Custom/illegal spells

