# Smart Contract Implementation Languages
This paper serves as a source of compliation for languages used to implement smart contracts, and information about them. It also answers the questions:
+ What smart contract implementations are out there?
    + And if they dont use Solidity, can they still compile down into Ethereum bytecode? 

### Table of Contents
+ [Ethereum Smart Contracts](#ethereum\ smart\ contracts)
    + [EVM Bytecode](#evm\ bytecode)
+ [Smart Contract Languages](#smart\ contract\ languages)
    + [Bitcoin Script](#bitcoin\ script)
    + [Ethereum: Serpent](#ethereum:\ serpent)
    + [Ethereum: Vyper](#ethereum:\ vyper)
    + [Solidity](#solidity)
    + [Scilla](#scilla)


## Ethereum Smart Contracts
Ethereum's blockchain requires smart contracts to be compiled into EVM bytecode before they can be added to a block and ran by miners in an EVM, or **E**thereum **V**irtual **M**achine. EVM bytecode is an assembly language, using opcodes to perform instructions like allocating registers of memory or multiplying integers [[3]](#references). 

Languages such as: [Serpent](#ethereum:\ serpent), [Vyper](#ethereum:\ vyper), & [Solidity](#solidity) utilize compilers to change their high-level code down into EVM Bytecode. 

### EVM Bytecode


## Smart Contract Languages
### Bitcoin Script
Bitcoin script is a rather simple language for implementing smart contracts. Validity conditions must be met before any coins can be transfered, from and to, accounts. 

### Ethereum: Serpent
Ethereum offered a Turing-complete language, Serpent, to implement contracts in. This language allowwed loops, which presented both benefits and drawbacks. Among drawbacks, chiefly was the potential for infinite loops. Because Ethereum contracts use "gas" as payment to miners for executing code, "gas" could run out via an infinite loop and present no further incentive to miners to continue executing a smart contract [[1]](#references).

### Ethereum: Vyper
Ethereum supports an open-source project of a new "pythonic" language called Vyper. This language came after the creation of Serpent. [[2]](#references)

### Solidity
Solidity has become the forefront of smart contract languages. The Ethereum project now officially supports Solidity, with its documentation available [here.](https://solidity.readthedocs.io/en/v0.5.7/) 

### Scilla
Scilla is an intermediate-level language designed to be compiled from a higher-level language, and then compiled further into executable bytecode [[4]](#references). It is designed to possess the following qualities:
+ **Separation between Computation and Communication**

    Contracts are thought of as *communicating automata* in Scilla. All computations are independent of each other as *standalone atomic transistions*. This separated and automata structure allows partioning of contract-specific logic for cleaner reasoning. [[4]](#references)

+ **Seraration between effectful and pure computations**

    

+ **Separation between invocation and continuation**



## References
[1] [Vitalik Buterin. A Next-Generation Smart Contract and Decentralized Application Platform, 2014.](https://cryptorating.eu/whitepapers/Ethereum/Ethereum_white_paper.pdf) 

[2] [ethereum/vyper github README.md](https://github.com/ethereum/vyper)

[3] [Compiling and Smart Contracts: ABI Explained](https://www.sitepoint.com/compiling-smart-contracts-abi/)

[4] [Sergey, Ilya, Amrit Kumar, and Aquinas Hobor. Scilla: a smart contract intermediate-level language., 2018.](https://arxiv.org/abs/1801.00687)

[5] []()