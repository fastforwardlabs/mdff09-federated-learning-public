## Appendix: federated.py

In this appendix we illustrate the core functionality of `federated.py`, our
PyTorch federated learning simulation module. The module consists of two
classes, one representing a node and the other the server. Each node object has a
subset of the training data, and a `train` method that initiates a single epoch
of training then returns its model and the amount of training data it used. 

The server object manages connections to many nodes. It has two important
methods. The user of the module calls `round` repeatedly to do federated
learning:

```python
def round(self):
    """
    Do a round of federated learning:

     - instruct each node to train and return its model
     - replace the server model with the weighted average of the node models
     - replace the node models with the new server model

     Nodes with `participant=False` train but are not included in the weighted
     average and do not receive a copy of the server model.
     """
     updates = [node.train() for node in self.nodes]
     self.fedavg([u for u, node in zip(updates, self.nodes) if node.participant])
   self.push_model(node for node in self.nodes if node.participant)
```

The method `fedavg` implements the federated averaging calculation:

```python
def fedavg(self, updates):
   """
   Replace the server model with the weighted average of the node models.

   `updates` is a list of dictionaries, one for each node, each of which has
   `state_dict` (the weights on that node) and `n_samples` (the amount of
   training data on that node).
   """
   N = sum(u["n_samples"] for u in updates)
   for key, value in self.model.state_dict().items():
       weight_sum = (u["state_dict"][key] * u["n_samples"] for u in updates)
       value[:] = sum(weight_sum) / N
```

Aside from the systems and networking issues discussed in this report, these
two methods are a complete federated learning implementation.
