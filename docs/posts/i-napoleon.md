---
draft: false
date: 2024-08-19
authors:
  - me
categories:
  - Boardgames
---

# I, Napoleon

This isn't a review, or a post about my thoughts on the game. It's more a players guide. Something that's digital, mobile friendly, and a shortcut to many of the things I consistently look up in the official rulebook.

<!-- more -->

## Napoleon's Luck
Once per year, you may use Napoleon's Luck for any one of the following:

 - To reroll any one Die Roll
 - To immediately Ignore any one Mandatory Card just drawn and redraw a new card instead
 - During the Campaign Resolution Phase, use Napoleon's Luck instead of paying the cost of any one **Commander** or **Strategy & Tactics** card

Napoleon's Luck may not be used after Abdication

## Game Ending Scenarios
The game immediately ends if any of the following occur at any point during the game:

 - Card text explicitly noting that the game ends
 - Glory reaches - during `FCD` or `EMD`
 - `FCD` isn't triggered by the end of 1802
 - `EMD` isn't triggered by the end of `FCD`
 - Resolving certain cards will end the game (97, 156, 209, etc...)
 - During Abdication
 - At the conclusion of the Final Year

## Sequence of Play

This is a semi-detailed Sequence of Play that covers just enough to avoid referencing the rulebook.

### Year Setup Phase
This phase is skipped in the first year of any scenario.

1. Advance the Year marker
2. Reset the Card Draw marker
3. Take Resource Income

    1. May exchange **Political** for **Administrative** `1:1`
    2. May exchange **Glory** for **Administrative** `3:1`

!!! note ""

    Either Resource conversion may only yield up to a maximum combined total of 3 Admin points per year.

### Card Deck Phase
This phase is skipped in the first year of any scenario.

1. Remove Cards

    !!! note ""

        Years when cards are removed:  
        1794, 1795, 1796, 1802, 1803, 1813, 1814, 1815, 1816

2. Add Cards
3. Shuffle Draw Deck

    !!! warning ""

        The game ends If

        - Brumaire (47) has not been played by the end of CMD
        - or if Imperial Plebiscite (110) has not been played by the end of FCD.

### Card Action Phase

1. Draw a New Card and do what it says

    ??? note "A note on card costs"

        - If the Cost of a Mandatory card **can** be paid, it **must** be paid.
        - If the cost of a mandatory card **cannot** be paid, the card is Discarded and a new card is drawn without increasing the Card Draw marker.
        - If you do not have enough to pay the cost (or do not want to pay the cost) of a non-Mandatory card, you cannot Play that card and must Discard it. _However, on non-Mandatory cards, you do not redraw if you Discard the card._

2. Play an Existing Card. Any Eligible, face-up Placed Cards on the map may now be Played, by resolving the Main Section. After Playing the card, be sure the Remove or Discard it, if required. If the card has an Ongoing Effect whcih can only Trigger once per Year, you may want to exhaust the card by rotating it as a reminder that its effect has been used.

3. Phase Advancement Roll

    ??? note "A note on exhausted draw deck during CMD"

        In the extremely rare event that there are no cards left in the draw deck during this segment in the CMD Era, immediately skip to the Campaign Resolution Phase and Play Brumaire (47). If you cannot afford the combined 20G/P, the game immediately ends.

### Campaign Resolution Phase

If the Campaign Box is empty, skip to Campaign Clean-up... **UNLESS** Spanish Ulcer (221) is active.

#### Assign Commanders
Pay the cost indicated for every assigned commander regardless of use. Keep in mind A/G/P you may want to spend on Strategy & Tactics.  

If _Spanish Ulcer Expeditions_ (221) is active, one Independent Commander _must_ be assigned (if possible) to the SPAIN Box before any other Commander cards may be assigned to the curent campaign.

