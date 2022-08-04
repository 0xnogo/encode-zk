# Homework

```
1
```

```
## Create a function that accepts a parameter and logs it
func log_value(y : felt):      

   ## Start a hint segment that uses python print()
   %{
      print(ids.y)
   %}

   ## This exercise has no tests to check against.

   return ()   
end

```

```
2
```
```
from starkware.cairo.common.uint256 import (uint256_add, Uint256)

## Modify both functions so that they increment
## supplied value and return it
func add_one(y : felt) -> (val : felt):
   return (y + 1)
end

func add_one_U256{range_check_ptr}(y : Uint256) -> (val : Uint256):   
   alloc_locals
   let (local res : Uint256, local carry : felt) = uint256_add(y, Uint256(low=1, high=0))
   return (res)
end

```

```
3
```

```
from starkware.cairo.common.serialize import serialize_word

## Perform and log output of simple arithmetic operations
func simple_math():
    
    ## adding 13 +  14
    serialize_word(13 + 14)

    ## multiplying 3 * 6
    serialize_word(3 * 6)
    ## dividing 6 by 2
    serialize_word(6 / 2)
    ## dividing 70 by 2
    serialize_word(70 / 2)
    ## dividing 7 by 2 
    serialize_word(7 / 2)
    return ()
end
```

```
4
```

```
## Return summation of every number below and up to including n
func calculate_sum(n : felt) -> (sum : felt):
    if n == 0:
        return (0)
    end
    return(calculate_sum(n-1) + n)
end
```

```
5
```

```
from starkware.cairo.common.math import abs_value

## Implement a funcion that returns: 
## - 1 when magnitudes of inputs are equal
## - 0 otherwise
func abs_eq{range_check_ptr} (x : felt, y : felt) -> (bit : felt):
    let (abs_x) = abs_value(x)
    let (abs_y) = abs_value(y)
    
    if abs_x == abs_y:
        return (1)
    else:
        return (0)
    end
end
```