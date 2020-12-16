# BDD
 Stand-alone BDD package

**Diff will be large as the whole project is formatted with CLion!!!**

## Solution

### Expanded truth table

Truth tables are implemented such that they can deal with 2^32 variables. This is implemented using an unordered map
from a vector of boolean to the 64 bit table. The implementation is an extension such that 6 variables are directly
encoded into the keys of the map while the other possible 2^32 - 6 variables are used to build keys of the map.

A truth table with 6 variables the constant result is true would look like this:

() --> 0xffffffffffffffff

A truth table with 6 variables where the result is true when variable 0 is true would look like this:

() --> 0xaaaaaaaaaaaaaaaa

A truth table for 8 variables such that all value are 0 would look like this:

(false, false)  --> 0x0000000000000000

(false, true)  --> 0x0000000000000000

(true, false) --> 0x0000000000000000

(true, true) --> 0x0000000000000000

A truth table with 8 variables where the result is true when variable 7 is true would look like this:

(false, false)  --> 0x0000000000000000

(false, true)  --> 0xFFFFFFFFFFFFFFFF

(true, false) --> 0x0000000000000000

(true, true) --> 0xFFFFFFFFFFFFFFFF

### Operation cache

Operation cache is split in two parts:

- Cache selection
- Existence check

#### Cache selection

Selection the operation cache is done based on the functional equivalence of possibly previously computed operations.
This means that we are able to use laws of commutativity, De Morgan's laws, etc. to reduce the number of created nodes.
As a result we can check if the following have been previously computed, for each operation:

- AND(f, g): AND(f, g), AND(g, f), OR(NOT(F), NOT(G)), OR(NOT(G), NOT(F))
- OR(f, g): OR(f, g), OR(g, f), AND(NOT(F), NOT(G)), AND(NOT(G), NOT(F))
- XOR(f, g): XOR(f, g), XOR(g, f), XOR(NOT(F), NOT(G)), XOR(NOT(G), NOT(F))
- ITE(f, g, h): ITE(f, g, h), ITE(NOT(f), h, g), ITE(f, NOT(g), NOT(h)), ITE(NOT(f), NOT(h), NOT(g)).

