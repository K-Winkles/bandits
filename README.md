# Bandits!
by Kiana Alessandra V. Villaera

A manual implementation of Multi-Armed Bandits and its many variants over configurable game constraints.

This notebook serves as an easy way to experiment with different MAB algorithms and game constraints. Everything from reward distribution, number of dominant arms per state, stationarity, and others are configurable

Additionally, the Agent classes are written in such a way that it is intuitive. The only libraries used are your common Data Science staples like `numpy`, `random`, `pandas`, and `matplotlib`.

Agent classes are written with an obvious pattern. They all use the same methods. The only difference between them is what happens within those methods.

Truly, the notebook is a mental exercise more than anything.

Concepts can be found in this book: [Mastering Reinforcement Learning with Python: Build next-generation, self-learning models using reinforcement learning techniques and best practices](https://www.amazon.com/dp/B08M3ZF7Z8?ref=ppx_yo2ov_dt_b_fed_digi_asin_title_351)

## Running the notebook

1. As per usual, run `pip install -r requirements.txt`
2. Notebook should run from top to bottom without issue.
3. Configure the experiment settings by fiddling with the hyperparameters listed under **Game Constraints**

## Introduction

The following notebook is an experimental notebook that focuses on some of the fundamental concepts of Reinforcement Learning. Specifically, Multi-Armed Bandits and Contextual Bandits.

This notebook was created during a weekend wherein the author was desperately missing the feeling of coding and analysis typical of Data Science. She also felt nostalgic about her college thesis on [Temporal Difference Learning with the game 2048](https://ieeexplore.ieee.org/document/6932907). She is currently looking for the soft copy of her transcript...

Agents are manually defined. The only libraries used were `numpy`, `random`, `matplotlib`, and `pandas`. This is a mental exercise more than anything.

For Agent 6, it uses `VowpalWabbit` and is used as a benchmark to test the hand-coded Agents against industry standards.

There are 6 different agents:
1. `RandomAgent` - This guy just vibes. He does not reason. His performance represents the baseline.
2. `EpsilonGreedyBandit` - An implementation of the Epsilon-Greedy Bandit. He's a smart guy.
3. `UCB` - Builds on EGB by replacing random exploration with upper confidence bounds — no epsilon needed.
4. `ThompsonSampling` - Builds on UCB by replacing confidence bounds with Bayesian probability distributions. Instead of asking *"how uncertain am I?"*, it asks *"what's the probability this arm is the best?"*
5. `ContextualBandit` - An implementation of the Contextual Bandit. He's an even smarter guy. He has developed the power of Emotional Intelligence, because he accounts for context.
6. `VowpalWabbit` - This uses a library called [VowpalWabbit](https://vowpalwabbit.org). This is the industry standard I want to pit the above against.

The notebook also compares Agent performance across the above methods.

## Game Constraints

The game has several configurations. You may set the following hyperparameters:

1. `scramble_step` - If set to -1, the game is stationary. Otherwise, all state rewards will be regenerated at every time step as indicated. 
2. `max_iter` - Maximum iterations
3. `num_states` - Total number of states
4. `reward_dist_width` - Total number of reward values per state
5. `num_games_to_play` - Number of games to generate. Note that new states are generated for every game.
6. `reward_dist_mode` - Dictates what kind of reward distribution each state has. This will certainly affect behavior.
7. `epsilon` - Rate of exploration.
8. `alpha` - Discount rate. How much weight is put on more recent moves. Helps keep exploration dynamic especially in non-stationary games.
9. `uncertainty` - Uncertainty $c$ as seen in the formula for UCB indicated in the proceeding cells.
10. `gamma` - Discount rate for ThompsonSampling. If the game is staionary, set `gamma` = 1. If you do not do this, the Agent will fight against recent history which may impede convergence.

Note that some hyperparameters are lists, like `Epsilon`, for example. This is because we search for the best configuration. Essentially, we do a grid search over these parameters. We are doing hyperparameter tuning.


## States
A state only has 3 features:
1. `id` - Some integer that represents a unique state
1. `possible_paths` - List of all the next possible states you can get to from this current state S
2. `reward_action_mapping` - Mapping of the reward signal you will get if you transition to any of the possible next states

When we run `generate_states`, we randomly generate possible paths for each state wherein the minimum is 50 and the maximum is the total number of states.

Note that if `scramble_step` is greater than 1, this means that all states will generate new reward values at the indicated time step. This will give the game an element of non-stationarity. Otherwise, if `scramble_step` is set to -1, then the reward values for all states will be static throughout the game--making it stationary.

## Agent Comparison

| Agent | Exploration Strategy | Uses Context? | Key Hyperparameter | Best For |
|---|---|---|---|---|
| `RandomAgent` | Pure random | No | — | Baseline only |
| `EpsilonGreedyBandit` | Random with prob $\varepsilon$ | No | $\varepsilon$, $\alpha$ | Simple, interpretable environments |
| `UCB` | Confidence bound | No | $c$ | Stationary/non-stationary; no randomness in selection |
| `ThompsonSampling` | Bayesian sampling | No | — | Binary reward settings; naturally adaptive |
| `ContextualBandit` | Confidence bound on context | Yes | $\alpha$ | Personalized decisions with rich feature context |
| `VowpalWabbit` | Industry-optimized | Yes | — | Production benchmark |


## Production Strategies
There are 3 strategies for production:
1. One global bandit - Takes into account all users actions. Recommendations end up being the same. Simple, cheap, but highly impersonal. Ideal for use cases like gaming.
2. One bandit per user - Highly personalized but expensive. Ideal for use cases like music/movie/social media feed recommendations.
3. Segmented bandits - Requires customer segmentation initially, then assign a bandit per segment. A balance between the above 2 approaches. Ideal for the same use cases as #2.

However, for contextual bandits, if a rich user embedding as the context vector exists — it can achieve highly personalized results even if the production strategy is not #2.

## Good Reads

1. [Reinforcement Learning for Machine Learning Model Deployment: Evaluating Multi-Armed Bandits in MLOps Environments](https://arxiv.org/abs/2503.22595)
2. [Offline Policy Evaluation: Run fewer, better A/B tests](https://edoconti.medium.com/offline-policy-evaluation-run-fewer-better-a-b-tests-60ce8f93fa15)
3. [Sample code for CB](https://github.com/uguryi/contextual_bandit/blob/main/ab_vs_mab_vs_cb.ipynb)
4. [Mastering Reinforcement Learning with Python: Build next-generation, self-learning models using reinforcement learning techniques and best practices](https://www.amazon.com/dp/B08M3ZF7Z8?ref=ppx_yo2ov_dt_b_fed_digi_asin_title_351)
