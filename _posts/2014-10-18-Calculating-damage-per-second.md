---
layout: post
title:  "Calculating the damage per second of a DotA 2 Hero"
date:   2014-10-18 12:00:08
categories: dota2 gaming optimisation
published: true 
comments: false
---

#Introduction to DotA 2

As you might be able to tell by the name of this blog, I like to play videogames.
It's something I've done since I can remember. I was something I was brought up with. 
My mother plays videogames, my brother plays and my girlfriend plays them.

For the last year or so I've been taken with a pariticular game called Defense of the Ancients 2 (DotA 2).
The basic idea of a game is to destroy the enemy base, called an Ancient. 
5 hero's (from a pool of 114) on each team attempt to push through the oppositions base defenses, Towers which are static structures which put out a lot of damage  and barracks, which weaken the opposing team if you can destroy them.

The level of complexity in this game is astounding.
At it's highest level the games attract millions of viewers and a prize pool of 10 million dollars.
Even playing at my level I don't think I've played two games which are exactly alike.
Different hero's on both the allied and enemy side cause each game to go very differently.

Something I'd like to investigate in this blog is how game theory can be used to analyse a given match. 
This would involve breaking down a match into smaller and smaller pieces, and finding the optimal action to take in these situations.
This is the first peice of that: optimising damage output.

#Optimising Damage per Second

While each match is different, they follow the same pattern.
It starts of with the hero's weak, unable to fight the base defenses.
Each team spends time getting stronger and stronger called farming.
This continues until one team feels confident enough to make an assualt on the base, and take out a tower or two. 
This will go back and forth with each team chipping away at towers and barracks, all the while getting stronger and stronger.

Within each team one hero will be designated the 'carry'.
This hero is designed to be weak, at the start of the game, and if not controlled by the opposition (by killing them and stopping thier farm) become strong enough to completely wipe out the enemy team by themselves.
This requires that the carry buys the correct items to maximise thier damage per second (DpS).
Getting the correct mix of attack speed and attack damage can be tricky as each hero can only hold 6 items, which is what the short script I've written does. 


Lets simplify the situation. Our carry is a hero called Faceless Void. 
He's a cherry fellow that has a nasty habit of disappearing for 20-40 mins, reappearing and walking through your team, towers, barracks, Ancient, hopes and dreams like they were made of butter.
The reason he can do this is his ultimate ability called chronosphere.
He presses a button and the he gets 5 seconds of uninterupted hitting you in the face, where you cant fight back.
However once this time is up, if he hasn't murdere everything you hold dear, he dies in about 4 hits from your carry.
He's built for damage, not survivability.

So which items should he purchase? Eventualy he will have the pick of the items, so the two we will consider are the best damage and attack speed items respectvely. The Divine Rapier and the Mjolnir.

The Divine Rapier gives the most raw attack damage at a ridiculous +300.
It's a risky item however, if you're killed while carrying it, the enemy who killed you gets it.
Rapier's are only bought in 2 circumstances usually, if you are so far behind that it would be the only way to come back, or your hero has a way to use its damage safely, exactly like voids chronosphere.

The Mjolnir is a much less risky proposition, it gives a +24 damage but greatly improves the rate at which you attack. 
It something most carries will pick up at some point, as it also increases the rate at which your hero will farm.

So now the quetion becomes, what combinations of these two items should void pick up?

The quick script I wrote will maximise the DpS of Void.

{% highlight python %}
class Hero():
     
    def __init__(self, base_damage, primary_attribute, base_attack_time, strength, inteligence, agility):
        
        self.base_damage           = base_damage
        self.base_attack_time      = base_attack_time
        self.primary_attribute     = primary_attribute
        
        self.base_strength         = strength[0]
        self.strength_per_level    = strength[1]
        
        self.base_inteligence      = inteligence[0]
        self.inteligence_per_level = inteligence[1]
        
        self.base_agility          = agility[0]
        self.agility_per_level     = agility[1]
{% endhighlight %}


These are all the attributes which define a hero in DotA 2 (along with 4 abilities, but abilities wont play much of a part in the DpS).
We have base damage, which is what a hero with 0 stats would start of with.
Base attack time, which is how fast an attack would be with 0 attack speed.
Primary attribute tells us which of the 3 stats are converted to damage.
Then the three stats that all heros have, stregnth, inteligence and agility, which are enetered as lists, first element being the base number, then the number gained per level.

Next we need an Item class


{% highlight python %}
class Item():
    def __init__(self, damage, attack_speed, strength, inteligence, agility):
        
        self.damage       = damage
        self.attack_speed = attack_speed
        
        self.strength     = strength
        self.inteligence  = inteligence
        self.agility      = agility
{% endhighlight %}

All the above is how much of each attribute an item gives a hero.

and finally as a method of the Hero class, we have calculate dps

{% highlight python %}
def calc_dps(self, level, items = []):
         
    increased_attack_speed = self.base_agility + self.agility_per_level*level + sum([item.agility + item.attack_speed for item in items])
    increased_attack_speed /= 100 
    
    increased_attack_speed = max(0.80,increased_attack_speed)
    increased_attack_speed = min(4,increased_attack_speed) 
    
    damage = self.base_damage + eval('self.base_%s + self.%s_per_level * level' %(self.primary_attribute,self.primary_attribute))
    
    for item in items:
        damage += item.damage
        damage += eval('item.%s' %(self.primary_attribute))
        
    attacks_per_second = (1 + increased_attack_speed)/(self.base_attack_time)
    
    return attacks_per_second * damage
{% endhighlight %}

The first thing to do is to calculate the attack speed, which is called increased attack speed in DotA, and is taken to be a percentage.
IAS is increased by 1 for every point of agility you have, and increased by any amount of IAS given by items.
The formula used to convert attack speed to attacks per second is: 

\\[ \\frac{(1 + \text{IAS})}{\text{BAT}} \\]

The damage calculation is easier, its simply the raw daamge given by any items and base damage plus one point for every one point of stats in a heroes primary attribute.

Then it simply returns the DpS!

So lets see what combination of items void should take. By increaseing the number of Rapiers up to are maximum of 6, and filling empty slots with mjolnirs gives the following result:

![](/gamingtheorist/_assets/images/fvoid.pdf)

Which shows that the largest DpS is given at 4 Rapiers and 2 Mjolnirs!
The next stage is to use these classes to simulate a fight between two carries and predict the outcome, more to come on that! 
