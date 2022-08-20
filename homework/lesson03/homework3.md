# Homework - lesson 3

```
1
```
Generate a proof to show a prover knows the square root of 25.

```
def main(private field a, field b) {
	assert(a * a == b);
	return;
}
```

2
```
Was not able to create an invalid proof due to the assert that is making the generation of the witness fail with a non square root.

```
3
```
Same logic applied when you want to verify that P knows the preimage of hash digest

```
import "hashes/sha256/512bitPacked" as sha256packed;

def main(private field a, private field b, private field c, private field d) {
    field[2] h = sha256packed([a, b, c, d]);
    assert(h[0] == 263561599766550617289250058199814760685);
    assert(h[1] == 65303172752238645975888084098459749904);
    return;
}
```

```
4
```
To verify that an address has more > 1 Eth balance, we would need to create `.zok` checking that the balAnce of an address is bigger than 1. But the challenge is in how to get the balance? If the DSL in ZoKrates allows us to fetch API, this could be feasible (i.e. calling an Oracle).
In general, we would need a sort of trusted authority to provide the balance and to pass it as an input to generate the witness.