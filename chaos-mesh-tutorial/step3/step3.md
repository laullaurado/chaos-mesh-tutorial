### Basic resource faults
Fault injection is the key of Chaos experiments. Chaos Mesh covers a full range of faults that might occur in a distributed system, and provides three comprehensive and fine-grained fault types: basic resource faults, platform faults, and application-layer faults. In our executable tutorial we will discuss two types of features during two experiments.

#### Experiment 1 – PodChaos
This PodChaos experiment simulates Pod failures in the details service of the Bookinfo application. It injects fault into a specified Pod to make it unavailable for a period of time.
To Create a PodChaos Experiment we have to do these steps:

1. Define the PodChaos YAML Configuration: 

    Create a file `named pod-failure-details.yaml` with the following content:
    ```
    apiVersion: chaos-mesh.org/v1alpha1
    kind: PodChaos
    metadata:
    name: pod-failure-details
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

3. Verify the PodChaos experiment is running

    After applying the PodChaos experiment, ensure that it has been successfully created and is active. We can verify the status and get details using the following command:

    `kubectl describe podchaos pod-failure-details -n chaos-testing`{{exec}}
    
    This command provides insights into the experiment's specifications, current status, and any events related to it.

4. Monitor the impact on the Bookinfo application
    
    While the PodChaos experiment is active, monitor the Bookinfo application's behaviour to observe how it handles pod failures.
    If you refresh the page, you can see that the 'details' section (left column) is unavailable.


### Experiment 2 – NetworkChaos  
By creating a NetworkChaos experiment, we can simulate a network fault scenario for a cluster. Currently, NetworkChaos supports the following fault types:
- Partition: network disconnection and partition.
- Net Emulation: poor network conditions, such as high delays, high packet loss rate, packet reordering, and so on.
- Bandwidth: limit the communication bandwidth between nodes.

We will create a net emulation experiment.

To Create a NetworkChaos experiment we have to do these steps:
1. Create the experiment by  using the YAML file to introduce a network delay
    
    Write the experiment configuration to the `network-delay-productpage.yaml` file, as shown below:

    ```
    apiVersion: chaos-mesh.org/v1alpha1
    kind: NetworkChaos
    metadata:
    name: network-delay-productpage
    namespace: chaos-testing
    spec:
    action: delay
    mode: one
    selector:
        namespaces:
        - default
        labelSelectors:
        "app": "productpage"
        "version": "v1"
    delay:
        latency: "10s"
        jitter: "500ms"
    duration: "2m"
    ```{{copy}}

The provided YAML file  targets a specific Pod (with app=productpage and version=v1 labels in the default namespace) and adds a 10-second latency (with up to 500ms jitter) to its network traffic. The experiment runs for 2 minutes. The configuration helps simulate real-world network delays to test the resilience of our application under adverse conditions.

2. Apply the NetworkChaos  Experiment
    
    Run the following command to apply the NetworkChaos experiment:
    `kubectl apply -f network-delay-productpage.yaml`{{exec}}

3. Verify the PodChaos experiment is running

    After applying the NetworkChaos experiment, ensure that it has been successfully created and is active by verifying the status and getting details using the following command:

    `kubectl describe networkchaos network-delay-productpage -n chaos-testing`

    This command provides insights into the experiment's specifications, current status, and any events related to it.

4. Monitor the impact on the Bookinfo application
    
    While the NetworkChaos experiment is active, monitor the Bookinfo application's behaviour to observe how it handles network delays.
    If you refresh the page, you can see that it takes longer to load, and it maybe gives a 502 error.
