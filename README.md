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

There are some studies of optimal QAOA parameters for some graphs being transferable to other graphs (instance independence); see [Galda+ 2021](https://arxiv.org/abs/2106.07531), [Brandao+ 2018](https://arxiv.org/abs/1812.04170), or [Wurtz+ 2021](https://arxiv.org/abs/2107.00677).


It would be nice to discuss some results on barren plateaus here, and also mention what happens to error.

## Extensions to QAOA

Several adjustments to QAOA have been proposed:
* ST-QAOA [Wurtz+ 2021a](https://arxiv.org/abs/2103.17065)
* CD-QAOA [Wurtz+ 2021b](https://arxiv.org/abs/2106.15645)
* Alternating Operator Ansatz [Hadfield+ 2017](https://arxiv.org/abs/1709.03489), [Hadfield+ 2021](https://arxiv.org/abs/2105.06996)
* RQAOA [Bravyi+ 2019](https://arxiv.org/abs/1910.08980)

Some of these extensions are "true extensions" (sampling things from quantum computers), such as penalty proposals and adjusting the mixers; while others are new protocols that use QAOA inside of it (CD-QAOA, RQAOA).

Some of these protocols turn QAOA into a non-local algorithm.

## Open problems

* Which problems and families of instances can we show that QAOA provides advantage over classical algorithms?
* How can we analyze QAOA at higher depth?
* How can we better choose the QAOA parameters?

## Popular, incorrect statements about QAOA

####  WRONG: MAX-CUT is an interesting problem for quantum advantage
This is the case because...

####  WRONG: VQE is the same as QAOA
It's worth making clear similarities and differences between VQE (e.g. electronic structure) and QAOA (diagonal eigenvalue problems vs non-diagonal eigenvalue problems)

In VQE -- the ansatz holds something I can't hold classically, so intermediate strings don't tell me what the state is.

####  WRONG: The expected performance is what matters

You really want the highest value the QAOA returns; when you run the experiment many times, you take the best one, not the average one. But we use the average because it's easier to calculate, and chebyshev & chernoff to get bound on tail.

The QAOA may also perform well even if the overlap with optimal value is low.

There are alternate metrics of performance, such as ___.

####  WRONG: QAOA is just adiabatic computation

Although there are many similarities, there are settings where QAOA improves upon adiabatic computation. See [Zhou+ 2018](https://arxiv.org/abs/1812.01041)