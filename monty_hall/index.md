# Monty Hall Problem

 


<br>
On a fine friday evening, I got a text from my friend asking me this puzzle question.

> Let's say you are in a game show and there are 3 doors. Behind 2 doors there are goats and behind the remaining one door there is a car. You do not know what's behind which door and are asked to choose one door. Next, the host will reveal the door behind which there is a goat. Then the host asks you whether you want to switch your choice with the other closed door or not. Question is, Should you switch or not?

At a first glance one would think that, does it even matter which door should you choose? There is one goat and one car behind the 2 doors and you have 50% chance of getting the car. But the real answer is that if you decide to switch the door your chance of getting the car shot upto **66.67%** here 🙀. 

And if this feels counter-intutive or confusing to you, don't worry you are not alone! 

This problem is famously known as **Monty Hall Problem** named after the host appeared in the American TV show game. 

In this post, I will try to explain the Monty Hall problem from the perspective of probability theory and we will also do the simulation using Python.

## 1. Introduction

Along with the question stated earlier, there are few assumptions that need to hold true for this puzzle question.

* The host can only open the door that was not chosen by the player
* The host knows what's behind each door and he must open the door with the goat behind it for revealation
* The host has to offer the choice to player for switching the door after revealation

If any of the above assumptions do not hold true, then the puzzle's answer will change drastically.
<hr>
Now, coming back to the question of why it's not 50% of finding the car behind one of the 2 doors. 

If host had opened one of the doors randomly then the event of choosing the door and host opening the door would have been independent and probability would have been 50%. But host knows the location of car and he must always open the door with goat behind it. That's why both events are not independent.

In other words,

If the player chose the door with car then host is free to reveal either of the door as both doors have goats behind it. But if the player chose the door with goat then host must reveal the remaining door with the goat behind it. 

Now that we know its not 50% we will try to calculate what's the actual probability is.

## 2. Probability Theory

In this post, we will use Bayes' theorem to calculate what is the probability of winning the car if you switch the door 🤓.

Let's say $A$ is the event that car is behind the door chosen by the player. This event serves as our hypothesis in the bayes' theorem. I know, this is not what we want to calculate but please be patient with me here 🙂.

Let's say $B$ is the event of host revealing the door with goat behind. This event serves as our evidence in the bayes' theorem.

Next, let's write the bayes' theorem.

$$
P(A | B) = \frac{ P(B|A)P(A) }{ P(B) }
$$

Let's understand and calculate each term.

* The RHS term $P(A | B)$ defines the probability of winning the car by not switching the door given that host reveals the door with goat behind it.
* On LHS side, term $P( B | A)$ is the probability of host revealing the door with goat given the car is behind the door chosen by the player. As host must always reveal the door with goat, the probability is $1$.
* Next, term $P(A)$ is the probability of winning the car by not switching the door. Here we are not conditioned on any other event. Hence, the probability of winning the car is $\frac{1}{3}$.

To calculate $P(B)$ we have to further simplify it by conditioning it on event $A$.

$$
P(B) = P(B | A)P(A) + P( B | A')P(A')
$$

* The term $P(A')$ is probability of car not being behind the door chosen by the player. Which can also be calculated as $1 - P(A) = \frac{2}{3}$
* The term $P( B | A')$ is the probability of host revealing the goat behind the door given the car is not behind the door chosen by the player. Which is also $1$ as host must always reveal the goat.

Putting these together,

$$
P(B) = 1 \times \frac{1}{3} + 1 \times \frac{2}{3} = 1
$$

Putting everything together,

$$
P(A | B) = \frac{1 \times \frac{1}{3}}{1} = \frac{1}{3}
$$

We have derived the probability of winning the car with the player choosen door (not switching) given the host has revealed the door with goat behind it.

However our original question was what is the probability of winning the car if we switch the door given the host has revealed the door with goat behind it. The probability of this event can be represented as $P(A' | B)$ which is $1 - P(A | B) = 1 - \frac{1}{3} = \frac{2}{3}$.


Hence, we have derived that if we switch the door after the host reveals the door with goat we can increase our chances of winning the car to approximately 66.67%

## 3. Still Confused?

After the derivation if you still feel the solution is counter-intutive, it's alright. Even many mathematicians and physicist refused to accept this even with the proof. In the field of paradox, they call this kind of solutions veridical type paradox.

I will try to explain the solution in very simple words now,

> Let's say you are blindly trusting me and going with the strategy of always switching. Now if you have initially chosen the door with car (which has the probability of $\frac{1}{3}$) you will fail when you do the switching. But if you have chosen the door with goat at start (which has the probability of $\frac{2}{3}$) you will win the car when you do switching because the other door with goat was already opened. Hence with switching you have $\frac{1}{3}$ chances of losing or in other words $\frac{2}{3}$ of winning the car.

Still not convinced? Let's do the simulation in Python next!

## 4. Simulation

We will try to simulate random behaviour of this game by using Python's `Random` module. With the comments, below code should be self-explanotary.

```python
import random

# Only 3 configuration of doors possible
door_possibilties = [
    ['C', 'G', 'G'],
    ['G', 'C', 'G'],
    ['G', 'G', 'C']
]
# we will call door no 0,1,2 instead of 1,2,3🙂
door_nos = [0, 1, 2]

def play_game():
    # first we will initialize door config for the current game by randomly selecting
    # one possibilties from the defined config
    this_game_doors = random.choice(door_possibilties)
    # Next, player will randomly select a door
    player_selected_door = random.choice(door_nos)
    
    # host now must select the door with goat
    
    # Let's first get remaining 2 door nos
    remaining_door_nos = door_nos.copy()
    remaining_door_nos.remove(player_selected_door)  # removing player selected door
    
    # If the door chosen by player has car
    if this_game_doors[player_selected_door] == 'C':
        # then host can choose either of the remaining door as both have goats behind them
        revealed_door_no = random.choice(remaining_door_nos)    # host will randomly chose either of door
    # If the door chosen by player door has goat
    else:
        # Host have to choose the remainig door with goat
        # let's call remaining doors a & b for now
        door_a, door_b = remaining_door_nos[0], remaining_door_nos[1]
        # We will find the door with goat from a & b
        revealed_door_no = door_a if this_game_doors[door_a] == 'G' else door_b
        
    # To calculate the probability of winning by switching, we will always switch now
    
    # Out of remaining doors, we will remove the door no revealed by the host
    remaining_door_nos.remove(revealed_door_no)
    # List will have single door which will be chosen by player for switching
    final_door_no = remaining_door_nos[0]
    
    # We return 1 for winning the car and 0 for losing, to keep track of success cases
    if this_game_doors[final_door_no] == 'C':
        return 1
    else:
        return 0

tries = 100000
success = 0
for _ in range(tries):
    result = play_game()
    success += result

prob = success/tries
print(f"Probability: {prob}")
```

Copy the code, save in file and try to run it with python. You can also tweak the value of variable `tries` and see how it changes the probability.


## 5. End Remarks
This problem being very famous, I doubt you would get the chance to apply it in real life as no game would use the exact mechanism (it would be a disadvantage to host). 

The reason I wrote this blog is because I loved its paradoxical nature and how by using probability theory concepts you can always explain real-life phenomena.

If you loved this problem and blog, there is also an extension to this problem. 

(If you guessed it correctly) The problem is extended to $N$ doors with host revealing $k$ doors where $0 \leq k \leq N-2$ as we have to leave atleast 2 doors closed. I would encourage you to derive the probability in same manner.


