---
title: Troubleshooting
summary: Troubleshooting issues with CockroachDB.
toc: true
---

## General Troubleshooting

When you run into a problem, the best place to start is having CockroachDB tell you what's going on:

```shell
$ cockroach start <flags> --logtostderr`
```

## Starting Clusters & Nodes

### Node Won't Join Cluster

**Description**: You specified the `--join` flag, but receive one of the following messages:

~~~ shell
not connected to cluster; use --join to specify a connected node
~~~
~~~ shell
node 1 belongs to cluster {"cluster hash"} but is attempting to connect to a gossip network for cluster {"another cluster hash"}
~~~

**Solution**: Choose a different directory to store the CockroachDB data *or* remove the existing directory. After making that decision, start a node joining the cluster again.

~~~ shell
# Remove the directory
$ rm -r cockroach-data/

# Start a node joining the cluster
$ cockroach start <flags> --join=<node host>:26257
~~~

**Explanation**: When you start a node, the directory you choose to store the data in also identifies the cluster the data came from. This causes conflicts when you've already started a node on the server, have quit `cockroach`, and then try to start a "new" node to a different cluster. Because the existing directory's cluster ID doesn't match the new cluster ID, the node cannot join.

## Replication

### Replicas Failing on a Single-Node Cluster

When running a single-node CockroachDB cluster for testing, an error about replicas failing will eventually show up in the node's log files, for example:

~~~ shell
E160407 09:53:50.337328 storage/queue.go:511  [replicate] 7 replicas failing with "0 of 1 store with an attribute matching []; likely not enough nodes in cluster"
~~~

This error occurs because CockroachDB expects three nodes by default. If you do not intend to add additional nodes, you can stop this error by updating your default zone configuration to expect only one node as follows:

~~~ shell
$ cockroach zone set .default 'replicas: [attrs: []]'
~~~

See [Configure Replication Zones](configure-replication-zones.html) for more details.

### Replicas Failing on a Multi-Node Cluster

When running a multi-node CockroachDB cluster, if you see an error like the one above about replicas failing, some nodes might not be able to talk to each other. Here are some recommended actions:

1. Check to make sure that every node but the first was started with the `--join` flag set to the hostname and port of the first node. If the flag was not set correctly for a node, shut down the node and restart it with the `--join` flag set correctly. See [Stop a Node](stop-a-node.html) and [Start a Node](start-a-node.html) for more details. 

2. If all `--join` flags were set correctly, look in the logs of each node for messages like “connection refused". If you see such messages, check your network or firewall configuration.

## Something Else? 

If we don't have an solution here, you can try:

- [CockroachDB Community Forum](https://forum.cockroachlabs.com): Ask questions, find answers, and help other users.
- [Join us on Gitter](https://gitter.im/cockroachdb/cockroach): This is the most immediate way to connect with CockroachDB engineers. To open Gitter without leaving these docs, click **Chat with Developers** in the lower-right corner of any page.
- **Send Us Feedback**: In the **Was this page helpful?** section below, click **No**, and then send us details about your issue.

