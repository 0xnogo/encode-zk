# Homework - lesson 2

```
1
```
Group properties:
* Closure ☑️
* Associativity ☑️
* Identity element ☑️ (0+0=0, 1+0=1)
* Inverse element ☑️ (1+1 = 0, 0+0 = 0)
```
2
```
All odds square are = 1 mod 8

{1...11} mod 8 = 1

All even squares = mod 8

it looks like it is cyclic:
* 2^2 = 4 mod8
* 4^2 = 0 mod8
* 6^2 = 4 mod8
* 8^2 = 0 mod8
* 10^2 = 4 mod8

```
3
```
Vanity address generation is using brute force by generating random private key until the public key associated (P = n x G) has the vanity pattern we want.

We can also split the vanity address generation without having to share the private key by using the follwoig property: `A + B = a x G + b x G = G x (a + b)`

More explanation [here](https://asecuritysite.com/blockchain/vanity).

```
4
```
By O(), we should understand the complexity in time/space of an algorithm based on its size input.

Let's take time complexity order by the ASC:
* O(1): constant meaning that the execution won't depend of the size of the input
* O(logn): logarithmic
* O(n): linear on the input size

```
5
```
The best complexity is 0(1). Constant complexity usually means a very efficient algorithm.