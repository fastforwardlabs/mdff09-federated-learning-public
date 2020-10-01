## Landscape

In this chapter we discuss use cases for federated learning. Most of these are
natural and obvious but, for now at least, speculative. As a very new
technology, federated learning is not yet in production use at many companies
willing to discuss their experiences. We share what we've learned from the
exceptions. Finally, we discuss the
tools and vendors you can use if you decide to explore federated learning.

### Use Cases

#### Smartphones and Browsers

Machine learning has huge potential to improve the user experience with smartphones 
and the web in general. In part that is because the training data is so abundant: users
generate vast amounts of potentially valuable on-device data in their normal
day-to-day interactions with these devices.

But aside from the practical challenge of getting this data off a device with a
slow connection, the personal aspect of some of this data (what people type,
where they travel, what websites they visit) makes it problematic. Users are reluctant 
to share this sensitive data, and possessing it exposes technology companies to
security risks and regulatory burdens. These characteristics make it a great
fit for federated learning. The use case is so compelling that it comes as no
surprise that
[Google
researchers](https://ai.googleblog.com/2017/04/federated-learning-collaborative.html) are usually credited with its invention, and
[Samsung engineers have also contributed
significant ideas](https://arxiv.org/abs/1712.07473). 

#### Healthcare

The healthcare industry offers huge financial incentives to develop effective
treatments and predict outcomes. But the training data required to apply
machine learning to these problems is of course extremely sensitive. The
consequences of actual and potential privacy violations can be serious. For
example, Facebook was recently forced to distance itself from press reports that it was
sharing anonymized data with hospitals,^[The project is on hiatus so
that Facebook can focus on "other important work, including doing a better job
of protecting people's data." See
["Facebook
Sent a Doctor on a Secret Mission to Ask Hospitals to Share Patient Data,"](https://www.cnbc.com/2018/04/05/facebook-building-8-explored-data-sharing-agreement-with-hospitals.html)
CNBC, April 5, 2018.] and a British hospital was reprimanded for sharing the
health records of 1.6 million patients with Google DeepMind as part of a trial
to test an alert, diagnosis, and detection system for acute kidney
injury.^[See ["Royal
Free - Google DeepMind Trial Failed to Comply with Data Protection Law,"](https://ico.org.uk/about-the-ico/news-and-events/news-and-blogs/2017/07/royal-free-google-deepmind-trial-failed-to-comply-with-data-protection-law/)
Information Commissioner's Office, July 3, 2017.]

By keeping the training data in the hands of patients or providers, federated
learning has the potential to make it possible to collaboratively build models
that save lives and make huge profits. The European Commission's Innovative
Medicine Institute is soliciting proposals to build a
[federated,
private, international platform for drug discovery](https://ec.europa.eu/research/participants/portal/desktop/en/opportunities/h2020/topics/imi2-2018-14-03.html).

In [2.3 Medical AI](#medical-AI) we described a wearable medical device use case for
federated learning (inspired in part by the Google DeepMind controversy). In
[4.2.2 Owkin](#owkin) we share what we learned from our conversation with Owkin, an
ambitious and advanced startup that makes it possible for healthcare data
owners to collaborate.

#### Industrial IoT

As discussed in [3. Prototoype](#prototype), industrial applications of decentralized machine
learning can be subject to privacy concerns: if the nodes are in competition
with one another, they may be unwilling to share training data because of what
it reveals about their operations. Our predictive maintenance prototype explores
this scenario. 

Communication costs are also an issue. The sheer volume of relevant sensor data
can be a challenge, even for fixed factory equipment with a stable, fast
internet connection. For mobile equipment and equipment at remote facilities, such as
offshore infrastructure or mines, the problem is even harder. By transferring
models rather than training data (and thereby saving considerable bandwidth), 
federated learning can help.

Federated learning's use of the computational resources of the nodes can also
save money. Nodes can be inexpensive commodity edge IoT hardware or can even be
someone else's financial responsibility entirely (see [4.1.3 Industrial IoT](#industrial-iot)).

#### Video Analytics

![Moving video data to a server requires a lot of bandwidth.](figures/ff09-18.png)

Machine learning is increasingly being applied to video data. Applications include home
security cameras and baby monitors, in-store video for retail analytics, and
autonomous vehicles. It's expensive to move lots of video from multiple sources
over a network.^[["Why
the Future of Machine Learning Is Tiny"](https://petewarden.com/2018/06/11/why-the-future-of-machine-learning-is-tiny/) tells the story of a person with an 
in-home camera system who experienced significantly higher ISP usage in the 
month of December than in the rest of the year. Why? Because
"his blinking Christmas lights caused the video stream compression ratio to drop
dramatically, since so many more frames had differences."] If those sources do
not have fixed connections (such as in the case of autonomous vehicles), it can be almost
impossible. Assuming you can move the data, it can be difficult to store it
once you get it to the datacenter because of its volume. And when the video is
from a home security device, privacy is also a particular concern. These
situations present engineering and privacy challenges that make federated
learning a great fit. 

#### Corporate IT

![Companies are reluctant to share their internal discussions with the developers of corporate IT products.](figures/ff09-19.png)

The application of machine learning to corporate data is challenging because
access to the training data is typically restricted by competitive or legal constraints.
If enterprise users install software on their own hardware or in their own
cloud, the developer of that software will find it hard to get training data
from those users. If the ability to participate in a federated learning network
is added as an (optional) feature to such software, the developer can train new
intelligent features on real user data.

Collaboration tools like Jira and Slack are good examples of this. Atlassian
(the developer of Jira) does not have direct access to the data its users
(other companies) generate on local installations of Jira. The simplest
workaround for Atlassian is to train models using data from its own internal
Jira instance. This data has the virtue of being available, but obviously
Atlassian's internal use of its product is not typical, and a model trained
on this data may perform poorly for the average user. Federated learning could
offer the machine learning teams at companies like Atlassian indirect access to
the much more representative data generated by their paying customers.

### Users

#### Firefox

![URL autocompletion in Firefox.](figures/ff09-13.png)

Google employees have published a huge amount of academic research on federated
learning, but Google's public discussion of its own production use has so far
been very high-level. By far the most detailed description of federated
learning in a production context in any industry is of 
[its use in the open
source web browser Firefox](https://florian.github.io/federated-learning-firefox/). That article is well worth reading if you plan to
use federated learning in a production setting, but we'll point out some
notable aspects here.

Like all modern browsers, Firefox offers to autocomplete URLs as you type them.
The exact equation that determines the rank of a suggestion currently uses
arbitrarily chosen coefficients. These would be better determined by machine
learning, but Firefox users (understandably) do not want to share their browser history with
Firefox's developers. The Firefox federated learning project treats the
browsers as nodes in a federated network that learns the optimal autocompletion
strategy. Users can opt in to participate in the network, but they do not need
to share their browser history with Firefox. 

The Firefox project is also a great demonstration of the fact that you don't
need to use deep learning to do federated learning.
The model is an SVM, which gets around the difficulty of training deep models
on edge devices. This project also benefits
from Firefox's built-in telemetry system, which handles the network
communication.

#### Owkin

[Owkin](https://owkin.com/) is a French healthcare startup with federated learning at the heart of its
business model. Owkin sets up the hardware and software infrastructure to train
models onsite at hospitals and other medical research institutions,
connects these systems into networks, and uses federated learning to combine
models across institutions working on similar problems. For example, it might form a
federation of hospitals treating patients with a certain medical condition—say,
a certain type of cancer—and research institutions working on therapies for
that type of cancer. Owkin also uses federated learning to share research models
with pharmaceutical companies researching therapies for those medical conditions.

Owkin uses federated learning to protect the privacy of medical data, comply with strong European
data protection laws, and safeguard commercially sensitive information in
federations that may include competing pharmaceutical companies. It puts
substantial resources into security and is constantly on the lookout for new
threat models. Owkin also faces challenges in regularizing models across
institutions that collect somewhat different types of data. This is part of the
reason it suits the company to install its own hardware and software, to help
maintain consistency at edge sites.

Owkin was founded in 2016 and has about 40 employees. It currently supports around a
dozen hospitals and research institutions, and is adding more.

#### OpenMined

[OpenMined](https://openmined.org/) is a community of open-source developers who
are working to build tools for secure, privacy-preserving machine learning. The
core mission of OpenMined is to establish a marketplace that makes it possible
to train models on data that is kept private and owned by clients, while
allowing models to be shared securely amongst multiple owners. To achieve these
broad goals, the community seeks to combine federated learning, differential
privacy, and advanced cryptographic techniques in a complete software
ecosystem.

The idea is that this three-sided market will enable sources of training data to 
monetize that data while retaining privacy, will enable sources of compute power to
monetize those cycles for training of models, and will enable users of
models to pay for the data and compute resources necessary to create them. 
OpenMined needs to do all this while guarding against bad actors who seek to
violate the privacy of users of the marketplace, to poison models with bad
training training data, or to steal models.^[See
["How to Backdoor Federated Learning"](https://arxiv.org/abs/1807.00459) by 
Eugene Bagdasaryan et al.] These are ambitious goals! Some of the project's progress 
is visible in [4.3.1](PySyft).

### Tools and Vendors

At the time of this writing we could not identify any commercial vendors of 
federated learning solutions. Commercial federated learning requires a rigorous 
approach to preserving data privacy, which is still an area of intense research.
Fortunately, where there is some level of trust between members of a federated
network, it is possible to build a federated learning solution using existing
tools.

Federated learning is a combination of machine learning and network communication.
Rich ecosystems of tools already exist for doing both, and can be used to build
a federated learning solution. There are also tools that specialize in
federated learning that aim to make it easier to combine the individual parts.

#### PySyft

[PySyft](https://github.com/OpenMined/PySyft) is an open-source Python library,
built by OpenMined, for private federated learning. It
provides abstractions on top of popular machine learning libraries that make it
easy to move computations to remote clients where the data resides. PySyft
currently provides first-class support only for PyTorch, but integration with
TensorFlow is on the roadmap.

PySyft is currently under very active development and is more of a research
platform than a production-ready federated learning library. Because of its
dependence on PyTorch, it is not suitable for federated learning on edge
devices, mobile devices, or browser-based clients. It is more suitable in settings
where clients are server-based, with larger resource pools, such as private
cloud environments where data has a single owner but cannot be moved to a
centralized location.

#### General-Purpose Tools

Federated learning requires software that can distribute computation across
multiple physical machines and manage communication between them. Many machine learning
libraries already provide this functionality and can, in theory, run on any
device with a CPU and a network connection. However, the key difference between
federated learning and distributed machine learning is that the machines that
participate in federated learning may be heterogeneous, unreliable, and highly
resource-constrained. The libraries that currently exist for distributed 
machine learning were not designed for the federated setting and therefore 
may perform poorly. 

We do not expect this functionality gap to last long, though, and while
performance may vary, there are still some cases today where federated learning
can directly leverage existing machine learning tools.

#### Browser-Based

![Browser-based machine learning tools could open up more distributed possibilities.](figures/ff09-14.png)

Web browsers are a common interface to billions of client devices, each of
which has data and computational resources that can be put to use.
Libraries like TensorFlow.js make
it possible to run machine learning workloads directly in the browser, so
that a user's training data never has to leave their machine. Additionally,
because training happens on the client's machine, the model can still learn
even when the user has no network connection.

[TensorFlow.js](https://js.tensorflow.org/) is a JavaScript library that provides
a TensorFlow-like API for doing machine learning directly in the browser. It
supports both training and inference and can leverage client-side GPUs for fast
computation. The library is maintained under the TensorFlow family of projects,
but shares no code with TensorFlow itself and only provides a subset of the
full TensorFlow functionality.

#### Mobile and Edge

![Training models on the edge devices is exciting, but library support is immature.](figures/ff09-15.png)

Because mobile and edge devices have low computational power and small energy
budgets, the computations they run need to be heavily optimized. This usually
means the software they run is written in Java or C\+\+, but most existing
machine learning tools require Python. This gap has led to the creation of
several lightweight machine learning libraries that are optimized for mobile
and have interfaces in Java and C++. Examples are TensorFlow Lite, Caffe2, and
Core ML.

Currently these lightweight libraries only support the inference stage of
machine learning. They don't provide a way of computing the model updates on-device,
as is required for federated learning. One solution is to directly use the
full-featured machine learning libraries that support training and provide Java
or C++ APIs for mobile development. A second option is to develop custom code
for updating your federated learning model.

Federated learning can be applied to any model type that has a proper notion of
an incremental update, but among existing tools deep learning libraries are
typically best suited for federated learning. Libraries like TensorFlow,
Caffe2, MXNet, and Deeplearning4j all make it easy to compute model updates
(gradients) and provide APIs in C++ and Java that are amenable to mobile
development. Using these full-featured libraries will not be performant in
general, but they may still be viable options when on-device resources are less
constrained.

While developing customized code is generally undesirable, it may be the best
or only viable option given the current state of federated learning tools. The
burden of implementing custom deep learning logic may be deemed too high, but simpler
solutions like linear models, SVMs, or clustering can be done with relatively
little custom code. If a federated learning solution enables applications that
were previously impossible, even these simple models can provide significant
gains.
