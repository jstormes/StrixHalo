# AMD Ryzen 7 5700G Benchmark

## System Specifications

**CPU:**
- Model: AMD Ryzen 7 5700G with Radeon Graphics
- Cores: 8 (16 threads)
- Speed: 400 MHz - 4673 MHz (max boost)

**GPU:**
- AMD Radeon Vega (Cezanne) - integrated graphics

**RAM:**
- Total: 64 GB (2x 32 GB)
- Type: DDR4
- Speed: 3600 MT/s
- Form Factor: DIMM
- Part Number: F4-3600C16-32GTZN (G.Skill Trident Z Neo)
- Configuration: Dual-channel (Channel A + Channel B)
- Voltage: 1.2 V

## Performance (Qwen3-Coder-30B-A3B)

| Model | Context | KV Cache | Prompt Speed | Generation |
|-------|---------|----------|--------------|------------|
| Q4_K_XL | 256K tokens | q4_0 | ~74 tok/s | ~13 tok/s |

> **Note:** The Radeon Vega iGPU has less memory and bandwidth than newer RDNA GPUs.
> The 512K context exceeds available GPU memory; 256K is the maximum tested configuration.

## Notes

- The Ryzen 7 5700G has 64 GB RAM
- Radeon Vega iGPU uses shared system memory
- DDR4-3600 has lower bandwidth than DDR5-5600
