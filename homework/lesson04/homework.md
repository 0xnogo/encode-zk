# Homework

```
1
```
Shielded tx in Zcash: https://zcashblockexplorer.com/transactions/8c28fd396505e29e3b25946b7c3e59547c65813f95ec3f8d820b800caa85b27f

```
2
```

Cairo is a language for writing provable programs: running a Cairo program produces a trace that can then be sent to a trustless prover, which generates a STARK proof for the validity of the statement or computation represented by the Cairo program. The proof can then be verified using a verifier (which may or may not be on-chain).

Let’s look at a toy example – a blockchain-based Sudoku game, where the person who presents a correct solution gets a prize. 

Today, you need to implement the entire business logic of verifying whether a solution to the puzzle is correct in Solidity – that’s an expensive computation to perform on-chain. Instead, you can write this logic in Cairo, and execute it off-chain. The Cairo program will check the solution and will then trigger the SHARP to generate a proof, verify it on-chain and write a fact – a stamp of approval that this solution was the right one. The Sudoku’s smart contract checks that this fact exists, and pays the prize to the winner. We saved both the expensive computation of solution verification and the transmission of the Soduko’s solution on-chain

The role of the dApp’s smart contract changes – from the component in charge of executing expensive business logic, it becomes the component in charge of dealing with the inexpensive consequences of that business logic (in our example, sending the prize to the winner or updating some state), while relying on the fact that the business logic was executed correctly.


https://www.youtube.com/watch?v=xjsV47k-cvo:
* Warp: transpiler to convert Solidity in Cairo
* OpenZeppelin is working on a set of boiler plate smart contracts for Cairo
* Back-end contract / front-end contract: all the logic is in the base file. And the front-end contract will import them and expose them.
* On Eth, public key can hold funds. In Starknet, every account is a smart contract. Tx are sent to an "entry point".
* IDE: HardHat (JS) // Nile (python)
* Fee: storage is expensive but computation is cheap
* Bridge: we could be brindging permissions/computations/instructions.
 * dev can develop their own bridge (not bridging assets but data so pretty flexible) -> `send_message_to_l1` method in cairo
 * It will send the proof to l1. In Solidity, we need to listen to messages where we verify that the message was sent. Inside the message, we can have a payload and process it.
 * same from l1 to l2. starknet will consome the call immediatly.