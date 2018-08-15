## Kickstartereum

[Stephen Girder's Udemy course](https://www.udemy.com/ethereum-and-solidity-the-complete-developers-guide/) gave a quick and thorough intro to Ethereum and Solidity, with a long project replicating Kickstarter. In the project, contributors donate money to campaigns and can approve requests made by the campaign manager to use the funds.

However, this contract fails in the following way: an `approvalCount` variable is kept by each request, and if the `approvalCount` is larger than half of the `contributorCount`, the request is granted. But `contributorCount` is incremented every time someone donates to the campaign, opening the door to Sybil attacks where the same person (even with the same account) donates several times.

In this project, we propose a different implementation where the total amount of contributions is tracked by a variable `approversWeight`. Each time a contributor `c` donates money to the campaign, the contract checks if they have donated before and increments their weight `contributions[c]` by the new donation amount. A request is granted if and only if a majority weight of donators approves the request, similar to shareholder votes that are usually proportional to participation.

### Pain points

As explained (very clearly) in the class, _mappings_ are good data structures to link data to account public keys. However, it is not possible to iterate on them and thus, we cannot, e.g., obtain the total weight of all contributors who approved request `i` in that manner.

To circumvent this issue, we track for each request `x` the `x.approvalWeight` variable corresponding to the current weight of all approvers of that request. We check every time some contributor adds money to her donation if she has approved request `x` previously, in which case we add her new donation to `x.approvalWeight`.

This check is done by looping over the `requests` array. It is clear that loops should be used with caution in Ethereum as they may consume a lot of gas if the array grows, but it is plausible that the campaign manager will not make an arbitrarily large number of requests. For instance, one can imagine that contributors would be bothered if they had to approve a million tiny requests by the manager.

### Tests

We add some tests to the previous test file created by Stephen Girder to make sure our new implementation works as intended (it seems to).
