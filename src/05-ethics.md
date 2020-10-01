## Ethics

###  Privacy

The macOS and iOS installer contains a dialog box that tells the user:

::: info
Apple believes privacy is a fundamental human right, so every Apple product is
designed to:

 - Use on-device processing wherever possible
 - Limit the collection and use of data
 - Provide transparency and control over your information
 - Build on a strong foundation of security
:::

Along similar lines, the European Union's General Data Protection Regulation
requires that:

::: info
- Data must be collected for a specific purpose.
- That purpose must be one to which the user has consented.
- The data must be necessary to achieve that purpose.
- It must be deleted when it is no longer necessary for any purpose.^[This 
informal language is taken from 
["On
Purpose and by Necessity: Compliance Under the GDPR."](https://blog.acolyer.org/2018/03/21/on-purpose-and-by-necessity-compliance-under-the-gdpr/)]
:::

The 2012 White House report
["Consumer
Data Privacy in a Networked World"](https://obamawhitehouse.archives.gov/sites/default/files/privacy-final.pdf) argues for a principle of "focused
collection":

::: info
Consumers have a right to reasonable limits on the personal data that companies
collect and retain. Companies should collect only as much personal data as they
need to accomplish [clearly specified purposes]. Companies should securely
dispose of or de-identify personal data once they no longer need it, unless
they are under a legal obligation to do otherwise.
:::

Most simply, Maciej Ceglowski gave the following advice about data to the
Strata 2015 audience in his keynote address
["Haunted by Data"](http://idlewords.com/talks/haunted_by_data.htm):

::: info 
- Don't collect it.
- Don't store it.
- Don't keep it.
:::

By keeping data on a device that belongs to the user, federated learning makes
it easier to use machine learning while following these (hopefully
self-evident) ethical imperatives and legal requirements. Because the model
owner does not have direct access to the data, concerns and liabilities seem to
fall away.

But there is a problem with this rosy picture: it is sometimes possible to
infer things about the training data from a model. This problem is not unique
to federated learning—any time you share the predictions of a trained model
you open up this possibility. However, it is worth considering in detail in the
context of federated learning for two reasons. First, preserving privacy is one
of federated learning's main goals. Second, by distributing training among
(potentially untrustworthy) participants, federated learning opens up new
attack vectors.

The most indirect way to infer information about the training data requires
only the ability to query the model several times. Anyone with indirect access
to the model via an API can attempt to attack it in this way. This attack
vector is not unique (or any more dangerous) in federated
learning.^[See e.g. ["Membership
Inference Attacks Against Machine Learning Models"](https://arxiv.org/abs/1610.05820) by Reza Shokri et al. and
["Model Inversion Attacks that
Exploit Confidence Information and Basic Countermeasures"](https://dl.acm.org/citation.cfm?id=2813677) by Matt Fredrikson,
Somesh Jha, and Thomas Ristenpart.] The usual protection against it is
_differential privacy_. Differential privacy is a large and mathematically
formal field with applications far beyond machine learning.^[For a
modern, accessible, ML-oriented introduction we recommend
["Privacy
and Machine Learning: Two Unexpected Allies?"](http://www.cleverhans.io/privacy/2018/04/29/privacy-and-machine-learning.html) by Nicolas Papernot and Ian
Goodfellow. For a more general overview, we recommend the short and mercifully
informal three-part series
["Understanding
Differential Privacy and Why It Matters for Digital Rights."](https://www.accessnow.org/understanding-differential-privacy-matters-digital-rights/)] In a production
machine learning context, its application generally means that the server adds
noise to the weights of the model before opening it up to users.

In a federated learning setting where the server and nodes are justified in
trusting each other, this type of attack is the only concern. But if the server
or nodes are not trustworthy, other kinds of attacks are possible.

![It can be possible to infer information about the data on a node from the models it sends to the server.](figures/ff09-20.png)

For example, the server must be able to directly inspect the node's model in
order to average it. But for certain classes of model, the mere fact that a
weight has changed tells you a particular feature was present in the training
data. For example, suppose a model takes a bag of words as features, and the
tenth word in the vocabulary is "dumplings." If a node returns a model where
the tenth coefficient of the model has changed, the server or an intermediary
may be able to infer that the word "dumplings" was present in the training data
on that node.

![Training data (left) can be reconstructed (right) by a malicious node (images taken from ["Deep Models Under the GAN: Information Leakage from Collaborative Deep Learning"](https://arxiv.org/abs/1702.07464) by Briland Hitaj, Giuseppe Ateniese, and Fernando Perez-Cruz).](figures/ff09-27.png)

This attack is more difficult to carry out against modern (and more complex)
models in practice. The risk can be mitigated by differential privacy (again)
or secure aggregation. The differential privacy approach has each node add
noise to its model before sharing it with the server.^[See Naman
Agarwal et al.'s ["cpSGD:
Communication-Efficient and Differentially-Private Distributed SGD"](https://arxiv.org/abs/1805.10559).] Secure
aggregation protocols make it possible for the server to compute the average of
the node models using encrypted copies which it does not have the ability to
decrypt. Both these approaches add communication and computation overhead, but
that may be a trade-off worth making in highly sensitive contexts.

To sum up our discussion of privacy: by leaving the data at its source,
federated learning plugs the most obvious and gaping security hole in
distributed machine learning. But it is not a silver bullet. Differential
privacy and secure aggregation will both almost certainly be a piece of the
security puzzle. These techniques are already in production use—but as with any
computer security risk, adversaries will make progress too. Production
deployment of federated learning in highly sensitive contexts must be done with
care. Eliminating the many threats to a shared model is a work in progress.

### Consent

A node is surrendering computational resources and, as we saw in the previous
section, risking the privacy of its data. It is therefore hopefully
self-evident that you need to get a user's consent before making them a node in
your federated learning network. 

But there are more formal, legal arguments for consent. The European Union's
General Data Protection Regulation doesn't directly address federated learning
(or indeed apply worldwide), but it provides a lens through which to make
decisions about machine learning generally. Among other things, the GDPR
requires informed and active consent. _Informed_ consent is tricky in the
context of federated learning because the network asks individuals to share an
abstraction of their data and to give up some of the processing power on their
devices. These may turn out to be difficult ideas to get across in a way that
will withstand legal scrutiny.

The requirement to obtain _active_ consent complicates federated learning. If
it is a legal requirement to allow a node to withdraw, an active federated
learning network must be robust against this possibility in the algorithmic
sense (see [2.7 Systems Issues](#systems-issues)). This is in addition to one of the challenges that the
GDPR's "right to be forgotten" poses to machine learning in all forms: what
happens to the trained model if the source of the training data withdraws
consent?^[See e.g. Section IV.B of
["Slave to the Algorithm? Why a 'Right to
an Explanation' Is Probably Not the Remedy You Are Looking For,"](http://dx.doi.org/10.2139/ssrn.2972855) by Lilian Edwards and
Michael Veale.]

### Environmental Impact

Internet-connected devices will be responsible for 20% of the world's
electricity consumption by
2025.^[["Tsunami
of Data Could Consume One Fifth of Global Electricity by 2025"](https://www.theguardian.com/environment/2017/dec/11/tsunami-of-data-could-consume-fifth-global-electricity-by-2025), _The Guardian_,
December 11, 2017.] In addition to the proliferation of such devices in
technologically advanced countries, access to the internet is increasingly
considered a [human right](http://digitallibrary.un.org/record/845728?ln=en).
With billions of people coming online, it is important to consider the
environmental impact of those devices and the data centers required to support
them.

In this sense, federated learning is good news. By reducing the amount of
information that needs to be sent over the network, this approach to
distributed machine learning significantly reduces the power consumed by edge
devices (see [6.1 Reducing Communication Costs](#reducing-communication-costs)). And by reducing the amount of data that needs
to be stored in data centers, federated learning reduces the need for
long-lived, climate-controlled infrastructure.
