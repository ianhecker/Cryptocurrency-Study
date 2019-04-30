# Smart Contract Implementation Languages
This paper serves as a source of compliation on information about languages used to implement smart contracts. It aims to answer the questions:

+ What smart contract implementation languages are out there?
    + Can they compile down into Ethereum bytecode? 

### Table of Contents
+ [Ethereum Smart Contracts](#ethereum-smart-contracts)
    + [EVM Bytecode](#evm-bytecode)
+ [Smart Contract Verification](#smart-contract-verification)
+ [Smart Contract Languages](#smart-contract-languages)
    + [Ethereum: Serpent](#ethereum-serpent)
    + [Ethereum: Vyper](#ethereum-vyper)
    + [Solidity](#solidity)
    + [Scilla](#scilla)
    + [Bamboo](#bamboo)


## Ethereum Smart Contracts
Ethereum's blockchain requires smart contracts to be compiled into EVM bytecode before they can be added to a block and ran by miners in an EVM, or **E**thereum **V**irtual **M**achine. EVM bytecode is an assembly language, using opcodes to perform instructions like allocating registers of memory or multiplying integers [[3]](#references). 

Languages such as: [Serpent](#ethereum-serpent), [Vyper](#ethereum-vyper), [Bamboo](#bamboo) & [Solidity](#solidity) Are able to compile their code into EVM Bytecode. 

### EVM Bytecode
**E**thereum **V**irtual **M**achine Bytecode is specially designed assembly code by the Ethereum project. 

## Smart Contract Verification
### Formal Verification
The semantics of Smart Contracts needs to be ensured to be exactly as imagined by developers. After committing to a blockchain, the contract will be immutable. The most famous attack on the Ethereum platform:

+ Re-entrance Attack
    + In 2016, an attack on DAO, or **D**ecentralized **A**utonomous **O**rganization, was performed. The DAO was created as an open-source crowd-funding platform that would vote on projects to fund. The attack used recursive re-entrance via a call to the DAO contract to siphon off a third of all the ether, 3.6 million, into a seperate DAO account. The account had a 28 day holding period, as specified by the DAO contract, and was subsequently recovered [[9]](#references) [[10]](#references). 

In order to prevent attacks or contract errors semantics must be proven with proofs beforehand. 
+ Scilla uses a tool called Coq to translate Scilla into a functional language called Gallina to verify semantics [[4]](#references).
+ The paper [Formal Verification of Smart Contracts](https://dl.acm.org/citation.cfm?id=2993611) outlines a framework to analyze and verify contracts' runtime and correctness through translation to F*, a functional language [[11]](#references). 
+ Solidity

## Smart Contract Languages
### Ethereum: Serpent
Ethereum's first language for smart contracts was a Turing-complete language, Serpent. This language allows loops, which presents both benefits and drawbacks. Among drawbacks, chiefly is the potential for infinite loops. Because Ethereum contracts use "gas" as payment to miners for executing code, "gas" could run out via an infinite loop and error out the contract. This error would cause the caller to lose the subsequent gas [[9]](#references) [[1]](#references).

### Ethereum: Vyper
Ethereum supports an open-source project of a new "pythonic" language called Vyper. This language came after the creation of Serpent. [[2]](#references)

### Solidity
Solidity has become the forefront of smart contract languages. The Ethereum project now officially supports Solidity, with its documentation available [here.](https://solidity.readthedocs.io/en/v0.5.7/) 

### Scilla
Scilla is an intermediate-level, Turing incomplete language designed to be compiled from a higher-level language, and then compiled further into executable bytecode. This language was proposed by a team of researchers as a remedy to smart-contract implementation language failures, such as the famous DAO Ethereum theft from a non-tail call in a function to another contract [[4]](#references)[[5]](#references). Scilla aims to offer formal verification of smart contracts before their immmutable addition to the blockchain [[6]](#references). 

#### Scilla Principles

+ **Separation between Computation and Communication**

    Contracts are thought of as *communicating automata* in Scilla. All computations are independent of each other as *standalone atomic transistions*. These separated automata structures allow division of contract-specific logic from blockchain interactions for cleaner reasoning [[4]](#references).

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

    Transitions are simliar to Solidity methods or functions, and is the lingo for a Scilla "function". This is because of the automata formatting of Scilla code and contracts [[4]](#references). Formally, when moving to a new state in automata, it is said you are "transitioning". 

    Additionally, the term transition refers to the atomicity of the computations being performed. Each transition only changes the state of the contract, not altering other parties or contracts in the process. To reiterate, Scilla contracts do not interact with other contracts during computations, only at the very beginning or end, through initiation of the contract or post-computation communication to others [[4]](#references). 

    Transitions are activated with a transaction from another party, and therefore are only explicitly called. Transaction parameters are formatted to accept data from a message, and contain a *tag* of type string that specificies which contract transition. The parameters can also contain other fields for data from messages, including: 
    + *sender*: the address of the messenger
    + *value* : an uint of some transaction amount
    + etc.
    
    When a transition performs a *send*, or a transaction to another entity, the final field will be either a *continuation* or the constant *MT* that specifies to call no continuation, and hence perform no more computations. 

+ **Continuations**

    Continuations can be considered "remaining computations" to do after a call to another contract. While Scilla enforces non-tail calls from transitions, it allows transitions to explicitly call a continuation post-call of another contract to perform computations [[4]](#references).

    ```scilla
    (* Example code taken from Scilla Whitepaper [4] *)

    (* Specifying a  continuation in a Caller contract *)
    continuationUseResult (res : uint)
        send(<to→owner, amount→0, tag→"main", msg→res>, MT)
        
    (* Using a continuation in a transition of Caller *)
    transitionClientTransition
        (sender : address, value : uint, tag : string)
        (* code of the transition *)
        send(<to→sender, amount→0,
            tag→"main", msg→res>, UseResult)
    
    (* Returning a result in a callee contract  *)    
    transitionServerTransition
        (sender : address, value : uint, tag : string)
        (* code of the transition *)
        return value
    ```

+ **Looping**

    Looping constructs are handled with recursive definitions. This allows finality of a looping definition to be proven statically during formal verification [[4]](#references). 

+ **Control Flow**

    Scilla does not allow calls to other contracts within its transistions, only through the sending of a message. This is to prevent another occurence of the famous $60 mil dollar DAO theft [[5]](#reference) from reoccuring. This exploit occured via a call to another contract inside of a function, which lead to contract-state manipulation in a "re-entry attack". The solution is called a *tail-call*, where other contracts are called post-transition and is now considered a best practice in Solidity [[4]](#references). 

#### Scilla Formal Verification
+ **Coq Proof Assistant**

    Coq Proof Assistant is a theorem prover tool with dependent-type theory used to verify programs using mathematical libraries. Scilla is being developed hand-in-hand with this tool. Semantics are modeled into Coq, using Coq's specification language called Gallina [[4]](#references).

### Bamboo

Bamboo is very similar in structure to Scilla and similarly attempts to solve re-entrance problems. Bamboo has even started to produce (with limited success) EVM bytecode that "ran as expected" [[7]](#references). The OCaml compiler still remains buggy, and is now maintained by Cornell Blockchain in [this github repository](#https://github.com/CornellBlockchain/bamboo). Bamboo is only at version 0.0.03, with a year since the last commit made to the forked branch made by Cornell Blockchain [[8]](#references). 

---
## References
[1] [Vitalik Buterin. A Next-Generation Smart Contract and Decentralized Application Platform, 2014.](https://cryptorating.eu/whitepapers/Ethereum/Ethereum_white_paper.pdf) 

[2] [ethereum/vyper github README.md](https://github.com/ethereum/vyper)

[3] [Compiling and Smart Contracts: ABI Explained](https://www.sitepoint.com/compiling-smart-contracts-abi/)

[4] [Ilya Sergey, Amrit Kumar, and Aquinas Hobor. Scilla: a smart contract intermediate-level language., 2018.](https://arxiv.org/abs/1801.00687)

[5] [Michael del Castillo. The DAO Attacked: Code Issue Leads to $60 Million Ether Theft, 2016.](https://www.coindesk.com/dao-attacked-code-issue-leads-60-million-ether-theft)

[6] [Emerging Scilla & Vyper Programming Languages for Smart Contracts](https://blockonomi.com/scilla-vyper/)

[7] [Yoichi Hirai. Bamboo compiler started producing EVM bytecode, 2017.](https://medium.com/@pirapira/bamboo-compiler-started-producing-evm-bytecode-6a55e4633de9)

[8] [Bamboo Compiler maintained by Cornell Blockchain](https://github.com/CornellBlockchain/bamboo)

[9] [Atzei, Nicola, Massimo Bartoletti, and Tiziana Cimoli. A Survey of Attacks on Ethereum Smart Contracts (SOK), 2017.](https://link.springer.com/chapter/10.1007/978-3-662-54455-6_8)

[10] [The DAO (Organization) Wikipedia](https://en.wikipedia.org/wiki/The_DAO_(organization)#cite_note-FT-TheDao20160517-6)

[11] [Bhargavan, Karthikeyan, Antoine Delignat-Lavaud, Cédric Fournet, Anitha Gollamudi, Georges Gonthier, Nadim Kobeissi, Natalia Kulatova et al. Formal Verification of Smart Contracts: Short Paper, 2016.](https://dl.acm.org/citation.cfm?id=2993611)