# HordesIO_Simulator

## <a href="https://saqirmdevx.itch.io/hordesio-simulationcraft"> Simulationcraft app </a>

This is simulation for <a href="https://hordes.io"> Hordes.Io </a>

## APL Example

[Warrior-APL](https://github.com/saqirmdevx/HordesIO_Simulator/blob/master/warrior.siml)

[Mage-APL](https://github.com/saqirmdevx/HordesIO_Simulator/blob/master/mage.siml)

[Archer-APL](https://github.com/saqirmdevx/HordesIO_Simulator/blob/master/archer.siml)

[Shaman-APL](https://github.com/saqirmdevx/HordesIO_Simulator/blob/master/shaman.siml)


## How APL Works? 

### Default config: 

```php
# - this tag stands for comments. All text after # is ignored by parser at the line.

debug=0 # Default: 0 - Values 0 / 1 - If this is 1 ( true ) it will display debug text (simulation combat log)
slowmotion=0 # Default: 0 - Values 0 / 1 - if this is 1 (true) it will simulate game in real time
simulationtime=300000 # Default: 300000 - This is time to simulate in miliseconds

targets=1 # Default: 1 - Values between 1 - 20 - Number of targets (All AoE abilites will always hits all targets !!! Some AoE Abilites has limited max targets)
simulators=100 # Default: 100 - Values between 1 - 10000 - Number of simulators. Higher value will provide more precise results. !!!(More simulators can increase process time)
mitigation=0.0 # Default: 0 - Value between 0 - 1 - Damage reduction by % this is replace for target's defense
```

### Stats

```php
autoattack=0 # Defines if player is using autoattack (Warrior, archer, shaman) Mage is exception
mana=600 # Defines player's max mana
stats.manaregen=8 # Defines player's manaregen
stats.block=0.1 # Defines player's block !!! Game conversion is -> 35.5% block => 0.355 !!! 
stats.mindamage=184 # Defines player's min damage
stats.maxdamage=244 # Defines player's max damage
stats.haste=0.3 # Defines player's haste !!! Game conversion is -> 28.6% haste => 0.286 !!! 
stats.critical=0.3 # Defines player's critical !!! Game conversion is -> 20% critical => 0.20 !!! 
stats.attackSpeed=0.0 # Defines player's attack speed !!! Game conversion is -> 34 attack speed => 0.34 !!! 
```

### How to add ability to apl script
Ability requires ID, Rank and end with push() function.

Every ability contains some conditions like - Required Aura, Required mana %, If certain ability is on Cooldown. In example below i show you how to add certain conditions

Example: 
```php
#Ice Bolt
ability.id=20 # This is ability ID (Below you will see all IDs of abilites and auras)
ability.rank=5 # Rank between 1 - 5 !!! Some abilites has different max rank !!!
ability=push() # Push the ability into player's ability list. Now parser is looking for another ability

#Icicle Orb
ability.id=20 # This is ability ID (Below you will see all IDs of abilites and auras)
ability.rank=5 # Rank between 1 - 5 !!! Some abilites has different max rank !!!
ability.condition.aura=2002 # This ability will be casted only if target has aura 2002 => Ice bolt's freeze
ability=push() # Push the ability into player's ability list. Now parser is looking for another ability
```

- Conditions:
Conditions can be negated by using != instead of =
```php
ability.condition.aura=(auraId) # Ability with this condition will be casted only if target has aura (auraId)
ability.condition.aura!=(auraId) # Ability with this condition will be not casted if target has aura (auraId)

ability.condition.mana=(mana%) # If player has (mana%) and more % of mana
ability.condition.mana!=(mana%) # If player has less % mana than (mana%)

ability.condition.cooldown=(abilityId) # If player has cooldown on (abilityId) this ability will be casted
ability.condition.cooldown!=(abilityId) # If player has no cooldown on (abilityId) this ability will be casted

ability.forced=1 # Value between 1 and 0 (Boolean) - If ability is forced to cast even if aura is up

ability.once=0 # Value between 1 and 0 (boolean) - Ability flaged with once will cast only once for the simulation (once per simulator)
```
More conditions will be added in future.

### Ability Queue (Cast consequence)
This is used to make combos and own casting order
All abilites will be casted in order. Only if they met conditions, if condition is not met, it will go to next ability in queue.

- You have to add all abilites inside queue or they will be never casted in simulation !
- You can use same abilityId multiple times like - ... Ice bolt - Icicle Orb - Ice bolt ...
```php
...
abilityQue.add=23 #Enchant - Will be casted first
abilityQue.add=24 #Artic Aura - Will be casted 2.
abilityQue.add=22 #chilling Radiance - Will be casted 3.
abilityQue.add=20 #Icebolt - Will be casted 4.
abilityQue.add=25 #Hypothermic Frenzy - Will be casted 5.
abilityQue.add=20 #Icebolt - Will be casted 6. !!! This is valid syntax
abilityQue.add=21 #Icicle Orb - Will be casted 7. and repeat from 1. ability (enchant)
```

- Abilites like Enchant, Arctic Aura, unholy Warcry will be casted only if aura expires. We can apply `ability.once=1` and ability will be casted only once
- If pointer is on abilityQue [3] and ability is on cooldown or does not met conditions it will turn to position [4] and so ...

## Ability and Aura List of IDs

### Warrior :
Name - Id 
```code
    Slash = 0
    Crescent Swipe = 1
    Unholy Warcry = 2
    Centrifugal Laceration = 3
    Armor Reinforcement = 4
    Taunt = 5
    Charge = 6
    Crusader Courage = 7
    Bulwark = 8 /* Same id for BLOCK% buff */
     - Bulwark Damage Buff = 1006 /* Damage buff */

    Colossal Reconstruction = 9
    Tempering = 10
```

### Mage :
Name - Id 
```code
    Icebolt = 20
     - Icebolt Debuff - Slow = 2001
     - Icebolt Debuff - Freeze (5. stack) = 2002
     - Icebolt Buff - Instant Cast = 2003

    Icicle Orb = 21
    Chilling Radiance = 22
    Enchant = 23
    Arctic Aura = 24
    Hypothermic Frenzy = 25
    Ice Shield = 26
    Teleport = 27
```

### Archer: 
Name - Id 
```code
    Swift Shot = 28
     - Swift Shot - Instant Aura = 3000 /** Applied by precise shot */

    Precise Shot = 29
     - Precise Shot - Instant = 3001 /** Applied by Dash */

    Dash = 30
    Serpent Arrows = 31
    Poison Arrows = 32
    Invigorate = 33
    Pathfinding = 34
    Cranial Punctures = 35
    Temporal Dilatation = 36
```
### Shaman: 
Name - Id 
```code
    Decay = 37  /** Same id for debuff **/
    Plaguespreader = 38 /** Same id for buff **/
    Soul Harvest = 39
    Canine Howl = 40 /** Same id for buff **/
    Mimir's well = 41 /** Same id for buff **/
    Animal Spirit = 42 /** Same id for buff **/
    //** There are not healing abilites as we are simulating DPS only. Might be added in future **//
```

### Items :
Name - Id
```code
    Small Mana potion = 5001
    Medium Mana potion = 5002
    Large Mana potion = 5003
     - Mana potion buff = 6000 //** All mana potions apply this buff id. use ability.condition.aura=6000 **//

    Charm: Tattooed Skull = 5004
```
