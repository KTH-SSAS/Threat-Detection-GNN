# Threat-Detection-GNN
Ideas on the next version of the GNN
I propose that the graph presented to the GNN (the training graph) looks like this. That would allow it to understand who does what and when. Every time step will generate a different graph as the oldest event is deleted and a new appended. The most recent event determines the label of the sample: is that event malicious or not?
This approach will ensure that all previous malicious events are in the neighborhood of the event under consideration, since they are connected to the compromised account and IP.

<img width="1041" alt="image-5" src="https://github.com/user-attachments/assets/f5b3265c-e858-4dbe-8da8-9430097c56f0">

However, this approach would not be sufficient in order to predict the probability of compromise of any attack step, because attack steps are not explicitly represented in the graph. To achieve that, we would at least need to represent the attack steps explicitly.  
Here is therefore a better graph structure, where attack steps are made explicit.
With this structure, it will also be possible to make predictions on attack steps that are not present in the log. 

<img width="1010" alt="image-6" src="https://github.com/user-attachments/assets/8fac9bd6-202b-4e5f-9a39-274ba6438511">

Actually, we could train the network to not only estimate the current state of compromise, but also to predict the future state of compromise! This could be done simply by, for each time step graph, label the attack steps based on not the current state of compromise, but the state of compromise N time steps into the future.

One good thing with creating a dedicated time step graph as suggested above, is that it does not need to include the complete attack graph, as it can be limited to the nodes that are N-neighbors to the log events. Parts of the graph where no events are recorded during the time window can therefore be omitted. It will also be possible to limit the heat map arbitrarily to attack graph regions of interest to the user. We should therefore not need to worry excessively about scalability.
A simpler alternative, which I think ought to work equally well, is to ignore the instance model objects and instead include the attack graph relations.

![image-7](https://github.com/user-attachments/assets/00a51e38-4a82-4abc-9e14-6a02d53f8a9e)


That actually brings us back to the attack graphs. I think this is a more efficient codification,  bringing the relevant information closer to the node under consideration as possible.

This way, the difference to our current codification won't be that big:

![image-8](https://github.com/user-attachments/assets/eadfe340-ab23-40c2-9d1f-1097682bde66)

The attack graph remains, but history is not represented as a feature vector, but as a part of the graph.
This is also nice because it allows us to look back in time not in fixed time steps, but in actual events. The sequence of activities thus becomes much more obvious than in the previous encoding.
The features of the different nodes are now simple. Each node will have a type (as defined by the legend). Attack will be compromised or not. Also events can be labeled as malicious or not, but that is no longer important, since the attack graph is present.

## Benefits of this training graph
- It should be able to identify that the service account or IP address that performed the action prevously.
- It should be able to identify that a set of jointly suspicious attack steps were performed, say, by a service account.

## Problem with this training graph
One thing that this training graph is does not suited for, however, seems to be sequential information. The GNN will only consider a limited depth of neighbors. With the current structure, while it will be able to observe e.g. all the actions taken by a service account during the defined time window, it will not know in what order those actions were taken. While that information is in fact present in the graph in the form of the log event sequence, the GNN will only be presented with a small part of it due to its limited depth. 


