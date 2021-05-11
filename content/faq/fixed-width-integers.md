---
title: "What are fixed-width integers?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Fixed-width integers are integral types with a fixed number of bits. The C++ standard only defines a minimum byte count for types like `short`, `int` and `long` (however, it does specify that `char` is always 1 byte wide). Fixed-width integers guarantee a specific size, but usage may affect portability, since they are not available on all platforms.

Fixed-width integers are typically implemented as `typedef`'s, which means they are usually aliases for built-in integral types that satisfy the "X bits" condition.

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

Types that optimize for memory (smallest integral type with at least X bits):
- `std::int_least8_t` / `std::uint_least8_t`
- `std::int_least16_t` / `std::uint_least16_t`
- `std::int_least32_t` / `std::uint_least32_t`
- `std::int_least64_t` / `std::uint_least64_t`

Types that optimize for speed (fastest integral type with at least X bits):
- `std::int_fast8_t` / `std::uint_fast8_t`
- `std::int_fast16_t` / `std::uint_fast16_t`
- `std::int_fast32_t` / `std::uint_fast32_t`
- `std::int_fast64_t` / `std::uint_fast64_t`

## Fixed-width integer range
Unsigned integers with X bits range from 0 (inclusive) to 2<sup>X</sup> - 1. Signed integers with X bits typically range from -2<sup>X-1</sup> to 2<sup>X-1</sup> - 1, assuming two's complement representation.
- `std::int8_t` &#8594; \[-128 -- 127]
- `std::int16_t` &#8594; \[-32,768 -- 32,767]
- `std::int32_t` &#8594; \[-2,147,483,648 -- 2,147,483,647]
- `std::int64_t` &#8594; \[-9,223,372,036,854,775,808 -- 9,223,372,036,854,775,807]
- `std::uint8_t` &#8594; \[0 -- 255]
- `std::uint16_t` &#8594; \[0 -- 65,535]
- `std::uint32_t` &#8594; \[0 -- 4,294,967,295]
- `std::uint64_t` &#8594; \[0 -- 18,446,744,073,709,551,616]

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