#### Spanish Ulcer Expeditions (EMD Only)
The _Spanish Ulcer Expeditions_ (221) becomes active under two conditions:

 - _Spain Rises_ (199) is Played
 - Spain becomes Hostile

It remains active until the game ends, or Napoleon abdicates.  

The Spanish Ulcer must be resolved during each Campaign Resolution Phase. 

 - Use any eligible cards in the BRITAIN Box, and any assigned Independent Commanders, along with any _Strategy & Tactics_ cards to determine DRMs. 
 - Apply the result of Success or Failure. Failure requires any one IC assigned to be randomly Discarded (unless Junot. He must be removed per card instructions). 
 - Used Commanders and Strategy & Tactics cards are returned to their boxes, face-down.

#### Campaign Resolution

1. **Select Commanders** among those assigned, pay attention to any indicated "combat round" restrictions
2. **Add Strategy & Tactics Cards**. Unlike Commander cards, these do not need added ahead of time, and may be reused in subsequent Combat Rounds. This is not always the case though... again, pay attention to combat round restrictions.
3. **Check Hostile Nation Effects** (mid-late game)
4. **Check Campaign Modifiers** for DRMs during specific Combat Rounds
5. **Determine Final DRM**
6. **Flip Used Commanders Face-Down**. That is: Flip all Commanders slected for the Combat Round face-down as a reminder that they may not be used in any subsequent Combat Round.
7. **Combat Roll and Resolution**

    | Modified DR Result | Outcome          |
    |--------------------|------------------|
    | 1 or less          | Bloody Defeat    |
    | 2                  | Defeat           |
    | 3-4                | Bloody Stalemate |
    | 5-6                | Stalemate        |
    | 7-8                | Bloody Victory   |
    | 9 or more          | Victory          |

8. **Check for Adjusted Result** Some campaign modifier cards alter the outcome of a battle.  

    - **If both friendly and enemy cards can adjust the result, the enemy card's effect is applied first.** This may mean that the Friendly card no longer adjusts the result, or that it now does adjust the result.
    - If the final outcome was **Bloody**, place a Bloody Result marker near the Domestic Politics Table. This will `-1 DRM` for that phase. _Bloody Result markers are not placed during CMD._
    - If Victory, Treaty cards are Placed face-down in the CURRENT TREATIES Box. If the result calls for a Trety to be placed and it is not in the POTENTIAL TREATIES area of the board (because it was Placed preveiously, or Removed), the affected nation may be [Conquered]() instead.
    - If Stalemate, `-2G` and `-1 DRM` for the next combat round, and any subsequent combat rounds.

#### Commander Casualties
Roll a D10 for all assigned commanders regardless of use. On 1, or 2 Commanders are discarded.

#### Other Campaign Events
Resolve main section of any Campaign Modifiers with an effect that triggers after campaign resolution.

#### Additional Campaign Resolution
Sometimes multiple campaigns must be resolved. In this case:

 - Place the new campaign
 - Flip commanders used in the first campaign up (new commanders may not be added)
 - Flip Strategy & Tactics cards up. If they have a cost, it must be paid again
 - Any campaign modifiers from the original campaign are discarded

#### Campaign Cleanup
 - Remove **Campaign Modifiers**, and **Campaigns** that have the "when played" icon in the bottom-right
 - Return **commanders** and **strategy & tactics** cards to their boxes face-up.
 - Return **Potential Campigns** cards to their area
 - Discard Campaign Modifiers that were not removed.

#### Abdication Check
If the Campaign Resolution phase result was a Defeat in _La Patrie en Danger_ (149), Game End has been triggered. Perform the [Abdication Segment]().

### Diplomatic Phase
#### Foreign Diplomacy
At the start of this phase, you may exchange:

 - **Glory** for **Diplomatic** `3:1`
 - **Political** for **Diplomatic** `1:1`

 for a maximum of one Diplomatic point per year. Diplomatic points may be spent to +1 DRM / point.

