# Cache
No single technology that is optimal for all use cases required by the computer system. Hence, we have various systems of memory storage.

The hierarchy looks like this. `CPU <-> Cache <-> Memory <-> Hard disk(or SSD)`. The cache is approximately x10 faster then the main memory, however, it is way smaller then the main memory.

Modern computers have different "level" caches, with each level being bigger(but slower) then the previous one. These usually come in 3 levels. Being L1, L2, and L3 caches.

## Cache timeline
- 1980s - no on-die cache(built on chip)
- 1990s - on-die L1 cache, L2 on motherboard
- 1995s - on-die L1 cache(Inst + data), L2 on motherboard
- 2000s - L1, L2, and L3 on die

## Data + Instruction caches
At some point, data and instruction caches got separated.

Allows simultaneous fetching of instructions and data by the CPU,
allows easier designs for instruction cache,
allows data intensive programs to run without being stopped.

## Data Cache
Used when well... data! \
Data is stored in the cache, and accessed when needed by the processor. If the cache does not contain the required data, new data from main memory will be stored there.

### Principle of locality
AKA locality of reference, there is two basic types.

1. **Temporal locality** - if data is used, its bound to be used in the future(recent accesses)
2. **Spatial locality** - when data is used, data around it is bound to be used soon

## Instruction Cache
Used to store instructions! Also utilizes [Principle of locality](#Principle%20of%20locality) like spatial locality, especially in loops, etc.

## Cache hits + misses
### Cache hit
Cache hits happen when the data requested by the CPU is stored in the cache.
Nothing extra happens, and the CPU simply accesses the data.
### Cache miss
Cache misses happen when the data requested by the CPU is **not** stored in the cache.
The CPU will then need to fetch the data from the main memory, or wherever it resides.

Cache misses can happen due to different reasons
1. Starting a new program
2. Execution code too big to be stored(instruction cache)
3. Cache line conflict(Direct mapped cache only)

## Cache types
- Direct Mapped: each line in memory has one place in cache
- Fully Associative: line can be put anywhere in the cache
- Set Associative: line in memory can only be placed in certain areas in the cache
### Direct Mapped
Direct Mapped caches can only allocate a *cache sized* **block** of main memory into the cache at one go.

The Cache is split into two parts: `Tag`(address) and `Cache line`(data).
### Cache index
<!-- I might have to redo this table -->
<table>
<caption>32-bit address</caption>
<tr>
<th>Tags</th>
<th>Index</th>
<th>Offset</th>
</tr>
<tr>
<td><table>
<caption>19-bits</caption>
<tr>
<td>T</td><td>T</td><td>T</td><td>T</td>
<td>T</td><td>T</td><td>T</td><td>T</td>
<td>T</td><td>T</td>
<td>T</td><td>T</td><td>T</td><td>T</td>
<td>T</td><td>T</td><td>T</td><td>T</td>
<td>T</td>
</tr>
</table></td>
<td><table>
<caption>8-bits</caption>
<tr>
<td>I</td><td>I</td><td>I</td><td>I</td>
<td>I</td><td>I</td><td>I</td><td>I</td>
</tr>
</table>
</td>
<td><table>
<caption>5-bits</caption>
<tr>
<td>O</td><td>O</td><td>O</td><td>O</td>
<td>O</td>
</tr>
</table>
</td>
</tr>
</table>

- `Tags`: Address portion saved in cache
- `Index`: Selects the cache line
- `Offset`: Desired data from cache line

`Tags` are stored in the cache. `Index` and `Offset` are not.
#### Formulas
> Offset = $\log_{2}{(\text{Cache line length})}$ bits \
> Index = $\log_{2}{(\frac{\text{Cache Size}}{\text{Cache line length}})}$ bits \
> Tag = $\text{System memory address} - \text{offset} - \text{index}$ bits

### Fully Associative Cache
<!-- I probably need to elaborate more when I actually... know -->
- Uses larger tags, but does not select via index bits(as compared to [Direct Mapped Caches](#Direct%20Mapped))
- uses upper address bits to find the tag in the cache. Needs more hardware then Direct Mapped Caches.
- The tag size will hence be 27-bits, and offset is 5-bits

### 2-way Associative Cache
Divides the cache into two, giving two ways that the cache line is able to be stored as.

## Reducing Cache Miss Rates
### Increase cache sizes
Increasing cache sizes is the most obvious way to reduce cache misses. By having more data in the cache.

However, there's a drawback. With bigger caches, access times will be impacted. Making each access slower.
### Increase line sizes
Increasing line sizes reduce compulsory misses, and spatial locality misses.

However, it takes up a lot of space, increases traffic between caches, and can cause more conflict misses.
### Higher Associativity
Higher associativity helps reduce conflict misses. 2-way caches of capacity $\frac{N}{2}$ has the same cache misses as a 1-way cache.

However, due to higher associativity, access times will be slower too.
### Way prediction
Way prediction helps reduce access times, and makes it similar to a [direct-mapped cache](#Direct%20Mapped). It also helps to reduce power consumption too.

## Cache Write
### Write through
Info is written to both line in cache and memory
### Write back
Info is written only to line in cache. Only written in main memory after being it's replaced.