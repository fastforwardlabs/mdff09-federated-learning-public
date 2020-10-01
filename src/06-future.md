## Future

We have already discussed perhaps the most active area of federated learning
research ([5.1 Privacy](#privacy)). In this chapter we discuss two more economically and
technologically significant open questions: how to further reduce communication
costs and personalize models.

### Reducing Communication Costs

The basic premise of federated learning—transferring the models rather than the
data—reduces the communication costs of distributed training significantly.
But reducing data transfer even further is an area of active research.

Why is it so important to further reduce communication? First, bandwidth
(especially upload) is an expensive resource, particularly on consumer mobile
connections. Users will refuse to participate in a federated network that uses
up all their bandwidth. 

Second, avoiding communication is synonymous with conserving power on
battery-powered devices. That's because the radio antenna is the most
power-hungry part of smartphones and specialized edge hardware. It consumes
more energy than a device's sensors or processor, by a factor of tens,
hundreds, or even thousands.^[See
["Why
the Future of Machine Learning Is Tiny"](https://petewarden.com/2018/06/11/why-the-future-of-machine-learning-is-tiny/) for more detailed numbers.] Users will
refuse to participate in a federated network that uses up all their battery.

Clearly, then, it's valuable to reduce data transfer. But how? One set of
approaches reduces the size of the trained model that is transferred to and
from each node.^[See ["Federated Learning:
Strategies for Improving Communication Efficiency"](https://arxiv.org/abs/1610.05492) by Jakub Konečný et al.] Options to do 
this include structured updates (where the training process restricts the model parameters
such that they can be parameterized more compactly) and sketched updates (where the
model can be trained in the usual way, but is lossily compressed—i.e., 
__quantized__—for communication).

![It is sometimes possible to compress the array of numbers that define a model, which saves bandwidth](figures/ff09-21.png)

Another set of approaches modifies the simple protocol defined in [2.5 A Federated Learning Algorithm](#a-federated-learning-algorithm) to
reduce the number of rounds of communication a given node participates in. This
not only saves on communication by transferring fewer copies of the model, but
speeds up the overall process by eliminating the latency associated with
establishing a connection. Some researchers have proposed eliminating rounds by
instructing nodes to report back only when they have accumulated significant
changes to the model.^[See ["Deep Gradient
Compression: Reducing the Communication Bandwidth for Distributed Training"]https://arxiv.org/abs/1712.01887 by 
Yujun Lin et al. and ["LAG: Lazily Aggregated 
Gradient for Communication-Efficient Distributed Learning"](https://arxiv.org/abs/1805.09965) by Tianyi Chen et al.] 
Others have suggested decreasing the probability that a given node is asked to 
participate in a particular round by the server (perhaps in proportion to its 
power or connection speed).^[See Takayuki Nishio and Ryo Yonetani's ["Client Selection for Federated Learning 
with Heterogeneous Resources in Mobile Edge."](https://arxiv.org/abs/1804.08333)]

![A node that is not participating in a particular round of federated learning saves bandwidth.](figures/ff09-22.png)

This is an area in which rapid progress was being made even as we wrote
this report. We look forward to further advances that make it easier to use
federated learning in parts of the world without robust internet
infrastructure, and without wasting energy. We expect significant progress in
months rather than years.

### Personalization

In all the training scenarios we've described so far in this report, the
server's goal was to use the data on every node to train a single global model.
But in situations where a node plans to _apply_ the model (not just to
contribute to its creation), it will usually care much more that the model
captures the patterns in _its_ data than any other node's. In other words, each
node will care more about the model's accuracy on its test set than on any
other.

If the global model has an appropriately flexible architecture and was trained
on lots of good training data, then it may be better than any local model
trained on a single node because of its ability to capture many idiosyncrasies
and generalize to new patterns. But it is true that, in principle and sometimes
in practice, the user's goal (local performance) can be in tension with the
server's (global performance).

Of course each node has the option to use their own local model for inference
if they choose, but it would perhaps be better to offer _personalized_
federated models. These models would benefit from all the data on the network,
but be optimized for application by a particular node. This is an area of
current academic research. In ["Federated
Multi-Task Learning"](https://arxiv.org/abs/1705.10467), Virginia Smith and collaborators frame personalization
as a multi-task problem where each user's model is a task, but there exists a
structure that relates the tasks. In
["Differentially Private Distributed Learning
for Language Modeling Tasks"](https://arxiv.org/abs/1712.07473), a team from Samsung consider the example of
predictive text on smartphones. They describe training a central model for new
users, and allowing each user's model to diverge a little from that central
model—but not so far that the model exhibits "catastrophic forgetting" (which
in this context would be, e.g., forgetting standard English because a user uses
lots of emojis).

We were not able to learn about the details of any non-research use of
personalized federated learning, but it is doubtless coming soon to smartphones
(if it isn't already in production).

### Sci-fi Story: Merrily, Merrily, Merrily, Merrily

![](figures/ff09-24.png)

A short story written by [Luc Rioual](https://twitter.com/theyoungluc). Inspired by federated learning.

_Here is a god. Her name is Geoff and she is at work._

_Geoff has been a god for a very, very long time, and her tour will come to an
end at some point soon, once the Earth—her charge—has ceased to be useful._

_You see, Geoff is but one of many._

_It is not so much that one denomination or another got it right, but that they
all got parts right—which is itself, too, a cliché. How do I put this? Gods,
like you, tire. They are not architects; their professions do not have built-in
respites; they are caretakers at best, which is not meant to badmouth. You try
sweeping deserts, combing fields, growing trees. Do you know the time it takes?
The will? The water? The focus?_

_Contrary to popular belief, there is no seventh day. No eighth. Ninth. It is
all one long day._

_Tours last who knows how long._

_At the end there is another contraction, like the first, and Geoff withdraws,
vacates for Kokomo, for steel drums, etc., and in her absence unfolds a
replacement: an omniscient, a custodian. We don't know her name or temperament,
but we know what she'll know, not the facts and figures themselves but their
shape: everything that is the case and is not. How is this? It's complicated.
It takes time._

_Geoff likes her job. She likes her coworkers. Takes no breaks because she does
not need them. Geoff has stamina._

_Besides her obligations as terrestrial steward, she is charged with prep._

_Omniscience is not inbred; it is, like a coat, presented to, put on, twirled
in; hands will find tips left in the pockets, tags of authorship sewn by the
collar, room in the size, restraint in the cut. Omniscience is not so much about
power as it is access, which two are, on Earth, one and the same. Here it is
different._

_Geoff's job is, then, to some extent, archival, but deeper, more integral. Yes,
she stores the daily runoffs and ephemera, receipts and bills and dog-ears, but
this is small potatoes, you see, to the bigger crop: her manner of resolution.
Yield, here, is second to method. Anyone can keep newspapers, clip bits, but it
takes practice to properly sort them, to learn to do it better. This is her
purpose: best practice._

_As I've said, Geoff is not alone. There are Sarah, Jean, Ida, Kevin, Ross, Jack,
Toby, Tara, Ben, Heidi, Tom, Thom, Thomas, Tommy Boy, Diana, Nell, Kristen,
and others, whose roles are all the same: to oversee. There are not yet infinite
numbers of gods, realms, so on and so forth, though there is infinite
potential; they have not all yet begun, but have, like members of a chorus
singing in a round, begun in succession. They are not on different planes but
in different spaces, homogeneous in law, hodgepodge in orchestration. Up is
always up. Gravity pulls. Time's arrow flies. What the laws stipulate, of
course, too, is that no cross-pollination can occur. One would contaminate the
other, tear holes in complicated fabrics, bring about Universal End—Geoff would
cease; Sarah, Jean, Ida, poof, all, too. Bye bye birdie, yellow brick road,
etc. No one knows precisely what another knows. They keep—as pertains
practicals—to themselves._

_And yet, as has been said—this is chief—Geoff et al. know everything that is the case and
is not, Ludwig just a man, Geoff a god. We are talking gods, for god's sake!
Omniscience does not mean, here, infallibility. To know everything is not to
always make the right decision. Resolution is hard. Will is what we share with
Geoff, choice our first gift. Decisions require choices. Choices require
knowledge. Knowledge can overwhelm. Geoff's job is to care: verb, "look after
and provide for the needs of." Latin states one cannot end a sentence on a
preposition, but I can, and I will._

_Geoff is, in some respects, a guinea pig, perhaps. There will be more like her.
They will not be named Geoff. This will all happen again, but better, in
hi-def._

_Consider this moral dilemma: you watch someone litter. Do you pick up after
them? Scold? Snap? Now multiply that. Multiply that by however many people
there might be on a given planet, subtracting a bit for the morally robust, and
you still have several billion people littering. How do you manage this? Where
do you intervene? When do you give up? You need more than just what you have
before you. You need everything, everyone, a team effort. Supreme courts have
nine justices, not one. Everything that is the case and not._

_Tommy Boy has no namesake film—Chris Farley a periodontist—Heidi no
Princess Di. In Kevin's there's no cutlery to speak of so people everywhere eat
with their hands, and in Tara's no one keeps pets in their homes. Jean has blue
ducks. Kristen no caves. Sarah lacks yoga. And in Jack's everyone sleeps for
days. You would not believe their skin. Their happiness. Jack is proud. All
there is is difference._

_Things happen. The gods respond (or don't). Effectiveness is measured.
Adjustments made to what we might call protocols of assessment—they become
smarter. Humans can be tricky. Geoff uploads. Geoff downloads. They all
incorporate. What-ifs build out the properties of what-nows. Ida, Ross, Ben, et
al.—Geoff and her lively cohort—how shrinks cannot talk, per se, cannot share
what they know of their constituents, their worlds, so they share process, they
share technique; together, they learn; together, they build best practice; they
hone their craft. They pass it down. There is a Foreman, somewhere else, who
keeps track of it all. They only call him that: the Foreman. Some have said his
name is Evan, but who knows. He disperses updates with consistency and briefs
the newbies._

_And yet, they are not perfect. Only so much can be done. Geoff is tired, too.
What can be done? Drop interest rates? Frogs? Bombs? They do what they can to
protect us from ourselves. A nudge here, a net there._

_Geoff's replacement will know so much. Everything and more._

_People do not read. The poor can go hungry. Buffoons swell and rise like dough.
Everything happens in rounds. Row, row, row your boat._
