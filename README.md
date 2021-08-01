Last updated 2021 July

## What is QAOA?

"Quantum Approximate Optimization Algorithm"

* Protocol for approximately solving an optimization problem on a quantum computer
* Can be implemented on near-term devices!
* Variable parameter $$p$$ (depth) -- the optimal performance increases with $$p$$ and solves the problem as $$p \to \infty$$
* In order to use this algorithm optimally, you have to tune $$2p$$ parameters (this gets hard as $$p$$ grows!)
* Some tutorials [here](https://pennylane.ai/qml/demos/tutorial_qaoa_maxcut.html) and [here](https://qiskit.org/textbook/ch-applications/qaoa.html)
* This is a 'local' algorithm, which may limit its power.

## Performance analysis at low depth

| Problem             | QAOA depth | Graph                                       | optimal performance (satisfying fraction) | best known algorithm?               | Papers                                                                                                                                           |
|---------------------|------------|---------------------------------------------|-------------------------------------------|-------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| MAX-CUT             | 1          | triangle-free                               | $$1/2 + 0.30/\sqrt{D}$$                   | No                                  | [Wang+ 2018](https://arxiv.org/abs/1706.02998), [Hastings 2019](https://arxiv.org/abs/1905.07047)                                                |
| MAX-CUT             | 1          | any                                         | see formula                               | ? (KM project in progress)          | [Wang+ 2018](https://arxiv.org/abs/1706.02998), [Hastings 2019](https://arxiv.org/abs/1905.07047)                                                |
| MAX-CUT             | 2          | girth above 5                               | $$1/2 + 0.41/\sqrt{D}$$                   | No                                  | [Marwaha 2021](https://arxiv.org/abs/2101.05513)                                                                                                 |
| MAX-3-XOR (E3LIN2)  | 1          | no overlapping constraints, or random signs | at least $$1/2 + O(1/\sqrt{D})$$          | ? (KM project in progress)          | [Farhi+ 2015](https://arxiv.org/abs/1412.6062v2), [Barak+ 2015](https://arxiv.org/abs/1505.03424), [Lin+ 2016](https://arxiv.org/abs/1601.01744) |
| MAX-3-XOR (E3LIN2)  | 1          | any                                         | at least $$1/2 + O(1/\sqrt{D log D})$$    | so far, No                          | [Farhi+ 2015](https://arxiv.org/abs/1412.6062v2), [Barak+ 2015](https://arxiv.org/abs/1505.03424)                                                |
| SK Model            | 1-12       | infinite size                               | see formula                               | ?                                   | [Farhi+ 2019](https://arxiv.org/abs/1910.08187)                                                                                                  |
| Max Independent Set | 1          | any                                         | at least $$O(n/d)$$                       | so far, No (KM project in progress) | [Farhi+ 2020](https://arxiv.org/abs/2004.09002)                                                                                                  |

## Choosing optimal parameters
At high depth, the algorithm's success is dependent on choosing the best parameters. Strategies to make this easier include:
* Fourier method and interpolation method ([Zhou+ 2018](https://arxiv.org/abs/1812.01041))
* Variational methods that alternate between a computer and quantum computer ([Farhi+ 2014](https://arxiv.org/abs/1411.4028))

There are some studies of optimal QAOA parameters for some graphs being transferable to other graphs (instance independence); see [Galda+ 2021](https://arxiv.org/abs/2106.07531) or [Brandao+ 2018](https://arxiv.org/abs/1812.04170)


## Extensions to QAOA

Several adjustments to QAOA have been proposed to get around limitations:
* ST-QAOA [Wurtz+ 2021a](https://arxiv.org/abs/2103.17065)
* CD-QAOA [Wurtz+ 2021b](https://arxiv.org/abs/2106.15645)
* Alternating Operator Ansatz [Hadfield+ 2017](https://arxiv.org/abs/1709.03489), [Hadfield+ 2021](https://arxiv.org/abs/2105.06996)
* RQAOA [Bravyi+ 2019](https://arxiv.org/abs/1910.08980)


## Open problems

* Which problems and families of instances can we show that QAOA provides advantage over classical algorithms?

* How can we analyze QAOA at higher depth?

* How can we better choose the QAOA parameters?