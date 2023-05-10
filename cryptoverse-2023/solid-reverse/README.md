# solid reverse

## problem

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ReverseMe {
    uint goal = 0x57e4e375661c72654c31645f78455d19;

    function magic1(uint x, uint n) public pure returns (uint) {
        // Something magic
        uint m = (1 << n) - 1;
        return x & m;
    }

    function magic2(uint x) public pure returns (uint) {
        // Something else magic
        uint i = 0;
        while ((x >>= 1) > 0) {
            i += 1;
        }
        return i;
    }

    function checkflag(bytes16 flag, bytes16 y) public view returns (bool) {
        return (uint128(flag) ^ uint128(y) == goal);
    }

    modifier checker(bytes16 key) {
        require(bytes8(key) == 0x3492800100670155, "Wrong key!");
        require(uint64(uint128(key)) == uint32(uint128(key)), "Wrong key!");
        require(magic1(uint128(key), 16) == 0x1964, "Wrong key!");
        require(magic2(uint64(uint128(key))) == 16, "Wrong key!");
        _;
    }

    function unlock(bytes16 key, bytes16 flag) public view checker(key) {
        // Main function
        require(checkflag(flag, key), "Flag is wrong!");
    }
}
```

This used solidity (ethereum stuff I think).

I used Remix IDE to test stuff out and read documentation to help with debugging.

Here, once we knew the key, we could get the flag via an XOR operation between goal and key.

There are four conditions in checker function

- `bytes8(key) == 0x3492800100670155` first eight bytes are 0x3492800100670155
- `uint64(uint128(key)) == uint32(uint128(key))` next four bytes are 0x00000000 (Same logic as 0x0033 == 0x33)
- `magic1(uint128(key), 16) == 0x1964` here, magic1 was a function that masked the last 16 bits (2 bytes) of the key with an AND bitwise statement ((1 << 16) - 1 is 0b1111111111111111)
- `magic2(uint64(uint128(key))) == 16` magic2 takes the last 32 bits (4 bytes) of the key and keeps rightshifting it until it equals 0. this means putting a 1 before the 0x1964

I found the key to be `0x34928001006701550000000000011964` and the flag to be decoded as `cvctf{s0L1d_xDD}`
