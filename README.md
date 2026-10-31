# Self-sealing Airlocks Revived V2

An [Oxygen Not Included](https://store.steampowered.com/app/457140/) mod that makes airlocks airtight.

- **Auto** or **Locked**: the door stays sealed. Gases and liquids can't pass, even while a duplicant is walking through.
- **Open**: the door behaves like vanilla, so gas and liquid flow through.

It applies to every door except the Pneumatic Door, which is left vanilla.

This version fixes the crash reported after the 2026 game updates (build U57-704096):

```
Exception in: (GravitasDoor).Door+Controller.root.closed.SetWorldStateClosed
System.MissingMethodException: Method not found: void .SimMessages.SetCellProperties(int,byte)
```

## Credits

This mod is a continuation of earlier work by other people:

- **[chromiumboy](https://github.com/chromiumboy)** wrote the original
  [Self-sealing Airlocks](https://github.com/chromiumboy/OxygenNotIncludedMods/tree/master/SelfSealingAirlocks) mod. The idea and the original approach are theirs.
- **[LoneMarauder](https://github.com/LoneMarauder)** (Greg Dickinson) ported it to Harmony 2 and kept it alive as
  [Self-sealing Airlocks Revived](https://github.com/LoneMarauder/ONI-Mods/tree/main/SelfSealingAirlocksRevived)
  ([Steam Workshop](https://steamcommunity.com/sharedfiles/filedetails/?id=2542160819)).
- **[data54](https://github.com/data54)** contributed fixes to the Revived version, including letting gas through doors set to Open.

Thank you all. V2 exists only because the Revived version stopped working after a game update.

## What changed in V2

Modified by [yohan-id](https://github.com/yohan-id), 2026-10-31.

- **Crash fix.** The game added an optional `callbackIdx` parameter to `SimMessages.SetCellProperties` and `SimMessages.ClearCellProperties`. The old DLL was compiled against the previous signature, so it threw `MissingMethodException` whenever a door changed state.
- **Works with the game's door code.** The old patch replaced `Door.SetSimState` completely. V2 only steps in when a door in Auto/Locked mode swings open for a duplicant, and keeps the door's cells solid. Everything else runs the vanilla code, including temperature, insulation, falling sand and rendering. The patch no longer calls the sim API that broke, so game updates are less likely to break it.
- **Fixed:** in Auto mode the old patch kept the cell solid but still switched the door's temperature handling to "open", which could cause heat glitches.
- **Fixed:** switching a door to Open while a duplicant stood in the doorway left it sealed.
- **Save cleanup:** earlier versions stamped a GasImpermeable flag on airlock cells, and saves keep it. V2 clears it when a door loads, so doors set to Open pass gas again.
- **Removed** the animation-override patch, which the game now does itself, and an unused `config.json`.
- **Build** targets .NET Framework 4.8 and compiles against your installed game's DLLs instead of copies checked into the repo.

## Installing

1. Disable or unsubscribe from the old *Self-sealing Airlocks Revived* mod.
2. Build the mod (see below), or download a release.
3. Copy `SelfSealingAirlocks.dll`, `mod.yaml` and `mod_info.yaml` into
   `Documents\Klei\OxygenNotIncluded\mods\Dev\SelfSealingAirlocksRevivedV2\`.
4. Enable it in the game's Mods menu and restart.

**Known issue with existing saves:** if a door was deconstructed while the broken Revived version was running, that cell may keep an invisible gas barrier. Building and removing a door on that cell clears it.

## Building

You need the [.NET SDK](https://dotnet.microsoft.com/download) (any recent version) and Oxygen Not Included installed.

```bash
dotnet build SelfSealingAirlocksRevivedV2.csproj -c Release -p:GameManaged="C:\Program Files (x86)\Steam\steamapps\common\OxygenNotIncluded\OxygenNotIncluded_Data\Managed"
```

Point `GameManaged` at your own game's `OxygenNotIncluded_Data\Managed` folder. The packaged mod is written to `ReleaseFiles/`.

To copy the build straight into your mods folder, add `-p:ModsDevDir="<path to Documents>\Klei\OxygenNotIncluded\mods\Dev"`.

Always rebuild against the current game DLLs after a game update. Compiling against an old copy is what caused the crash above.

## License

[GNU General Public License v3.0](LICENSE), the same license as the Revived version this is based on.
