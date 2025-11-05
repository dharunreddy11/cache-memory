# Cache Memory Implementation in Verilog

A parameterized direct-mapped cache memory implementation written in Verilog, designed for educational purposes and hardware simulation.

## 📋 Overview

This project implements a simple **direct-mapped cache** with write-through and write-allocate policies. The cache is highly configurable through parameters and can be used as a learning resource for computer architecture concepts or integrated into larger digital design projects.

## ✨ Features

- **Direct-Mapped Cache Architecture**
- **Configurable Parameters:**
  - Cache Size (default: 4096 bytes)
  - Block Size (default: 16 bytes)
  - Address Width (default: 32 bits)
  - Data Width (default: 32 bits)
- **Write-Through Policy:** Writes update both cache and main memory
- **Write-Allocate Policy:** Misses on write allocate a new cache line
- **Synchronous Design:** All operations occur on clock edges
- **Hit/Miss Detection:** Real-time cache hit indicator

## 🏗️ Architecture

### Cache Organization

```
Address Breakdown:
┌─────────────┬──────────────┬─────────────┐
│     Tag     │    Index     │   Offset    │
└─────────────┴──────────────┴─────────────┘
```

- **Tag:** Identifies which memory block is stored
- **Index:** Selects the cache line
- **Offset:** Selects the byte/word within the block

### Default Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| CACHE_SIZE | 4096 bytes | Total cache capacity |
| BLOCK_SIZE | 16 bytes | Size of each cache line |
| NUM_BLOCKS | 256 | Number of cache lines |
| INDEX_BITS | 8 bits | Address bits for indexing |
| OFFSET_BITS | 4 bits | Address bits for offset |
| TAG_BITS | 20 bits | Address bits for tag |

## 📁 File Structure

```
.
├── cache_memory.v          # Main cache module
├── tb_cache_memory.v       # Testbench
└── README.md               # This file
```

## 🚀 Getting Started

### Prerequisites

- Verilog simulator (ModelSim, Cadence Xcelium, Icarus Verilog, Vivado, etc.)
- Basic knowledge of Verilog and cache memory concepts

### Running the Simulation

#### Using Icarus Verilog

```bash
# Compile
iverilog -o cache_sim cache_memory.v tb_cache_memory.v

# Run simulation
vvp cache_sim

# View waveform (optional)
gtkwave dump.vcd
```

#### Using Cadence Xcelium

```bash
xrun cache_memory.v tb_cache_memory.v -access +rwc
```

#### Using ModelSim

```bash
vlog cache_memory.v tb_cache_memory.v
vsim -c tb_cache_memory -do "run -all; quit"
```

## 🔧 Module Interface

### Ports

| Port | Direction | Width | Description |
|------|-----------|-------|-------------|
| `clk` | Input | 1 | System clock |
| `rst` | Input | 1 | Active-high reset |
| `addr` | Input | 32 | Memory address |
| `write_data` | Input | 32 | Data to write |
| `mem_read` | Input | 1 | Read enable |
| `mem_write` | Input | 1 | Write enable |
| `read_data` | Output | 32 | Data read from cache |
| `hit` | Output | 1 | Cache hit indicator |

### Example Usage

```verilog
cache_memory #(
    .CACHE_SIZE(4096),
    .BLOCK_SIZE(16),
    .ADDR_WIDTH(32),
    .DATA_WIDTH(32)
) my_cache (
    .clk(clk),
    .rst(rst),
    .addr(address),
    .write_data(data_in),
    .mem_read(rd_en),
    .mem_write(wr_en),
    .read_data(data_out),
    .hit(cache_hit)
);
```

## 🧪 Testbench Description

The testbench (`tb_cache_memory.v`) performs the following operations:

1. **Reset:** Initializes all cache entries to invalid state
2. **Write Test:** Writes data to address `0x00000040` (initial miss, then allocates)
3. **Read Hit Test:** Reads from the same address (should hit)
4. **Read Miss Test:** Reads from a different address `0x00001040` (should miss)

### Expected Output

```
================================================
         CACHE MEMORY TEST STARTED              
================================================
[60 ns] WRITE @0x00000040  Data=0xaabbccdd  Hit=0
[90 ns] READ  @0x00000040  Data=0xaabbccdd  Hit=1
[120 ns] READ  @0x00001040  Data=0xdeadbeef  Hit=0
================================================
         CACHE MEMORY TEST FINISHED             
================================================
```

## 📊 Performance Characteristics

- **Access Time:** 1 clock cycle (on hit)
- **Miss Penalty:** Returns `0xDEADBEEF` on read miss
- **Write Policy:** Write-through (no write-back required)
- **Replacement Policy:** Direct-mapped (no choice on replacement)

## 🔍 Key Implementation Details

### Write Operation

- Updates cache line with new data
- Sets valid bit and stores tag
- Reports hit if tag matches (write hit)
- Allocates new line on write miss

### Read Operation

- Checks valid bit and compares tag
- On hit: Returns data from cache
- On miss: Returns `0xDEADBEEF` (magic number indicating miss)

### Reset Behavior

- Clears all valid bits
- Resets all tags to zero
- Initializes output signals

## 🛠️ Customization

### Changing Cache Size

```verilog
cache_memory #(
    .CACHE_SIZE(8192),    // 8 KB cache
    .BLOCK_SIZE(32)       // 32-byte blocks
) my_cache (...);
```

### Changing Data Width

```verilog
cache_memory #(
    .DATA_WIDTH(64)       // 64-bit data words
) my_cache (...);
```

## ⚠️ Limitations

- **Direct-Mapped:** High conflict miss rate for certain access patterns
- **No Multi-Level Cache:** Single-level cache only
- **No Dirty Bit:** Write-through policy doesn't require dirty tracking
- **Simplified Miss Handling:** Doesn't implement actual memory fetch on miss
- **No Burst Transfers:** Single-word access only

## 🎓 Educational Use

This project is ideal for:

- Learning cache memory fundamentals
- Understanding Verilog parameterized modules
- Studying memory hierarchy in computer architecture
- Digital design coursework and projects

## 📈 Future Enhancements

Potential improvements for extended learning:

- [ ] Implement set-associative cache (2-way, 4-way)
- [ ] Add LRU replacement policy
- [ ] Implement write-back policy with dirty bits
- [ ] Add burst transfer support
- [ ] Integrate with main memory controller
- [ ] Add performance counters (hits, misses, hit rate)
- [ ] Support for cache coherency protocols

## 📝 License

This project is open-source and available under the MIT License. Feel free to use, modify, and distribute.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.

## 👤 Author

 R. Venkata Dharun Reddy
 
##  GitHub:
 https://github.com/dharunreddy11
##  Email:
 dharunreddy05@gmail.com

## 🙏 Acknowledgments

- Inspired by computer architecture courses and textbooks
- Based on standard cache memory design principles
- Thanks to the digital design community for resources and support

---

**⭐ If you found this project helpful, please consider giving it a star!**
