# AMD Ryzen 9 7940HS Benchmark

## System Specifications

**CPU:**
- Model: AMD Ryzen 9 7940HS w/ Radeon 780M Graphics
- Cores: 8 (16 threads)
- Speed: 400 MHz - 5263 MHz (max boost)

**GPU:**
- AMD Radeon 780M (Phoenix1) - integrated graphics

**RAM:**
- Total: 64 GB (2x 32 GB)
- Type: DDR5
- Speed: 5600 MT/s
- Form Factor: SODIMM
- Manufacturer: Micron Technology (Crucial)
- Part Number: CT32G56C46S5.M8B1
- Configuration: Dual-channel (Channel A + Channel B)
- Voltage: 1.1 V

## Performance (Qwen3-Coder-30B-A3B)

| Model | Context | KV Cache | Prompt Speed | Generation |
|-------|---------|----------|--------------|------------|
| Q4_K_XL | 512K tokens | q4_0 | ~30 tok/s | ~31 tok/s |

> **Note:** The Radeon 780M iGPU has significantly less memory bandwidth than the Radeon 8060S,
> resulting in slower inference speeds. The 1M token context is not feasible on this system;
> 512K is the maximum tested configuration.

## Notes

- The Ryzen 9 7940HS has 64 GB RAM, which may limit context size compared to higher-memory systems
- Radeon 780M iGPU uses shared system memory
