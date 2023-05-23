# sourcemod-nt-onroundconcluded-event
Provides the `OnRoundConcluded` global forward for plugin developers.

## Background
In NT, we have the [native events](https://wiki.alliedmods.net/Neotokyo_Events) `game_round_start` and `game_round_end` for tracking game state. The problem with `game_round_end` is that it triggers instantly before the next `game_round_start`, so it can't be used for detecting the moment a round has reached its conclusion. This leads to most plugins that need this state rolling their own looping solutions, ending up in duplicate work and a source for bugs. This plugin looks to alleviate that, by exposing a global forward that other plugins can listen to for capturing this info.

## Prototype
```sp
/**
 * Triggers when a round that has started with >0 players in both player teams
 * (TEAM_JINRAI and TEAM_NSF), ends.
 *
 * @param winner    Integer value of the winner team index, or TEAM_NONE if the round ended in a tie.
 */
function void OnRoundConcluded(int winner);
```

## Example use
```sp
#include <sourcemod>

// The Neotokyo TEAM_... index definitions are provided by the neotokyo.inc include
#include <neotokyo>

// Optional; use if the OnRoundConcluded is critical to your plugin
public void OnAllPluginsLoaded()
{
    if (FindConVar("sm_onroundconcluded_version") == null)
    {
        SetFailState("This plugin requires the \"NEOTOKYO OnRoundConcluded Event\" plugin");
    }
}

public void OnRoundConcluded(int winner)
{
    char teams[][] = {
        "none", "spectator", "Jinrai", "NSF"
    };

    PrintToServer("Round concluded; winner is: %s", teams[winner]);
}
```

## Notes
* The forward will **not** trigger if a round ends before it had started (ie. all players in a team dying/disconnecting before the freeze time ends, invalidating the round). Only rounds that have properly gone through the full round start freeze time, and concluded in a team victory or stalemate will trigger the forward.

* This forward is game mode agnostic, and takes no stance on whether the ghost was captured or not. Use the [OnGhostCaptured forward](https://github.com/softashell/nt-sourcemod-plugins/blob/master/scripting/nt_ghostcap.sp) for detecting a ghost capture, instead.

* The forward is **not** an Event, and as such you can't intercept it with Plugin_Handled/Plugin_Stop etc. If you want to manipulate the team score, you'll have to do so manually after receiving the forward.
