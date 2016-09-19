原文：[AI challenge in 78 lines of Python](http://kootenpv.github.io/2016-09-07-ai-challenge-in-78-lines)

---


## The challenge (Tron)

Tron is a multiplayer game in which 2-4 players play a snake-like game. Every step one makes, the board gets filled by one block. Whenever a player has nowhere to go, they lose. Last man standing.  See a video below of 4 bots in action on Codingame:

<iframe width="560" height="315" src="https://www.youtube.com/embed/PNRNwxia0Z4" frameborder="0" allowfullscreen=""></iframe>

I will explain the intuition and the implemented algorithms for a bot reaching 133/3131 as of 9 September 2016, using only [78 lines of PEP8 style guide code](https://gist.github.com/kootenpv/3d20fbc2e8cf37eaa045f8090a0216a7). In comparison, [there are bots](https://github.com/search?utf8=%E2%9C%93&amp;q=codingame+tron) with thousands of lines of code.

## Algorithms

We will have to consider how good each of the possible moves are from any given position. After having played around with the game, a very simple but efficient idea came to mind. What if we consider being the closest to as many tiles as possible (in comparison to our enemies) to be the most important?

That is, for each move (UP/DOWN/RIGHT/LEFT), we can consider how many tiles we are the closest in comparison to the enemy. Ask the question for each tile: who would be here first if racing with the enemies? This difference might be small when comparing UP/DOWN/LEFT/RIGHT in a relatively unimportant situation, but you’ll see that when there is a dead end, the score drops for the move that steps into this dead end. You could do this without taking into account the enemies, but it gets stronger when you implement enemies taking turns too (like in a real game!).

This is done by simulation: “virtually” expand each round in _all_ directions – for each bot in turn. Whenever there is an empty spot, they obtain it, and will expand from this spot in the next virtual round. It will stop when there have been no untouched tiles obtained by any of the players (indicating either the board to be filled or those parts of the map unreachable).

Here is a visual aid:

```
# o: visited by player 0 in previous turns
# x: visited by player 1 in previous turns
# 0: visited by player 0 this turn
# 1: visited by player 1 this turn
# a: turn for player 0
# b: turn for player 1

# Start:
0)
x . . . . .
. . . . . .
. . . o . .
. . . . . .

# Let's compare 2 examples:
1a)  UP            1a) DOWN
x . . . . .        x . . . . .
. . . 0 . .   vs   . . . . . .
. . . o . .        . . . o . .
. . . . . .        . . . 0 . .

# Example 0 went UP:
1b)          2a)          2b)          3a)          3b)          4a)           end
x 1 . . . .  x x . 0 . .  x x 1 o . .  x x x o 0 .  x x x o o .  x x x o o 0   x x x o o o
1 . . o . .  x . 0 o 0 .  x 1 o o o .  x x o o o 0  x x o o o o  x x o o o o   x x o o o o
. . . o . .  . . . o . .  1 . . o . .  x . 0 o 0 .  x 1 o o o .  x x o o o 0   x x o o o o
. . . . . .  . . . . . .  . . . . . .  . . . . . .  1 . . . . .  x . 0 0 0 .   x x o o o o

# Example 0 went DOWN:
1b)          2a)          2b)          3a)          3b)          4a)           end
x 1 . . . .  x x . . . .  x x 1 . . .  x x x . . .  x x x 1 . .  x x x x . .   x x x x x x
1 . . . . .  x . . . . .  x 1 . . . .  x x . . . .  x x 1 . . .  x x x . 0 .   x x x x o o
. . . o . .  . . . o . .  1 . . o . .  x . 0 o 0 .  x 1 o o o .  x x o o o 0   x x o o o o
. . . o . .  . . 0 o 0 .  . . o o o .  . 0 o o o 0  1 o o o o o  x o o o o o   x o o o o o
```

We can observe here that going UP is more beneficial to player 0: he will be closest to 15 tiles, compared to 11 tiles if he would have chosen to go DOWN.

This automatically makes the bot rather smart: it really seems like it is pursuing the enemy to try and lock them up.

In actuality, calculating the evaluation score for which move is best is using variations on this closeness:

*   number of tiles we are closest to (higher=better)
*   number of tiles enemies are closest to (lower=better)
*   summed distance for reaching each tile for all enemies (higher=better)

```
# simple weighting, importance: num_my_tiles > num_enemy_tiles > enemies_dist
return sum([num_my_tiles * 10000000, num_enemy_tiles * -100000, enemies_dist])
```

It’s a fun excercise to see what happens when you change these numbers, and perhaps even add your scoring metrics.

## Scripts

I have provided 3 scripts: one which contains only the [neccessary 78 lines](https://gist.github.com/kootenpv/3d20fbc2e8cf37eaa045f8090a0216a7), one which contains [logging on the codingame website](https://gist.github.com/kootenpv/af270c0a9f4b5c84dadbe6494b77c1c0) which provides more insight in what is going on, and the last one [contains comments](https://gist.github.com/kootenpv/32d1a0d97e391392dec10a83070336f8) for most of the lines to better understand how the current solution has been implemented.

## Improving the current solution

There is still a lot that could be improved:

*   Assumes bots try to optimize space-gain; this is suboptimal (conservative) with multiple players and limited options
*   Consider the disappearance of enemies upon their deaths
*   Consider keeping an escape route (related to enemy death)
*   Implement in faster language to be able to consider even more turns ahead
*   Improve evaluation scoring
*   Consider possible enemy moves ahead, and choose something that is good for us:

        *   Implement minimax
    *   Alpha-beta pruning (don’t keep considering moves that we know lead to bad results)
*   Use the time budget (return best move when time is almost up, ~98ms)

## Main message

The actual main message here is that intuition can do very well: while many people have submitted overcomplicated code, only considering a single move ahead based on closeness does really well already.

