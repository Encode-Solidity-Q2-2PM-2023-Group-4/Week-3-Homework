# Week 3 Homework

This week, we were tasked with editing a tokenized ballot contract in Solidity and deploying both the voting token and ballot contracts with TypeScript. Once these contracts were deployed, we had to mint a/some voting token(s), transfer them between ourselves and vote. After this had been done, we could check the outcome of the ballot. Task completed by:

Nanda Girish: [0xc3E...038](https://sepolia.etherscan.io/address/0xc3Ef2b9e4D9EAa4Efa28919213959CdD7E86B038)                                                                                                    
Linus Kelsey: [0xa65...150](https://sepolia.etherscan.io/address/0xa65b289a1F8AFA8105133523F0B912c6D5Fc1150)                                                                                                    
Antony Siahaan: [0x1Cf...a31](https://sepolia.etherscan.io/address/0x1Cf2ed52a7E6b4E8254f55624c85ed5958308a31)                                                                                                  
Adam Czopp: [0xbe1...df4](https://sepolia.etherscan.io/address/0xbe124408de4a263d0e746e9db41744f66c3e5df4)

## Timeline

### Token Deployment

The token, a mintable ERC20 with voting rights and roles, was deployed at [0x4A3...9A8](https://sepolia.etherscan.io/address/0x4A3f928B2909C33A8c27400F109f3C07FB5aE9A8) on the Sepolia testnet. From this point, the deployer can mint and transfer tokens between other members of the group.

### Token Minting

Then, one VoteToken (VTK) was minted by the deployer in transaction hash [0xcea...f59](https://sepolia.etherscan.io/tx/0xcea65fa47cc7f7ea7db14c2462d5cb310d5eff2a4b059038e294d66c85069f59), ready to be split into four and transferred to the rest of the team:

![Screenshot 2023-08-13 at 08 55 36](https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/21c3230c-f3c1-4184-bd8d-3a8a6005f093)

### Token Transfer

Next, a quarter of the minted supply was transferred to each team member, leaving everyone involved with 0.25 VTK:

![Screenshot 2023-08-13 at 11 54 10](https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/ddaaafc7-1497-426d-a9a6-f4259f7ac54e)

### Ballot Deployment/Self-delegation

Following this, the tokenised ballot was finally deployed to the blockchain in transaction hash [0x662...9a5](https://sepolia.etherscan.io/address/0x6623d414f729caaf17c393e5c0d4f4cd6a6839a5), with proposals Ethereum, Solana, Bitcoin, Dogecoin, Binance, Avalanche, Fantom and Polygon. Initially, at this point we ran into an issue, whereby the `targetBlockNum`, by which we reference voting power, was prior to any self-delegation which occured. However, in order to self-delegate, we needed to have a Ballot contract up and running, as this is where we accessed a user's voting power from. We were stuck in a loop and needed a fix, by which point we realised that, since the `.delegate` method was an `ERC20Votes` method, if we simply delegated via a *dummy* contract, we could then have real voting power for the actual contract (linked above). Reflecting on this, we could have simply made the snapshot take into account tokens at the time, as opposed to voting power, but thankfully se found a workaround in the time remaining.

Following on from this discussion and realisation, we delegated using the dummy contract at [0xa09b745Da6FF0E5E8AC4B79B4F14870af19aE843](https://sepolia.etherscan.io/address/0xa09b745Da6FF0E5E8AC4B79B4F14870af19aE843), and then deployed [0x662...9a5](https://sepolia.etherscan.io/address/0x6623d414f729caaf17c393e5c0d4f4cd6a6839a5) as the actual ballot with which we would track votes:

![Screenshot 2023-08-13 at 19 05 20](https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/e97d89d2-95e4-4be3-a66b-e6da1e87f675)

![Screenshot 2023-08-13 at 19 05 45](https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/cb6b4d02-6abd-439c-b91d-731d1d75572b)

### Voting

Next we submitted our votes - here we realised that the voting power was given in decimal units, thanks to an unaccounteed conversion somewhere amongst the various scripts/contracts, and that the full amount with which we wished to vote would cause overflow errors. To combat this we tested 1400000000000000 decimal units and realised this did not cause any such issues, and were able to vote with this. Upon calling [`CurrentTally.ts`](scripts/CurrentTally.ts), we could check what these votes looked like:

<img width="736" alt="Screenshot 2023-08-13 at 19 10 10" src="https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/1a1ba13b-2ed4-4ad7-ba41-c643326d0cfc">

After realising this, we made small voting power transactions. Again, this would have been an easy fix had we spotted it sooner.

Example Voting txs:
https://sepolia.etherscan.io/tx/0x728b26de4158b653d66dee0201ea8dc6d642a355bde2556ab248c7f38534034c
https://sepolia.etherscan.io/tx/0xfff0e9d2f816278e28430dc7cdf0c3abba554f35c8d9bc58938222e377b80817
https://sepolia.etherscan.io/tx/0xfff0e9d2f816278e28430dc7cdf0c3abba554f35c8d9bc58938222e377b80817


### Winning Proposal

[`winnerName.ts`](scripts/winnerName.ts) could be called with ease after all votes (of 1400000000000000 decimal units) were placed (including one erroneous vote from a test):

![image](https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/38251144/fbdbbad4-9541-4073-b7c4-e8663afe5b26)

### Appendix: Tests

A number of errors were tested throughout the process, although not all were recorded effectively. When attempting to vote pre-delegation, we were struck with errors saying that we had no voting power, and when attempting to vote with more power we had we received an error saying we did not have enough voting power, just as in the previous case.

#### Voting power before delegation

When checking voting power before self-delegating we were hit with the expected error:

<img width="519" alt="voting_power_before_delegation" src="https://github.com/Encode-Solidity-Q2-2PM-2023-Group-4/Week-3-Homework/assets/96599839/5438aca6-ba27-4f2f-a518-9e82e84b15fa">

It seems that self-delegating is required for the ERC20's getPastVotes() method to return the updated value. 
