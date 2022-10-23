Hey guys! I took SaffronCitizen's suggestion and took a look into how item generation works. Without spoiling anything, it's pretty interesting! Below I provide a general outline of how the game selects what items to present to the to player. If you interested in getting a rough outline on how that works start from the top. But, if you are just interested in what that means for players you can skip straight to the analysis section and click the link to look at the graphs.

Disclaimer: The information contained in this post are focused specifically on the "Championship Race" and "Single Race" modes. Item wheel selection differs for other modes and is not covered here. Also, there is obviously the slight chance there is a bug in the item lookup and graph generation, so if you see something that doesn't make sense or doesn't match, please let me know!

# Item Wheel Basics
As was previously suspected by the community, there exists a list of predefined item wheels in the game. Looking at the code, it appears the specific number of pre-generated item wheels is 228. When an item wheel is requested by the player, the game refines this list down to **three** specific wheels. It is from these three wheels that the game then ultimately selects the wheel that is displayed to the player. While not the subject of this research (but already known by the community) it is then possible for a skilled player to accurate select one of the eight items in the wheel with a well timed button press. For the sake of this write-up it is assumed the player can execute this selection with 100% accuracy. ;)

# Wheel Refinement
On the first pass of item wheel selection, the game reduces the possible number of item wheels from 228 down to 3. Interestingly, this initial refinement process is entirely deterministic! It is based solely only the players position on the track and their position relative to the other cars in the race. The initial refinement process is driven by the following parameters:
- Lap Count
- Checkpoint Crossed
- Race Position*

Before moving on, however, a few details/caveats must be clarified. "Checkpoint Crossed" exists as a "flag" that tracks if the player has completed more than half of a given lap. This parameter is "sticky" meaning that driving backwards back into the first half of the track will not clear it. It will stay set until the finish line is crossed. Given that both this parameter and the lap count parameter both track the players progress through a track, they can be combined into a single parameter where progress is measure in half steps. e.g. The players lap count after completing a single lap (just starting their second) would be 2.0. If a player was over halfway done with their final lap their progress would be 3.5.

The other caveat is "Race Position". In the game, the displayed race position of the player is between 1 (first) and 8 (last). However, there exists a secret 9th position. This 9th position is reserved for a player who is significantly behind the second-to-last player (the one directly in front of them) where "significantly" is defined as >40,001 in game units. To give a practical example, this is roughly the distance from the start/finish line to the apex of the first turn on Toon Island II.
https://imgur.com/fUnIkYv.png

# Wheel Selection
Finally, once the game has narrowed the possible item wheels available down to three, it must make its final selection. While technically still deterministic, from the players perspective this selection is essentially random. This selection is done using a counter that is incremented every tick of the game. On the same frame the wheel refinement occurs, so too does this final wheel selection. The aforementioned counter is divided by the number of wheels (3 in this instance). The remainder of this division is then used to select one of the three wheels. The manipulation of this final wheel selection is almost assuredly a TAS-only trick.

# Analysis and Exploitation
As part of this research, the item list (and corresponding lookup table/functions) were dumped. By reimplementing the calculations and lookups done it is possible to explore any hypothetical situation desired. Furthermore, while any individual scenario may be of interest, analyzing the entirety of the dataset can reveal underlying trends. To this end, 2d [heatmaps](https://imgur.com/a/cbLfGIv) have been generated that represent the likelihood of a specific item appearing in an item wheel given a players lap and position. e.g. the darkest rectangle means the item will for sure show up in the wheel and a green rectangle means the desired item will only show up in one of the three possible wheels. Assuming the player is perfectly able to select an item from a wheel, the likelihood of an item encounter (the item being in the wheel presented to the player) is the same as the players chance to receive that item.

These heatmaps provide evidence/proof that the initial wheel refinement process is not only deterministic, but was implemented with intent. Item encounter rates frequently strongly correlate with player position. For example, looking at the heatmap for "Pandora's Box" it is clear that players in the back of the pack are most likely to receive this item. If a player in 9th position has completed at least half a lap, they will have a 100% chance of the item appearing. https://imgur.com/Li28QDE.png

Another interesting example is the "Bomb" which appears to have a linear trend with race progression. Presumably, the item is intended to be used most frequently in the mid-field, with players in the back never being offered a bomb/mine and players in the front only occasionally being provided the item.
https://imgur.com/AAlCNXl.png

# Conclusion
In order to keep this post to a somewhat reasonable length the vast majority of technical details (e.g. memory address, functions, look-up tables, etc) have been left out. If there is interest, these details could be included in a sperate, more technical post. Additionally, if there is interest in a standalone program for looking up the set of wheels that would be encountered under a given circumstance I would be open to cleaning up my code and providing a github link to the relevant data and tools used for analysis.

With that, I you've found this writeup entertaining! I really appreciated the suggestion. :) Reverse engineering the code has been a fun, puzzle-like challenge for me and I found the resulting data particularly amusing. I'll be interested to see if anything useful can come of this data with respect to speedruns. As always, any questions, suggestions or recommendations are welcome!