---
title: "The Markov Chains of Elina Harper"
date: 2023-12-26T15:36:29+01:00
draft: true
slug: the-markov-chains-of-elina-harper
categories:
  - maths
tags:
  - probability
---

*Spoiler Warning*: This blog post contains mild mechanical spoilers for the
second scenario of the *Innsmouth Conspiracy* expansion of *Arkham Horror LCG*.

In *The Vanishing of Elina Harper*, the players need to find out who abducted
agent Elina Harper and at what location she's taken hostage. In total, there are
6 suspects and 6 locations. During setup, the players need to randomly (and
secretly) choose one suspect and one location and shuffle the remaining
locations and suspects together as the *Leads deck*. During actual gameplay, the
players may then trade a certain amount of clues to look at the top (up to 3)
cards of this deck, choose to draw one of the revealed cards and shuffle the
remaining cards together with a replacement card from the encounter deck.

This means that at all times there are 10 cards in the Leads deck and the
players know a certain amount of cards in that deck. The goal of this scenario
is to see as many cards of the Leads deck as possible. Because the players have
13 rounds to do so but may only look at the top cards once per round, the
question for the optimal strategy arises: Should they look at the top cards 
each round or should they wait until they have acquired enough clues to look at
the maximum amount of (3) cards?

Related to this is the question what the expected number of draws is when
looking at the top 1, 2 or 3 cards respectively. Interestingly, while the
expected number of draws when looking at the top card may be computed in a
brute-force manner, we desperately need to organize the computation for the
other two cases. This is where Markov chains will come into play.

## TLDR: The results

Because the following is math-heavy, let's first state the results.

- If you decide to only ever look at the top card of the Leads deck, you will
  need over **29 rounds** on average until you have seen every card.
- If you decide to look at the top 2 cards of the Leads deck, you will need over
  **14 rounds** on average until you have seen every card.
- Finally, if you decide to look at the top 3 cards of the Leads deck, you will
  need over **9 rounds** on average until you have seen every card.

Of course, this assumes that you are able to gather the required amount of clues
each round. Here's a table showing the probabilities of having seen each card
after each draw for the three strategies.

| Draw | Only drawing 1 | Only drawing 2 | Only drawing 3 |
|-----:|---------------:|---------------:|---------------:|
|    4 |              0 |              0 |         0.0149 |
|    5 |              0 |         0.0006 |          0.088 |
|    6 |              0 |         0.0095 |         0.2188 |
|    7 |              0 |         0.0388 |         0.3726 |
|    8 |              0 |         0.0939 |         0.5186 |
|    9 |              0 |         0.1713 |         0.6419 |
|   10 |         0.0004 |         0.2624 |          0.739 |
|   11 |          0.002 |         0.3583 |         0.8124 |
|   12 |         0.0062 |         0.4519 |         0.8663 |
|   13 |         0.0143 |         0.5387 |         0.9053 |

A few comments:

- To have a decent chance at success, your group should be able to gather at
  least 2 clues per player per round.
- If your group is able to gather *exactly* 2 clues per player each round, it
  might be better to spend those two clues each round (you will only have 8
  chances of looking at cards otherwise, totalling a probability of success at
  51.86% compared to 53.87% of spending 2 clues per player each round for 13
  rounds).
- This doesn't take into account that you might want to fish for victory points
  on locations in the Leads deck.

## Brute-forcing the case of only looking at the top card

Let $d$ be the number of cards we look at from the top of the Leads deck, $1
\leq d \leq 3$.
Let $k$ be the number of cards we have already seen. The [binomial
coefficient](https://en.wikipedia.org/wiki/Binomial_coefficient) $\binom{10}{d}$
is the number of possible outcomes to draw $d$ cards from a deck of 10 cards
(disregarding order of the drawn cards). Likewise, there are $\binom{k}{d}$ ways
to draw $d$ cards from the $k$ cards we have already seen. Consequently, the
probability to only draw cards we have already seen when having seen $k$ cards
and drawing $d$ cards at once is
\\[
q_{d,k} = \frac{\binom{k}{d}}{\binom{10}{d}} = \frac{k!(10-d)!}{(k-d)!10!}.
\\]
In the case $d=1$ this simplifies to $q_{1,k} = k/10$ so that the probability to
see a new card when already having seen $k$ cards is $p_{1,k} = 1-q_{1,k} = (10-k)/10$.

Let's compute the expected number of rounds we have to play when only drawing
one card from the Leads deck each round. Let $X_{k}$ be the random variable of
rounds we have to play until we see a new card when having seen $k$ cards from
the Leads deck. Then we may compute the expected value of $X_k$ as follows.
$$
\mathrm{E}[X_k] = 1 \cdot P(X_k = 1) + 2 \cdot P(X_k = 2) + 3 \cdot P(X_k = 3) +
\dotsb = \sum_{i=1}^\infty i \cdot P(X_k = i).
$$

The probability that we have to play exactly $i \geq 1$ rounds is 
$$
P(X_k = i) = q_{1,k}^{i-1} p_{1,k} = \frac{k^{i-1} (10 - k)}{10^i}
$$
because we don't have to make any progress in the first $i-1$ rounds (which has
probability $q_{1,k}^{i-1}$) and then finally see a new card in the $i$th round
(which has probability $p_{1,k}$). Plugging this into our computation for the
expected value, we end up with

