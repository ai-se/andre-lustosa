
# **Basics**
* divide data into 4 bins for hyperparams for effort estimation
* 10 params 4 values
* Get 10^3 configs out of these
* Run **SNEAK 2.0**  both with oracle (20 times per dataset)
* Run **SNEAK 2.0**  mannualy (a few per dataset)

# **SNEAK 2.0** 
Introduce Sym Num Structure from ASE21

Switch to numerical division in fastmap

Cluster the world (Global sway tree from **SNEAK** )

Use **SNEAK**  heuristics to figure out the best node to talk about.
On that node:
* We will discretize the world here on each side and figure out ranges for parent for east and for west.
* Using the discretizing (get ranges, merge ranges) methods from ASE21
*  Select the variable containing a range that has the best expected information gain (largest entropy)
* From here we have to somehow evaluate wether this range is  `good` or  `bad` 

From here we have two possible paths.

## **Path A:** 

We will remake the subtree from the parent node using this best range only for the  `good` part and dropping the  `bad` part.
  * This assumes a few things:

    1) Fastmap (Heuristic method) was correct when separating the current node to the ones not below it (Might not have)
    2) This range is only best for things below the parent node.
    3) Remaking the subtree under that node using those ranges is feasible through **SNEAK 2.0**

From there we jump into a new loop iteration until **SNEAK**  metrics run out of interesting questions to ask.

## **Path B:** 
Once we obtain the best range and wether it selects for  `good` or  `bad`. We eliminate all of the datapoints in bad. We use the surviving datapoints to rebuild a cluster tree from scratch (**SNEAK**)

  * This assumes a few things:

    1) Fastmap, being a heuristic method, is not to be trusted. As we are aware Fastmap's first cuts often do not perform the best split of the data. Containing heuristic biases that can miss important information. Once we are down a few recursive steps however, this changes. As such, the best range obtained from the selected node through **SNEAK**'s heuristics are more relevant than the original definition.
    2) This range is the best for the entire dataset.

  * After generating the new cluster-tree we run the first iteration of **SNEAK** on it again.

From there we jump into a new loop iteration until **SNEAK**  metrics run out of interesting questions to ask.
           
# EVALUATION ON **SNEAK 2.0** 
To evaluate on **SNEAK 2.0**  we must opt between using **X-Reasons** or **Y-Reasons**. For each of those there are different ways to go about it listed below.



## **X-Reasons:**
For evaluation within X-Reasons we have two options:
1. **Human Interaction** (iSBSE, **SNEAK**  technique):
    * This assumes that humans are capable of evaluating the variables and how they relate to the task _(Assumes there are experts)_.
    * We might need experts in the model, the task and the dataset simultaneously.
2. **Automatically Generated Knowledge Base**(KB):
    * **Generating the KB**
      * To generate this KB we must acquire multiple datasets for a given task.
      * We also need multiple _state of the art_ (SOTA) hyperparameter optimizers.
      * We then select a single model.
      * We do multiple runs of each SOTA optimizer on each dataset.
      * We log all of the parameters generated and tested and their results.
      * We now have many different parameter configuration mappings and their Y-Values. This is done once per model/task pair that we wish to build a KB for.
   * **Using the KB**:
     * Once we have the KB, for any given configuration in the dataset we can use clustering methods to retrieve an estimate of performance for that configuration.
     * This KB is dataset agnostic. As such we can reuse this KB for any dataset given we are using the same model/task pair.


## **Y-Reasons:**
To evaluate on Y-Reasons a given configuration we:
* Train the model.
* Test the model and return the metrics from the test set.

There are many different ways of doing these two steps above, and we should configure our test/train rig depending on our cpu-time constraints.

