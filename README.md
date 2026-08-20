# csgo_movement_unlocker (fork)

Removes the ground speed cap in CS:GO so movement feels like CS:S. This is Peace-Maker's
plugin, and my fork is only a syntax pass: same logic, updated to modern SourcePawn so it
compiles on current SourceMod without a wall of deprecation warnings.

Upstream is 84 lines, this is 85. Nothing about how it works has changed.

## Install

Drop the `addons` folder into your `csgo` folder:

```
addons/sourcemod/plugins/csgo_movement_unlocker.smx              the compiled plugin
addons/sourcemod/scripting/csgo_movement_unlocker.sp             source
addons/sourcemod/gamedata/csgo_movement_unlocker.games.txt       required, do not skip this
```

The gamedata is not optional. Without it the plugin calls `SetFailState` on load, because it
has no address to patch.

No convars.

## How it works

`CGameMovement::WalkMove` scales your wish velocity down to `m_flMaxSpeed` when it exceeds it.
This plugin finds that instruction sequence by signature, walks forward by a fixed offset, and
overwrites the capping instructions with `0x90` NOP bytes. It saves the original bytes first
and restores them in `OnPluginEnd`, so unloading the plugin puts the server back rather than
requiring a restart.

That is a live patch to server memory found by byte signature, which means a CS:GO update that
touches `WalkMove` breaks it until the signature in the gamedata is updated. That is inherent
to the approach, not something the fork changed.

## What I changed

Old-style declarations to new ones: `new` to `int`, `new Address:x` to `Address x`,
`Address:iCapOffset` casts to `view_as<Address>`, `public Plugin:myinfo` to `public Plugin
myinfo`, and `public OnPluginStart()` to `public void OnPluginStart()`. Added
`#pragma newdecls required` so it stays that way.

Version bumped 1.0 to 1.1 to tell the two apart on a server. Peace-Maker's comments are
untouched.

## Credits

Original **CS:GO Movement Unlocker** by **Peace-Maker**, published on AlliedModders:
[forums.alliedmods.net/showthread.php?t=255298](https://forums.alliedmods.net/showthread.php?t=255298)

The gamedata file is his as well.

## License

GPL-3.0, see `LICENSE`.
