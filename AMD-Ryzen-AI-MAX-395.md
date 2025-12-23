# AMD Ryzen AI MAX+ 395 Benchmark

## System Specifications

**CPU:**
- Model: AMD Ryzen AI MAX+ 395 w/ Radeon 8060S
- Cores: 16 (32 threads)
- Speed: 599 MHz - 5187 MHz (max boost)

**GPU:**
- AMD Radeon 8060S (Strix Halo) - integrated graphics with dedicated VRAM

**RAM:**
- Total: 128 GB (8x 16 GB)
- Type: LPDDR5
- Speed: 8000 MT/s
- Form Factor: Soldered (on-package)
- Manufacturer: Hynix
- Part Number: H58G78CK8BX185N
- Configuration: Octa-channel (Channels A-H)
- Voltage: 0.5 V

## Performance (Qwen3-Coder-30B-A3B)

| Model | Context | KV Cache | Prompt Speed | Generation |
|-------|---------|----------|--------------|------------|
| Q8_K_XL | 1M tokens | 52 GB (q8_0) | ~400-480 tok/s | ~35-40 tok/s |
| Q4_K_XL | 1M tokens | 27 GB (q4_0) | ~450-500 tok/s | ~38-42 tok/s |

> **Note:** The Radeon 8060S features dedicated GPU memory (up to 32GB VRAM carved from system RAM),
> providing significantly higher memory bandwidth than shared-memory iGPUs like the 780M.
> The first request after startup is slower (~120 tok/s) due to Vulkan shader compilation.

## Notes

- The Ryzen AI MAX+ 395 is the flagship Strix Halo APU
- Radeon 8060S has 40 RDNA 3.5 compute units
- The dedicated VRAM design allows for larger context sizes and faster inference
- 16 Zen 5 cores with 32 threads provide strong prompt processing
- Octa-channel LPDDR5-8000 provides ~256 GB/s memory bandwidth
- Soldered on-package memory for lower latency than SO-DIMM configurations
