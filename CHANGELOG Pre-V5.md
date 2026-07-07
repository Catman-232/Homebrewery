# 4.7.30 - 2026-04-15 - Follow-up x2

### Added
- Config option ``Constant Hurt Eyes at Low HP`` to make the hurt eyes state stay on between low health flashes rather than toggle at half the rate.

### Fixed
- Truly I need some decent sleep for once. I reverted the StatStruct stuff to the wrong point, meaning the removed stats were uncapped rather than non-functional like I said in the previous notes. I'd feel bad if I left that unchecked, so I've sorted it out. The Resists and Magic Defense & Crit stats should be zeros now, in maybe a week or two (if Kiseff does manage to update the game this month) they'll be gone anyway and I'll make a quick update to uncomment out the new stats (Ailment Resist & Elemental Power) and the renamed ones (xResist -> xPower). **I cannot yet make aliases for the stat names, so custom items will need to be updated to rename the stats in their params.**
- When attempting a Mesh or ArmorRender assignment while AtlyssGLTF is unavailable, HB will now check if the asset string contains a '-', and if it does, it will return NothingMesh/NothingArmorRender rather than ``null`` so the assignment doesn't technically fail. This should prevent a useless flood of errors when you don't have AtlyssGLTF installed. Custom items that wanted to use custom model parts will probably look the same as they already did without AtlyssGLTF anyway.

### Changed
- Redid the jiggle thresholds patch since the UI code was still winning over it, now I've used transpiler patches on both UI methods to make them work for me.
- If for some reason a PNG file that should be inside of Internal is missing, HB will now log an error about it and proceed with creating it a 1x1 empty Sprite instead of exploding.

### Removed
- A redundant SFX folder and files had been left inside Internal, at some point I moved them all to be imported the HomebreweryFiles way instead and I guess I forgot to remove them.

# 4.7.25 - 2026-04-12 - Follow-up

### Fixed
- Commented out some WIP experimental code that will only work in the newer version of Unity and Mirror that QATest is upgraded to. It can cause some error spam in the live version due to using methods that didn't exist in it.
- The StatStruct "bridge" idea totally didn't work at all, and I haven't a single clue why. So I've reverted back to the vanilla struct, but I have left the to-be renamed fields commented out, this means I can't provide the aliases for the property names after all. Since Kiseff has changed those property names in the next update, it would end up exploding the entire mod if HB was still trying to access them, but I can't pre-emptively implement accessing the new named ones either since that will explode the entire mod right now. Very cool. I hope you can survive without the resist stats on your weapons for a couple weeks (hopefully).
- A bool check for a gear item being vanilla or not was unintentionally inverted, meaning certain custom item properties wouldn't display.
- Certain model parts on custom gear might not have been updating when editing the param files. Auto-reloading in the old code is pretty jank, so I don't expect it to be working perfectly even still. It's a lot cooler in the new content loader code set I've been grinding away at.

# 4.7.22 - 2026-04-09 - Stranger in a Strange Land

### Added
- Config option added ``Disable Vanity Sparkle``, which blocks any instance of that sparkle effect happening, which is most often caused by a player changing their character's appearance.
- A warning log output has been added for when an image file is loaded that is not really a PNG, Unity will still handle the image's bytes if it was actually in JPG format, but this has unpredictable graphics quirks that Unity also complains about. It would be preferable to ensure your texture files are actually PNGs, the file extension being ``.png`` doesn't really make it a PNG internally.
- MouthClosed consumable effect to pair with the MouthOpen one.
- Config options: ``BellyJiggleThreshold``, ``ButtJiggleThreshold``, ``BoobsJiggleThreshold``. These allow you to override the value the game uses to disable the jiggle bones when the proportion of the body part is smaller than the value. In vanilla this value is 35 for the belly and butt, however the game doesn't do it for the boobs, they can always jiggle if an equipped chestpiece allows them to, so you can use this setting to make a threshold for them too if you wanted.
- (QATest) Kiseff added two new properties to StatStruct: ``_elementalPower`` which gets added to all of the element resist/power stats (See below in Changed, they were all renamed!) and ``_ailmentResist``, which appears to be both a chance for a status effect to "miss" applying and also for the duration to be reduced. Both are integer properties. These will not do anything until the game updates.

### Fixed
- HB now reacts to dye consumables being used in order to set all slots colours, rather than only when you use the context menu button labelled "All Gear", this should mean it'll work for anyone using a controller. No idea what I can do about the lack of context menu though, that's on Kiseff.
- I think I accidentally figured out what was causing custom hair meshes on Byrdle to be textured strangely, so hopefully they will now look fine.

### Changed
- The reload hotkey no longer works when in-game, instead there's a new chat command for it in "reload". Using it like that is the same as Ctrl+R, using it with "force" as a subcommand (e.g. ``/hb reload force``) will do what Ctrl+Shift+R does, reload all files without checking if they were even updated. It's jank right now so there's not much use for it anyway, the auto-reloading for param files and PNGs does a decent job, but for models and sounds it might fail to reassign properly so it wasn't great to have been able to accidentally input it previously.
- (QATest) Kiseff renamed all the ``_xResist`` stats to ``_xPower`` e.g. ``_fireResist`` is now ``_firePower``, I have created my own struct to act as a staging platform to transfer old to new, but I cannot really have the code trying to access both the old and new properties right now, so these stats will be offline until the game updates.
- I've tinkered with the temporary clamp points to try to get items back down to near vanilla maximums again. I will get the config file for it done eventually! They're all +5 (or +5%) higher than the vanilla maximums (or specifically those in QATest last I checked).

### Removed
- Kiseff removed ``_magicDefense`` and ``_magicCriticalRate`` from StatStruct, they will no longer do anything for custom items.

# 4.7.13 - 2026-01-23 - Caught Somewhere in Time

### Added
- Added a subcommand to testdye: "drop" (or "give" / "spawn"), which will locally drop a TEST Dye item for you, in case you needed one and didn't want to get it from Skrit.
- HB Call alias for the Mad Statue, which I didn't notice has the same lowercase first letter of name quirk that Ammagon has.
- Added a config setting: "NametagDistance" which lets you customise the distance that nametags will appear within, if there's line of sight to them.
- HB Call can now be used to skip to a specific dialog branch for the NPC you called, for example, index 3 is the Homebrewery Shop on Skrit (unless any others were put in his list first). All you have to do add a number as an extra argument after the NPC's name, like ``/hb call skrit 3``.
- Kubold claws, their hand and feet ones, will now be assigned the current chest texture. Kinda like how the Chang hands and nose work, in vanilla they have one of the default chest textures but it never gets changed when you change skin. Now they can be recoloured or made transparent, the UVs for them are in the middle of the bottom half of their chest texture, looks a bit like an X there.
- Weather command now has subcommands: "on", "off" and "reset". Using the command without a subcommand works just as it did already, using "on" or "off" will force the weather to be on/off, and set the buffer value to -600k so that the roll to change weather will reasonably not happen again unless you managed to run the game for almost an entire week. "Reset" will set the buffer back to 0, where it will then count upwards to the map's weather change interval as normal.

### Fixed
- Maybe fixed an infrequent case of NullRefException when the game was saving the character file before you were even fully loaded in, which meant the custom stuff wasn't ready to be saved yet.
- Patching for CanDieInSanctuary has been simplified, and a new patch has been added to reset health to max when entering or leaving a safe zone by any means, like teleporting or walking into one inside the current map, like Ammagon's Hut.
- Turns out the vanilla code never attempts to hide Chang noses when culling the head for full helms, so I added a line of code to do it.

### Changed
- ``Log Command outputs in BepInEx Console`` has been replaced with ``Chat Command Output``, which is now a multi-select setting that covers logging in the chatbox, logic message feed (bottom left corner) and the Bepin console, or just nowhere at all.

# 4.7.3 - 2026-01-09 - Follow-follow-follow-up

***Hotfix patch made by Mario***

### Fixed
- Kiseff changed the type of chat input back to TextInput, which caused errors to be thrown out in CondEffectManager. No actual code changes are involved, but HB required a rebuild to fix it.

# 4.7.2 - 2026-01-08 - Follow-follow-up

### Fixed
- AtlyssCommandLib updated and removed an unintentional namespace that was unintentionally being used in my code, so it unintentionally broke. At least this also means I can update the readme to include the bit about this dependency, haha.
- Setting the time directly to 12am or 12pm would end up with the Day/Night aspect set opposite to what it should be.

# 4.7.1 - 2026-01-08 - Follow-up

### Fixed
- Equipment tooltips were broken due to the game not null-checking the new fields related to equipment being able to have condition effects on them or something??? Custom items don't have that yet (please don't beg me to add this in a hurry) so the fields were left null, I've put in empty objects so the game doesn't cry about it.

# 4.7.0 - 2026-01-08 - Chat Commands Deluxe - A little earlier than I hoped

