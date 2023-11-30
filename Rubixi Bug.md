In the Rubixi incidence, the developer changed the contract’s name from `Dynamic Pyramid` to `Rubixi`. However, he forgot to rename his constructor function from `DynamicPyramid()` to `Rubixi()`.

Adversaries were then able to call the now publicly invokable `DynamicPyramid()` function to gain control of the contract and transfer its ethers out.