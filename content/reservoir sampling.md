---
title: Reservoir Sampling
permalink: reservoir_sampling
draft: false
tags:
- programming
---


## How It Works (The Intuition)

1. **Fill the Reservoir:** You immediately put the first k elements from the stream into your "reservoir" (your sample array).
2. **The Lottery:** For every subsequent element (let's say the i-th element, where i>k):
    - You pick a random integer j between 0 and i.
    - If j happens to fall within the range of your reservoir (i.e., j<k), you replace the element at index j with the new element.
    - Otherwise, you discard the new element.

As the stream grows, the probability of newer elements getting into the reservoir decreases, which perfectly balances out the fact that older elements have had to survive more rounds of elimination. Mathematically, every item ends up with a strict $n/k$​ chance of being chosen.


(useful in situations like randomly sampling IDs stored in 100+ parquet files)

```python
import random

def reservoir_sampling(stream, k):
    """
    Selects k random elements from an unknown/infinite stream.
    """
    # Step 1: Initialize the reservoir with the first k elements
    reservoir = []
    for i, item in enumerate(stream):
        if i < k:
            reservoir.append(item)
        else:
            # Step 2: For the i-th element, pick a random index from 0 to i
            j = random.randint(0, i)
            
            # If the random index falls within the reservoir size, replace the item
            if j < k:
                reservoir[j] = item
                
    return reservoir

# --- Example Usage ---

# Simulate a giant stream of data (e.g., numbers from 0 to 999,999)
# Note: Using a generator expression means we don't load the whole list into memory!
giant_stream = (f"Item-{x}" for x in range(1000000)) 

# We want a random sample of 5 items
sample_size = 5

random_sample = reservoir_sampling(giant_stream, sample_size)
print(f"Random Sample: {random_sample}")

```