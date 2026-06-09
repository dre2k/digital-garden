---
title: Bloom Filter
permalink: bloom_filter
draft: false
tags:
- programming
---


> In technical terms, a Bloom filter is a **space-efficient, probabilistic data structure** used to test whether an element is a member of a set. It tells you if something is _definitely not_ in the set, or _maybe_ in the set.


Simple implementation 

```python
import hashlib

class SimpleBloomFilter:
    def __init__(self, size=1000):
        # Create an array of 'size' bits (initialized to 0)
        self.bit_array = bytearray(size)
        self.size = size

    def _hashes(self, item):
        """Generate multiple index positions for one item."""
        # We can use different seeds with MD5 to simulate multiple hash functions
        h1 = int(hashlib.md5(item.encode()).hexdigest(), 16)
        h2 = int(hashlib.sha1(item.encode()).hexdigest(), 16)
        return [h1 % self.size, h2 % self.size]

    def add(self, item):
        for position in self._hashes(item):
            self.bit_array[position] = 1

    def __contains__(self, item):
        # If any position is 0, the item is DEFINITELY not there
        return all(self.bit_array[position] == 1 for position in self._hashes(item))

# Usage
bf = SimpleBloomFilter()
bf.add("user_123")

print("user_123" in bf)  # Output: True (Definitely or Maybe)
print("user_456" in bf)  # Output: False (Definitely Not)
```

### How a Bloom Filter Works in a Nutshell

- **The Setup:** Create a bit array of a fixed size (e.g., 1,000 slots). The array size equals the total number of available positions, initialized entirely to `0`.
- **To Add an Entry:**
    - Generate hashes for the user ID (e.g., `user_123`). In our example, we used two types: `md5` and `sha1`.
    - Use the hash outputs to "toggle" positions in the array. By applying the **modulo operator** (`% array_size`), we map the massive hashes down to exactly TWO valid positions in our array and flip them to `1`.
    - To use _more_ positions (a stronger fingerprint), you need additional distinct hash values. This is achieved by adding entirely new hash functions, "salting" a single function, or using [[double hashing]] to mathematically generate new positions from just two base hashes.
- **To Filter/Check an Entry:**
    - Calculate the hashes and determine the target positions for the ID you want to look up.
    - **Check the bit status for those positions:**
        - If **any** of the positions are `0` (e.g., `0/0`, `0/1`, or `1/0`), you know with 100% certainty that the ID is **definitely NOT** in the array.
        - If **all** positions are `1` (`1/1`), it is **possible** the ID is in the array. This is a "Maybe" and requires a fallback check against a "slow" lookup (like a database).
- **Why a "Yes" is Only a "Maybe":** * **False Positives:** It is mathematically possible to get a `1/1` reading for an ID you never added. This happens because unrelated IDs can overlap and flip the exact same positions—a risk that explodes as the array gets crowded with more entries.
- **Why We Use It:**
    - The array can reside entirely in fast CPU cache or RAM, using a tiny fraction of the memory a standard Python `set()` or `list()` would require.
    - Lookups are instant (O(1) constant time) because the CPU jumps directly to the calculated positions, bypassing the need to iterate through or search a list.