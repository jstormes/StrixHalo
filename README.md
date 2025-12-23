# StrixHalo LLM Server

Optimized llama.cpp server with Vulkan GPU acceleration for AMD Strix Halo (Ryzen AI Max+ 395) integrated GPUs.

## Features

- **1M token context window** - Full model capacity
- **Vulkan GPU acceleration** - Works with AMD Ryzen AI Max+ 395 and similar iGPUs
- **q8_0 KV cache quantization** - Balance of quality and memory efficiency
- **OpenAI-compatible API** - Drop-in replacement for OpenAI endpoints

## Quick Start

1. **Download the model** (choose one):
   ```bash
   # Q8 quantization (~34GB) - Higher quality, recommended
   ./download-model.sh

   # Q4 quantization (~18GB) - Smaller, faster, less RAM
   ./download-model-q4.sh
   ```

2. **Build and run**:
   ```bash
   docker compose up -d
   ```

3. **Test**:
   ```bash
   curl http://localhost:8091/v1/chat/completions \
     -H "Content-Type: application/json" \
     -d '{"model": "qwen3-coder", "messages": [{"role": "user", "content": "Hello"}]}'
   ```

## Model Options

Two quantization levels are available from [Hugging Face](https://huggingface.co/unsloth/Qwen3-Coder-30B-A3B-Instruct-1M-GGUF):

| Model | Script | Size | RAM Required | Quality |
|-------|--------|------|--------------|---------|
| Q8_K_XL | `./download-model.sh` | ~34GB | ~85GB | Higher |
| Q4_K_XL | `./download-model-q4.sh` | ~18GB | ~50GB | Good |

To switch models, update `MODEL_PATH` in `docker-compose.yml`:
```yaml
- MODEL_PATH=/models/Qwen3-Coder-30B-A3B-Instruct-1M-UD-Q4_K_XL.gguf
```

## Configuration

Edit `docker-compose.yml` to customize:

| Variable | Default | Description |
|----------|---------|-------------|
| `MODEL_PATH` | - | Path to GGUF model file |
| `CTX_SIZE` | 1048576 | Context window (tokens) |
| `PARALLEL` | 1 | Concurrent request slots |
| `GPU_LAYERS` | 999 | Layers on GPU (999=all) |
| `KV_CACHE_TYPE` | q8_0 | KV cache quantization |
| `FLASH_ATTENTION` | true | Enable flash attention |

### KV Cache Options

| Type | Memory | Quality | Use Case |
|------|--------|---------|----------|
| `q4_0` | Lowest | Good | Maximum context |
| `q8_0` | Medium | Better | Recommended |
| `f16` | Highest | Best | Short contexts |

## Performance (Ryzen AI Max+ 395)

| Model | Context | KV Cache | Prompt Speed | Generation |
|-------|---------|----------|--------------|------------|
| Q8_K_XL | 1M tokens | 52 GB (q8_0) | ~400-480 tok/s | ~35-40 tok/s |
| Q4_K_XL | 1M tokens | 27 GB (q4_0) | ~450-500 tok/s | ~38-42 tok/s |

> **Note:** The first request after startup is slower (~120 tok/s) due to Vulkan shader
> compilation. Subsequent requests achieve full speed.

## Performance (Ryzen 9 7940HS)

| Model | Context | KV Cache | RAM | Prompt Speed | Generation |
|-------|---------|----------|-----|--------------|------------|
| Q4_K_XL | 512K tokens | q4_0 | 64 GB | ~30 tok/s | ~31 tok/s |

> **Note:** The Radeon 780M iGPU has less memory bandwidth than the Radeon 8060S. The 1M token
> context exceeds available GPU memory; 512K is the maximum tested configuration on this system.

## Performance (Ryzen 7 5700G)

| Model | Context | KV Cache | RAM | Prompt Speed | Generation |
|-------|---------|----------|-----|--------------|------------|
| Q4_K_XL | 256K tokens | q4_0 | 64 GB | ~74 tok/s | ~13 tok/s |

> **Note:** The Radeon Vega iGPU (Zen 3) with DDR4-3600 has less bandwidth than newer systems.
> The 512K context exceeds available GPU memory; 256K is the maximum tested configuration.

## Performance Summary

| System | GPU | RAM | Max Context | Prompt | Generation |
|--------|-----|-----|-------------|--------|------------|
| Ryzen AI Max+ 395 | Radeon 8060S | 128GB | 1M | ~450 tok/s | ~40 tok/s |
| Ryzen 9 7940HS | Radeon 780M | 64GB DDR5 | 512K | ~30 tok/s | ~31 tok/s |
| Ryzen 7 5700G | Radeon Vega | 64GB DDR4 | 256K | ~74 tok/s | ~13 tok/s |

## System Requirements

- Docker with GPU support
- AMD GPU with Vulkan support (Radeon 8060S or similar)
- Sufficient RAM for model + KV cache:
  - Q8 model with q8_0 KV cache: ~85GB
  - Q4 model with q4_0 KV cache: ~50GB

## Endpoints

- `GET /health` - Health check
- `GET /v1/models` - List models
- `POST /v1/chat/completions` - Chat completions (OpenAI-compatible)
- `POST /v1/completions` - Text completions
- `GET /metrics` - Prometheus metrics

## File Structure

```
StrixHalo/
├── docker-compose.yml    # Container configuration
├── Dockerfile            # Build instructions (Vulkan + llama.cpp)
├── entrypoint.sh         # Server startup script
├── download-model.sh     # Download Q8 model (~34GB)
├── download-model-q4.sh  # Download Q4 model (~18GB)
├── models/               # Model storage directory
└── README.md             # This file
```

## Troubleshooting

### GPU not detected
Check that `/dev/dri` is accessible:
```bash
ls -la /dev/dri/
```

Verify group IDs in `docker-compose.yml` match your system:
```bash
getent group video render
```

### Out of memory
Reduce context size or use q4_0 KV cache:
```yaml
- CTX_SIZE=524288
- KV_CACHE_TYPE=q4_0
```

### Slow first request
This is normal. Vulkan shaders compile on first use. Subsequent requests will be fast.

## Using with Qwen Code CLI

[Qwen Code](https://github.com/QwenLM/qwen-code) is an open-source AI coding agent for the terminal (similar to Claude Code). It can connect to StrixHalo as its backend.

### Install Qwen Code

```bash
# NPM (requires Node.js 20+)
npm install -g @qwen-code/qwen-code@latest

# or Homebrew
brew install qwen-code
```

### Configure for StrixHalo

Set environment variables to point to your local server:

```bash
export OPENAI_API_BASE=http://localhost:8091/v1
export OPENAI_API_KEY=not-needed
export OPENAI_MODEL=qwen3-coder
```

Or create a config file:

```bash
# Run qwen-code and configure via settings
qwen
# Then use /settings to configure the API endpoint
```

### Usage

```bash
# Interactive mode in your project directory
qwen

# Headless mode for scripts/CI
qwen -p "explain this codebase"

# Single question
qwen -p "write a function to parse JSON"
```

### Features

- **Agentic coding**: Understands codebases, writes code, runs commands
- **Plan mode**: Complex multi-step task planning
- **IDE integration**: VS Code and Zed extensions available
- **Open source**: Both the CLI and model are fully open source

For more information: https://github.com/QwenLM/qwen-code

## Related Projects

- [Qwen3-Coder](https://github.com/QwenLM/Qwen3-Coder) - The model powering this server
- [Qwen Code CLI](https://github.com/QwenLM/qwen-code) - Terminal AI agent
- [llama.cpp](https://github.com/ggerganov/llama.cpp) - Inference engine
- [Unsloth GGUF](https://huggingface.co/unsloth/Qwen3-Coder-30B-A3B-Instruct-1M-GGUF) - Quantized models

## License

MIT
