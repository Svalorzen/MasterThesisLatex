Multi-camera systems are now everywhere. They are used for real time tracking,
surveillance and many other purposes.  While the cost for the technologies
themselves has been constantly decreasing over time, operational costs for such
systems have actually increased due to their sheer size.

In order to do their job, these systems have to be comprehensive. But adding
more and more cameras is not only costly in the short term: the more cameras
are deployed, the more electricity is consumed.  The data they produce has to
be wired somewhere, and then analyzed and stored.

---

Instead we would like to use such sensors more efficiently, only gathering data
when we know it will contain useful information, and infer missing data where
it cannot be gathered, be it for energy constraint, missing physical coverage,
or bandwidth or computation limitations.

Doing this is not easy. The system controlling the sensors must have an
internal representation of the world in order to be able to decide that
activating a sensor will provide useful information. At the same time, this
requires reasoning with uncertainty, since neither the environment can be
perfectly modeled, nor sensors will provide exact measurements every time.

---

Such problem is also referred to as active perception. An active perception
task is defined in terms of an agent, in our case the camera system, that must
take actions in order to reduce some form of uncertainty, in our case the state
of the world.  And it must do so while subject to resource constraint, which
would be energy, bandwidth, coverage, computation.

---

In our work, we approached the active perception task using the Partially
Observable Markov Decision Process framework. This is a theoretical framework
that allows the modeling of non-deterministic partially observable
environments.  Within this framework, a single agent can act upon the
environment in order to influence its evolution through time. At each timestep,
the agent can select and perform an action. In doing so, it will receive
observations which will enable it to obtain informations on the environments
state.

In this work, the agent is represented by the camera system. While it won't be
able to directly influence the environment, its actions - which are cameras
activations - will allow it to receive information about the state of the
world. The idea is that the agent will try to predict what the environment
will do in the future, and try to predict the most rewarding actions accordingly.

We define a POMDP as a tuple <SATRWOh>. The tuple components are:

- The horizon, which is the number of timesteps that the agent will try to look ahead
  when planning its actions.
- State space, which is the set of all possible configurations of the environment. In our
  case this would be every possible configuration of targets to track in the environment.
- Action space, which are the actions the agent can take at each timestep. In our case,
  these would be the cameras that the agent has access to.
- The transition function. This models how we expect the environment to evolve over
  time. For each pair of states, the transition function gives the probability that
  the environment will transition from one to the other.
- The observation space, which is the set of all different observations that the agent
  can obtain when looking at the environment.
- The observation function, that maps the state of the environment with the action
  taken by the agent, and returns the probability of obtaining a given observation.

camera->room?

how would the agent actually keep track of the state of the environment though?
Let's do a simple example: a single room being observed by a single camera. Suppose
the camera can only report seeing someone in the room, or not. And suppose that this
camera has a 20% chance of reporting wrong information either way. If one was to
interrogate the camera, and it said the room was occupied, how would one know the
true state of the room? Intuitively, it's probably occupied, but we wouldn't be able
to discount the empty hypothesis either. How about after two camera interrogations?

Turns out that in the POMDP framework doing this is relatively easy:

-- belief update formula

::: explanation :::

Using this we can always keep track of what possible states the environment could be
in, and thus act accordingly. But what should the agent try to do? This is where the
reward function comes in:

- The reward function.

The reward function in a normal POMDP specifies the reward that the agent obtains when
the environment transitions in a particular way; however in our case this is irrelevant,
as our agent cannot directly influence the environment. Instead our reward function is
based on the amount of information that the agent is able to extract. This is also
called a belief based reward function since it specifies some reward from an input belief.
POMDPs that use a belief based reward function are called rpomdps.

-----

How to use POMDPs and to evaluate stuff:

-----

It turns out that rpomdps are extremely difficult to plan for. The main problem is that planning
in advance, or offline, requires defining a specific strategy, or policy, for every possible belief the
agent might encounter. But since beliefs are infinite, this is pretty much impossible!

One way this can be done is by approximating the reward function. This can be done in multiple
ways, but the effect is the same: to give a bound on the number of optimal
policies required in order to cover the whole belief space. This is done by obtaining a PWLC
value function.

Another way is to avoid trying to plan offline and instead plan online, only for the belief
that the agent is currently facing. This is what we did.

----

The algorithm POMCP is the algorithm we used as a basis for our work. It implements a fast MCTS based
approach to POMDPs, and is able to deal with beliefs.

...

---

In our work we modified POMCP into a variant, rPOMCP, which is able to additionally cope with
some specific belief based reward functions; in particular entropy and max-of-belief.

...

---

Experiments...
