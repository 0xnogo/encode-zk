# Homework

```
1
```

Warp installation and testing

```
2
```

Battleship
```
%lang starknet
from starkware.cairo.common.cairo_builtins import HashBuiltin, BitwiseBuiltin
from starkware.cairo.common.uint256 import Uint256, uint256_le, uint256_unsigned_div_rem, uint256_sub
from starkware.starknet.common.syscalls import get_caller_address
from starkware.cairo.common.math import unsigned_div_rem, assert_le_felt, assert_le
from starkware.cairo.common.alloc import alloc
from starkware.cairo.common.hash_state import hash_init, hash_update 
from starkware.cairo.common.bitwise import bitwise_and, bitwise_xor

struct Square:    
    member square_commit: felt
    member square_reveal: felt
    member shot: felt
end

struct Player:    
    member address: felt
    member point: felt
    member revealed: felt
end

struct Game:        
    member player1: Player
    member player2: Player
    member next_player: felt
    member last_move: (felt, felt)
    member winner: felt
end

@storage_var
func grid(game_idx : felt, player : felt, x : felt, y : felt) -> (square : Square):
end

@storage_var
func games(game_idx : felt) -> (game_struct : Game):
end

@storage_var
func game_counter() -> (game_counter : felt):
end

func hash_numb{pedersen_ptr : HashBuiltin*}(numb : felt) -> (hash : felt):

    alloc_locals
    
    let (local array : felt*) = alloc()
    assert array[0] = numb
    assert array[1] = 1
    let (hash_state_ptr) = hash_init()
    let (hash_state_ptr) = hash_update{hash_ptr=pedersen_ptr}(hash_state_ptr, array, 2)   
    tempvar pedersen_ptr :HashBuiltin* = pedersen_ptr       
    return (hash_state_ptr.current_hash)
end


## Provide two addresses
@external
func set_up_game{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(player1 : felt, player2 : felt):
    let (current_game_counter) = game_counter.read()

    let game = Game(
        Player(player1, 0, 0),
        Player(player2, 0, 0),
        0,
        (0,0),
        0)

    games.write(current_game_counter, game)
    game_counter.write(current_game_counter + 1)

    return ()
end

@view 
func check_caller{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(caller : felt, game : Game) -> (valid : felt):
    if (game.player1.address - caller) * (game.player2.address - caller) == 0:
        return(1)
    end

    return(0)
end

@view
func check_hit{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr: BitwiseBuiltin*}(square_commit : felt, square_reveal : felt) -> (hit : felt):
    alloc_locals

    let (local hashed) = hash_numb(square_reveal) 
    let (q, r) = unsigned_div_rem(square_reveal, 2)

    if square_commit == hashed:
        if r != 0:
            return(1)
        end
    end

    return(0)
end

@external
func bombard{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr: BitwiseBuiltin*}(
    game_idx : felt, 
    x : felt, 
    y : felt, 
    square_reveal : felt):
    alloc_locals

    # 1. Checks caller is a player
    let (caller) = get_caller_address()
    let (game) = games.read(game_idx)
    let (is_caller_in_game) = check_caller(caller, game)
    assert 1 = is_caller_in_game

    # 2. Checks it's player's turn
    if game.next_player != 0:
        let isNextPlayer = game.next_player - caller
        assert 0 = isNextPlayer
    end

    local opposite_player
    local opposite_score
    if caller - game.player1.address == 0:
        opposite_player = game.player2.address
        opposite_score = game.player2.point
    else: 
        opposite_player = game.player1.address
        opposite_score = game.player1.point
    end

    # 3. Reveal last move's hit and if so increment other player's score
    let (lastMoveSquare) = grid.read(game_idx, opposite_player, game.last_move[0], game.last_move[1])
    let (is_hit) = check_hit(lastMoveSquare.square_commit, lastMoveSquare.square_reveal)

    local opposite_player_score = opposite_score + is_hit

    # 4. If score surpasses threshold declare other player winner
    local winner = 0
    if opposite_player_score == 4:
        winner = opposite_player
    end
    
    # 5. Mark currently shelled square as having bin hit
    # 8. Update square 
    let (currentSquare) = grid.read(game_idx, caller, x, y)
    let newSquare = Square(currentSquare.square_commit, square_reveal, 1)
    grid.write(game_idx, caller, x, y, newSquare)

    # 6. Set the next player's address
    # 7. Update game state with new points, next player, last move and potential winner
    local player1_point
    local player2_point

    if caller - game.player1.address == 0:
        player1_point = game.player1.point
        player2_point = game.player2.point + is_hit
    else:
        player1_point = game.player1.point + is_hit
        player2_point = game.player2.point
    end

    let newGame = Game(
        Player(game.player1.address, player1_point, 0),
        Player(game.player2.address, player2_point, 0),
        opposite_player,
        (x, y),
        winner)
    games.write(game_idx, newGame)

    return ()
end



## Check malicious call
@external
func add_squares{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(idx : felt, game_idx : felt, hashes_len : felt, hashes : felt*, player : felt, x: felt, y: felt):
    alloc_locals

    let (game) = games.read(game_idx)
    let (is_caller_in_game) = check_caller(player, game)

    assert 1 = is_caller_in_game

    load_hashes(idx, game_idx, hashes_len, hashes, player, x, y)

    return ()
end

## loops until array length
func load_hashes{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(idx : felt, game_idx : felt, hashes_len : felt, hashes : felt*, player : felt, x: felt, y: felt):
    if idx == hashes_len:
        return ()
    end
    
    grid.write(game_idx, player, x, y, Square(hashes[idx], 0, 0))

    if x == 4:
        load_hashes(idx+1, game_idx, hashes_len, hashes, player, 0, y+1)
    else:
        load_hashes(idx+1, game_idx, hashes_len, hashes, player, x+1, y)
    end

    return ()
end
```
