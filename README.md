# sourcemod-nt-onroundconcluded-event
Provides the `OnRoundConcluded` global forward for plugin developers.

## Prototype
```sp
/**
 * Triggers when a round that has started with >0 players in both player teams
 * (TEAM_JINRAI and TEAM_NSF), ends.
 *
 * @param winner    Integer value of the winner team index, or TEAM_NONE if the round ended in a tie.
 */
public void OnRoundConcluded(int winner);
```

## Example use
```sp
#include <sourcemod>

#include <neotokyo>

public void OnRoundConcluded(int winner)
{
	char teams[][] = {
		"none", "spectator", "Jinrai", "NSF"
	};
	
	PrintToServer("Round concluded; winner is: %s", teams[winner]);
}
```
