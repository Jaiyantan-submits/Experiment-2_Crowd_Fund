# Experiment 2: Blockchain-Based Crowdfunding (Kickstarter Alternative)
## Aim:
To create a decentralized crowdfunding platform where donors contribute funds only if the campaign goal is met.

## Algorithm:

1. #### Deploy the Contract

    ```
    Deploy Crowdfunding(_goal, _duration)
    ```
    - Create a new crowdfunding campaign with a funding goal (_goal) and duration (_duration).

    - Initialize the campaign state, including the creator's address, deadline, and initial raised amount.

2. #### Accept Contributions

    ```
    contribute(amount) if block.timestamp < deadline
    ```

   -  Allow users to contribute funds to the campaign as long as the deadline has not passed.

   - Update the total amount raised and record the contributor's address and contribution amount.

3. #### Check Goal Achievement

    ```
    if amountRaised >= goal then set goalMet = true
    ```

    - Verify whether the total contributions meet or exceed the funding goal.

    - If the goal is met, mark the campaign as successful (goalMet = true).

4. #### Withdraw Funds

    ```
    withdrawFunds() if caller == creator and goalMet == true
    ```

    - Allow the campaign creator to withdraw all raised funds if the goal is met.

    - Reset the total raised amount to zero after transferring funds to the creator.

5. #### Refund Contributors

    ```
    refund() if block.timestamp > deadline and goalMet == false
    ```

   -  Enable contributors to request refunds if the campaign fails to meet its goal after the deadline.

   - Transfer the contributor's original contribution back to their account and reset their recorded contribution to zero.

6. #### Handle Edge Cases

    ```
    revert() if invalid conditions are met
    ```

    - Prevent invalid actions, such as contributing after the deadline or withdrawing funds before the goal is met.

    - Revert transactions with descriptive error messages to ensure proper contract behavior.
## Program:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Crowdfunding {
    struct Campaign {
        address creator;
        uint256 goal;
        uint256 deadline;
        uint256 amountRaised;
        bool goalMet;
        mapping(address => uint256) contributions;
    }

    Campaign public campaign;

    constructor(uint256 _goal, uint256 _duration) {
        campaign.creator = msg.sender;
        campaign.goal = _goal;
        campaign.deadline = block.timestamp + _duration;
    }

    function contribute() public payable {
        require(block.timestamp < campaign.deadline, "Campaign ended");
        campaign.amountRaised += msg.value;
        campaign.contributions[msg.sender] += msg.value;
    }

    function withdrawFunds() public {
        require(msg.sender == campaign.creator, "Only creator can withdraw");
        require(campaign.amountRaised >= campaign.goal, "Goal not met");
        payable(msg.sender).transfer(campaign.amountRaised);
        campaign.goalMet = true;
    }

    function refund() public {
        require(block.timestamp > campaign.deadline, "Campaign still active");
        require(campaign.amountRaised < campaign.goal, "Goal was met");
        uint256 amount = campaign.contributions[msg.sender];
        campaign.contributions[msg.sender] = 0;
        payable(msg.sender).transfer(amount);
    }
}
```
# Expected Output:

1. The Crowdfunding contract at address 0xddaAd340b0f1Ef65169Ae5E41A8b10776a75482d received a contribution.

2. The contribution transaction was successful and updated the contract's state with the transaction hash:- 0xcc1d6fa6a8b812446f937e50545f67b3ab3f1da5fad51aa0c23baed389a7be50.

3. The amountRaised in the campaign struct has increased.

4. If the goal is not met, contributors can claim a refund.

# Output:

![image](https://github.com/user-attachments/assets/3d960efd-35fe-471c-9bb5-349b069197c6)

![image](https://github.com/user-attachments/assets/23f5af50-64a9-4d70-93ee-70c1a9787e19)



# High-Level Overview:
Teaches decentralized fundraising.


Avoids fraud by ensuring funds are only transferred if the goal is met.

# RESULT: 

The expected output is achieved.
