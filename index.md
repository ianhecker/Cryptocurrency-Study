# Smart Contract Implementation Languages
This paper serves as a source of compilation on information about languages, besides Solidity, used to implement smart contracts. It aims to answer the questions:

+ What smart contract implementation languages are out there?
    + Can they compile down into Ethereum bytecode? 

---
### Table of Contents
+ [Smart Contracts](#smart-contracts-)
    + [Origin of Smart Contracts](#origin-of-smart-contracts-)
    + [Ethereum Smart Contracts](#ethereum-smart-contracts-)    
+ [Smart Contract Verification](#smart-contract-verification-)
    + [Attacks on Smart Contracts](#attacks-on-smart-contracts-)
    + [Formal Verification](#formal-verification-)
+ [Smart Contract Languages](#smart-contract-languages-)
    + [Promising Languages](#promising-languages-)
        + [Pact](#pact-)
        + [Scilla](#scilla-)
        + [Solidity](#solidity---brief-note-)
        + [Vyper](#vyper-)            
    + [Deprecated Languages](#deprecated-languages-)    
        + [Bamboo](#bamboo-)
        + [Serpent](#serpent-)
+ [References](#references-)

---        

## Smart Contracts <a href="#table-of-contents">^</a>

### Origin of Smart Contracts <a href="#table-of-contents">^</a>
The original concept of smart contracts can be credited to Nick Szabo in 1997. In [The Idea of Smart Contracts](http://www.fon.hum.uva.nl/rob/Courses/InformationInSpeech/CDROM/Literature/LOTwinterschool2006/szabo.best.vwh.net/idea.html), Szabo discusses basic ideas of contracts and real-life applications for them. He champions the vending machine as the original ancestor to contracts; accepting coins and dispensing goods when conditions are satisfied [[16]](#references). 

Coin parking meters are similar; variations of coins add time for valid parking, but it is important to note that parking meters often require a third party to verify parking has become invalid. Smart contracts can allow interactions on a blockchain without the need for a third party; using mathematically-provable semantics and cryptography as the trust mechanism between mutually-distrusting peers. 

### Ethereum Smart Contracts <a href="#table-of-contents">^</a>
Ethereum's blockchain requires smart contracts to be compiled into EVM bytecode before they can be added to a block and ran by miners in an EVM, or **E**thereum **V**irtual **M**achine. EVM bytecode is an assembly language, using opcodes to perform instructions like allocating registers of memory or multiplying integers [[3]](#references). 

Languages such as: [Serpent](#serpent), [Vyper](#vyper), [Bamboo](#bamboo) & [Solidity](#solidity) Are able to compile their code into EVM Bytecode. 

## Smart Contract Verification <a href="#table-of-contents">^</a>
### Attacks on Smart Contracts <a href="#table-of-contents">^</a>
The semantics of Smart Contracts needs to be ensured to be exactly as imagined by developers. After committing to a blockchain, the contract will be immutable. The most famous attack on the Ethereum platform:

+ Re-entrance Attack
    + In 2016, an exploit occurred of a DAO contract, or **D**ecentralized **A**utonomous **O**rganization. The DAO was created as an open-source crowd-funding platform that would vote on projects to fund. The attack used recursive re-entrance via a call to the DAO contract to siphon off a third of all the ether, 3.6 million, into a separate DAO account. The account had a 28 day holding period, as specified per the original DAO contract, and was subsequently recovered through a forking of the blockchain by the Ethereum community [[9]](#references) [[10]](#references). 

### Formal Verification <a href="#table-of-contents">^</a>
In order to prevent attacks or contract errors, semantics should be proven mathematically beforehand. 
+ Scilla uses a tool called Coq to translate Scilla into a functional language called Gallina to verify semantics [[4]](#references).
+ In [Formal Verification of Smart Contracts](https://dl.acm.org/citation.cfm?id=2993611), it  outlines a framework to analyze and verify contracts' runtime and correctness through translation to F*, a functional language [[11]](#references). 

---
## Smart Contract Languages <a href="#table-of-contents">^</a>
### **Promising Languages** <a href="#table-of-contents">^</a>

### Pact <a href="#table-of-contents">^</a>
Pact is a declarative, simplified control-flow language written for smart contract implementation in the Kadena blockchain.  [[19]](#references). The creation of Pact comes after the failure of logic in the DAO contract that utilized Solidity. [Pact's white paper](http://kadena.io/docs/Kadena-PactWhitepaper-Oct2016.pdf) states that Solidity is dangerous due to unconstrained freedom, and lack of integrated safety features. Pact follows a similar route to the language Scilla of implementing tooling for safety, while Vyper focuses on simplification for code readability and added difficulty for incorrect/malicious contracts. 

#### Pact Principles <a href="#table-of-contents">^</a>

+ **Turing-incomplete Safety**

    Turing-completeness resulted in an exploit in an extremely complex Ethereum contract, altering Ethereum forever with a community-driven fork in the blockchain. Turing-incompleteness is safer, and therefore the ethical choice in transaction-based smart contracts [[9]](#references) [[10]](#references) [[19]](#references).

+ **Human-readable Ledger Code**

    Modifying code can sometimes result in semantics not intended, so code is stored as-written on the blockchain to ensure unmodified logic [[19]](#references).

+ **Atomicity**

    Ethereum contracts can fail after altering variables, due to out-of-gas errors or faulty contract logic. Progress shouldn't be lost during contract failures, just rolled-back.

+ **Encapsulation**

    Code modules encapsulate data tables for safety, ensuring only the code modules can alter the data and not outside forces [[19]](#references).

#### Pact Implementation <a href="#table-of-contents">^</a>
+ **Contract Tables, Keysets, & Module**

    Data is stored in schema-less tables in "key-rows", allowing a versioned history via the table columns. Keysets are groups of public keys used to access data, and can be specified to be global or row-specific. Modules encapsulate data tables and keysets for data safety, and house all code. [[19]](#references).

+ **Transaction & Contract Atomicity**

    A message to the blockchain is atomic, meaning that if the message fails, the previous state before the operation is rolled-back to via the columnar history of the data table. Only the full execution of the code will allow updates. This prevents the need of the good-practice of tail-calling other transactions that Solidity strives for, and Scilla enforces [[4]](#references) [[19]](#references).

+ **Querying Contract Data**

    Data is simply exported, rather than processed inside miners. This alleviates out-of-gas problems where miners could use all the gas in Ethereum, resulting in incomplete contracts.  [[19]](#references).

+ **Bitcoin Opcode Inspiration**

    Bitcoin Script in contracts can only offer pass or fail results. However, Bitcoin's signature-verification via opcodes *CHECKSIG* & *OP_CHECKMULTISIG* inspired Pact design's keyset idea [[19]](#references).  

+ **LISP Syntax**

    With Pact code stored directly on the ledger, code takes more time to execute than bytecode would. To counter this, Pact uses LISP *s-expression* syntax that represents the AST tree to be execute. The Pact compiler offers runtime errors and detailed errors with stack tracing  [[19]](#references).

#### Pact Features Specifically Not Included <a href="#table-of-contents">^</a>
+ **Unbounded Looping & Recursion**

    This is to keep the language Turing-incomplete, and prevent errors. Bounded Looping is allowed on finite list structures [[19]](#references).

+ **Conditionals**

    Pact does include IF statements, but suggests that programmers utilize the *enforce* that can abort the transaction [[19]](#references).

+ **Nulls**

    Because Pact employs a database-like design, nulls are not allowed. Nulls also allow programmers to avoid issues behind why values are null. No nulls forces programmers to handle these scenarios [[19]](#references).

+ **Lambdas, Macros, or *eval***

    While LISP does support these, Pact does not. Lambdas' anonymous functions reduce performance but can also severely complicate scopes [[19]](#references).

#### Pact Code Example <a href="#table-of-contents">^</a>
This example is taken from [The Pact Smart-Contract Language](http://kadena.io/docs/Kadena-PactWhitepaper-Oct2016.pdf) white paper. It is a simple function for an account [[19]](#references). 


```LISP
(define-­‐keyset 'accounts-­‐admin
    (read-­‐keyset "accounts-­‐admin-­‐keyset"))
    
    (module accounts'accounts-­‐admin
    "Simple account functionality."
    
    (defun create-­‐account(address keyset)
    (insert'accounts address
        { "balance": 0, "amount": 0, "keyset": keyset,
           "note": "Created account"}))
        
    (defun transfer(src dest amount)
      "transfer AMOUNT from SRC to DEST"
     (with-­‐read 'accounts src
        { "balance":= src-­‐balance
        , "keyset":= src-­‐ks }
      (with-­‐keyset src-­‐ks  
       (check-­‐balance src-­‐balance amount)
        (with-­‐read 'accounts dest { "balance":= dest-­‐balance }
         (write 'accounts src
                { "balance": (-­‐src-­‐balance  amount)
                , "amount": (-­‐amount)
                , "note": { "transfer-­‐to":  dest } })
         (write 'accounts dest
         { "balance": (+ dest-­‐balance amount)
         , "amount": amount
         , "note": { "transfer-­‐from": src } })))))
         
    (defun check-­‐balance(balance amount)
      (enforce (<= amount balance) "Insufficient funds"))
   )
   
   (create-­‐table 'accounts 'accounts)
```

---

### Scilla <a href="#table-of-contents">^</a>
Scilla is an intermediate-level, Turing incomplete language designed to be compiled from a higher-level language, and then compiled further into executable bytecode. This language was proposed by a team of researchers as a remedy to smart-contract implementation language failures, such as the famous DAO Ethereum theft from a non-tail call in a function to another contract [[4]](#references) [[5]](#references). Scilla aims to offer formal verification of smart contracts before their immmutable addition to the blockchain [[6]](#references). Per the [Scilla Documentation](https://scilla.readthedocs.io/en/latest/), it is specific to the [Zilliqa](https://zilliqa.com/) blockchain (cannot be compiled into EVM bytecode).

#### Scilla Principles <a href="#table-of-contents">^</a>

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

#### Scilla Implementation <a href="#table-of-contents">^</a>
+ **Implicitly declared fields**
    
    All Scilla contracts when initialized create a public and mutable field called *balance* that holds the contract's funds. This field can only be editted through explicitly moving funds to other accounts, which are held in *uints*, or 32 bit **U**nsigned **Int**egers [[4]](#references). 

+ **Transitions**

    Transitions are simliar to Solidity methods or functions, and is the lingo for a Scilla "function". This is because of the automata formatting of Scilla contracts [[4]](#references). Formally, when moving to a new state in automata, it is said you are "transitioning". 

    Additionally, the term transition refers to the atomicity of the computations being performed. Each transition only changes the state of the contract, not altering other parties or contracts in the process. To reiterate, Scilla contracts do not interact with other contracts during computations, only at the very beginning or end, through initiation of the contract or post-computation communication to others [[4]](#references). 

    Transitions are activated with a transaction from another party, and therefore are only explicitly called. Transaction parameters are formatted to accept data from a message, and contain a *tag* of type string that specificies which contract transition. The parameters can also contain other fields for data from messages, including: 
    + *sender*: the address of the messenger
    + *value* : an uint of some transaction amount
    + etc.
    
    When a transition performs a *send*, or a transaction to another entity, the final field will be either a *continuation* or the constant *MT* that specifies to call no continuation, and hence perform no more computations [[4]](#references). 

+ **Continuations**

    Continuations can be considered "remaining computations" to do after a call to another contract. While Scilla enforces non-tail calls from transitions, it allows transitions to explicitly call a continuation post-call of another contract to perform computations [[4]](#references).

+ **Looping**

    Looping constructs are handled with recursive definitions. This allows finality of a looping definition to be proven statically during formal verification [[4]](#references). 

+ **Control Flow**

    Scilla does not allow calls to other contracts within its transistions, only through the sending of a message. This is to prevent another occurence of the famous $60 mil dollar DAO theft [[5]](#reference) from reoccuring. This exploit occurred via a call to another contract inside of a function, which lead to contract-state manipulation in a "re-entry attack". The solution is called a *tail-call*, where other contracts are called post-transition and is now considered a best practice in Solidity [[4]](#references). 

#### Scilla Formal Verification <a href="#table-of-contents">^</a>
+ **Coq Proof Assistant**

    Coq Proof Assistant is a theorem prover tool with dependent-type theory used to verify programs using mathematical libraries. Scilla is being developed hand-in-hand with this tool. Semantics are modeled into Coq, using Coq's specification language called Gallina [[4]](#references).

#### Scilla Code Example <a href="#table-of-contents">^</a>
Scilla code example taken from [Scilla: A Smart Contract Intermediate-Level Language](https://arxiv.org/abs/1801.00687) [[4]](#references).

```ML
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

---

### Solidity - Brief Note <a href="#table-of-contents">^</a>
Solidity has become the forefront of smart contract languages. It is by and far the most popular language, having been referenced the most during my time researching. The Ethereum project now officially supports Solidity, with its documentation available [here.](https://solidity.readthedocs.io/en/v0.5.7/) 

Arguments have been made against Solidity, the language used in the DAO contract exploit, that the language is too versatile and unconstrained [[19]](#references). Solidity's inheritance, turing-completeness, non-tail external contract calls, and lack of safety features have produced efforts towards smart-contract languages that are Turing-incomplete, functional languages with  integrated semantic-testing tools such as Scilla or Pact.  

---

### Vyper <a href="#table-of-contents">^</a>
The Ethereum project's language to succeed Serpent is a contract-oriented, open-source, "pythonic" language called Vyper. While Scilla focuses on mechanical formal verification of its semantics, Vyper seeks to be human-readable, with great difficulty in writing misleading or incorrect code. Readers of smart contracts are catered-to; they'll be able to understand Vyper code without much prior experience [[2]](#references). With support from the Ethereum project, Vyper will be compilable to EVM bytecode [[12]](#references).
 
#### Vyper Principles <a href="#table-of-contents">^</a>
+ **Security**
    
    Vyper strives to make secure contract-creation easy. As a major design principle this, as well as the Auditability principle, suggests that the language will have extensive security research for its simple expressions [[2]](#references). 

+ **Language & Compiler Simplicity**

    A compiler and language top-priority, simplicity is highly sought after. This co-aligns with Auditability's purpose of making the language simple to understand for non-contract writers [[2]](#references).

+ **Auditability**

    Aiming for maximum human readability, Vyper will be similar to Python with minimal syntax in favor of indentation for program scopes. Hand-in-hand is the difficulty it should take to write misleading, or incorrect code. Readers will not have to have in-depth understanding in order to spot potentially malicious or faulty contracts [[2]](#references).

#### Vyper Features <a href="#table-of-contents">^</a>
+ **Bounds & Overflow Checking**

    Accessing array indices will feature bounds checking, and math will offer overflow-checking [[2]](#references). With these tools, contracts can further avoid runtime errors, where both gas costs and progress would be lost for the caller after a premature contract conclusion.

+ **Support for Signed Integers & Decimal Fixed-Point Numbers**

    Scilla supports unsigned integers, but Vyper will additionally support signed integers and fixed-point decimal numbers [[12]](#references). 

+ **Decidability**

    With Ethereum contracts featuring using gas per instruction, Vyper will allow precise calculations of gas consumption upper bounds of a function call [[12]](#references). Out-of-gas errors are a loss for both parties, caller and miner alike. 

+ **Strong Typing**

    Something interesting that vyper offers support for are unit types. While timestamps aren't too surprising, the units Wei & Wei per second are among those implemented [[12]](#references). Wei is the smallest denomination of Ether, ( 10 to the <sup>-18th</sup> of an Ether ), and named after the cryptographer Wei Dai [[13]](#references).  

+ **Small & Understandable Compiler Code**

+ **Limited Support for Pure Functions**

    Pure functions, or functions that do not have side effects of modifying local/non-local variables or mutable reference arguments [[14]](#references). Anything marked with the following declaration is not allowed to change the state [[12]](#references). 
    ```Vyper
    @constant
    ```    
    It can be infered that this precaution is a similar attempt in nature to preventing contract malfunctions or exploits to that of the DAO attack, with an exploit using a recursive non-tail call. Changing the state of the contract will ensure logical steps are properly recorded.

#### Vyper Features Specifically Not Included <a href="#table-of-contents">^</a>
+ **Modifiers**

    Modifying methods increases difficulty of auditability with jumps around a file when altering a variable

+ **Class Inheritance**

    Inheritance complicates files and readability with inherited functions

+ **Inline Assembly**

    Adding assembly into code increases auditability effort required

+ **Function Overloading**

    Multiple similar functions allows for misleading code where same-named functions could have very different outcomes with just a parameter change.

+ **Operator Overloading**

    Similar to function overloading, operator overloading creates uncertainty on what task will be performed.

+ **Recursive Calling & Infinite Length Loops**

    In order to set upper bounds for gas-limits, recursion is not implemented as well as the possibility of infinite-length loops.

+ **Binary Fixed Point**
    
    Binary fixed-points can require approximations, unlike decimal fixed-point which will have an exact value when written as a literal. 

#### Vyper Testing <a href="#table-of-contents">^</a>
Vyper is still in the process of implementing [ethereum-tester](https://github.com/ethereum/eth-tester), but will utilize this library. Command-line tools will allow function execution of contracts with event logging [[4]](#references).
```sh
vyper-run yourContractName.vy "yourFunction();" -i some_init_param, another_init_param
```

#### Vyper Crowdfund Example Contract <a href="#table-of-contents">^</a>
Vyper can implement a crowd-fund contract in a little over 60 lines of code, including comments and newlines.

From 'Vyper By Example' Documentation [[15]](#references)

```Python
# Setup private variables (only callable from within the contract)

struct Funder :
  sender: address
  value: wei_value

funders: map(int128, Funder)
nextFunderIndex: int128
beneficiary: address
deadline: public(timestamp)
goal: public(wei_value)
refundIndex: int128
timelimit: public(timedelta)


# Setup global variables
@public
def __init__(_beneficiary: address, _goal: wei_value, _timelimit: timedelta):
    self.beneficiary = _beneficiary
    self.deadline = block.timestamp + _timelimit
    self.timelimit = _timelimit
    self.goal = _goal


# Participate in this crowdfunding campaign
@public
@payable
def participate():
    assert block.timestamp < self.deadline, "deadline not met (yet)"

    nfi: int128 = self.nextFunderIndex

    self.funders[nfi] = Funder({sender: msg.sender, value: msg.value})
    self.nextFunderIndex = nfi + 1


# Enough money was raised! Send funds to the beneficiary
@public
def finalize():
    assert block.timestamp >= self.deadline, "deadline not met (yet)"
    assert self.balance >= self.goal, "invalid balance"

    selfdestruct(self.beneficiary)

# Not enough money was raised! Refund everyone (max 30 people at a time
# to avoid gas limit issues)
@public
def refund():
    assert block.timestamp >= self.deadline and self.balance < self.goal

    ind: int128 = self.refundIndex

    for i in range(ind, ind + 30):
        if i >= self.nextFunderIndex:
            self.refundIndex = self.nextFunderIndex
            return

        send(self.funders[i].sender, self.funders[i].value)
        clear(self.funders[i])

    self.refundIndex = ind + 30
```
---
### **Deprecated Languages** <a href="#table-of-contents">^</a>
### Bamboo <a href="#table-of-contents">^</a>

Bamboo is very similar in structure to Scilla and similarly attempts to solve re-entrance problems. Bamboo has even started to produce (with limited success) EVM bytecode that "ran as expected" [[7]](#references). The OCaml compiler still remains buggy, and is now maintained by Cornell Blockchain in [this github repository](#https://github.com/CornellBlockchain/bamboo). Bamboo is only at version 0.0.03, with a year since the last commit made to the forked branch made by Cornell Blockchain [[8]](#references). 

---
### Serpent <a href="#table-of-contents">^</a>
Ethereum's first language for smart contracts was a Turing-complete language, Serpent. This language allows loops, which presents both benefits and drawbacks. Among drawbacks, chiefly is the potential for infinite loops. Because Ethereum contracts use "gas" as payment to miners for executing code, "gas" could run out via an infinite loop and error out the contract. This error would cause the caller to lose the subsequent gas [[9]](#references) [[1]](#references).

Vitalik Buterin tweeted in July 2017: [[17]](#references) 
    
    "PSA: I now consider Serpent outdated tech; not nearly enough safety protections by current standards."

Additionally, the [ethereum/serpent](https://github.com/ethereum/serpent) github repository states in the README as of 2017: [[18]](#references)

    Being a low-level language, Serpent is NOT RECOMMENDED for building applications unless you really really know what you're doing. The creator recommends Solidity as a default choice, LLL if you want close-to-the-metal optimizations, or Viper if you like its features though it is still experimental.

---

## References <a href="#table-of-contents">^</a>

[1] [Vitalik Buterin. A Next-Generation Smart Contract and Decentralized Application Platform, 2014.](https://cryptorating.eu/whitepapers/Ethereum/Ethereum_white_paper.pdf) 

[2] [ethereum/vyper github README.md](https://github.com/ethereum/vyper)

[3] [Compiling and Smart Contracts: ABI Explained](https://www.sitepoint.com/compiling-smart-contracts-abi/)

[4] [Ilya Sergey, Amrit Kumar, and Aquinas Hobor. Scilla: a smart contract intermediate-level language., 2018.](https://arxiv.org/abs/1801.00687)

[5] [Michael del Castillo. The DAO Attacked: Code Issue Leads to $60 Million Ether Theft, 2016.](https://www.coindesk.com/dao-attacked-code-issue-leads-60-million-ether-theft)

[6] [Emerging Scilla & Vyper Programming Languages for Smart Contracts](https://blockonomi.com/scilla-vyper/)

[7] [Yoichi Hirai. Bamboo compiler started producing EVM bytecode, 2017.](https://medium.com/@pirapira/bamboo-compiler-started-producing-evm-bytecode-6a55e4633de9)

[8] [Bamboo Compiler github repository. Cornell Blockchain fork](https://github.com/CornellBlockchain/bamboo)

[9] [Atzei, Nicola, Massimo Bartoletti, and Tiziana Cimoli. A Survey of Attacks on Ethereum Smart Contracts (SOK), 2017.](https://link.springer.com/chapter/10.1007/978-3-662-54455-6_8)

[10] [The DAO (Organization) Wikipedia](https://en.wikipedia.org/wiki/The_DAO_(organization)#cite_note-FT-TheDao20160517-6)

[11] [Bhargavan, Karthikeyan, Antoine Delignat-Lavaud, Cédric Fournet, Anitha Gollamudi, Georges Gonthier, Nadim Kobeissi, Natalia Kulatova et al. Formal Verification of Smart Contracts: Short Paper, 2016.](https://dl.acm.org/citation.cfm?id=2993611)

[12] [Vyper Documentation](https://vyper.readthedocs.io/en/v0.1.0-beta.9/)

[13] [Jake Frankenfield. Wei, 2018](https://www.investopedia.com/terms/w/wei.asp)

[14] [Pure Functions, Wikipedia](https://en.wikipedia.org/wiki/Pure_function)

[15] ['Vyper By Example' Documentation](https://vyper.readthedocs.io/en/v0.1.0-beta.9/vyper-by-example.html#crowdfund)

[16] [Nick Szabo. The Idea of Smart Contracts, 1997](http://www.fon.hum.uva.nl/rob/Courses/InformationInSpeech/CDROM/Literature/LOTwinterschool2006/szabo.best.vwh.net/idea.html)

[17] [Vitalik Buterin. Tweet on Serpent](https://twitter.com/VitalikButerin/status/886400133667201024)

[18] [ethereum/serpent github README.md](https://github.com/ethereum/serpent)

[19] [Stuart Popejoy. The Pact Smart-Contract Language, 2016](http://kadena.io/docs/Kadena-PactWhitepaper-Oct2016.pdf)