| Modified DR | Result                                                      |
|-------------|-------------------------------------------------------------|
| 1 to 5      | Remove Treaty or move the marker towards (or to) Hostile.   |
| 6 or more   | No effect.                                                  |

#### Current Treaties

 - Face-down treaties are not rolled.
 - Roll for face-up treaties. On a modified DR of 1-5:
    - During `FCD`, move the marker towards Hostile
    - During `EMD`, remove the treaty from the CURRENT TREATIES Box


!!! tip "Keeping track of the diplomatic landscape"

    I find it helpful to rotate a nation's diplomacy marker if I'm not performing a diplomacy roll for some time. For example, after victory in Austria (Italy) Austria will be Neutral under _The Treaty of Luneville_ until Napoleon becomes Emperor.

#### Breaking Treaties
Napoleon may Remove any Treaty card in the CURRENT TREATIES Box. If you choose to do so:

 - Set affected nations to Hostile
 - Remove the treaty from the game

#### Conquest
**Austria**, **Prussia**, and **Britain** may be Conquered. Conquering a nation results in +10G and +1P per nation to the player during the Resource Income segment.  

**Austria and Prussia**  
If Austria and/or Prussia are defeated in a Campaign and no Potnetial Treaty card exists, the nation is considered Conquered. Flip its marker to its Conquered side. Austria or Prussia remain Conquered until:

 - Napoleon suffers a Defeat in a Campaign
 - Or cards:
    - Prussia Rises (198)
    - English Gold Austria (188)
    - English Gold (189)

**Britain**  
Britain can only be Conquered by playing _Trafalgar_ (117) IF _Nelson_ (36) is not face-up in the BRITAIN Box. Flip its marker to its Conquered side and remove the following cards from Play:

 - John Moore (116)
 - Wellington (118)
 - English Expedition (131)
 - Continental System (133)
 - English Gold (Austria) (188)
 - English Gold (189)
 - Lines of Torres Vedras (193)

A Conquered Britian remains Conquerted for the rest of the game.

**Russia and Spain**  
Russia and Spain may never be Conquered. If no treaty, they remain Hostile. However:

 - If Russia suffers a Defeat while Prussia and Austria are Conquered, flip its marker to "Inactive"
 - Inactive Russia does not take part in any Campaigns until Austria or Prussia become Hostile.
 - Inactive Russia is still considered Hostile for Domestic Politics

#### Potential Campaigns
Determine whether or not a Potential Campaing card is Placed in the CAMPAIGN Box for next year. This is Optional during `FCD` but Mandatory during `EMD`.  

**FCD Potential Campaigns**  
If **Spain** and/or **Prussia** is Hostile, you may pick either:

 - Republican War with Prussia (70)
 - Republican War with Spain (71)

**EMD Potential Campaigns**  
Check the EMD Potential Campaigns Table below for Campaign selection. If the Diplomatic Status of a nation changes in the subesquent year, check for a better matching campaign card, and replace the current campaign.