$$
E[X_k] = \sum_{i=1}^\infty i \cdot \frac{k^{i-1} (10 - k)}{10^i} =
\frac{10-k}{10} \sum_{i=1}^\infty i \cdot \left(\frac{k}{10}\right)^{i-1}.
$$

Why have I rewritten the result in this form? Because we see now that we've
ended up with the first derivative of the [geometric
series](https://en.wikipedia.org/wiki/Geometric_series) $\sum_{i=0}^\infty
(k/10)^i$. Because the geometric series has the closed form
$$
\sum_{i=0}^\infty r^i = \frac{1}{1-r}
$$
which has the derivative $1/(1-r)^2$, we deduce
$$
\sum_{i=1}^\infty i \cdot \left(\frac{k}{10}\right)^{i-1} = \frac{1}{(1-k/10)^2}
= \frac{10^2}{(10 - k)^2}.
$$

Finally, we arrive at
$$
E[X_k] = \frac{10}{10 - k}.
$$

Note that this formula also holds for $k=0$ and yields $E[X_0] = 1$ (if we
haven't seen any card yet, we are guaranteed to see a new card in the first
round). Summing these numbers up yields the desired average number of rounds
when only drawing one card:

$$
\sum_{k=0}^9 E[X_k] = 10 \sum_{k=0}^9 \frac{1}{10-k} = 10 \cdot \sum_{i=1}^{10}
\frac{1}{i} = 10 \cdot H_{10}.
$$

where $H_{10}$ denotes the 10th [harmonic number](https://en.wikipedia.org/wiki/Harmonic_number).

Likewise, we can compute the probabilities that we only take, say, 10 rounds to
see all cards when only drawing one card from the Leads deck each round. In this
case, we need to have $X_k = 1$ for all $0 \leq k \leq 9$ and the probability
for that is
$$
\prod_{k=0}^9 p_{1,k} = \frac{10!}{10^{10}} = \frac{567}{1562500} = 0.00036288.
$$


## Modelling drawing from the Leads deck with Markov chains

Doing similar computations for the case where we draw more than 1 card each
round is possible but becomes unwieldy quickly. To orchestrate computations, we
use [Markov chains](https://en.wikipedia.org/wiki/Markov_chain) in the form of
transition matrices.

First, let us think about the case where we draw 2 cards each round:
- In the first round, when we haven't seen any cards yet, we are guaranteed to
  see two new cards.
- After the first round, we have three possibilities:
  - We could only draw the two cards we have already seen,
  - We could draw one card we have already seen and one new card,
  - We could draw two cards we haven't seen yet.
- At some point, we have either seen all cards or we end up with 9 cards seen --
  in the latter case, we only have two possibilities (see the last card or not).

A *state diagram* helps to visualize these possibilities. In our case, the state
is the number of cards we have already seen: The initial state is $0$ (we
haven't seen any cards) and the terminal (or *absorbing*) state is $10$ (we have seen everything
in the Leads deck). We draw an edge between states if it is possible to advance
to that state. Here's how it could look like:

{{<mermaid>}}
flowchart LR
    0 --> 2
    2 --> 2 & 3 & 4
    3 --> 3 & 4 & 5
    4 --> 4 & 5 & 6
    5 --> 5 & 6 & 7
    6 --> 6 & 7 & 8
    7 --> 7 & 8 & 9
    8 --> 8 & 9 & 10
    9 --> 9 & 10
{{</mermaid>}}

From a state diagram, we may derive the *transition matrix* $P$ that
has the probability of transitioning from state $i$ to state $j$ as its
$(i,j)$-entry. This transition matrix is our tool of orchestrating
our computations. We need to use two of its properties.

It can be proven (by using induction and the definition of matrix
multiplication) that $P^k$ contains the probabilities of transitioning
from state $i$ to $j$ in exactly $k$ steps as its $(i,j)$-entry. This
is the first property we need and we'll use it to compute the probabilities
of transitioning to the absorbing state in any given number of rounds.

To explain the second property, note that our transition matrix has the
[canonical form](https://en.wikipedia.org/wiki/Absorbing_Markov_chain)

\\[
P =
\begin{pmatrix}
Q & R \\\\
0 & 1
\end{pmatrix},
\\]

where $Q$ is the *transition matrix of transient states*, i.e. of all
non-absorbing states and $R$ is the vector of probabilities of transitioning
to the absorbing state.

From this, it can be shown (as a generalization of the geometric series to
matrices) that
\\[
N := \sum_{i=0}^\\infty Q^i = (\mathrm{id} - Q)^{-1}.
\\]
$N$ is the *fundamental matrix* and contains the expected number of steps of
transitioning from state $i$ to state $j$ before being absorbed as its
$(i,j)$-entry. This means that $N \cdot 1$ (where we abuse notation and denote
the vector that only contains ones by $1$) contains the expected number of steps
before being absorbed when starting at state $i$ as its entry at position $i$.
This is the property we use to compute the expected number of rounds.

