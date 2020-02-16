# Kafka Mirror Maker demo

[![Alt text](https://img.youtube.com/vi/Zz-5WUNh_ik/0.jpg)](https://www.youtube.com/watch?v=Zz-5WUNh_ik)

## Install Kafka cluster in Europe

Start by creating namespace `skupper-demo` on cluster in eu-west-1:

```
kubectl create namespace skupper-demo
```

Install the Strimzi cluster operator.
On MacOS:

```
curl -L0 https://github.com/strimzi/strimzi-kafka-operator/releases/download/0.16.0-rc1/strimzi-cluster-operator-0.16.0-rc1.yaml | sed 's/namespace: .*/namespace: skupper-demo/' | kubectl apply -n skupper-demo -f -
```

On Linux:

```
curl -L0 https://github.com/strimzi/strimzi-kafka-operator/releases/download/0.16.0-rc1/strimzi-cluster-operator-0.16.0-rc1.yaml | sed 's/namespace: .*/namespace: skupper-demo/' | kubectl apply -n skupper-demo -f -
```

Once the cluster operator is running, we can deploy the Kafka cluster:

```
oc apply -f eu-west-1/kafka-cluster.yaml -n skupper-demo
```

Once the cluster is running, we can deploy some clients:

```
oc apply -f eu-west-1/kafka-clients.yaml -n skupper-demo
```

Check in the logs that the consumer and producer work.

## Install Kafka cluster in US

Start by creating namespace `skupper-demo` on cluster in us-east-1:

```
kubectl create namespace skupper-demo
```

Install the Strimzi cluster operator.
On MacOS:

```
curl -L0 https://github.com/strimzi/strimzi-kafka-operator/releases/download/0.16.0-rc1/strimzi-cluster-operator-0.16.0-rc1.yaml | sed 's/namespace: .*/namespace: skupper-demo/' | kubectl apply -n skupper-demo -f -
```

On Linux:

```
curl -L0 https://github.com/strimzi/strimzi-kafka-operator/releases/download/0.16.0-rc1/strimzi-cluster-operator-0.16.0-rc1.yaml | sed 's/namespace: .*/namespace: skupper-demo/' | kubectl apply -n skupper-demo -f -
```

Once the cluster operator is running, we can deploy the Kafka cluster:

```
oc apply -f us-east-1/kafka-cluster.yaml -n skupper-demo
```

Once the cluster is running, we can deploy some clients:

```
oc apply -f us-east-1/kafka-clients.yaml -n skupper-demo
```

Check in the logs that the consumer and producer work.

## Initialize and connect Skupper

First we have to initialize Skupper and connect the clusters.

On both clusters which you want to connect, we have to initialize Skupper first:

```
skupper init
```

Next we have to connect the clusters together using Skupper.
OI the eu-west-1 cluster, run the following command to create the token:

```
skupper connection-token skupper-token.yaml
```

In the US clusters, use the token to connect them:

```
skupper connect skupper-token.yaml
```

## Expose the Kafka cluster using Skupper

Now expose the Kafka stateful set using Skupper.
First expose the eu-west-1 cluster to us-east-1.

Run the following command on the eu-west-1 cluster:

```
skupper expose statefulset my-cluster-europe-kafka --headless --port 9092
```

And the same on the us-east-1 cluster:

```
skupper expose statefulset my-cluster-us-kafka --headless --port 9092
```

## Deploy Mirror Makers

Deploy Mirror Maker in eu-west-1:

```
oc apply -f eu.west-1/kafka-mirror-maker.yaml -n skupper-demo
```

And in us-east-1:

```
oc apply -f us-east-1/kafka-mirror-maker.yaml -n skupper-demo
```

## Mirroring

Wait for the mirroring to kick in and see how the messages are mirrored.
