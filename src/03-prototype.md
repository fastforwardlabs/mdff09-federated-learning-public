## Prototype

Turbofan Tycoon, the prototype we created for this report, is a federated
learning solution to one of the scenarios we saw in the previous chapter:
training a predictive maintenance model using real-world customer data. In this
chapter we describe how we selected the problem, prepared the data, built the
federated model, and created the user interface. If you'd like to skip the
predictive maintenance part of the chapter and get straight to the federated
learning, jump ahead to [3.5 The Federated CMAPSS Model](#the-federated-cmapss-model).

### Predictive Maintenance Primer

If a piece of equipment generates revenue, its failure costs money. The costs
multiply if the failure makes a production line stop, wastes time-sensitive raw
materials, or forces customers to wait for their orders. The equipment may
fail in a way that requires it to be scrapped completely, or even in a way that
damages other equipment. The owner of an expensive piece of equipment
maintains it with the hope of avoiding these costly failures.

But maintenance also causes downtime. This downtime is planned, so it is
hopefully less expensive than unplanned downtime due to equipment failure, but
it is not free. Undermaintenance and overmaintenance are therefore both
expensive mistakes. How can we avoid them?

![Corrective maintenance waits for a failure. Preventative maintenance maintains on a schedule. Predictive maintenance uses machine learning to decide when to maintain.](figures/ff09-02.png)

The simplest maintenance strategy is _corrective maintenance_. This is a fancy
technical term that means "waiting for the equipment to break." This is what
you're doing when you take your car to the repair shop _after_ the engine
catches fire. This strategy is suboptimal because it undermaintains.

The next step up the evolutionary tree is _preventative maintenance_. This is
servicing on a schedule. The schedule is fixed and does not depend on detailed
observations of the current state of your particular piece of equipment.
Rather, the schedule is derived from average experience. Taking your car in for
a service every 10,000 miles as recommended by its manufacturer is an example
of preventative maintenance.

In order for preventative maintenance to work, its schedule must necessarily be
conservative. So, while corrective maintenance undermaintains equipment,
preventative maintenance tends to overmaintain.

The most intelligent maintenance schedule is _predictive maintenance_. Here, a
machine learning model is applied to your particular piece of equipment to
predict its remaining useful life (RUL). When that RUL estimate drops below
some threshold, you can intervene by maintaining or replacing the equipment. 

If the machine learning model at the heart of a predictive maintenance strategy
is good, this most advanced approach can save a huge amount of money relative
to the alternatives.

### Why Federated Predictive Maintenance

It only makes sense to apply federated learning to machine learning problems
where the training data is difficult or impossible to move (see [2.6 Applicability](#applicability)).
We chose a predictive maintenance scenario for our prototype because it really
is sometimes subject to this constraint. 

As we discussed in the previous chapter, if a manufacturer wants to build a
predictive model to share with its customers, then the training data that
belongs to those customers is the gold standard. It is more diverse and
representative of real-world use than data the manufacturer collects in
laboratory stress tests and simulations. Assuming they can access it, collecting 
customer data is also cheaper for the manufacturer.

But sometimes competitive pressures can result in a business being unwilling to
share data with suppliers (who may themselves be competitors, or may be
suppliers to competitors). There can also exist legal constraints that prevent
a facility of strategic importance, such as a power station or military base,
from exporting its data.^[See
["Data
Protection: The Growing Threat to Global Business,"](https://www.ft.com/content/6f0f41e4-47de-11e8-8ee8-cae73aab7ccb) _The Financial Times_, May
13, 2018.] Even if a facility is willing and legally able to share data with a
supplier, predictive maintenance data can be so voluminous that this goal
presents a practical engineering challenge for the network. This challenge can
be particularly acute in industries such as mining, when the facilities are in
remote locations or the equipment is mobile.

The fact that federated learning makes it possible to train on
a huge amount of private data while only sending small models over the network
therefore makes its application to industrial predictive maintenance a very
exciting possibility.

### The CMAPSS Dataset

To build our prototype, we used the CMAPSS turbofan degradation dataset. A
turbofan is a kind of jet engine. This dataset is to the predictive maintenance
community as ImageNet or MNIST is to the computer vision community.^[It
is available from the
[NASA
website](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/)].

We used data for 400 engines, each of which was allowed to run until failure.
There were 24 time series (21 sensor readings and 3 operational settings) for
each engine. The sensor readings are temperatures, pressures, and fan rotation
speeds for various components of the jetfan. The task was to predict the
remaining useful life of the engine at a randomly chosen time before failure
using the available history of sensor readings and settings. We used 16 of the
24 sensors and settings in constructing our model. 

![Sensor data for one turbofan.](figures/ff09-25.png)

### Modeling the CMAPSS Data

The CMAPSS training data can be modeled in several ways and, as discussed in
[2.6.3 Not Only Deep Learning](#not-only-deep-learning), federated learning does not in itself place many
restrictions on the kind of model we build.

Because the focus of this report is federated learning rather than predictive
maintenance, and because federated learning gives us considerable freedom to
choose our modeling approach, our prototype uses the simplest modeling approach
that performs significantly better than a naive model. A naive model of the
CMAPSS dataset predicts RUL by assuming that every engine in the test set has a
total useful life of 181 time steps (the median life of engines in the training
set).^[In a more familiar classification context, the naive model is
usually a dummy classifier that always predicts the most prevalent class in the
training set. Such a dummy classifier will have an accuracy equal to the
prevalence of that class. At a minimum, a trained model should have an accuracy
greater than this. (For example, if 90% of emails are spam, a spam classifier
with an accuracy less then 90% is doing worse than
chance!)] This naive model has a root mean squared error of 94.2. 

![Each time step is treated as a training example.](figures/ff09-16.png)

The simplest modeling approach is to treat each time step as a training example.
In this approach, an engine that has a total useful life of 314 time steps
becomes 314 training examples. The features are the instantaneous values of the
16 sensors at each time, and the target variable is the remaining useful life at
each time. The model does not have access to the history of the sensor data. It
can only use the sensor readings at each instant to predict the remaining
useful life.

Despite its simplicity, this formulation results in a model that is comfortably
better than the naive model, so we use it rather than, e.g., modeling the full
sequence with a recurrent neural network.^[See _FF04: Summarization_
for more on recurrent neural networks. For the particular problem of modeling
the time until an event (e.g., customer churn, engine failure), we like the
[Weibull Time-to-Event
RNN](https://ragulpr.github.io/2016/12/22/WTTE-RNN-Hackless-churn-modeling/).
But some machine learning experts have found that an autoregressive feed-forward
neural network (i.e., a regular or convolutional neural network) also works well
for sequence problems. See [When Recurrent Models Don't Need to be
Recurrent](https://bair.berkeley.edu/blog/2018/08/06/recurrent/)] The 400
engines in the set became 58,798 training examples and 16,245 test examples,
each with 16 features. We standardized the features and target variable for
numerical stability.

The model that learns how to predict the RUL from the 16 input features is a
simple neural network with one hidden layer. The non-federated version of this
model (i.e., trained with direct access to all the data on a single machine) has
a root mean squared error of 62.4.

### The Federated CMAPSS Model

In our federated version of the problem, we randomly partitioned the 320 engines
that comprised the training set across 80 nodes. You can think of each of the
nodes as a factory that has observed four turbofan failures. Assuming a factory
is unwilling or unable to share the sensor data from these failures, it can
choose from four maintenance strategies:

- A corrective maintenance strategy (waiting for the engines to fail)
- A preventative maintenance strategy (maintaining at a fixed time, hopefully some
time before the average engine fails)
- A local predictive maintenance machine learning model, trained on the
factory's four failed engines
- A federated predictive maintenance machine learning model, trained
on the collective data of all 80 factories using federated learning

To train the federated model we wrote `federated.py`, an implementation of
federated averaging, in about 100 lines of PyTorch. This implementation is a
_simulation_ of federated learning in the sense that no network communication
takes place. The server and the nodes all exist on one machine. However, it is
an algorithmically faithful implementation: the server and nodes communicate
only by sending copies of their models to each other.

This approach makes it possible to experiment rapidly with very large numbers
of nodes, without getting bogged down in network issues. And despite the
simplification, we can reproduce many of the challenges discussed in
[2.7 Systems Issues](#systems-issues). For example, we can dynamically flag nodes as non-participants
(which means they train on their local data, but do not send or receive model
updates) or failed (which means they stop training altogether). We show the
most important few lines of of `federated.py` in [8. Appendix federated.py](#appendix%3A-federated.py).

The models trained on each node (and the federated model that is their average)
are simple feed-forward neural networks with one hidden layer. Including
biases, the models are defined by 865 weights. The federated model has a final
root mean squared error of 64.3. This is almost as good as the 62.4 of the
non-federated model trained on the same data without federated learning (and
much better than the 94.2 of the naive model).

The federated model requires around 10 rounds of federated learning to reach
its final error (where each node trains for one epoch per round). This is
slower than the non-federated model. We show the loss curve in
the figure below. The characteristic sawtooth shape of the loss
curves of the nodes is due to their training between rounds of communication.

![Mean squared error loss (in standardized units) for the federated model, 79 nodes that participate in the federated model, and 1 node that does not.](figures/ff09-26.png)

We marked one node in our network as a non-participant. This is a node that
trains as often as every other, but does not contribute to or benefit from the
averaging process. This model is therefore effectively a non-federated model
trained on just four engines. With such a small amount of training data, it is
vulnerable to overfitting. Its exact performance is very sensitive to exactly
which four engines it uses (some are more typical than others, and result in a
model that generalizes fairly well, while others result in a catastrophically
bad model). But regardless of this randomness, it is almost always
significantly worse than the federated model. We show the loss curve for the
non-participant model we use as the "local" model in the prototype in
the figure above.

### Product: Turbofan Tycoon

We knew that the model trained using federated learning made better predictions.
The challenge for the frontend was how to communicate that fact to the
prototype user in a form more visceral than a static chart. Our solution was to
make the user a factory owner in charge of four running turbofans. We simulate a
running turbofan by playing through the data step by step. The user decides the
strategy for when a turbofan is maintained, and the effectiveness of that
strategy determines the factory's profit. By simulating the lifespan of each
turbofan we made the prototype dynamic, and by assigning a dollar value to
accurately predicting that lifespan we were able to dramatize the payoff of
federated learning.

In Turbofan Tycoon, every timestep a turbofan completes a cycle (one step) the
factory makes $250. If a turbofan breaks down it costs $60,000 to repair, and it
takes 60 cycles to get it running again. Turbofan maintenance can head off a
breakdown, but it costs $10,000 and takes 20 cycles to perform. When maintenance
is performed is determined by which of the four maintenance strategies the user
selects: corrective, preventative, local predictive, or federated predictive.

![The Turbofan Tycoon prototype.](figures/ff09-28.png)

#### The Life of a Turbofan

We visualize the turbofan lifespan with a collection of graphs. On the left we
show the data from the 16 sensors used by the local and federated predictive
models to predict the remaining useful life of the turbofan. If you watch the
sensor data very closely and turn the simulation speed down very low, you, like
the predictive models, may be able to make a guess about when a turbofan failure
is coming, but it's a tough job (one better left to algorithms). We included the
sensor data because we wanted to give a sense of the information the predictive
models are distilling into strategy.

![The turbofan visualization shows the sensor data and maintenance strategy.](figures/ff09-29.png)

On the right side of the turbofan slot is a visualization of the maintenance
strategy. For the corrective and preventative strategies, there isn't a whole
lot to see. For corrective, it's simply a line marking how many cycles that
turbofan has gone through. For preventative, the preselected threshold (193
cycles) at which maintenance is performed is shown as a dotted line. The local
predictive and federated predictive graphs are richer. For each cycle each model
predicts the remaining useful life of the turbofan. Maintenance is performed
when that prediction drops below 10 cycles. The local predictive and federated
predictive strategy graphs are visualized in the same way but differ in their
predictions because of the different amounts of data they are trained on. The
federated predictive model's larger training dataset makes it the more accurate
predictor.

![More advanced strategies become available as the simulatiion progresses.](figures/ff09-30.png)

To guide the user through the different maintenance strategies, we set up the
simulation so that they become available one by one, as their requirements are
satisfied. These requirements (four turbofan failures for data to upgrade to
preventative, hiring a data scientist to upgrade to local predictive, getting a
federation offer to upgrade to federated predictive) have some basis in the
real world but are vastly simplified so as not to distract from the prototype's
main purpose: to demonstrate the effectiveness of the different strategies.

#### Visualizing Strategy Effectiveness

The effectiveness of each strategy is visible in the factory's maintenance and
failure counts. We further condense each of those measurements into one number: profit.
The formula for profit calculation is `active_turbofan_cycles *
profit_per_cycle
- (turbofan_maintenances * maintenance_cost + turbofan_failures
* failure_cost)` for each turbofan slot in the factory. Because of how it's
calculated, the factory's profit shows the effectiveness of each strategy over time.

![The Factory Scoreboard shows the effectiveness of different strategies over time.](figures/ff09-31.png)

As a measure of the effectiveness of the different maintenance strategies, the
factory's profit is only a useful indicator in the context of strategy paths not
taken. In our prototype, these alternate paths are shown as the user's competitor
factories. The other factories follow the same upgrade rules as your own factory,
except they each max out at a different strategy level (corrective,
preventative, and local predictive). As long as you follow the prototype's
strategy upgrade suggestions, the strategy caps on the other factories mean
you'll pull away from the pack as the simulation progresses and the
effectiveness of the federated predictive strategy reveals itself.

#### The Simulation Trade-off

Behind the scenes, the simulation works by selecting a new random turbofan from
the dataset each time maintenance or failure occurs. This gives the simulation
a realistic variety: some turbofans run over 400 cycles, some cut out at 150.
The law of large numbers means that, over enough time, each strategy's
effectiveness will reveal itself. But, just like in life, streaks of good or bad
luck can make a strategy look more or less effective than it actually is (if a
factory using the corrective strategy gets a lucky streak of long-lasting
turbofans, for example). Ultimately we decided the drama of the simulation was
worth the potential of temporarily misleading results, especially because it
contains a real-world lesson: if your competitor takes the lead with a
subpar strategy, don't panic! Stick with what you know is effective, and over
time you'll win out.
