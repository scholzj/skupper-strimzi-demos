# Kafka clients demo

[![Alt text](https://img.youtube.com/vi/W7aUOgCTyOg/0.jpg)](https://www.youtube.com/watch?v=W7aUOgCTyOg)

Start by creating namespace `skupper-demo` on all clusters:

```
kubectl create namespace skupper-demo
```

## Init and connect Skupper

First we have to initialize Skupper and connect the clusters.

On all clusters which you want to connect, we have to initialize Skupper first:

```
skupper init
```

Next we have to connect the clusters together using Skupper.
On the main cluster where you want to run Kafka, run the following command to create the token:

```
skupper connection-token skupper-token.yaml
```

On the other clusters, where the clients should run, use the token to connect them:

```
skupper connect skupper-token.yaml
```

## Deploy Strimzi and Kafka cluster

On the main cluster (in my case eu-west-1), install the Strimzi cluster operator.
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

## Expose the Kafka cluster using Skupper

Now expose the Kafka stateful set using Skupper.
Expose the port 9092:

```
skupper expose statefulset my-cluster-kafka --headless --port 9092
```

Once the service is exposed, 

## Install Kafka clients on the other clusters

Now we have to install the clients on other clusters.
On us-east-1:

```
oc apply -f us-east-1/kafka-clients.yaml -n skupper-demo
```

And on local Minishift:

```
oc apply -f minishift/kafka-clients.yaml -n skupper-demo
```
