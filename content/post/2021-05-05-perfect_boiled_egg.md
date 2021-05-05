+++


title = "Making the perfect boiled egg"
description = "Making the perfect boiled egg if you had the funds"
date = "2021-05-05"
tags = ["Miscellaneous", "tangents"]
slug = "perfect-boiled-egg"

summary = "Your guide to making that perfect boiled egg"


+++

## Step 1: Define a perfect boiled egg

The perfect boiled egg should have the following features: 

* A perfectly centered and spherical yolk
* No cracks on the shell after boiling i.e oval through the major axis. 
* Not too hard inside, but no liquid should be found. 

## Step 2: Drawbacks of the current approach

Like almost every other thing we do, the current procedure that we use to boil our eggs is far from perfect. It still uses primitive tools like pots. 

When boiling, the egg sometimes tumbles in a non uniform manner, and the yolk tends to settle down on a side instead of being on the center. 

## Step 3: The solution

We'll need the the following things: 

* A rocket to get into orbit
* A perfectly insulated chamber with enough cushioning so that the egg does not crack while lifting off 
* A heat chamber, whose shape is perfectly similar to that of the eggs (preferably nuclear powered)
* A little bit of liquid nitrogen for cooling down the heating chamber.
* 2 eggs (one backup)

The procedure could be as follows:

1. Get into an orbit with the rocket. 

    The cost of getting 1 gram of stuff into space is $2.72[1], and the average weight of an egg is about 60 grams[2]. Since we're carrying 2 eggs, we can do the math now:

    ```python
    
    def calculate_cost_of_putting_eggs_into_space(num_eggs):
        cost = 60*2.72*num_eggs
        return cost

    ```

    So for 2 eggs, it comes to about: $326.4, not too bad I guess.

2. Rotate the egg on it's major axis, this stabilizes the egg's orientation and brings the yolk to the center. 

3. In order to ensure that the egg heats up uniformly through the surface, the heating surface should be similar in shape to that of the egg. Similar but larger.

4. As we start heating the egg, the proteins gain energy and literally shake apart the bonds between the parts of the amino-acid strings, causing the proteins to unfold. As the temperature increases, the proteins gain enough energy to form new, stronger bonds (covalent) with other protein molecules. Thus making the egg hard.

5. We keep the egg at around 100 degree celsius for about 8 minutes[3] and then turn off the heat and bring it down to room temperature using small doses of liquid nitrogen to cool off the chamber. 

6. To peel off the shell, we'll need a robotic arm with a micro grinding machine. The grinder would make a cut on the circular plane through the minor axis of the egg. After that, two simple suction cups could be used to pull off the peels from the top and bottom (major axis). 

Congratulations, you now have your perfectly boiled egg. 



[1] - "For a SpaceX Falcon 9, the rocket used to access the ISS, the cost is just $2,720 per kilogram." https://theconversation.com/how-spacex-lowered-costs-and-reduced-barriers-to-space-112586

[2] - "The weight of an egg varies between 50 and 70 g depending mainly on the age of the hen and, to a lesser extent, on its genotype." https://www.sciencedirect.com/topics/agricultural-and-biological-sciences/egg-weight

[3] - "8 minutes – The white is fully set, and the yolk is set, but tender." https://norecipes.com/perfect-boiled-eggs/