| Austria                    | Prussia                    | Russia                     | Spain                | Potential Campaign to Select                                                                                                 |
|----------------------------|----------------------------|----------------------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Hostile                    | Allied, Neutral, Conquered | Allied, Neutral, Inactive  | Any                  | Austria Declares War (145)                                                                                                   |
| Hostile                    | Hostile                    | Allied, Neutral, Inactive  | Any                  | Austria & Prussia Declare War (146)                                                                                          |
| Hostile                    | Hostile                    | Hostile                    | Any                  | Austria, Prussia, & Russia Declare War (147)                                                                                 |
| Hostile                    | Allied, Neutral, Conquered | Hostile                    | Any                  | Austria & Russia Declare War (148)                                                                                           |
| Conquered                  | Allied or Neutral          | Hostile                    | Any                  | Austria & Russia Declare War (148) - Place in the CAMPAIGN Box but Austria's cards may not be used when resolved.            |
| Any                        | Any                        | Any                        | Any                  | La Patrie en Danger (149) - Must Place when Triggered by a Campaign Result.                                                  |
| Allied, Neutral, Conquered | Allied, Neutral, Conquered | Allied, Neutral, Inactive  | Hostile              | Napoleon in Spain (150)                                                                                                      |
| Any                        | Any                        | Any                        | Any                  | Polish Campaign (151) - May Place and resolve immediately when Triggered by Victory in Prussia and Russia Declare War (153). |
| Allied, Neutral, Conquered | Hostile                    | Allied, Neutral, Inactive  | Any                  | Prussia Declares War (152)                                                                                                   |
| Allied of Neutral          | Hostile                    | Hostile                    | Any                  | Prussia & Russia Declare War (153)                                                                                           |
| Allied or Neutral          | Conquered                  | Hostile                    | Any                  | Prussia & Russia Declare War (153) - Place in the CAMPAIGN Box but Prussia's cards may not be used when resolved.            |
| Allied or Neutral          | Allied                     | Hostile or Neutral         | Any                  | Retreat From Russia (154) - Must Place and resolve immediately when Triggered by Stalemate or Defeat in Russia (155).        |
| Allied or Neutral          | Allied                     | Hostile or Neutral         | Any                  | Russia (155) - May Place in CAMPAIGN Box. -5 G if not played when possible.                                                  |
| Conquered                  | Conquered                  | Hostile                    | Any                  | Roll a Die: <br> 1-5: Austria & Russia Declare War (148) - Place in the CAMPAIGN Box but Austria's cards may not be used when resolved. <br> 6-10: Prussia & Russia Declare War (153) - Place in the CAMPAIGN Box but Prussia's cards may not be used when resolved. |
| Allied, Neutral, Conquered | Allied, Neutral, Conquered | Allied, Neutral, Inactive  | Allied or Neutral    | No EMD Campaign must be selected.                                        |


### Domestic Politics Phase
At the start of this phase, you may exchange:

 - **Admin** for **Political** `2:1`
 - **Glory** for **Political** `3:1`

for a maximum of one Political point per year. Political points may be spent to +1 DRM / 2P spent. Calculate the Final DRM accounting for variables on the Domestic Politics Table. Then adjust Glory as follows:

| Modified DR | Result     |
|-------------|------------|
| 0 or less   | –10 Glory  |
| 1-3         | –5 Glory   |
| 4-6         | –3 Glory   |
| 7-9         | –2 Glory   |
| 10 or more  | No effect  |


### Clean-Up Phase

1. Flip all face-down cards in the CURRENT TREATIES Box face-up
2. Flip the Napoleon's Luck marker, if used, to its unused side
3. Reset Admin and Diplomacy markers to zero (Admin is **not** reset in the first year)

## Links

- [Rulebook](https://gmtwebsiteassets.s3.us-west-2.amazonaws.com/INapoleon/I_Napoleon_Rules_Final_Hi-Res_WEB.pdf)
- [FAQ and Clarifications](https://gmtwebsiteassets.s3.us-west-2.amazonaws.com/INapoleon/INapoleon_FAQ_Clarifications_8-15-24.pdf)

## Interactions on BGG

Questions:

- [Napoleon's Luck](https://boardgamegeek.com/thread/3344399/russo-swedish-war-and-napoleons-luck)
- [Josephine De Beauharnais' Death](https://boardgamegeek.com/thread/3344393/in-the-event-of-josephine-de-beauharnais-death)
- [FCD Potential Campaigns](https://boardgamegeek.com/thread/3341845/fcd-potential-campaigns-change-in-diplomatic-statu)

General:

- [Playbook Digital Copy](https://boardgamegeek.com/thread/3353205/playbook-digital-copy)
- [Rulebook 14.7 Natural Frontiers - Typo](https://boardgamegeek.com/thread/3355131/rulebook-147-natural-frontiers-typo)