### Added
- HARD DEPENDENCY ADDED: [Soggy_Pancake's AtlyssCommandLib plugin!](https://thunderstore.io/c/atlyss/p/Soggy_Pancake/AtlyssCommandLib/) This comes with improvements to the chat commands such as help text, which I was sorely lacking, contributed by Soggy_Pancake themselves who pretty much did all the code conversion for this anyway. Thanks also to Marioalexsan for helping with the code merging. I think CommandLib enables some interesting uses such as serverside commands, so there might be a cool thing or two to try out in the future.
- Config settings have been added for the ability to disable NPC bounceback and collision hitboxes. ``Disable NPC Knockback`` for the one that bounces you off when you jump at/on them, and ``Disable NPC Collision`` for the normal collision they have when you walk into them. If you leave that on but turn off the bounceback one, you can stand on top of them!
- A special alias for HB Call has been added: "portal" (also "worldportal" and "world portal"), which allows you to directly call the World Portal (huh?) if there's one in the map you are in, and it's attuned already.
- A new chat command has been added: "weather", a host/singleplayer only command to toggle the weather on or off, assuming the map you are in can have weather at all.
- A new chat command has been added: "tail" (or "tailmode", "capetailmode"). This lets you force the CapeTailMode state to either "cape" (default, just does whatever the cape wants it to be), "up" or "down". This is part of the custom properties savefile where the slot dyes and part names are kept. There is a CodeYapper packet for this so that it will be synced to other clients too.

### Fixed
- Custom ArmorRender based Helms that lacked a Mesh for any race could cause the game to scream NullReferenceException. This happens due to the glb file not containing a Mesh for every single race. Note that you don't really have to do it this way unless you need to alter the placement and scale of the model per race, you could just use a vanilla ArmorRender and use your custom Mesh as the OverrideMesh instead for a much easier time making a Helm.
- Slash hotkey will no longer work when the Settings menu is open, so it can't interfere with any input capturing parts of the UI.
- Weapon type specific vanilla sound effects should be being assigned properly now.

### Changed
- Custom sound effects are now stopped four seconds after when the player that started them dies.
- Log messages for each content pack currently being processed have been reimplemented.
- Altered the 1x1 texture used for the Dyable Cape to be a proper (0, 255, 0) Green so it recolours via Dyes properly.
- HB Call will now end the active dialogue if you call someone else without ending it first, which is possible using ChatMacros.
- Maximum Knockback effect strength has been doubled again. Now it's about four times larger than what the game clamps damage based knockback at.
- The weapon type "Sword" was renamed to "Blade", so I've added another alias for that.

### Removed
- Kiseff removed ``_weaponConditionSlot`` from ScriptableWeapon, which was where the on-hit condition effects were defined, so for the time being I have commented out the code that handled this aspect for custom weapons.
- He's also removed ``Cmd_AddCondition``, which was what enabled the Gash, Burn and Cold conditions to be applied by custom effects, so I have commented that out for now as well.

# 4.6.40 - 2025-12-08 - If It Ain't Broke, Don't Fix It

### Added
- Config setting added ("UnarmedDiveJump") so you can turn off the tweak to Unarmed that adds the dive jump to it.
- Added HB Call alias for Ammagon, who sadly doesn't get a capital A in his object data name like the other NPCs do, this is important since the Unity method for finding the GameObject is case-sensitive and my code makes the first letter of the input be capitalised so you don't have to type that manually for the other NPCs.

### Fixed
- Texture assignments for the Chang nose and hands, as well as the specific texture areas (boobs, arms, upper legs, lower legs, feet) were not applying on the character creation screen.
- Calling HB Phone by chat command was not updating the DialogTrigger position or setting the current map instance's portal data for the World Portal UI.
- Gear display wasn't refreshing as it should when changing armor or vanity slots due to a now malignant fix for the issue of players that join while you are on a different map being naked when you first see them, which was recently fixed by Kiseff, turns out that was a vanilla bug after all hah.

### Changed
- Updated the game version warning since we're not on a3 anymore.
- Not particularly noteworthy but I implemented my own code fixes for the things Mario made those hotfixes for while I was still asleep that morning.

## 4.6.35 - 2025-11-27 - Hotfix Hotfix Part 2 (Flamin' Hot)

***Hotfix patch made by Mario, go yell at them if something breaks***

### Fixed
- The projectile set container for shotguns wasn't grabbed correctly.
- Weapon type mapping didn't take into account case insensitive matching and hold clip index clamping, should be fixed now.

## 4.6.34 - 2025-11-27 - Hotfix Hotfix (Hot)

***Hotfix patch made by Mario, go yell at them if something breaks***

### Fixed
- The way scriptable weapon types work has been changed. In particular, weapon type names have been changed, and hold clip indexes are gone. To deal with this, old weapons will have their weapon type and clip index remapped to the new weapon types.
  - For example: "Dexterity_Melee_2H" with index 0 will be remapped to "Katars", "Strength_Melee_2H" with index 2 will be "Polearm", etc.
- Scriptable weapon projectile set paths have been changed, so those had to be synced to the new paths.

## 4.6.33 - 2025-11-23 - Follow-up

### Fixed
- Accidentally made all the previously unavailable custom weapon stats use maxStamina instead of what they should be using.

# 4.6.32 - 2025-11-23 - Infinite Improbability

### Added
- Not exactly "added" but this is kind of important: Since people keep asking about it, stat maximums for custom weapons have all been set to 100 (except the percentage ones, they are clamped at 0.25) **for now**. In the future I will be implementing a config file so solo players/server hosts can customise the stat maximums, so you can define your own "how much is too much?" point. Also in the future, the relevant stats will be made available for all gear items.

### Fixed
- capeTailMode seemed to be managing to reset itself and not reapply when it should, especially noticeable in the inventory UI.
- "SuperRefreshGearSlot" was being a bit too eager to refresh the leggings display when the chestpiece changes and a robeSkirt no longer is present. Not much point in trying if there's no leggings item anyway.
- Apparently those damn item type tab buttons on the shop UI started acting up again, I changed some code in response to vanilla changes before even checking they were broken in-game first, but they seem to work as expected for me now so hopefully they will for everyone else.

### Changed
- Since the WIP new content loader code has become such a drag, I have implemented a really bodge async conversion for PNG loading in the current (old) code, so it might load them faster and with any luck it will not have broken them somewhere along the process.
- One of the item display name transpiler patches lost its target due to a bit of code being refactored, but all I had to do was change the method name for the target.
- Max level for custom weapons is now 32 (Level cap).
- Max vendorCost for custom weapons is now 2820 (Fier Blade).

### Removed
- The ancient dye index clamping patch has been deprecated since Kiseff has now implemented a getter/setter for the dye index that works like character part indexes do.

# 4.6.28 - 2025-11-06 - Taking It Easy, Is Actually Kinda Hard

### Fixed
- Missing gear refresh events when toggling boobs via effects and when swapping from a chestpiece with a robeskirt to one without.
- LimitCfg settings may have been resetting when packets were arriving from other players.
- When a Player joined while you're in a different map, the next time you see them they may have been nude due to a lack of gear display refresh. Not sure how this isn't also a vanilla issue considering the cached item fields in my component turned out to be empty, implying the vanilla gear display method never happened to begin with.

### Changed
- Reworked how the limits clamping is being done so that it should actually take into account whether the PlayerRaceModel it's being called for is your character or someone else's.

# 4.6.25 - 2025-10-30 - Under The Water, Carry The Water

### Added
- Vanilla tails are now spread to every other race. I couldn't tell you what compelled me to implement this and suffer the high amount of trouble it took to alter the positions for every single tail for every race individually, no promises that they're perfect. Fun fact: Chang's tails are inexplicably 100 times smaller than the other tails, which is to say that every tail from another race had to be scaled down by 100, and their tails had to be scaled up by 100 on other races. I also implemented the labels for the character customisation UIs.
- All the folders inside TextureReplace now contain a text file that lists all of the textures that go inside that folder, should mean that TSMM will actually bother to copy the folders to your BepInEx > config folder now since it was skipping them for being empty. The folders should also be up to date for the current version of the game too.

### Fixed
- Wasn't checking a race even has any miscs in either of their misc part arrays before trying to grab it for recolouring.
- Collar and shoulderpad colour adjust properties weren't being defaulted when swapping to vanilla chestpieces.
- The Knockback effect should work as expected again now.

### Changed
- Local part sync, like in the character select and vanity mirror UIs, may be better at actually doing it now.
- Item display name post-processing has been converted to a batch of direct Transpiler patches that don't need to do any string altering. Please let me know if I have missed any instances though, there are a loooooot of places the game displays or prints item names.

# 4.6.19 - 2025-10-23 - One Thing, I Don't Know Why

### Added
- Subcommand added to ``/hb jiggle`` called ``reload``, which tells BepInEx to load the file again, which should mean any changes you make to it while the game is running will end up loaded. Might be more convenient than all the individual setting subcommands.

### Fixed
- Forgot that the dye indexes wouldn't have been clamped before I was trying to use them when setting up a CharacterDisplayManager on other players.
- Shader for the eyes and mouths has been swapped again so that the vanilla part textures that are inexplicably translucent don't end up looking like stained glass. This probably means we can't use transparency in customs again but I don't think anyone even was anyway, considering it was never possible before.
- Tinkered a lot more with prompting the character display to refresh without opening the floodgates on it usually wanting to go every frame during the buffer period.

# 4.6.15 - 2025-10-20 - A Hotfix A Day, Keeps The Modders At Bay

### Added
- A new config setting has been added: "ResizeEffectsGetSaved". When true, proportion effects from items or the ``fx`` and ``scale`` commands will update the values in the character save file, making the changes permanent. The game networks your appearance with the values from the save file rather than the current displayed values, so this also means the game will send your mid-effect proportions to other players whenever an event prompts a resync (such as changing gear). HB will only invoke saving the file when the effects end.
- Speaking of which, I have added new subcommands to ``scale`` - ``save`` and ``reset``. When you use save, HB will collect up your currently displayed values and store them in your save file. When you use reset, HB will reload your display values from the save file. In both cases it will resend appearance to the server.
- Furthermore, when proportion effects end they will prompt an appearance sync to the server, updating the state of your proportions for others. If a player has HB they'd have seen the effect happen anyway, but sending a new appearance will "canonize" it for all players who didn't see the effect, including vanilla players, though the scale limits would stop them from seeing anything ridiculous. It'll also mean that players that joined after the effect had started will at least get a bit of a catch up on what you look like in your own game.

### Fixed
- Certain vanity slot changes were reloading character appearance, but the part markers packet wasn't telling it to refresh again when receiving it. The indexes were actually being set, so certain actions would suddenly cause the right parts to display anyway.
- Resetting the hideHair custom property was too powerful and was enabling hair to display even when the head was hidden due to the isFullHelm property.

# 4.6.11 - 2025-10-19 - Dye Hard With A Vengeance

### Added
- Vanilla dyes are now also set to be unperishable (infinite uses).
- A donation link is now on the mod's page since a couple people asked me about that, all at about the same time the other day.

### Fixed
- Regression from recent hotfixes causing custom sound effects to never play from other players since the check for whether they were on the same map as you was tricked into believing nobody was real.
- The player model in the vanity mirror wasn't applying its scale.
- ``/hb scale`` commands for boobs, torso, butt, arms or muzzle were not displaying correctly since the vanilla code is not redundantly setting the values into the blendshapes every frame anymore. Now the effects themselves do it, only while they're active.
- Setting gear into Vanity Slots wasn't refreshing the gear display.
- AllDyes packet wasn't doing its job because it wasn't properly being told how many dyes were in the packet.
- Custom helm property hideHair was hiding your hair indefinitely, probably since the vanilla code isn't trying to turn the renderer back on constantly anymore.

### Changed
- File path strings on Windows can now be longer than 260 chars thanks to a bit of work by Marioalexsan to make them use a fancy prefix that tells the IO code the path can be longer than the default buffer, something like that. I believe this wasn't a problem for other operating systems.

# 4.6.6 - 2025-10-15 - Kiseff Never Sleeps, While I Get Bad Sleep

### Fixed
- Kiseff renamed VersusCondition to VersusFlag, which broke my patch for the "you fragged player" messages' method, which still does not actually check that either entity is actually flagged for PVP. My patch does it so you don't see those messages when people take damage/die in safe zones (normally not possible).
- Kiseff improved the GetButton method to not need a redundant argument every time its called. I was using this in CameraDX's component script to mimic the vanilla camera gamepad controls, so that broke.

### Changed
- I've attempted to reimplement caching what gear has already been displayed so as to not be running the code every single frame, this means my code is attempting to tell the vanilla code to not redo it every frame so as to not invoke my code to run every frame in "retaliation", I also tried to optimise how the Materials are being grabbed in my code too.

# 4.6.4 - 2025-10-14 - I Dyed My Eyes Red And lost My Depth Perception

### Added
- CodeYapper dependency, swapping from the deprecated CodeTalker dependency. Packets that were just number values have been swapped to Binary packet form, which should make them smaller by a nice margin. These packets won't sync between the previous version of HB, or via CodeTalker either, so you'll experience some desync while everyone updates and swaps over.

### Fixed
- Freecam being switched off on death was being done even if Freecam wasn't actually on.

### Changed
- Since Depth is being such a pain right now, I have set it to just be the same as Width until I can get it to actually display and sync as intended.

# 4.6.3 - 2025-10-12 - Dye One Thousand Deaths

### Removed
- I have deprecated the test build since 1000+ people downloaded it already and only a handful of people actually came with any testing feedback (which I am very thankful for), so there wasn't really any point in making a test build at all was there?
- Kiseff just removed the cached armor fields that were being used to stop the gear display method from firing every single frame for no reason, so I guess that means my code has to run every frame too. Ugh.
- He's also changed where the flattened effect is being set and I don't have the patience to find it, so for the time being the consumable effect that would use it is dead.
- Might as well say my brain is removed, I don't feel anything inside my skull but pain.

# 4.6.2 - 2025-10-12 - The One Where I Nearly Dye

### Added
- A new chat command has been added, ``jiggle``, which lets you edit the ``JiggleSettings.cfg`` values. Using it with no arguments will toggle the custom jiggle on/off : ``/hb jiggle``. Specify a body part to print out the current values of all four properties, e.g. ``/hb jiggle boobs``. Specify a property as well to print only that one's value e.g. ``/hb jiggle boobs inert``. Finally, add a value between 0 and 1 as the last argument to update the value of that property e.g. ``/hb jiggle boobs inert 0.5``. The value will be clamped to within 0 and 1.
- Config setting to enable deactivating the void-out rescue feature, for whatever reason you'd have to do so.
- Undocumented change in the previous version was that all audio was being set to streamAudio mode for the import, which seemed to make them a lot faster, but unfortunately this also means Unity doesn't allow the AudioClip to play more than once simultaneously. So it's not ideal for instances such as swing sounds during dive attacks. Now HB will check if the size of the file is larger than ``12000 bytes`` x ``CustomSFXResetPoint`` (Representing constant 96Kbps). If that config setting is left at its default of 4 (seconds) then this cut-off point is a file size of larger than 48 kilobytes.
- Not sure if it could actually be considered a fix but textures on neckCollar and shoulderpad meshes were looking off-colour because the game does not set the colorAdjustParams into the Shader's properties. So I have made HB actually apply them for custom chestpieces. Additionally I have made it possible to have the dye colour apply to them, but this functionality requires custom boolean properties to be set to true: ``_dyeAffectsCollar`` and ``_dyeAffectsShoulderpads``.
- Continuing that previous note, I have made it possible for custom capes use the dye colour if they are set to be dyable (The vanilla property ``_canDyeArmor``).
- An item context menu option has been added, "Show Off", this reimplements something Kiseff showed off and for whatever reason unimplemented afterwards, the option for the display an item emote. HB already has a chat command for it which allows you to show things you don't even have, but being able to use this functionality on items in your inventory is a lot more convenient.
- The LimitsCfg system now covers the colour sliders, for both slider range overriding and value clamping to work as expected. Oh and the Depth setting is now in there too, since it might as well be.
- You can now use the scrollwheel while the cursor is hovering over a slider control to alter the value of the slider. Holding either shift speeds it up, or either ctrl slows it down.
- You can now use Dyes to colour specific gear slots, and even our eyes and mouths!
- HB now has it's own per character savefile system for the part markers, depth scale value, and the armor slot dyes and eye and mouth colours.
- Options on Dye items for un-dying gear (resets it to the vanilla dye you have on), and un-dying eyes and mouth (Sets them to the default ColorAdjust properties).
- Custom dyes are now infinite use.
- Added a config bind "NoShop", which can be used to make the Shop component never be instantiated, meaning no shop dialogue option on Skrit.

### Fixed
- The mod item shop UI might work a bit better, maybe.
- An AtylssGLTF related optimisation had escaped the confines of the soft-dependency area, causing a plugin load failure. Removed it for the time being.
- Vanity Mirror forgot who you were and then inexplicably made you 2D in the inventory UI.
- Vanilla or undyable capes were not resetting to default colorAdjust settings properly.
- Not sure if it's really a fix but I've tried making the "_cutoff" property in the shader for Imp body, arms and boobs be 0 and that seems to enable translucency to work in custom textures on them.

### Changed
- EasterEggChance config setting description has been improved, and the places this value was being used in have been made consistent with one another. Anything 0 or below will automatically fail the rolls, 1 will always succeed the rolls, 2 would be a 50:50 etc (Unless there's a multiplier on the value for the particular roll).
- Improved logging outputs for ``_enchantCostItem`` not getting any matches, or getting multiple matches.
- The effects for combined scaling of height, width and depth have been organised better into HeightWidth, WidthDepth (alias "Girth"), HeightDepth and HeightWidthDepth. The Size alias now refers to HeightWidthDepth rather than HeightWidth, which no longer affects your depth.
- AtlyssGLTF dependency updated to 0.2.0 - It can now handle GLB files that have compression applied to them I believe.

# 4.5.13 - 2025-09-29 - The Jiggle

### Added
- Homebrewery now optionally overrides the DynamicBone settings for Player Boobs, Butt, Ears, Tails and Belly, using the JiggleSettings.cfg file, which has an on/off setting at the top. It's off by default because the preset values I put in there are very Dead or Alive: Xtreme Beach Volleyball, better that you know for certain it's doing something than have no idea.
- A little text element has been added under the game version on the main menu to let you know you're playing modded with Homebrewery, since it can be hard to tell that right away if you have the console turned off.
- ``_hideHair`` has been reimplemented now as a custom property for helms, it just turns off the Hair Mesh renderer. It was concerningly easy to do.

### Fixed
- For manual install users, a rogue "ContentPacks" folder was being generated inside the plugins override folder which obviously isn't the right place for it. The code responsible for this has been reprimanded, it's work in progress and isn't supposed to be running yet anyway.
- The "blitbleed" shader has been tuned up and now should not cause textures with very low alpha to partly/totally disappear.

### Changed
- Freecam now makes gravity apply to you again while your movement is locked.
- Freecam now automatically switches off if you die, the camera also resets with a bit of distance rather than snapping back inside the head.
- Consumable effects should now only use YourLimits for yourself and ignore OthersLimits, while OthersLimits are used for everyone else.
- It's not possible to tell whether a ScriptablePlayerRace instance is yours or anybody else's when it tries to clamp proportions, so now HB will just use whatever value ends up furthest from zero after checking what the clamped proportion value is with both the YourLimits and OthersLimits settings.
- The ``#local#`` string replacement into Mod Name in model part and SFX strings now also works for ``_enchantCostItem``, in those cases the string would look like this: ``Item Name-#local#``.
- ``_enchantCostItem`` can now be left blank to make the weapon not require any item as material cost. The default value is still "Rock" if the property is absent from the file, and the Rock is used if an assignment is attempted but fails.
- Failed assignment log outputs for ChestRenderDisplay, ArmCuffRender, and LegPieceRender_01-04 have been supressed to one below the Warning level for the time being since I don't know when we'll be able to figure out working customs of those ArmorRender fields in order to reimport the stuff that Kiseff inexplicably removed after game version 1.6.2b.

## 4.5.9 - 2025-09-21 - Follow-up

### Added
- New config settings for custom swing sfx volume and for custom draw sfx volume have been added.
- Proportions clamping has been moved to a new config setting "ClampPlayerProportions", it is off by default.
- A setting for the maximum sound effect length before an AudioSource is stopped to prevent overlapping long length sounds, it is 4s by default, up a bit from the 2 seconds the code was using in the previous version.

### Fixed
- One of the config files being renamed and moved simultaneously had accidentally been made to check for its old file name where it was moving to, enabling a file collision to happen.

### Changed
- SliderRangeOverride is still off by default, but if it's on HB will use the OthersLimits values for X, Y, Z scale clamping, otherwise it will use the vanilla values. This is skipped entirely if ClampPlayerProportions is off.
- The LimitCFGs code has been refactored through a huge headache and the setting descriptions have been altered to better explain what they are used for.

# 4.5.7 - 2025-09-20 - The Sound

### Added
- You can now override the character creation slider ranges using the values in the YourLimits config files, and override the proportions clamps using the values in the OthersLimits config files. A config setting "SliderRangeOverride" has been added so you can turn this feature on and off.
- The part name labels for the character creation UI have finally been ported to the Vanity Mirror UI, coinciding with the slider range overriding.
- Custom SFX can now be Ogg format, and I've changed HB's MP3s to Ogg where they ended up smaller in filesize.
- A new chat command has been added "Time", which lets you stop and resume time, and set the time to a specific point. Examples: ``/hb time stop`` ``/hb time go`` ``/hb time day`` ``/hb time 9pm`` ``/hb time 18`` These are to replace a secret undocumented hotkey on F6 that would set the time to specific points and pause it there, not sure though if I will remove the hotkey before I implement a good way to allow altering the various hotkeys.
- Custom weapons can now set the bonus power and duration properties for their ScriptableCondition with the **integer** properties ``_bonusPower`` and ``_bonusDuration``, default is 0, minimum for both is -3, and the maximums vary based on the condition.
- Custom weapons can now use the ``_vendorCost`` property, specifically because the game multiplies it by three to determine the money cost for enchanting, this does not affect the item's actual price in the shop mind you. Maximum is 2300, which is the value the Valdur Blade has.
- Patches have been added in an attempt to force stop the AudioSources to prevent really long custom sound effects from overlapping. Now swinging, drawing, swapping (to a different type), sheathing (but not the automatic sheath from the sit emote), and consumable onuses should stop sounds that are playing before a new sound effect is started. This applies to sound effects that are over two seconds long.
- Adding on to that previous note, HB now has its own AudioSource components for the custom draw, swing and onuse sounds to play from, so that it can be more responsible for how they are being played. Consumable onuse sound effects will now work again, as much as they did in the past, the host needs to have HB and the item or else clients will not know the item was used. I'm reluctant to use CodeTalker for this since it would be delayed by the latency and kinda feel janky.

### Fixed
- HB will now clamp height and width to either vanilla limits or OthersLimits values as part of how the Width, Height and Depth effects work. If SliderRangeOverride is on, it will use the OthersLimits values.
- Custom weapons that are expected to use the vanilla sound effects for the weapon type should now actually use the sound effects they are supposed to be using.

### Changed
- HB will now automatically turn param txt files into "txthb" so that r2Modman's Config Editor won't grab ten morbillion item files as if they were config files. I've changed all of HB's own items and the template ones to be "txthb" manually. If you do this for your own mods, it will save HB from having to do it for users at runtime.
- The Bilinear texture filtering quirk (thin white/black lines around the edges of opaque pixels adjacent to transparent ones) fix by Nessie from 3.13.80 has been replaced by a proper BlitBleed shader, also by Nessie, and it is a hell of a lot faster. Shouldn't notice the processing time difference between Point and Bilinear anymore.
- All config files will automatically be relocated into a containment folder named "Homebrewery" inside ``BepInEx > config``

### Removed
- Many lines of code from the main file that have either been made redundant or ported to other files and improved upon there. It's now under 2000 lines! There's definitely still a lot more to move out to dedicated files and component scripts.

# 4.3.2 - 2025-09-04 - The Spectator

### Added
- CameraDX zoom step and speed can be customised now, step is for per scrollwheel input, speed is for the constant zoom rate from controller Dpad inputs. Chat commands for them work just like "min" and max", just with the aliases "step" and "speed", e.g. ``/hb cam step 35.5`` ``/hb cam speed 65.5``
- CameraDX now has optional extra keys for zooming, they are off by default just in-case of any conflicts, but you can rebind them, the KeyCode names are case sensitive though. By default they are PageUp for zooming out and PageDown for zooming in. Examples: ``/hb cam key``, ``/hb cam key in PageDown``, ``/hb cam key reset``
- Additionally, you can toggle whether those extra keys work like the scrollwheel or the controller Dpad, by default it's the controller Dpad style using speed. Command: ``/hb cam key step``
- Added contact name aliases for "Sally Workers" Craig and Frankie, and Trip the Dye Merchant; so you can call them with the command now.
- Consumables can now use the boolean (true/false) property "_infiniteUses" to make the item not be consumed when using it. This uses a new ScriptableConsumable property Kiseff added last month I think, that is called unperishable. I decided to use a different name for clarity.
- Model and sound name strings in param files can now use ``#local#`` instead of their own mod name to have it automatically replaced in for you.
- You can now override the horizontal and vertical camera offset slider ranges with four config settings, it is also possible to configure these using the "cam offset" command, e.g. ``/hb cam offset up 0.5``
- FREECAM! Yeah I implemented an entire freecam, it works only when CameraDX is on. It has its own commands under ``/hb fc``, you can lock the camera in-place to allow your character to move around ``/hb fc lock``, you can also toggle the movement of the camera to be world oriented (always stays level) rather than camera direction oriented (forward goes directly in the camera direction) ``/hb fc axis``. You can change the movement speed too ``/hb fc speed 40``, default is 40 like the character movement speed, minimum is 1. The zoom inputs (even the extra ones mentioned above) will move the camera forward and backwards at a quarter of the movement speed.
- Invisible effect now has the extra aliases "invis" and "hide".
- A chat command has been added "show" which uses the emote to show off an item, that Kiseff implemented, showed us working via an inventory item option, and then somehow never released? It uses the partial name matcher that I wrote for enchant cost items, and it will store the last item you displayed so you can reshow it without needing to type the name again. Examples: ``/hb show five hund`` (Should probably partial match Five Hundred Cigarettes) ``/hb show`` (If you have shown something previously, this will reuse the same item). Note that vanilla item names are Case Sensitive!
- You can now force off the fog effect, togglable with the command ``/hb fog``.
- CameraDX can now override the FarClipPlane, it is off by default, toggleable with ``/hb cam far`` (or farclip/farclipplane). Default value for it is 10000, configurable by using a number after the command like ``/hb cam far 2500``.
- A custom property for helms has been added: ``_noFlip``, if true, Homebrewery will check if the character wearing the helm has the lefthanded toggle on, and will unmirror the helm.
- Homebrewery will now look in HomebreweryFiles for a file called "HB_Info.json" which contains a single property that is "Name". If this file is present, and this property has something in it, it will be used instead of the folder name as the ModName part of ID strings. So you can use this to override whatever Thunderstore is calling your mod, and to also not have to care about the version number in the folder name either if you manually install mods.

### Fixed
- An oversight with the model name aliases for reimported removed vanilla model parts, it was trying to make the aliases before the imports were finished since I forgot to move the method call down.
- A naming conflict was causing no hipMeshTextures to be imported, which then also caused all hipMeshDisplays to silently be left unassigned too.

### Changed
- Improved how CameraDX detects height changes for aligning the head height follow object. Should now set itself in the right place when height is changed by the mirror or via other mods directly.
- Condition effects on weapons now have individual maximum chance clamp values, which only really means that Burn and Cold can be 0.2 now, the others are all still maxed out at 0.12 anyway.
- Rewrote the entire CodeTalker implementation to condense it a great deal, this means it's going to not work between people who haven't updated and those who have, sorry. But at least I won't have to break it again in the future since it's now passing values with a float or string array rather than the object structure. The packets should be smaller overall. Additionally, it now uses the Mirror NetworkIdentity netId property as a sender ID instead of using the SteamID, meaning you should be able to multibox and the packets will know what client they came from! Just bear in mind that right now if you leave as one of them CodeTalker will think all of them left, I can't fix that myself.

# 4.1.0 - 2025-08-14 - The Commands

### Added
- CHAT COMMANDS!!! Some better than others, one of them was giving me severe brainrot, but it works at least. Check out the documentation here: https://github.com/Catman-232/Homebrewery/wiki/Chat-Commands
- Custom weapons can now set their level requirement with "_level". Default and minimum is 1, maximum is 24 (Matching the Valdur Blade).
- Consumables have a new boolean (true/false) property you can use called "cond*X*delayed" which will follow the previous behaviour of counting to the first interval before applying an effect. (See the Fixed section)
- A new effect has been added, "depth", it's like Width but only does the Depth scale. I had to add a field to the CondEffectManager to keep track of it separately, since the game normally uses widthWeight for both X and Z localScale values.
- A new consumable effect called "sober" which finds and removes all Drunk effects.
- A new consumable effect called "removeall" / "clearall" / "clear" which finds and removes all effects, the method behind this already existed and was being operated on death.
- CAMERADX!!! A few config settings have been added related to camera properties: CameraDX On/Off, ~ Always Show Player, ~ Min Zoom and ~ Max Zoom. Should feel familiar to A+'s camera overrides. The default is off, and the default settings are the vanilla numbers.
- A config setting called CameraDX - Camera Orbits Head, which will move the camera orbit location to vaguely be the middle of the head, accounting for player scale changes as best as I could manage.
- A config setting called CameraDX - Ignore Collision, which will make the camera not be pushed towards you when it would normally collide with something.
- A set of eyes and a mouth for Poon, I made them myself, based upon... well, you'll know full well what they are!
- A config setting for a way to press forward slash and have the chat open with a slash already added to the input, "SlashShortcut" default true.
- A config setting "LogCommands" which will make HB also log the command usage output in the BepInEx console, default is false.
- maidArmor_collar has been reimported thanks to Stoingularity sending it to me, it got forgotten about.
- A prestigious weapon contributed by WilsonHough, thanks duder.

### Fixed
- More places to clean up the item display names.
- Consumable effects with an interval were being delayed since they would have to wait for the first interval in order to apply the first tick. They should now apply instantly, and then count to the interval for subsequent ticks.
- I forgot to actually add cond4 and cond5 to the handler patch, so they would never have been applying if your item even was using them.

### Changed
- IMPORTANT!!! Homebrewery will no longer be looking in config for HomebreweryFiles! I have moved it to be inside plugins. This is because it's become a pain to change stuff that HB comes with, without causing duplicate files to emerge, mod manager behaviour will wipe and reinstall this folder on updates so do not put your own custom stuff inside it! Please move it into "User-CustomStuff" inside config > ContentPacks, where it will be safe and not mixed up with any of HB's own items!
- The Emperor's New Fists are now level 20, matching the Golemfist Katars.
- Imported MP3 files no longer need to adhere to strict naming aliases, since that's kind of arbitrary and unintuitive here. It was carried across from how PNG naming is used for context, but it doesn't really need it for the way you assign sounds.
- The maximum strength of the Knockback effect has been doubled.
- BlinkPercent, the value that is used for the BlinkPatch's forcing the eyes to actually use the closed state, has had it's default value lowered from 87 to 30. Because I was looking at it and I honestly can't tell the difference. It's only at 20 and below that the eyes stay closed for longer than looks natural. Oh and the value type is now float (allows decimal places), if that matters.

### Removed
- Maybe I should remove this section if I have no idea what to actually put in here.

# 4.0.0 - 2025-08-05 - The Armory

### Added
- Custom capes can now have a custom boolean (true/false) property "_keepTailUp" to make it so the tail doesn't get squashed down when wearing the "cape".
- Custom weapons can now have the properties "_weaponDamage" (Min 1, Max 42) and "_damageBonus" (Min 0, Max 15). Both default to the minimums, matching the previous behaviour. The maximums are that of the current highest values among vanilla weapons, both are actually from the Quake Pummeler. If the host does not have the custom weapon, or their instance of it doesn't have the stats, then it will not apply the stats you see on the tooltip when you deal damage. I think this is fine since it prevents blatant cheating by making a maxed out stats weapon for free.
- Custom weapons can now have the bonus stat properties that vanilla gear can have, via the nested struct property "_statStruct". It is set out like "_textureColorAdjust" is, and you don't need to put every single property in even if you're not using it. Half of them are not used by any vanilla weapon, so those bonus stats will be supressed into 0s. The other bonus stats have clamps based on the maximum bonuses from any vanilla weapons.
- A new config setting called "NoWeaponStats" which you can set to true if you want HB to make all weapons have the base stats instead of custom stats. This will effectively make all clients in a server you are hosting not have custom stats apply to their damage output.
- Custom weapons can now be enchanted, the default cost is 5 Rocks like the Iron Weapons have. You can use the string property "_enchantCostItem" to specify an item by display name, if the string doesn't immediately match an item's display name, it will instead try to partial match an item by its object name, it will return the first match if there are multiple. You can use the integer property "_enchantCostAmount" for the amount of the item required, minimum is 1. They can't be enchanted if they are Cosmetic rarity, much like how vanilla items that have a rarity above Rare can't, since Cosmetic is actually higher than Exotic and Legendary, so for that reason you won't be able to make them be Cosmetic.
- Speaking of which, Custom weapons can have a rarity assigned using the string property "_itemRarity" which accepts only these properties: "0", "Common", "1", "Rare", "2", "Exotic". 0, 1 and 2 are shorthand for Common, Rare and Exotic. Bare in mind that Exotic items cannot be enchanted, I'm allowing custom weapons to be Exotic purely because there are already vanilla Exotic items, I am not going to fuck with all the relevant vanilla methods in order to make them be possible to enchant.
- Custom weapons can now have a ScriptableCombatElement assigned by integer ID with the property "_combatElement", the default is none unless the weapon has had its "_weaponProjectileSet" assigned to, in which case the default becomes 0 : Normal. The other elements are 1 : Fire, 2 : Water, 3 : Nature, 4 : Earth, 5: Holy, 6 : Shadow.
- Custom weapons can now have a chance based condition effect assigned by name with the property "_scriptableCondition". Valid values are the ones being used by vanilla items so far: "Gash", "Hex", "Brittle", "Burn", "Cold" and "Poison". The chance to apply on hit is set with the float property "_chance" (Min 0.001, Max 0.12, which represents 0.1% to 12%).
- Custom weapons can now customise the colours of the little particle effect created when swinging the weapon, using the properties "_trailColor1" and "_trailColor2". These are string properties but special, because they take a hexadecimal HTML colour code (e.g. RRGGBB: "#FFFF00", RGB: "#FF0"), or one of the dozens of HTML colour names (e.g "red", "cyan", "white"). This can also optionally include the alpha part (e.g. RRGGBBAA: "#888800FF", RGBA: "#880F"), but I don't know if that will actually do anything. The defaults are White & Red.
- Scrap Metal from TF2 as a trade item.
- HB's first log message now includes a formatted UTC timestamp so I can know when a log file was generated, and a warning log message will be posted if the current game version does not match the version the game was when I last compiled the mod.

### Fixed
- Like a billion coding conventions improvements.
- I had pretty much all this other stuff done before 82025.a1 came out. Kiseff merged the dye index fields into a single one, but *still* did not apply any clamping to the value to ensure it's inside the array. So my patch for that remains valid, but is now three lines shorter.

### Changed
- drawsound, swingsound and hitsound have been renamed to a more proper: "_drawSound", "_swingSound" and "_hitSound".
- The Emperor's New Fists is now effectively a weapon skin for the Golemfist katars.
- Frying Pan now has the damage and bonus damage of the Dawn Mace. It requires 1 Scrap Metal to enchant.
- Thanks to Remedy for going hard and making a new artwork for the Github repository/wiki embed image. It's perfect.
- I have altered some of HB's parts folder names to shorten them by removing redundant bits. Sorry if this causes any momentary part sync jank, the system works via these names, so keeping them shorter is optimal. - Kiseff just added a bunch of eyes anyway, so that might cause desyncs too.

### Removed
- Like a billion instances of outdated coding conventions.

## 3.13.86 - 2025-07-29 - Fix

### Fixed
- Hopefully this time the Nudge won't be triggered by anything but jumping while grounded three times in a row, you'd really think it would be easier to tell when that happens but evidently not. I need a holiday at this point.

## 3.13.85 - 2025-07-29 - Fix

### Fixed
- The Nudge was being triggered from knockbacks in combat because the method I was using like an event called "JumpPress" seems to just be invoked all the time regardless of whether you actually pressed to jump or not.
- The "fix" for the ContentPacks folder for mod manager users importing ironically broke the whole thing, it's getting difficult to cope with supporting both of these install methods at the same time.

## 3.13.84 - 2025-07-29 - Fix

### Added
- Consumables params have been expanded to handle a cond4 and cond5 group of properties.

### Fixed
- The ContentPacks folder was mistakenly using the same path check as the main folders grabbing was, rather than assuming the plugins override folder is present, which was the intended use case.
- Merchants would get confused when Skrit had just been distributing custom items, and they would somehow show the gambling items restock button. Should be back to normal now.

### Changed
- The list size for the active Consumable effects on the CondEffectManager component has been doubled from 10 to 20.

### Removed
- Altered the usage of UploadMeshData so that it shouldn't be doing it for custom cape meshes, since for some reason that causes them to cease to exist.

# 3.13.80 - 2025-07-25 - The Conditions

### Added
- I have written my own condition effects handler component that is now responsible for Homebrewery's custom effects. This enables me to offer duration and potency for most effects, which are set in the consumable item's params file. The aliases include many of the extras keywords from before, but most are named simply. You can have three different effects on a single item, they each can optionally have a duration (default & min 0 - they still apply once without a duration. A negative duration becomes a random value between 2 and 3.5 when the effect is applied), a power (default 1) and an interval property (default & min is vaguely 1/60). Each player has their own effect handler with a list that it will refuse to add more than ten individual effects to (as in anything that would last more than the initial OnUse effect). CodeTalker is used to sync these effects between Homebrewery users, so the host does not require the mod for this to work anymore.
- There are dedicated config files for each race, one personal, and another for other players, so you can limit the proportions that consumable effects can alter you and others. The set of values for the race you are playing as will be sent to others in order to establish your preferred scale limits in other player's game to prevent desyncs from how the effects actually work.
- HB is now loading all Meshes out of the helms container rather than specifically the halo container that's inside it, since Kiseff has been making Helm items using single meshes that are in there rather than ArmorRenders, like the new full helms.
- Kiseff added a field on Chestpieces called "hipMesh", it's an ArmorRender type model part field. So I've added handling the params property and textures accordingly named "hipMesh.png".
- The wiki now has a simple dump of the important scriptable items as text files, containing all the conveniently grabbable properties, so we have our own more up to date list for the texture and model parts names.
- Weapon animation sets are now determined by a property on the weapon itself, which is used for an array inside the WeaponType object. This property is called "_weaponHoldClipIndex" and takes an Integer. You still need to have the "weaponType" property!
- Consumables can now assign a sound effect that plays when they are consumed, works the same way as it does for weapons, not that anyone has probably realised that's possible. Imagine documenting things.
- Added a config setting for the volume of custom Consumable OnUse sound effects.
- ConditionEffects that are macros for all the emotes have been added, this is different to how the sit and dance extra effects worked before, but it's not a big deal.
- A dedicated effect for taking damage with a floating number has been added, "damage". The Gash status condition is now standalone, "gash". This does not conflict with or replace the actual property on consumables that is called "damage" that just drains health directly.
- An effect that does the flattened state has been added, "flattened".
- An effect that combines the height and width scaling has been added, "heightwidth".
- Consumables and Dyes can now assign a "_consumableObject" which is the GameObject that defines the particles and sound effect that happen when you consume the item. HB is loading a fair few of them from the Resources, including status effect ones and the new Dye ones. Likely to add more of them to the list eventually. The default for Consumables is the green effect from Bunbags like it was previously. The default for Dyes is the Black Dye's variant of the new effect they have. If you want the item to have no effect at all, use "Nothing" as the value.
- HB Phone now includes the new dialogue UI for the World Portal, the Dungeon Entrance option has been kept but is moved to the end of the list and has a new icon. I have taken a measure to prevent you from being able to warp to the same map you are currently on (vanilla behaviour), because otherwise you would unload the map and enter the void, falling forever. If that does happen to you, the HB Phone dialogue can still be opened and you'd be able to try warping somewhere else to save yourself.
- I have made aliases in the Dictionaries for model parts so that the ones that were renamed can still be used by their old names, this comprises name changes such as "hood01" becoming "hood", or "armCuff03" becoming "armCuffs_02". The wiki page for the model parts has been updated accordingly. Speaking of armcuffs, the area on the armor texture they use seems to have been expanded, so you may need to edit your chestpiece armor textures if they had armcuffs in use.
- Thanks to Stoingularity yet again for contributing vanilla meshes set up for being imported back into the game after they were removed in the game update, this means "maidArmor_collar", "thiefArmor_collar" and "warlockArmor_collar" are back, the UV layout on them has been improved too, so you may want to edit your neckCollar textures. As an added bonus, he's given us one of Vivian's collar too!
- These patch notes just get more and more ridiculous every month it feels like. So I have implemented a simple Texture Replacer functionality into Homebrewery. I don't really know what else to say.
- Added mitigation for the player falling into the void due to not loading into a map instance, it will try to portal you back to Sanctum, but if that fails it will try Sanctum Courtyard as a backup.
- SkinTextureGroups can now be given even more textures to apply to specific areas of the body - This enables textures for arms, boobs, upperleg, lowerleg and feet.
- Items can now have a boolean (true/false) property "secretitem" which prevents them from being listed in a Mod Shop.

### Fixed
- In the game update, Kiseff had refactored the tooltip code meaning I had to add extra patches for cleaning item name junk out of the tooltips. I missed the generic tooltips the game displays when looking at the vanity slots in the inventory UI.
- There's another Byrdle beak texture without nostrils, and it seems like it replaced the original one that did have nostrils. Now we have both of them as "Mouth" options anyway.
- Effects that rotate your character, like the knockback from Can of Whoopass have somehow gained the evil tendency to get you stuck in the floor. I have managed to work in a mitigation where if you jump three times consecutively and your CharacterController registers you as ground each time, then HB will snap toggle your collider off and on, and nudge you upwards the tiniest bit in between. This seems to enable you to move again.
- Thanks to Nessie for contributing a solution to mitigating how Unity's Bilinear filtering can cause thin white/black lines around the edges of opaque pixels adjacent to transparent ones. You might have noticed it on eyes and mouths a lot, shouldn't happen anymore.
- Since Kiseff removed hideHair, the "Do your plumes" Hairpin is now set to have 100 for blendHelmValue, so that it does something for Byrdle at least, even if helmHairDisplay doesn't. It's hard to tell what's going on with these helm properties, they've never been very consistent across the races.

### Changed
- Optimised how the CodeTalker functionality is used to ensure no packets are sent until a Player that has fully joined sends their own packet to ask for them.
- "_hideHair" was removed from Helms, and "_isFullHelm" was added. "_isFullHelm" will hide the entire head.
- The patch that allows taking damage in safe zones has been re-enabled.
- The "fragile" extra effect is now applied using the true/false "_destroyOnDrop" property that any item type can have.
- The "hourlong" extra effect is now applied using the float "dropitemtime" property that any item type can have, the default setting is -1, which will retain the default drop item timer. This value is a strange one and seems to count at an odd rate, I had used 11700 to represent one hour previously, which is a rate of ~195 per 60 seconds.
- Items now have 1 as their value, this is because Kiseff added a price override property to ShopItem which means items can be free in the shop. I have suffered tremendously to get that shop UI to behave.
- Logged Errors (Red) about failed assignments for model parts and custom sounds are now Warnings (Yellow) instead, so it doesn't spook players so much.
- Custom Shields are now Cosmetic rarity again since the refactored Enchantment system won't cause the display problem anymore.
- EasterEggChance can now be set to -1 to bypass the random roll and never get easter eggs. 0 and 1 will still be 100% chance.
- HB will now also load content packs from a "ContentPacks" in the mod manager's BepInEx Config directory, this should help for content pack creation since it will load from here expecting the plugins override folder that is commonly missed out on by mistake. I have even slapped together an example mod package in there!

# 3.11.70 - 2025-07-10 - The Game Update

### Added
- HARD DEPENDENCY ADDED: [Robyn's CodeTalker plugin!](https://thunderstore.io/c/atlyss/p/Robyn/CodeTalker/) This allows me to do part sync much more efficiently and without leaning on custom status conditions which only work when the host has them! This plugin uses the Steam lobby communication to pass messages behind the scenes, so no clogging up the chat either.
- Custom weapons can now have custom sound effects for drawing, swinging but not hitting. I had to make a patch in order to enable the hitsounds to be used since only draw and swing are actually part of the weapons scriptable, but that patch was acting up so I'm leaving it for later.
- Config options for FoodCapacity (Default value: 5) and FoodDigestRate (Default value: 40) in case you want to edit those values rather than use the other setting to bypass the capacity entirely.
- The Left 4 Dead Frying pan as a Melee type weapon, thanks to WolfKann for porting the assets for me. You just know this was the perfect example for weapon sound effects.
- Consumable extra keyword effects for specifically turning the boobs on or off rather than toggling them, "boobson" and "boobsoff".
- The slider effects now also have Slow variants, they take 100s to finish.
- All slider effects are now stackable condition effects, meaning you can add the keyword to an item multiple times to increase the potency of the effect, since it'll apply the condition that many times.
- Consumable extra keyword effects for the sliders for arms ("bigarms" & "weedyarms"), muzzle ("longface" & "stubnose"), head width ("widehead" & "pinhead"), torso ("steroids" & "lithe"), and voice pitch ("helium" & "deepspeak").
- Added a way for repackers to store full mods within Homebrewery's folder so that the part/item sources and the shop pages can work. This is for manual installations only! Inside of the config folder, you can add a folder called "ContentPacks" next to the existing "HomebreweryFiles". Inside "ContentPacks" you can place entire mod folders like you would have placed Homebrewery and other stuff into BepInEx > plugins. HB will load these mods after the ones out in the plugins folder. (You will still need to remove the version numbers from their folder names mind you!)
- Files that have changed should now automatically reload their associated thing when the change is detected!

### Fixed
- Added a measure to stop the phone from being possible to open during movement techniques like dive attacks.
- Set the tiny knockback on the "float" consumable effect to actually use the player's facing direction like the Wet Floor Signs' effect does.
- Prevented a NullRefException flood on the char select screen when there are no save slots filled. I am sure I fixed this months ago but I guess it came back again.
- Changed the way that HB gets the currently active character model in the character creation UI to prevent exception spam that would occur when skipping past the method I had patched to get it. This means the character edit mod won't cause trouble when going to edit a character.
- Half addition/half fix: I have added a patch to the equip display method that will check your vanity slots for invalid items and clear them out before the game tries to use them without null checking them first. This means that when mod items are not loaded, uninstalled, renamed etc; the game isn't going to throw a fit about it and break the rest of the equip display code too. This does not alter the save file itself, it just edits the game's loaded form of it, much like how the part restoring operates.
- I've added a patch to force the nametag distance check method to exit early when the DialogTrigger does not even have name tag renderers assigned, to prevent needless every frame raycasts. It was causing an error log spam linked to HB Phone since the phone's GameObject is not in a scene that can return a PhysicsScene, meaning it cannot do a raycast to check the distance from it to the Player in order to determine whether or not to show a nametag which it does not even have. The method only checks if the renderer component is there AFTER doing the raycast distance check.

### Changed
- Massive refactoring to split up the largest files into many concise and organised files.
- The FoodLevel stat now reduces at a default rate of 1 per 40 seconds, which matches the length of an in-game hour.
- Lowered the volume of the Can of Whoop Ass sound effect just a tad.
- Refactored the code for custom condition effects because it was getting extremely tall in the multiple places it needs to be in and I don't think switch case blocks are really supposed to have quite so many cases in them. Now using a Dictionary lookup to return a little class that holds the changes it needs to apply to a consumable, and Action objects with scripted effects the condition has to apply to a StatusEntity, unionising as much of the code as possible into its own CS file.
- Shortened and simplified the internal names of the custom condition effects as well, so you'll likely experience them not working until server hosts and most players have updated.

### Removed
- All those notes above were written before the game updated, so now here's the casualties:
- Custom condition effects, which means consumable effects and part markers are both dead. Part markers have been replaced by the CodeTalker implementation though at least, which was going to happen anyway. The work I did to refactor the consumables code isn't going to go to waste. Coming soon: syncing consumable condition effects using CodeTalker!
- The mod item shop doesn't seem to want to display the consumable and trade item tabs, which isn't so big a deal when the consumables aren't going to work until I sort them out again.
- Commented out the patch that made vanilla condition effect stacks tick properly, since there would be no use for it right now.
- Commented out the code that made it possible to take damage in safe zones.
- The patch I added to prevent the resolution setting from ever being out of range might be deprecated since I think Kiseff is handling that in a way that prevents it now.

# 3.8.62 - 2025-06-02 - The Shrink

### Added
- Thanks to Stoingularity for contributing fixed up vanilla Hair meshes, set up for being used by each race, replacing the former implementation that was just copying them from each race to spread them around.
- New config setting "UseDXT5Compression" which when set to True will have HB create its textures using DXT5 format rather than uncompressed ARGB32, whether or not this has any performance benefit I don't know, but I do know from before version 2.1.11 that the textures can get a bit ugly when compressed. But it's an option now if you think you might need it.
- Icons for Smoke Bomb, Immovable Object Gummy, Spring Latte, the Bunhost Ears, the example dog collar and the silly wings cape.
- A new trade item just for fun.
- A few very 'useful' consumables.
- Items can now have a "sortindex" property, which is an integer that will be used to sort the items in your content pack among themselves. If the item doesn't have it then it will fall back to the item name as before.
- Consumable items now have two more properties: "damage" which allows you to specify the amount of damage the item does when consumed (Positive numbers will be made negative automatically), and "cooldown" to set a custom usage cooldown (minimum of 1 second, default is 8).
- "chestRenderBoob" PNGs are now imported again since it was discovered that the game does use that texture field, even though it's only on two vanilla tabard chestpieces right now.
- A patch for Apply_CharacterDisplay to make Chang hands use the current body texture like their arms do, and make their nose use the current head texture, to enable custom skins to change the pink there like they already can for the feet. This actually affects Remedy's Chang skin, which has black hands in the body texture that were never being shown until now.
- New config setting "CondenseSmallMods" which takes an integer. Any content pack with an item count less than or equal to this integer will have its ModShop removed and the items will be sent to a combination shop called "Small Mods" instead. The default value is 0, so it will not do anything.
- New config setting "ShopInputDelay", allows you to customise the interval between the WASD inputs changing the page or mod in the shop, so that they can be made holdable but not at framerate overdrive.
- Unarmed weapon type can now do a dive jump, it has the same movement forces as the basic Katars dive attack, but without an animation set, so it just does the double jump front roll. Looks pretty good imo.
- You can make custom weapons now, it's a little rudimentary and I might struggle to document it properly for a little while (What's new?). I will expand upon this when I am not busy and braindead. Custom draw and swing sound effects will likely be a feature coming next.

### Fixed
- A regression caused by the new shop code, which was trying to list rejected items before they had been rejected. Fixed by the first note in the Changed list. This inadvertently gave the impression that the glTF plugin was a hard dependency, but I assure you it is optional. Homebrewery obviously has a few items that showcase the custom mesh support, and they were being rejected if you didn't have the glTF plugin.
- Not sure if this is a fix exactly but HB Phone no longer has an intro branch, meaning the game won't bother to check if you've already seen the intro branch, which involves a list of NPC nametag strings near the bottom of the save file. You'll likely already have "Homebrewery Phone" and "The Based Department" in there, but this change means any different nametags won't also get added in there (e.g. custom ones).
- Due to the mod folder name mistakes previously, I had accidentally used "Catman232-Homebrewery" instead of "Catman-Homebrewery" in the custom mesh and armorrender fields of Homebrewery's own items that use them. I have fixed this now, and I hope it doesn't cause any trouble. I shouldn't have to change this ever again.

### Changed
- Items with invalid ArmorRender names in the param will no longer be rejected, the model part just won't be assigned, so it'll be easier to tell when an item isn't set up right.
- The FilterMode config option is now used to cache whatever the Texture Filtering setting currently is, this is so HB doesn't need to wait for the settings to load, or guess what it will be when loading. It will update if you change the Texture Filtering setting.
- Minimum PNG size is now 4x4px rather than an arbitrary feeling 9x9px. I don't remember exactly why that was the case but it was something to do with DXT format Texture2Ds needing a minimum size and the check for PNGs being empty tripping up. Not sure. Probably doesn't matter anymore.
- Custom dyes can now stack to 99 like other kinds of consumables can.
- zPart Markers, the set of config file binds for storing part names and automatically adjusting your parts when the character appearance loads, has been updated to not be hardcoded to 7 slots. It should now work no matter what index the save slot is in, enabling compatibility with Marioalexsan's ExtraSaveSlots mod.
- Speaking of part markers, HB_ALLRACES parts now use a marker named as such, rather than being split into five separate ones.
- Reloading parts/items should now only try to reload anything that has actually been accessed since the mod finished importing the first time. Oh and it'll work for GLB files too!
- Since the file access timestamp seems to be very fallible, I have added an override hotkey to make HB reload everything without checking. Ctrl + Shift + R.

### Removed
- About 37% of the lines of code count in the main CS file, which was previously over 9000 lines long.

## 2.6.47 - 2025-05-04 - Fix

### Fixed
- Being sleep deprived means not having enough braincells to account for both methods of installing the mod, I fixed one method and accidentally broke the other. Now hopefully Homebrewery's own HomebreweryFiles should work again for the r2Modman way.

## 2.6.46 - 2025-05-04 - Fix

### Fixed
- Previous version changed how I was getting the mod folder names, which accidentally meant the check for Homebrewery's own folder stopped working as intended.
- Update message was always being opened because the check on the number had lost its equals sign after the greater than.

# 2.6.45 - 2025-05-03 - The Meshes

### Added
- CUSTOM MESH SUPPORT IS REAL!!!
- Added a few items to show off custom Mesh/ArmorRender parts, such as the coveted Bunhost Ears! More will come in the future.
- Rainbow Fur dye equivalent to Rainbow Hair dye. It works on both the body and hair at the same time.
- Icons for Nitro Coffee, Dance fever and Moon Chews.
- Labels have been added to a couple places in the character creation UI to say what the name of the custom part you are seeing is. It's a bit janky right now, it'll be improved next update.
- The shop code now checks if the ScriptableDialogData name ends with "#Homebrewery" as well as checking if the name was "dialogData_Skrit" (Skrit's one). This should allow a custom NPC's ScriptableDialogData to be picked up on for the Homebrewery Shop option.

### Fixed
- Rainbow Hair dye effect was unintentionally setting brightness to the saturation value when it ended.
- The blink percent config setting from the file was being ignored, whoops.
- HB will now ignore content packs which have a "manifest.json.old" file in them, which corresponds with what r2Modman adds to every file when a mod is deactivated. Homebrewery would still look in the folders and then spam tonnes of errors since no files would be valid to import.
- Self-damage effects should work for all players rather than just the host now.

### Changed
- A lot of code has been altered to properly run asynchronously, which might be a performance boost, I don't really know. But I needed to sort that out in order to properly use the glTF methods. Hopefully the import order of parts hasn't changed.
- A lot of code has been better organised too.
- Jaytwoster's Discord server keeps losing its custom invite link so I've replaced the one in the page with a regular infinite duration invite link.
- HB item shop has been overhauled and improved dramatically. I can't even explain the full extent of the changes in a mere patch note! Polish will come in the next update.
- Custom part save/load restore now saves the character name into the config as well so that it can skip altering the character if the slot has a different character in it on loading. This is for people who like to hotswap their characters in and out of the limited 7 slots there are.
- Improved warning and error log messages to actually say where the part/item is from, and be less needlessly wordy about it.
- HB Phone code has been overhauled, and now you can also customise the texts for The Based Department easter egg in a file within Internal!
- The CanDieInSanctuary patch has been altered to force any zone that is Safe to Field, rather than just Sanctum.

# 2.4.33 - 2025-04-06 - The ZPartMarkers

### Added
- HB will now remember your custom parts in the config file so it can automatically restore them if the indexes have changed when you load the character! Note that this does not edit the save file, but does edit the active appearance so it will apply when you do save the character.
- More condition effects for consumables, particularly some secret ones.
- The low gravity and float upwards effects actually do something now since I implemented scripted effects for them.
- Some debatably out-of-scope patches so that stacked status conditions tick properly. I don't think any condition in vanilla even can stack anyway, but the code for it would tick only the first one but again for each stack, so a ten minute timer while you had ten stacks would run out in about a minute. 

### Fixed
- The item name secret string (the weird blank space in the nametags) has now been suppressed everywhere I could think of them being.
- The link to the older Discord thread was accidentally the same link as the other one.
- Source location log messages for the parts being imported were being posted in the console output regardless of the config setting because I forgot to label it with a 2, whoops.
- Using HB Phone no longer makes you look towards the origin of the map.
- Reloading item parameters might be a bit less prone to exploding now since I think I missed adding something after the json is read and overwritten onto the object. It might also be a little more stable if the name has changed too.

### Changed
- The gear tab in the shop is now sorted by item name, so sets should now appear together rather than spread out across several pages.
- Tweaked how the part syncing operates to hopefully make it quicker to happen and less server intense due to the self-refreshing part marker status conditions.

# 2.3.24 - 2025-03-26 - The Part Sync

### Added
- THE BIG ONE!!! Using custom status conditions as markers, your character now self-applies conditions that represent the custom parts you are using, so that other players' games will detect them and try to look up what index that part actually is in their game, to then alter your appearance to match up with what you see in your own game! Currently it is implemented for Eyes, Mouths and Skins.
- The Thunderstore page now includes collage images of the mod's contents that were already in the mod for a while. I hope content pack creators will follow suit and add images to their mod pages! (I've added a section about how to do it in the User Manual)
- Some more consumables, with new custom condition effects. I still need icons for them though.
- Two quality of life gear items - Invisible Weight (Cape), Hairpin (Helm); and four custom Beak textures contributed by Kiruex, and an all race Eyes and Mouth contributed by Descen. This stuff is being moved from Clandestine ContentPack for not suiting the theme of the pack and making more sense being just part of Homebrewery itself.

### Fixed
- Not exactly a fix, but I've patched Apply_ArmorDisplay() to cap the dye index variables to the count of the dyes array which should prevent you from having the console flooded with the IndexOutOfRangeException when someone you're playing with is using a custom dye at an index outside of your game's array. Doesn't help vanilla players at all though, Kiseff needs to make this code change themselves.
- The placeholder mouth that Byrdles have in vanilla was accidentally not being cleared when you have the SpreadVanillaParts setting as false, meaning the first beak texture would be invisible.

### Changed
- The Shop UI will now not reset the tab when you change pages in the HB Shop, but will retain the vanilla behaviour everywhere else. There aren't pages anywhere else anyway.
- Chestpieces and Leggings part fields will no longer be checked if the corresponding texture wasn't marked done, which is always the case if there isn't a texture anyway. This will reduce some useless/confusing logging, now messages will point out you've forgotten to set a part in the params while you have a texture for it.
- Likewise, it won't bother logging a texture it doesn't find if the corresponding mesh field is blank.

### Removed
- Two sound effects in Internal > SFX which were from ages ago when I had experimented with adding hitsounds and killsounds, the code has been commented out for months, I think you can do it with Marioalexsan's ModAudio 2.0.0 now.

## 2.2.19 - 2025-03-16 - Fix

### Fixed
- Fixed a bug introduced in the previous version, a nullref exception when trying to sell an item while playing as a client in a server that has Homebrewery. The exception was happening client-side, but the server was reacting to it by kicking clients stating "possible exploit prevention". Ironically the cause was in the tiny piece of code checking that the shopkeep was the mod shop and not any other shop, it affected all shops as a result. Not very easy to test client-side stuff like this.

# 2.2.18 - 2025-03-14 - The Sale

### Added
- More consumable items, because we can't have enough of them. Including one that effectively reduces your food capacity stat from cap to 0. A lot of these will be moved to their own pack eventually.
- A Beak texture option by Cowpy (Beak textures are in the Mouth setting for Byrdle, it's activated for this)
- Added a function that reprints all collected warning and error log messages. You can make it print the current list using F5, which will also clear the list afterwards.
- A whole bunch of dialogue option icons have been added, not least of all: next and previous icons for the mod item shop.

### Fixed
- You'd probably never have noticed but Beaks without an 'open.png' were actually copying 'beak.png' back into their own 'beak/closed' texture rather than the 'open' texture, so the open beak was invisible, woops.
- The order of items in the shop was pretty jank with the custom dyes showing up after the consumables, now the custom dyes will be at the front of the list with the vanilla dyes.

### Changed
- All LCtrl hotkeys can now also be input using RCtrl instead.
- Capes and Shields with invalid Mesh names will now default to using 'cape01' and '_shield_01' respectively, rather than setting the field blank.
- The vanilla dyes cost money again, so you can actually sell them.
- Thanks to Marioalexsan for writing me some complicated looking transpiler patches that make it so nothing costs money from the mod shop even if it says it does, AND allows you to sell items with 0 value, so now you can get rid of them without littering!
- You can now take damage and die in Sanctum, this is so some consumable item effects can actually do anything. There is a config setting you can use to revert this if you would prefer to be invincible in the Sanctum. I don't think it works in multiplayer yet.

# 2.1.11 - 2025-03-06 - The Pages

### Added
- Skrit's Homebrewery Shop now has pages, and was so brain-rotten after getting that working.
- Customs for Consumables and Trade Items, pretty frivolous but why not?
- Yes, we absolutely needed over an entire page of ice cream cones.
- A new "Cape" by SaucyGoblin, I think a fair few people will really like this one.

### Fixed
- Previous version's patch notes had the wrong date on it due to copypasta.
- A NullReferenceException error was occurring at the character select screen from the Update() method due to assuming there's a PlayerRaceModel to apply the eye state controls to, but when you don't have any characters, there actually is no such PlayerRaceModel there yet. Hopefully I fixed it properly, it's a bit hard to test.
- Thanks to stone_slab442 for pointing out to me a remaining oversight with reloading changes to armor.png not updating chestpieces properly due to jank with the boobOverride texture when you're not even using it.

### Changed
- Param reloading previously created hashes of the txt file to compare and determine if the file had changed, this probably wasn't any faster than just comparing the strings, and it was actually failing to notice changes that had been made anyway. So now params should reload as long as any text has changed in the txt file.
- Texture2D format is now ARGB32 rather than DXT5, the game natively uses DXT1 and 5 but I guess it does a lot better when textures are made in Unity Editor, using LoadImage must be double compressing or something. Appearance+ uses ARGB32 anyway, and that's also the default format for PNGs loaded in by LoadImage if the texture is anything other than a DXT format.

### Removed
- Item list scrolling in the shop is gone, the code still exists but I would have to update how it detects the correct shop name, not much point now we have pages.

# 2.0.6 - 2025-02-25 - The Eyes

### Added
- The source of an item is now appended to the item's description to help with determining where an item even came from. This also will serve the purpose of pointing out that you've forgotten to remove the version number from a content pack mod's folder if you've manually installed it.
- Nine new Eyes parts by Remedy, some silly, some infamous. I hope you'll enjoy them, and sorry if you have to change your eyes back to what they were before this update. (Remember the Vanity Mirror was made free!)

### Fixed
- A log message that states what params fields a piece of gear doesn't use was logging even when there was nothing to say, and it was logging for dyes too accidentally.
- PNGs removed from an item folder were not being removed as textures on items, so now the code will make them blank textures in this context. This affected boobOverride.png and legOverlay.png on Chestpieces mostly. Additionally, boobOverride.png will now be handled correctly during reload hotkey usage, and will copy armor.png when it should.
- Attempted to improve the item list scrolling code so it would properly update the list's position if the window resolution is changed, probably still not great.

### Changed
- The Skin "Catman_WhatOnEarth" (Looked like Source Engine game missing texture) was confusing way too many people into thinking something is broken with the mod/game, so it has been changed to a translucent flat colour texture to sort of look like a slime creature.

# 2.0.0 - 2025-02-16 - The Scrolling

### Added
- Customs for Capes, Shields, Byrdle Beak textures.
- Config setting to customise when the eyes try to close during the blink animation.
- Bumpscosity.
- Ability to scroll the item list in Skrit's Homebrewery Shop, as a compromise while I eventually figure out a good pagination method.

### Fixed
- Item display names now include the folder name, item type and source folder (content pack folder name) to prevent them from ever colliding when inserted to the game's ScriptableItems Dictionary. It's hidden as best as it can be using text formatting magic.

### Changed
- Improved the reloading code drastically through a major refactor of how the mod handles the files, and structures the data it needs to create part and item objects.

# 0.7.4 - 2024-12-24 - The Beginning
(Not released on Thunderstore)
### Added
- The entire mod at this point before rewriting a lot.
- Customs for: Dyes, Mouths, Eyes, Skins, Helms, Chestpieces and Leggings.
- Hotkeys for previewing the eye and mouth states in the character customisation UIs.
- A patch to make the eyes use the closed state when they blink.
- The Vanity Mirror patched to be free.
- HB Phone, a custom NPC dialogue, with shortcuts to the Storage UI and the Vanity Mirror UI.
- Skrit's Homebrewery Item Shop.
- All race's parts are spread to all other races in character customisation. Can be turned off via a config setting.
- Reload textures and items using LCtrl+R.

# 0.0.1 - 2024-11-29 - The First Compile