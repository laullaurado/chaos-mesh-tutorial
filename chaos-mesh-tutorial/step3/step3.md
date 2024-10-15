### Basic resource faults
Fault injection is the key of Chaos experiments. Chaos Mesh covers a full range of faults that might occur in a distributed system, and provides three comprehensive and fine-grained fault types: basic resource faults, platform faults, and application-layer faults. In our executable tutorial we will discuss two types of features during two experiments.

#### Experiment 1 – PodChaos (Simulating Pod Failures)
This PodChaos experiment simulates Pod failures in the details service of the Bookinfo application. It injects fault into a specified Pod to make it unavailable for a period of time.
To Create a PodChaos Experiment we have to do these steps:

1. Define the PodChaos YAML Configuration: 

    Create a file `named pod-failure-details.yaml` with the following content:
    ```
    apiVersion: chaos-mesh.org/v1alpha1
    kind: PodChaos
    metadata:
    name: pod-failure-2
    namespace: chaos-testing
    spec:
    action: pod-failure
    mode: one
    duration: '2m'
    selector:
        namespaces:
        - default
        labelSelectors:
        'app': 'details'
        'version': 'v1'
    ```{{copy}}
    
    This experiment targets one pod with the labels app=details and version=v1 in the default namespace. The failure lasts for 2 minutes and is managed in the chaos-testing namespace.

2. Apply the PodChaos Experiment

    Run the following command to apply the PodChaos experiment:
    `kubectl apply -f pod-failure-details.yaml`{{exec}}
    
    Now, if you refresh the page, you can see that the 'details' section (left column) is unavailable.

