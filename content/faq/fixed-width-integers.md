---
title: "What are fixed-width integers?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Fixed-width integers are integral types with a fixed number of bits. The C++ standard only specifies a minimum byte count for types such as `short`, `int` and `long`. Fixed-width integers guarantee a specific size, but their use can have an impact on portability, since they are not supported by all platforms.

## Fixed-width integer types
These can be used by including `<cstdint>`. The `intX_t` types are signed integers with exactly `X` bits. `uintX_t` types are the same, but unsigned.
- `std::int8_t` / `std::uint8_t` (1 byte)
  - **Note:** These are treated as signed/unsigned characters on most systems. When printing variables of this type, they might show up as their ASCII equivalent.
- `std::int16_t` / `std::uint16_t` (2 bytes)
- `std::int32_t` / `std::uint32_t` (4 bytes)
- `std::int64_t` / `std::uint64_t` (8 bytes)

**Note:** The number of bytes is the number of bits in the type, divided by the number of bits in one byte. On architectures where bits per byte != 8, some or even all of these types may not be defined.

### The more portable alternative
To optimize integral types for faster access times and to ensure portability with more exotic architectures, the following types allow the compiler to choose a suitable integer size, while also ensuring a minimum number of bits.

For every `std::(u)intX_t` type there is a `std::(u)int_leastX_t` that represents the smallest integral type with at least X bits. These optimize for memory.

Likewise, there are types that optimize for speed: `std::(u)int_fastX_t` (the fastest integral type with at least X bits).

## Fixed-width integer range
Unsigned integers with X bits range from 0 to 2<sup>X</sup> - 1. Signed integers with X bits typically range from -2<sup>X-1</sup> to 2<sup>X-1</sup> - 1, assuming two's complement representation.

Accurate, platform-specific ranges can also be found in `<cstdint>`:
- `INTX_MIN` / `INTX_MAX` (minimum and maximum values for `std::intX_t` and `std::uintX_t`)
- `INT_LEASTX_MIN` / `INT_LEASTX_MAX` (minimum and maximum values for `std::int_leastX_t` and `std::uint_leastX_t`)
- `INT_FASTX_MIN` / `INT_FASTX_MAX` (minimum and maximum values for `std::int_fastX_t` and `std::uint_fastX_t`)

## Example

```cpp
#include <cstdint>
#include <climits> // for CHAR_BIT
#include <iostream>

int main() {
    // 16-bit (2-byte) integer
    std::uint16_t year = 1984;
  
    // Check size in bytes, will usually be 2
    std::cout << year << ": " << sizeof(year) << " bytes" << std::endl;
  
    // Multiply by CHAR_BIT (number of bits in one byte) to get size in bits
    std::cout << "Size of fastest integer with at least 16 bits: " << (CHAR_BIT * sizeof(std::int_fast16_t)) << std::endl;
    std::cout << "Maximum value: " << INT_FAST16_MAX << std::endl;
  
    return 0;
}
```

Compiling and running the above program with `gcc` on a 64-bit Intel architecture produces the following result:
```
1984: 2 bytes
Size of fastest integer with at least 16 bits: 64
Maximum value: 9223372036854775807
```

The `std::uint16_t` variable is 2 bytes wide, as expected. The compiler decided that the fastest integral type with at least 16 bits is the 64-bit integer on this architecture.

### See Also
* https://en.cppreference.com/w/cpp/types/integer
