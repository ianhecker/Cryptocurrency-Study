# Smart Contract Implementation Languages
This paper serves as a source of compliation for languages used to implement smart contracts, and information about them. It also answers the questions:
+ What smart contract implementations are out there?
    + And if they dont use Solidity, can they still compile down into Ethereum bytecode? 

### Table of Contents
+ [Ethereum Smart Contracts](#ethereum-smart-contracts)
    + [EVM Bytecode](#evm-bytecode)
+ [Smart Contract Languages](#smart-contract-languages)
    + [Bitcoin Script](#bitcoin-script)
    + [Ethereum: Serpent](#ethereum-serpent)
    + [Ethereum: Vyper](#ethereum-vyper)
    + [Solidity](#solidity)
    + [Scilla](#scilla)


## Ethereum Smart Contracts
Ethereum's blockchain requires smart contracts to be compiled into EVM bytecode before they can be added to a block and ran by miners in an EVM, or **E**thereum **V**irtual **M**achine. EVM bytecode is an assembly language, using opcodes to perform instructions like allocating registers of memory or multiplying integers [[3]](#references). 

Languages such as: [Serpent](#ethereum-serpent), [Vyper](#ethereum-vyper), & [Solidity](#solidity) utilize compilers to change their high-level code down into EVM Bytecode. 

### EVM Bytecode


## Smart Contract Languages
### Bitcoin Script
Bitcoin script is a rather simple language for implementing smart contracts. Validity conditions must be met before any coins can be transfered, from and to, accounts. 

### Ethereum: Serpent
Ethereum's first language for smart contracts was a Turing-complete language, Serpent. This language allows loops, which presents both benefits and drawbacks. Among drawbacks, chiefly is the potential for infinite loops. Because Ethereum contracts use "gas" as payment to miners for executing code, "gas" could run out via an infinite loop and present no further incentive to miners to continue executing a smart contract [[1]](#references).

### Ethereum: Vyper
Ethereum supports an open-source project of a new "pythonic" language called Vyper. This language came after the creation of Serpent. [[2]](#references)

### Solidity
Solidity has become the forefront of smart contract languages. The Ethereum project now officially supports Solidity, with its documentation available [here.](https://solidity.readthedocs.io/en/v0.5.7/) 

### Scilla
Scilla is an intermediate-level language designed to be compiled from a higher-level language, and then compiled further into executable bytecode. This language was proposed by a team of researchers as a remedy to smart-contract implementation language failures, such as the famous DAO Ethereum theft from a non-tail call in a function to another contract [[4]](#references)[[5]](#references). Scilla aims to offer formal verification of smart contracts before their immmutable addition to the blockchain [[6]](#references). 

#### Scilla Principles

+ **Separation between Computation and Communication**

    Contracts are thought of as *communicating automata* in Scilla. All computations are independent of each other as *standalone atomic transistions*. This separated and automata structure allows division of contract-specific logic from blockchain interactions for cleaner reasoning [[4]](#references).

+ **Separation between effectful and pure computations**

    Types of computation are divided into categories: 
    + Pure Expressions (primitive data type expressions and maps)
    + Impure Local State Manipulations (reading/writing fields of a contract)
    + Blockchain Reflections (reading number of current block, etc.)

    Semantics between these types of computations, Scilla ensures type preservation [[4]](#references).

+ **Separation between invocation and continuation**

    Scilla uses CPS, or **C**ontinuation-**P**assing **S**tyle. CPS is when contracts are built as communicating automata, and calls made to one another can be performed absolutely last. This can increase difficulty due to a change in style of coding, but is provided a solution via invoked *continuations* from the execution environment. This allows translations from Solidity to Scilla. Additionally, the use of automata structures aids in analysis, testing, and verification of contracts [[4]](#references).

#### Scilla Implementation
+ **Implicitly declared fields**
    
    All Scilla contracts when initialized create a public and mutable field called *balance* that holds the contract's funds. This field can only be editted through explicitly moving funds to other accounts, which are held in *uints*, or 32 bit **U**nsigned **Int**egers [[4]](#references). 

+ **Transitions**

    Transitions are simliar to Solidity methods or functions, and is the lingo for the Scilla "functions". This is because of the automata formatting of Scilla code and contracts [[4]](#references). Formally, when moving to a new state in automata, it is said you are "transitioning". 

    Additionally, the term transition refers to the atomicity of the computations being performed. Each transition only changes the state of the contract, not altering other parties or contracts in the process. To reiterate, Scilla contracts do not interact with other contracts during computations, only at the very beginning or end, through initiation of the contract or post-computation communication to others [[4]](#references). 

    Transitions are activated with a transaction from another party, that contains a *tag* of type string to specifically call a transition. These messages could also contain other fields to suit a transition's parameters, and common ones are: 
    + *sender*: the address of the messenger
    + *value* : an uint of some transaction amount

+ **Looping**

    Looping constructs are handled with recursive definitions. This allows finality of a looping definition to be proven statically during formal verification [[4]](#references). 

+ **Control Flow**

    Scilla does not allow calls to other contracts within its transistions, only through the sending of a message. This is to prevent the famous $60 mil dollar DAO theft [[5]](#reference) from reoccuring via a call to another contract inside of a function, just like it happened in Solidity. This solution is  called a *tail-call*, and is now considered a best practice in Solidity [[4]](#references). 









































## References
[1] [Vitalik Buterin. A Next-Generation Smart Contract and Decentralized Application Platform, 2014.](https://cryptorating.eu/whitepapers/Ethereum/Ethereum_white_paper.pdf) 

[2] [ethereum/vyper github README.md](https://github.com/ethereum/vyper)

[3] [Compiling and Smart Contracts: ABI Explained](https://www.sitepoint.com/compiling-smart-contracts-abi/)

[4] [Ilya Sergey, Amrit Kumar, and Aquinas Hobor. Scilla: a smart contract intermediate-level language., 2018.](https://arxiv.org/abs/1801.00687)

[5] [Michael del Castillo.The DAO Attacked: Code Issue Leads to $60 Million Ether Theft, 2016.](https://www.coindesk.com/dao-attacked-code-issue-leads-60-million-ether-theft)

[6] [Emerging Scilla & Vyper Programming Languages for Smart Contracts](https://blockonomi.com/scilla-vyper/)