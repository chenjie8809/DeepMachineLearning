# TensorFlow Based Deep Machine Learning (ML) For Power System Analysis

The purpose of this InterPSS project is to explore the possibility to apply the Deep Machine Learning (ML) approach to power system analysis. Google's [TensorFlow](https://www.tensorflow.org/) is used as the ML engine, while InterPSS is used to provide power system analysis/simulation model service: 1) to generate training data to train the neural network model; 2) provide service for checking model prediction accuracy. 

## System Architecture

![architecture](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/dmp_architecture.png)

InterPSS ML system architecture is shown in the above figure. It includes the following three main components:

* **Google [TensorFlow](https://www.tensorflow.org/) ML engine**

TensorFlow is an open source software library by Google for numerical computation using data flow graphs. Nodes in the graph represent mathematical operations, while the graph edges represent the multidimensional data arrays (tensors) communicated between them. The flexible architecture allows you to deploy computation to one or more CPUs or GPUs. See the [Installation and Configuration](https://github.com/interpss/DeepMachineLearning/wiki/Runtime-Env-Setup#installation-and-configuration) page for instructions to install and configure TensorFlow on a Windows environment.   

* **InterPSS Power System Analysis Model Service**

The purpose is to apply ML to power system analysis. The Power System Model Service module provides service to the TensorFlow ML engine. The analysis model is based on the [InterPSS object model](www.interpss.org) written in Java. The default TensorFlow programming language is Python. [Py4J](https://www.py4j.org/) is used to bridge the communication between TensorFlow Python runtime env and InterPSS Java runtime env. 

* **Pluggable Training Data Generator**

The NN model is first trained and then used for power system analysis, for example, predicting bus voltage. An NN model is in general trained for certain application purpose using a set of training data relevent to the problem to solve. The system architecture allows different traning case generator to be plugged-in for different model training purposes. A  [Training Case Generator Interface](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/src/org/interpss/service/train/ITrainCaseBuilder.java) is defined for the traning case generator implementation.    


## Neural Network (NN) Model

Neural networks typically consist of multiple layers, and the signal path traverses from the input, to the output layer of neural units. Back propagation is the use of forward stimulation to reset weights on the "front" neural units and this is sometimes done in combination with training or optimization where the correct result is known.

![nn_model](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/dmp_nn_layer.png)

A typical NN model is shown in the above figure. The output of a previous layer [x] is weighted-summarised to produce [y], and then feed the next layer.  


```      
                [y] = [W][x] + [b]
where, [W] - weight matrix
       [b] - bias vector
```

## Sample Case

In Loadflow study, network bus voltage is solved for a set of bus power (P,Q) as the input. Here we the [IEEE 14-Bus System](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/IEEE14Bus.jpg) to demonstrate how to apply TensorFlow to power system analysis to predict network bus voltage or branch active power flow based on the bus power input.

![net diagram](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/IEEE14Bus_small.jpg)

In the sample case, power is flowing from the Gen Area to the Load Area, as shown in the above figure. We wand to train NN model to predict network loadflow solution, for example, bus voltage or branch active power flow, when the total power flow from the Gen Area to the Load Area is adjusted, for example, by a scaling factor of 20% increase.

```      
           [P,Q] =>  [  NN Model ]  => [Bus Voltage] or [Branch P Flow]
```

In the sample bus load [P,Q] in the sample network is scaled by multipling a factor in range (0.5~1.5) to create a set of traning cases to train the NN model. To test the accuracy of the NN model bus voltage prediction, a random scaling factor in range (0.5~1.5) is used to create bus power [P,Q] as the input. The bus power [P, Q] is fed into the NN model to get a bus voltage [Vmsg, Vang] prediction.   Then the bus voltage predition is appled to the power system analysis model to calculation bus mismatch info, as follows: 

```
   dPmax :  0.00602 (pu) at Bus : Bus4,     dQmax :  0.00454 (pu) at Bus : Bus5
```

* **Bus Voltage Prediction**

![Result Comparison](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/dmp_busresult.png)

![Bus mismatch info](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/dmp_busmismatch.png)

After the training, the nn model is used to predict network bus voltage when network bus power is given. Network bus voltage prediction accuracy using the nn model is summarized in the above figure.

* **Branch Active Power Flow Prediction**

![Result Comparison](https://github.com/interpss/DeepMachineLearning/blob/master/ipss.dml/doc/image/dmp_branchresult.png)

## Links and References

* [Project Wiki](https://github.com/interpss/DeepMachineLearning/wiki)
