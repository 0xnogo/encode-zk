# Homework

```
1
```
Better to use recursivity rather than for loop

```
func compute_sum(n : felt) -> (sum : felt):
    if n == 0:
        # When 0 is reached, return 0.
        return (sum=0)
    end

    # Otherwise, call `compute_sum` recursively to compute 1 + 2 + ... + (n-1)
    let (sum) = compute_sum(n=n - 1)
    # Add the new value `n` to the sum.
    let new_sum = sum + n
    return (sum=new_sum)
end
```

```
2
```

It is not using tail-recursion as every recursive call has to be called for the beginning of the sum calculation