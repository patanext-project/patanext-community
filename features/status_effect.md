# PataNext 'Status Effects'
PataNext will offer a new system for status effects, which will be random-less compared to older games of the official series.

## Objectives!
- Advanced players should be able to guess if their or enemy units will succomb a status or not.
- For casual players, it should feel as a smooth transition, even if the system will be complex, it shouldn't force the player to learn it.
- The functions and terminologies should be simple, but hard to master:
- We need to ensure that players know that taking a fire equipment will not make them resistant to fire. 
- No random!
- But a way to resist to an effect the more we receive it!
  - So that an unit will not have its fate sealed
  - So that you can't just cheese a boss by doing the same tactic over and over.

## Terminology
- **Resistance**: The resistance (%) on that status effect.
- **Power**: How much resistance (%) are going to be removed on an attack.
- **Resistance Regen**: How much resistance (%) will be regenerated every second.
- **Immunity**: As gaining immunity over time, not total immunity. In (%) how much power will be removed on the next attack.
- **Immunity Decrease**: How much immunity (%) will be decreased every second.

# Let's go!
## The Basics
In the unit statistics, a status effect is exprimed in 4 statistics:
1. Power
2. Resistance
3. Regen/s
4. Immunity

It look very simple at first glance, but the complexity factor, and the thing that doesn't make stuff boring is the **Immunity** stat.
First, let's say that you have **Unit A** and **Unit B**, and **A** will deal fire damage:

|Unit|Power|Resistance|Regen/s|Immunity|
|----|-----|----------|-------|--------|
| A  | 10% |          |       |        |
| B  |     | 30%      | 1%    | 0.1%   |

Now that we know each unit stats, let's look in details on what 5 attacks on **B** will do.
For the sake of simplicity, **A** will always deal the same fire power, and the regeneration will not be applied.

|Attack N°|Resistance                         |Immunity                  |On Fire?
|:-------:|:---------------------------------:|:------------------------:|---|
| Before  | 30%                               | 0%                       |No|
| 1       | 20%  `-Power:10%`                 | 1% `+(Power:10% * 0.1%)` |No|
| 2       | 11%  `-(Power:10% - Immunity:1%)` | 2% `+(Power:10% * 0.1%)` |No|
| 3       | 3%   `-(Power:10% - Immunity:2%)` | 3% `+(Power:10% * 0.1%)` |No|
| 4       | -4%  `-(Power:10% - Immunity:3%)` | 4% `+(Power:10% * 0.1%)` |Yes|
| 5       | -10% `-(Power:10% - Immunity:4%)` | 5% `+(Power:10% * 0.1%)` |Yes|

If there was no immunity, **Unit B** would be on fire on the third attack, and would be at -20% resistance on the fifth attack.
Now imagine the possibilities, should you make a hero resistant in the short term, or long term?

## Complex variables (Resistance Regeneration and Immunity Decay)

### Resistance
The resistance regenerate every second based on the 'Regen/s' stat.
This regeneration is doubled when the current resistance is negative.

### Immunity
The immunity doesn't stay forever, but there is a small hidden counter that will decrease the immunity over time. 
The counter is reset at each received hit.  
The counter increase every second based on the 'Regen/s' stat and how further we are from negative initial resistance from a negative standpoint. 
Then, the immunity is linearly interpolated (`lerp(a, b, t)`) to 0 with ***t*** (progress) set as the counter.  

This is the most complex function to understand, so you should really understand it as: 
- My immunity counter will pause for a short time when I receive a hit
- My immunity will decay faster if my resistance is in the positive, but will decay slower if it's in the negative.
- My immunity will be kept longer if my 'Regen/s' is **lower**.

### Strategy
Having a higher 'Regen/s' is good for regenerating the resistance faster, but bad for keeping the immunity longer.  
If your unit is not one to be in the frontline everytime, the best would be to have a high 'Regen/s' stat.
If your unit is always at the frontline, then it's a hard choice:
- Do you predict the enemies will have a fast fire-rate with a status effect (eg: Jamsch attacks) you want to not have?
  - If yes, go with a low 'Regen/s', you will surely have the effects at the first second, but the immunity will build really fast, forcing the enemies to stop attacking you.
  - If no, go with a high 'Regen/s', you will have time to regenerate before the next attack can deal the effect.

### Easing the difficulty
The system itself is very complex, but the player shouldn't expect to understand how it work under the hood.
The player is expected to modify their equipment based on the incoming fight.
Only the current resistance and when you receive a status effect is shown in the interface for the sake of simplicity.