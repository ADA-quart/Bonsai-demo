# Bonsai 2 Community Benchmarks

Benchmark results submitted by the community for **Bonsai 2** (the ternary hybrid-attention 27B
release, [`prism-ml/Ternary-Bonsai-2-27B-gguf`](https://huggingface.co/prism-ml/Ternary-Bonsai-2-27B-gguf)).

Bonsai 2 is its own generation, not a new packing of the previous Ternary-Bonsai family: the weights
live in a rotated basis, so the two generations are not interchangeable and the numbers here are
**not** directly comparable with the [ternary-bonsai](../ternary-bonsai/) table. Bonsai 2 needs the
binaries from this demo / the [PrismML fork](https://github.com/PrismML-Eng/llama.cpp); stock llama.cpp
refuses the files (see [MODEL-FORMATS.md](../../MODEL-FORMATS.md)).

## Results

### Bonsai 2 27B

Sorted by decode speed (TG128). Both bands are measured with `-ngl 99 -fa 1 -p 512 -n 128 -r 3`,
default f16 KV cache unless noted.

| Hardware | Backend | Band | PP512 (t/s) | TG128 (t/s) | DSpark TG (t/s) | Details |
|----------|---------|------|------------:|------------:|----------------:|---------|
| NVIDIA Tesla V100-SXM2 16 GB | llama.cpp CUDA (Windows) | `PQ2_0` | 798 | 46.0 | | [link](cuda-tesla-v100-windows.md) |

The V100 report also contains `PTQ1_0` numbers (852 t/s pp512 / 34.3 t/s tg128), a prebuilt-vs-native
binary comparison, and q4_0 KV-cache long-context results.

## Formats

| band | bits/weight | size (27B) | notes |
|------|------------:|-----------:|-------|
| `PQ2_0` | 2.13 | 6.70 GiB | group-128 packing; usually the fastest decode on H100/A100/Blackwell, faster prompt processing everywhere |
| `PTQ1_0` | 1.75 | 5.53 GiB | smaller; usually the faster decode on Ada-generation cards and the L4 |

Both bands need the fork's kernels. `setup.ps1` / `setup.sh` downloads `PQ2_0` by default; grab the
smaller band as well if you have the disk:

```bash
hf download prism-ml/Ternary-Bonsai-2-27B-gguf --include "*PTQ1_0*" --local-dir models/bonsai2-gguf/27B
```

## How to Submit

There is no Bonsai 2 template yet (the [ternary-bonsai template](../ternary-bonsai/TERNARY-TEMPLATE-llama-cpp.md)
still describes the previous generation's files, so do not copy its download commands). Use the report
in this folder as the structure to follow:

1. Run `./setup.ps1` (Windows) or `./setup.sh` to download Bonsai 2 27B and the binaries; add the other
   band with the `hf download` line above.
2. Run `llama-bench` from the binaries the demo installed, e.g.
   `bin/cuda/llama-bench -m models/bonsai2-gguf/27B/Ternary-Bonsai-2-27B-PQ2_0.gguf -ngl 99 -fa 1 -p 512 -n 128 -r 3`.
3. Save the report here as `<backend>-<hardware>-<os>.md` (lowercase, dashes for spaces), paste the raw
   `llama-bench` output as-is, and note the KV-cache type and anything that affects the numbers
   (desktop VRAM in use, power limits, thermals).
4. Open a PR against this repo.
