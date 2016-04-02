# Hard Attention

#### Task
The images of handwritten digits, MNIST dataset, might be treated as a set of maps or two-dimensional environments which are traveled by an agent. The agent can see only 2 pixels in each direction and must learn a traveling policy to classify environments - digits. The policy trajectories are limited by the allowed number of walking steps.

#### Solution
We try to solve this task via Active Inference. Active Inference assumes that a policy is computed by maximizing the likelihood of desired observations. In this setting the rewards or state values, that are used in reinforcement learning and optimal control, are replaced by prior beliefs on expected observations or system states. And policy learning becomes equivalent to learning of expected observations  or state transitions. On policy, at each step a selected action maximizes likelihood of expected observation. The expected observations can be set by demonstration.

This task is similar to learning an attention policy as described in “Recurrent Models of Visual Attention” http://arxiv.org/abs/1406.6247 (RAM). So we would learn attention policy in the same way however the model would be different.

The differences:

1) No glimpse network, glimpse is a single patch (4x4), instead a collection of multiple resolution patches as in RAM

2) Actions, saccades, are discrete: move up, up-right, right, down-right, down, down-left, left from the current location. RAM actions are locations of patches.

3) Additional network is used to identify initial location from where glimpses start. The location network receives downsized image as input.

![alt tag](drawing.png)

Action and Initial Location networks are trained by a simple policy gradient algorithm, such as REINFORCE.

In baseline setup, the reward is class probability value at the last step of an episode.

In main setup, we walk the agent through an environment to set observation expectations from the demonstration. The demonstration is a sequence of observations (patches), which agent may learn or memorize.  The reward is likelihood of observation at each step of an episode, not just at last step as in baseline setup.

## Baseline setup results
20 glimpses of 4x4 size with action step size 2, 10 epochs
train – 97.48%, test – 95.56%

As you can see below, in many cases learned trajectories reproduce the form of digits.

This result shows optimality of using the same state for generating (actions) and recognition (perception) of trajectories, which is main postulate of active inference.

 |Images
---|---
original image|![alt tag](samples/actions_1.jpg)![alt tag](samples/actions_4.jpg)![alt tag](samples/actions_20.jpg)![alt tag](samples/actions_26.jpg)![alt tag](samples/actions_48.jpg)![alt tag](samples/actions_53.jpg)![alt tag](samples/actions_57.jpg)![alt tag](samples/actions_77.jpg)![alt tag](samples/actions_85.jpg)![alt tag](samples/actions_90.jpg)![alt tag](samples/actions_99.jpg)![alt tag](samples/actions_102.jpg)![alt tag](samples/actions_104.jpg)![alt tag](samples/actions_106.jpg)![alt tag](samples/actions_124.jpg)![alt tag](samples/actions_131.jpg)![alt tag](samples/actions_135.jpg)![alt tag](samples/actions_137.jpg)![alt tag](samples/actions_188.jpg)![alt tag](samples/actions_217.jpg)![alt tag](samples/actions_218.jpg)![alt tag](samples/actions_239.jpg)![alt tag](samples/actions_315.jpg)
trajectories (gray)|![alt tag](samples/actions_1-5.jpg)![alt tag](samples/actions_4-1.jpg)![alt tag](samples/actions_20-9.jpg)![alt tag](samples/actions_26-2.jpg)![alt tag](samples/actions_48-5.jpg)![alt tag](samples/actions_53-7.jpg)![alt tag](samples/actions_57-10.jpg)![alt tag](samples/actions_77-2.jpg)![alt tag](samples/actions_85-7.jpg)![alt tag](samples/actions_90-4.jpg)![alt tag](samples/actions_99-3.jpg)![alt tag](samples/actions_102-7.jpg)![alt tag](samples/actions_104-7.jpg)![alt tag](samples/actions_106-1.jpg)![alt tag](samples/actions_124-7.jpg)![alt tag](samples/actions_131-3.jpg)![alt tag](samples/actions_135-1.jpg)![alt tag](samples/actions_137-3.jpg)![alt tag](samples/actions_188-2.jpg)![alt tag](samples/actions_217-10.jpg)![alt tag](samples/actions_218-4.jpg)![alt tag](samples/actions_239-6.jpg)![alt tag](samples/actions_315-4.jpg)

### The model parameters
#### RNN
LSTM, one layer of 512 cells

#### Action Network
1 hidden layer with 512 neurons + soft max + stochastic multinomial module

#### Initial Location Network
one layer, 4 x downsized input, soft max + stochastic multinomial module that outputs location coordinates

#### Regularization
All networks use dropout 0.7. The dropout is essential for network stability and allows finding better action policies.

## Baseline setup results
To be published soon ...
