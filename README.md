<p align="center">
  <a href="https://discord.gg/RbeQMu886J">Join the community</a> •
  <a href="#contribute">Contribute to the library</a>
</p>

<img height="25" width="100%" src="https://user-images.githubusercontent.com/83510798/171454644-d4b980bc-15ab-4a31-847c-75c36c5bd96b.png">


# Learning AI optimization
This library aims to become a curated list of awesome material (cool papers, tutorials, courses, etc.) on optimization techniques to make artificial intelligence faster and more efficient 🚀 

Among the best acceleration techniques for deep learning are:
- [ ] Deep learning compilers
- [x] [Quantization](#quantization)
- [ ] Sparsity
- [ ] Distillation
- [ ] Cached datasets

And many others. Do you have any idea on material to expand the list? We are welcoming contributions! And don't forget to leave a star if you found this library insightful ⭐


# Contribute

We welcome support from the open-source community in any form 🥰

We have recently published this library. We will continue to update and upgrade it with great material on how to accelerate artificial intelligence models and make them ever more efficient. We also hope you will also learn with us and help us keep the library populated with the best quality content.

Open an [issue](https://github.com/nebuly-ai/learning-AI-optimization/issues) or drop a message in the <a href="https://discord.gg/jyjtZTPyHS">community</a> channel to
- Add topics you would like to be covered
- Ask any questions
- Report a typo
- Propose to include your research work


Fork the library, make the changes, and send us a [pull request](https://github.com/nebuly-ai/learning-AI-optimization/pulls) in case you want to
- Write new content on AI optimization
- Correct or improve some sections


Currently, the main contributor to the library is [Pier](https://www.linkedin.com/in/pierpaolo-sorbellini-64603012b/), and the library would not have been possible without [Diego](https://www.linkedin.com/in/diego-fiori-b64b3016a/)'s interesting insights and topic suggestions and support from the <a href="https://discord.gg/jyjtZTPyHS">community</a>. 


# The community for AI acceleration

Do you want to meet the contributors of this libraries and other developers who share the vision of an superfast and sustainable artificial intelligence? <a href="https://discord.gg/jyjtZTPyHS">Join the community</a> for AI acceleration on Discord!


<img height="25" width="100%" src="https://user-images.githubusercontent.com/83510798/171454644-d4b980bc-15ab-4a31-847c-75c36c5bd96b.png">

# Quantization
The complexity of NN models has increased significantly, which means that they cannot be used for many applications with limited resources, or even if possible, large computational resources and power are required.
Various types of optimizations are introduced for this purpose, quantization being one of them.
Quantization is a method of mapping floating-point values into integer values; a reduction in memory cost is automatically generated by the smaller amount of bits required to store the quantized value, while the decrease in energy and increase in computational speed depends on the way the operations are performed on the hardware (e.g., performing INT8 addition is 30 times more energy efficient and 116 times more area efficient than FP32 addition).
Quantization is one of the methods that can be used to compress a neural network, either during training or inference.
The application of quantization during training is quite complex and must be carefully managed to avoid divergence during training, so the most extreme quantization strategies are typically applied for inference. 
During quantization in neural networks, unlike what is commonly done in signal compression, the key aspect that must be preserved is not the precise representation of the quantized value, but accuracy. The goal is to reduce the accuracy of the parameters and activation maps, with minimal impact on the generalization/accuracy power of the model.

## Quantization techniques

Quantization techniques can be clustered by
-  [Type](#types)
-  [Granularity](#granularity)
-  [Tuning method](#fine-tuning-methods)
-  [Computational strategy](#computation-strategy)

```mermaid
flowchart LR
A[Quantization \n Inference \n Techniques] --> B[Type]
B --> BA[Uniform]
B --> BB[Non-Uniform]
BA --> BAA[Symmetric]
BA --> BAB[Asymmetric]
BA --> BAC[Static]
BA --> BAD[Dynamic]
B --> BD[Stochastic]
B --> BE[Extreme]
A --> C[Granularity]
C --> CA[Calibration]
CA --> CAA[Layer]
CA --> CAB[Group]
CA --> CAC[Channel]
CA --> CAD[Sub-Channel]
C --> CB[Mixed \n Precision]
A --> D[Tuning \n Methods]
D --> DA[Fine-Tuning]
DA --> DAA[QAT]
DA --> DAB[PTQ]
D --> DB[Tuning \n Strategy]
DB --> DC[Quantized \n Gradients]
DB --> DD[Distillation \n Assisted]
DB --> DE[Hardware \n Aware]
A --> E[Computation \n Strategy]
E --> EA[Simulated]
E --> EB[Integer \n Only]
E --> EC[Dyadic]
```

## Types

### Uniform
General quantization can be expressed as:

$$Q(r)=Int(r/S) - Z$$ 

Where:  
* $r$ is the floating point value to be quantized.
* $S$ is the scaling factor that maps the floating point value in the appropiate range for quantization.
* $Z$ is an integer applied to set the zero-point of the quantized representation.
  
And is said uniform since the spacing between two quantized values is fixed and imposed by $S$.

The scaling factor is computed as:

$$S=\frac{\beta - \alpha}{2^b - 1}$$

Where:

* $\beta$ is the upper bound that the quantized number needs to represent $r_{max}$.
* $\alpha$ is the lower bound that the quantized number needs to represent $r_{min}$.
* $b$ is the bit-width of the quantized value.

The process of selecting $a$ and $b$ is said **calibration**.

### Symmetric
In Symmetric Quantization 
$$|\beta| = |\alpha| = max(|r_{max}|,|r_{min}|)$$

This has the advantage of eliminating the need to compute $Z$ since the mapping is already centered in $0$. However, some of the resolution is wasted on representing values that are not interesting, since typically $|r_{max}| \neq|r_{min}|$.

### Asymmetric
In Asymmetric Quantization the most trivial choise could be:

$$\beta = r_{max}$$
$$\alpha = r_{min}$$

However more complex strategy can be used to improve the resolution at the price of losing some representation capability for the extreme values. Typical choise are:
* picking a percentile of the full-range.
* minimizing the K/L divergence.

The parameter $Z$ must be computed, but the accuracy of the representation is improved. This strategy is particularly effective when the constraints are strongly asymmetric, as in the case of ReLU, and $Z$ can be incorporated into the activation bias. 

### Static and Dynamic
The weights, once calculated as quantized values, their value does not change and the quantization is always static. However, the quantization of activation maps depends on the inputs and the range is not known a priori. Several strategies can be adopted in this case:

* *Dynamic Quantization:* the ranges are computed during run-time introducing overhead but high accuracy. 
* Run a series of input samples to determine resonable ranges.
* Calibrate the quantization during training.

### Non-Uniform
Instead of using a linear scale more complex strategies can be used:

* Logaritmic scale
* code-based: the quantization is expressed as a real linear combination of binary vectors. 
* computing ad-hoc scales by minimizing an optimization problem that is jointly trained with the model.
  
### Stochastic
Noting that small updates to the weights may not result in any change, since the rounding operation may always return the same weights. Different approaches can be used to avoid this situation, such as imposing that the rounding operation has a certain probability of generating an upper or lower value in the quantized scale with a certain probability. The disadvantage is that generating random numbers and evaluating this probability distribution involves some overhead. 

### Extreme
Extreme quantization occurs when the amplitude of the quantization bits is set as binary $[-1,1]$ or ternary $[-1,0,1]$ in combination with binary or ternary activation maps; usually ReLu and other activations can also be transformed into a $sign$ function. The increase in overall performance can be considerable if the hardware is well optimized for these operations; in practice any multiplication can be transformed into a simple logical XOR. Usually this type of approach also requires the use of mixed precision to avoid the loss of too much accuracy. Ternary quantization allows the zero representation to be retained, which can be used to avoid unnecessary calculations or to perform pruning and further optimization.

## Granularity

### Calibration Granularity
The granualarity used to compute the ranges $[\alpha,\beta]$ can be different:  
* **Layer** 
* **Group:** a subset of channel. 
* **Channel** 
* **Sub-Channel:** any group of parameters.

Usually the standard is to use the channel. 

### Mixed Precision
Each layer is quantized with a different bit precision and is usually done when using binary or ternary representations for quantization. The selection of this mixed precision for each layer is essentially a search problem. Typical approaches to selecting the number of bits for layer quantization are:

* Reinforment Learning.
* Second order sensitivity (Hessian) can measure the sensitivity of a layer to quantization.
* Linear Programming approaches.

## Fine-Tuning Methods

### PTQ - Post Training Quantization
PTQ consists of floating-point training and quantizing only at the end. The drop in accuracy is usually nontrivial, and some methods are used to reduce the impact of quantization:

* Equalizing the weight ranges (and implicitly activation ranges) between different layers or channels.
* Optimizing the L2 distance between the quantized tensor and the corresponding floating point tensor.
* Adapting rounding strategies instead of round-to-nearest.

### QAT - Quantization Aware Training
The main idea is to train again with quantized parameters to improve accuracy. The parameters are quantized after each gradient update. To recover accuracy, it may be necessary to perform this retraining for several hundred epochs, especially in the case of low bit-precision quantization. However, QAT produces higher accuracy than PTQ at the expense of higher overhead.

The quantization operator is not differentiable and is typically flat piecewise, so it has zero gradient. The so-called Straight Through Estimator (STE) is used to solve this problem.
The main idea is to treat the quantization gradient as an identity function. Other STE and non-STE approaches have also been proposed. 

## Tuning Strategy

### Hardware-Aware Quantization
The benefits of quantization are hardware-dependent, so the recent trend is to identify the type of quantization that can be performed based on the hardware specifications on which the model is deployed. A typical approach is to use an agent that learns which quantization steps can be performed.

### Distillation Assisted
The main idea is to incorporate model distillation to boost quantization accuracy.

### Improved Loss Function
Directly minimize the loss versus weights to improve the accuracy of determining the correct weights versus loss function instead of the accuracy of representing floating-point values.

### Quantized Gradient
To improve training time one apporach proposed is to quantize the gradients as well. 

## Computation Strategy

### Simulated 
In simulated quantization, quantized model parameters are stored in low precision, but operations (matrix multiplications and convolutions) are performed with floating-point arithmetic. Therefore, the quantized parameters must be dequantized before the floating-point operations.

### Integer-Only
In integer-only quantization, all operations are performed using low-precision integer arithmetic. Low-precision logic has many advantages over its full-precision counterpart in terms of latency, power consumption and area efficiency.

### Dyadic
All scaling operations are performed with dyadic numbers, that is, rational numbers with integer values at the numerator and a power of 2 at the denominator. This means that scaling can be performed simply by applying bitshift operations, which are extremely more efficient than divisions. All addition must have the same dyadic scale, which makes the logic of addition simpler and more efficient.


# Interesting material on quantization 

## HAWQV3: Dyadic Neural Network Quantization

[This Berkeley paper](https://arxiv.org/abs/2011.10680) proposes an approach to quantization with mixed dyadic symmetric and asymmetric quantization. It suggests an interesting strategy for selecting the quantization scheme of different layers using hardware-dependent metrics. The library is available at [HAWQ GitHub](https://github.com/Zhen-Dong/HAWQ/blob/main/ILP.ipynb).

The paper opens up many questions, and below are just a few of them.

* What are the limitations of the approach proposed in the paper? Is there room for improvement?
* Are there better schemes for performing quantization?
* Is it possible to implement this quantization approach in [nebullvm](https://github.com/nebuly-ai/nebullvm)?

Let us know your views on this topic, either by opening an issue or dropping a message on <a href="https://discord.gg/RbeQMu886J">the community channel</a>.

<img height="25" width="100%" src="https://user-images.githubusercontent.com/83510798/171454644-d4b980bc-15ab-4a31-847c-75c36c5bd96b.png">

<p align="center">
  <a href="https://discord.gg/RbeQMu886J">Join the community</a> •
  <a href="#contribute">Contribute to the library</a>
</p>
