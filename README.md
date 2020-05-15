# Smart Contracts

The following contracts were deployed on the Kovan Testnet.

## Associate Profit Splitter
The first contract is built with the intention of splitting a payment evenly among three employees. A constructor function is defined in order to require three account address when deploying. A variable, amount, is declared to hold one third of the total payment value: ```uint amount = msg.value / 3;```. Because Solidity does not support float values, if a number not divisible by 3 is divided by 3, the original value is rounded down to the closest number divisible by 3, so that the output, stored in 'amount', is a whole number. Therefore, 'amount' multiplyed by 3 may yield a number equivalent, 1 less, or 2 less than msg.value. This remainder is captured and sent back to the owner's account in ```msg.sender.transfer(msg.value - (amount * 3));``` after 'amount' is transferred to each of the three employees. Finally, deposit() is called in the fallback function in order to transfer these three amounts to each employee front the smart contract's address.

## Tiered Profit Splitter
This contract's purpose is to disperse unequal payments to three employees, given the percentage of the total amount to which each employee is entitled. Again, because Solidity does not support floats, the percentages are given as whole number values. In order to account for this, the total value paid out in the contract must be divided by 100: ```uint points = msg.value / 100;```. If msg.value is equal to a number not divisible by 100 wei, a remainder would result. The employees are entitled to 60%, 25%, and 15% of the total payment, respectively. After these adjusted amounts are transferred to the three employees, any remainder is caputred and sent to the first employee, the CEO: ```employee_one.transfer(msg.value - total);```. The variable 'total' represents the sum of the individual amounts sent to each employee. Once again, deposit() is called in the fallback function to transfer the amounts front the contract to the employees.

## Deferred Equity Plan
This contract is designed to distribute 1000 shares to an employee over a 4-year period. The employee is eligible to collect the first 250 shares after 1 year, then another 250 at the end of year 2, etc. The variable 'start_time' captures the precise time at which the contract is deployed: ```uint start_time = now;``` and 'unlock_time' captures the first date on which the employee is elibile to collect the first annual distribution of 250 shares: ```uint unlock_time = start_time + 365 days;```. Within the distribute() function, the following require statements put into action the 'unlock_time' restriction and also limit the amount of shares that can be distributed to the total_shares value, in the event the employee tries to collect shares 5+ years after deployment.  
```
require(unlock_time <= now, "You are not yet eligible to execute this contract.");
require(distributed_shares < total_shares, "Maximum number of shares reached.");
```
