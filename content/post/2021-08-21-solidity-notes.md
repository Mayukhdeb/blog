+++


title = "Notes on solidity"
description = "Notes on solidity"
date = "2021-08-21"
tags = ["solidity"]
slug = "solidity-notes"

summary = "Trying to learn a bit of solidity because why not"

+++

## Intro 

`solidity` is pretty neat, it lets you run your code in the ethereum blockchain. 

Some people really like to call the units as "smart contracts". For someone whos coming from python, you can simply think of them as a class wrapper which handles transactions etc, much like a python `class`.

## Caveman example

Every file of solidity code (`*.sol`) starts with something like: 

```solidity
pragma solidity ^0.8;
```

It specifies the minimum compiler version to be used, in this case it's `0.8`

Every "smart contract" that you write will start with the `contract` keyword: 

```solidity
contract RubberDucky {
    // your stuff here
}
```
Here we named our contract as `RubberDucky`.

Now let's try to fill up our `RubberDucky` with some real solidity stuff:

```solidity
pragma solidity ^0.8;

// a smart contract is just code that gets executed on the blockchain

contract RubberDucky{
    
    // solidity is a statically typed language, so we have to declare datatypes
    // string value is a variable that belongs to the entire smart contract
    // much like self.value in python, but here it gets stored on the blockchain 
    string value;
    
    // a constructor is basically like __init__ in python
    constructor() public {
        value = "myValue";
    }

    // now lets create a way to read this value
    // "public" sets the visibility of the function to public
    function get() public view returns(string memory) {
        return value;
    }
    
    // public means that anyone can set this value on a blockchain
    function set(string memory _value) public {
        value = _value;
    }
    
}
```

Instead of explaining this, I'll first try to write the python equivalent of this code: 

```python
class RubberDucky:
    def __init__(self):
        self.value = 'myValue'
    
    def get(self):
        return self.value
    
    def set(self, _value: str):
        self.value = _value
```

## Compiling/running

You can run/play around solidity on the browser on  the [remix IDE](https://remix.ethereum.org/), zero setup required. 

## Another example 

Taken and modified from [this source](https://github.com/willitscale/learning-solidity/blob/master/tutorial-01/myfirstcontract.sol)

```
pragma solidity ^0.8;

contract MyFirstContract {
    
    // setting up 2 private attributes name and age
    string private name;
    uint private age;
    
    // function to set a new value for name
    function setName(string memory newName) public {
        name = newName;
    }
    
    // returns the current value of name, you can think of name as self.name
    function getName() public view returns (string memory) {
        return name;
    }
    
    // function to set a new value for age
    function setAge(uint newAge) public {
        age = newAge;
    }
    
    // returns the current value of name, you can think of name as self.age
    function getAge() public view returns (uint) {
        return age;
    }
}
```
This is equivalent to: 

```python
class MyFirstContract:
    def __init__(self):
        """
        Except for the fact that there's no real "private" thingy in python
        this is equivalent to the solidity code shown above
        """
        self.name = ''
        self.age = 0

    def setName(self, newName:str):
        self.name =  newName

    def getName(self):
        return self.name
    
    def setAge(self, newAge:int):
        self.age =  newAge

    def getAge(self):
        return self.age
```