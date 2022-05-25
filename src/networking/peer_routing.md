# Peer Routing

Peer routing consists on the discovery (and connectivity) between peers in the network.
This is usually done on structured overlay networks by knowing that every peer in the system stores other peers with the best identifiers for routing in the network. You can check Chord[^1] or Kademlia[^2] as an example.

Ziku provides a novel peer routing strategy for unstructured overlay networks.

When a node wants to send a message, or wants to connect, to another node in the network (which it only knows the immutable node identifier).
It sends a routed message to the peers that have the lowest hamming distance[^3] between their node identifiers and the destination node identifier.
There is also the condition that the hamming distance of the peer should be lower than the hamming distance of the node which is sending the message to the destination node.

This process repeats in each hop.
You can see the strategy in pseudocode 1.

<figure>

```
State:
	nodeId	// node identifier
	peers	// set of peers

	Upon receiveRoutedMsg(dest, msg):
		If (nodeId = dest) then:
			Trigger processMsg(msg)
			return

		Foreach p ∈ peers do:
			self_distance ← hamming_distance(nodeId, dest)
			distance ← hamming_distance(p.nodeId, dest)

			If self_distance < distance then:
				continue
			Else If distance = 0 then:
				Trigger sendRoutedMsg(p, dest, msg)
				return
			Else:
				Trigger sendRoutedMsg(p, dest, msg)

	Upon sendRoutedMsg(peer, dest, msg):
		...

	Upon processMsg(msg):
		...
```

<figcaption align = "center">Pseudocode 1</figcaption>
</figure>

In our [overlay network](./overlay_network.md), nodes try to optimize for latency and hamming distance, thus, nodes will tend to have peers that have a small hamming distance. Apart from that, due to the [multilayer network](./multilayer_networks.md) approach, we also have random peers (from other applications) that might have lower hamming distances.


[^1]: [Stoica, I.; Morris, R.; Kaashoek, M. F.; Balakrishnan, H. (2001). "Chord: A scalable peer-to-peer lookup service for internet applications"](https://dl.acm.org/doi/10.1145/964723.383071)

[^2]: [Maymounkov, P.; Mazières, D. (2002). "Kademlia: A Peer-to-Peer Information System Based on the XOR Metric"](https://link.springer.com/chapter/10.1007/3-540-45748-8_5)

[^3]: [Hamming, R. W. (1950). "Error detecting and error correcting codes"](https://ieeexplore.ieee.org/document/6772729)