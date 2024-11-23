# Blockchain_CA3
### Introduction

In this task, we will create a K Framework rule that enforces access control on a `withdraw()` function in a Solidity smart contract. The rule will ensure that only the contract owner can execute the `withdraw()` function, preventing unauthorized users from withdrawing funds.

We will:

1. Write a vulnerable Solidity contract with a `withdraw()` function.
2. Write a K rule to enforce that only the contract owner can call the `withdraw()` function.
3. Provide a sample README file to document how to set up and use the K Framework to verify this rule.

### Step 1: Vulnerable Solidity Contract

Here is an example of a vulnerable Solidity contract where any user can call the `withdraw()` function, potentially leading to unauthorized fund withdrawals.
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VulnerableWallet {
    address public owner;
    uint256 public balance;
    
    constructor() {
        owner = msg.sender;
    }
    
    function deposit() public payable {
        balance += msg.value;
    }
    
    function withdraw(uint256 amount) public {
        require(balance >= amount, "Insufficient balance");
        payable(msg.sender).transfer(amount);
        balance -= amount;
    }
}
```
In this contract, the `withdraw()` function can be called by anyone who has a balance. This is a vulnerability because it lacks a restriction for the contract owner. Now, we need to create a K rule to ensure only the contract owner can execute the `withdraw()` function.

### Step 2: K Rule for Access Control

In the K Framework, we define rules using the K syntax, which is a formal specification language for describing the semantics of programming languages. The K rule will enforce that only the contract owner can call the `withdraw()` function.

Here’s the corresponding K rule:

```k
module ACCESS_CONTROL
  syntax Exp ::= "withdraw" "(" Exp "," Exp ")"  [function]
  rule <k> withdraw(Amt, _) => . ... </k>
       <env> .[msg.sender] = owner </env> // Check that msg.sender is the owner
endmodule
```

### Explanation of the Rule:

- `syntax Exp ::= "withdraw" "(" Exp "," Exp ")" [function]`: This declares that the `withdraw` function takes two arguments: an amount (`Amt`) and a user address.
- The rule `<k> withdraw(Amt, _) => . ... </k>` means that the withdrawal operation will only be executed under specific conditions that we’ll define.
- The condition `<env> .[msg.sender] = owner </env>` ensures that the function only executes if `msg.sender` (the address of the caller) is equal to the `owner`. If this condition is not met, the withdrawal will not happen.

### Step 3: K Rule for Verifying Access Control

Let's now refine the rule to include the owner check:

```k
module ACCESS_CONTROL
  syntax Exp ::= "withdraw" "(" Exp "," Exp ")" [function]
  rule <k> withdraw(Amt, _) => . ... </k>
       <env> .[msg.sender] = owner </env>
endmodule
```



### K Rule for Access Control

```k
module ACCESS_CONTROL
  syntax Exp ::= "withdraw" "(" Exp "," Exp ")"  [function]
  rule <k> withdraw(Amt, _) => . ... </k>
       <env> .[msg.sender] = owner </env> 
endmodule
```

## Setting Up the K Framework

### Prerequisites

1. Install the K Framework by following the instructions at: https://kframework.org/.
2. Install the required dependencies using `make` or `k` commands.

### Verifying the Contract

1. Write the vulnerable Solidity contract in a file named `Vulnerable.sol`.
2. Define the K rule for access control in a separate `.k` file.
3. Use the K Framework to run formal verification and test if unauthorized users are prevented from calling `withdraw()`.

```bash
$ k run vulnerable_contract.k
```

### Next Steps

- Add additional security features (e.g., `onlyOwner` modifier) to your contract.
- Extend the K rule for more sophisticated checks (e.g., handling contract state and events).
- Perform further analysis to detect potential vulnerabilities and errors in your Solidity contracts.

## License

This repository is licensed under the MIT License. See `LICENSE` for details.
Code for the K Framework Implementation:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SecureWallet {
    address public owner;
    uint256 public balance;
    
    constructor() {
        owner = 0x70997970C51812dc3A010C7d01b50e0d17dc79C8;
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }
    
    function deposit() public payable {
        balance += msg.value;
    }
    
    function withdraw(uint256 amount) public onlyOwner {
        require(balance >= amount, "Insufficient balance");
        payable(msg.sender).transfer(amount);
        balance -= amount;
    }
}
```
### Conclusion

In this guide, we created a vulnerable Solidity contract with an unprotected `withdraw()` function, then demonstrated how to use the K Framework to enforce that only the contract owner can access this function. The K rule checks that `msg.sender` is the contract owner before proceeding with the withdrawal, which ensures proper access control.

Make sure to test the contract and the K rules thoroughly to prevent unauthorized access and enhance the security of your smart contracts!
