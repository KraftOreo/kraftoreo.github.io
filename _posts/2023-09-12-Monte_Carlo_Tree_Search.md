---
title:  "Monte Carlo Tree Search"
mathjax: true
layout: post
categories: media
header-includes:
    - \usepackage[ruled, vlined, linesnumbered]{algorithm2e}
---

# Motivation of Monte Carlo Tree Search (MCTS)
* Play games optimally
* To be able to play in a reasonalbe time

# Type of games

| | Deterministic | Nondeterministic | 
| ------ | ------ | ------ | 
| Fully Observable | Chess, Checkers, Go | Backgammon, Monopoly | 
| ------ | ------ | ------ | 
| Partially Observable | BattleShip | Card Games | 

<div class=remarks>
    <b>Remarks</b>
    <p>
        <li>
        Here we will focus on The Deterministic and Fully observable games.
        </li>
        <li>
            We have perfect information of the games.
        </li>
        <li>
            Can construct a tree that contains all possible outcomes because everything is fully determined.
        </li>
        <li>
            The main approach is to use Minimax: The computer is trying to minimize the possible maximum lost. Namely, depending on the moves of human players which is used to maximize the oppotunity for them to win, the computer is trying to minimize the lost from human players.
        </li>
    </p>
</div>
<br>
<div class=remarks>
    <b>Remarks</b>
    <p>
        Since the tree is growing exponentiually, we don't actually need to explore every single possible node. We can selectively build a tree aysmmetrically while the main effort is used to explore the state that has more possibility to win.
    </p>
</div>

# MCTS Outline
* Descend through the tree
* Create new node
* Simulate
* Update the tree
* When we are out of time, return the "best" child node

# For a tree node $k$
* $$n_k$$  is the number of games played involving $$k$$ 
* $$w_k$$ is the number of games won involving $$k$$

# Detailed Outline
## Descending
Solution: *Upper Confidence Bound* 

$$
\begin{align*}
    UCB1(k,p) &= E[win|k,p] + C \sqrt{\frac{2ln(n_{parent(k)})}{n_k}}\\
    &\approx \frac{w_{k,p}}{n_k} + C \sqrt{\frac{2ln(n_{parent(k)})}{n_k}}
\end{align*}
$$

where the $$ E[win|k,p] $$ is the expand part and $$  C \sqrt{\frac{2ln(n_{parent(k)})}{n_k}} $$ is the explore part.
At each step, maximize $$ UCB1(k,p) $$

## Expanding
Make a new node and set $$n_k=0, w_k=0$$

## Simulating
Randomly play the game, randomly chooose state nodes from all descending children. If the game is win, $$\Delta=+1$$. If we lose or tie, $$\Delta =0$$. But in most literature the results of descending children in simulation is not stored.

## Updating the Tree
Propagate recursively up the parents.

Give simulation result $\Delta$,
for each $$k$$:
    $$n_{k_new} = n_{k_old} + 1 $$
    $$w_{k,1-new} = w_{k,1-old} + \Delta $$

## Terminate
Return the best-ranked first ancestor!

# Pros and Cons
## Pros
* Grows tree asymmetrically, balancing expansion and exploration
* Depends only on the rules
* Easy to adapt to new games
* Heuristics not requried, but can also be integrated
* Can finish on demand, CPU time is proportional to answer quality
* Complete: guaranteed to find a solution give time
* Trivially parallelizable

## Cons
* Can't handle extreme tree depth
* Requires ease of simulation, massive computation resources
* Relies on random play being "weakly correlated"
* Many variants, need expertise to tune
* Theoretical properties not yet understood
