# Homework

```
1
```
```
import {
    Field,
    PublicKey,
    SmartContract,
    state,
    State,
    PrivateKey,
    method,
    UInt64,
    Poseidon,
    Bool,
  } from 'snarkyjs';

export class Odds extends SmartContract {
    @state(PublicKey as any) ownerAddress = State<PublicKey>();
    @state(PublicKey as any) opponentAddress = State<PublicKey>();
    @state(Field) hashOfGuess = State<Field>();
    @state(Field) rangeUp = State<Field>();
    @state(Field) rangeDown = State<Field>();
    @state(UInt64) betAmount = State<UInt64>();
    @state(Bool) opponentPlayed = State<Bool>();
    @state(Bool) opponentWon = State<Bool>();
  
    @method init(
        initialbalance: UInt64, 
        ownerAddress: PublicKey, 
        opponentAddress: PublicKey, 
        rangeUp: Field,
        rangeDown: Field) {
        this.ownerAddress.set(ownerAddress);
        this.opponentAddress.set(opponentAddress);
        this.rangeDown.set(rangeDown);
        this.rangeUp.set(rangeUp);
        this.opponentPlayed.set(Bool(false));
    }

    @method startRound(
        numberToGuess: Field, 
        callerPrivKey: PrivateKey,
        betAmount: UInt64) {
        let ownerAddress = this.ownerAddress.get();
        let callerAddr = callerPrivKey.toPublicKey();
        callerAddr.assertEquals(ownerAddress);
        
        this.betAmount.set(betAmount);
        this.balance.addInPlace(betAmount);
        
        numberToGuess.assertGte(this.rangeDown.get())
        numberToGuess.assertLte(this.rangeUp.get())

        this.hashOfGuess.set(Poseidon.hash([numberToGuess]));
      }

    @method submitGuess(guess: Field, callerPrivKey: PrivateKey) {
        this.opponentPlayed.get().assertFalse();
        this.opponentPlayed.set(Bool(true));

        let opponentAddress = this.opponentAddress.get();
        let callerAddr = callerPrivKey.toPublicKey();
        callerAddr.assertEquals(opponentAddress);

        let userHash = Poseidon.hash([guess]);
        let stateHash = this.hashOfGuess.get();
        stateHash.assertEquals(userHash);
        
        this.opponentWon.set(Bool(true));
        this.balance.subInPlace(this.betAmount.get());
    }

    @method withdraw(callerPrivKey: PrivateKey) {
        // check if opponent is lost AND if he has played
        this.opponentPlayed.get().assertTrue();
        this.opponentWon.get().assertFalse();

        //if yes send back the fund
        this.balance.subInPlace(this.betAmount.get());

        // reset the game
        this.opponentPlayed.set(Bool(false));
        this.opponentWon.set(Bool(false));
    }
}
```