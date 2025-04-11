.. title: Buying GPU for local models (llm)
.. slug: buying-gpu-for-local-models-llm
.. date: 2025-04-08 21:34:55 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text


Buying GPU for Running Large Parameter Models (LLMs) on Your Local Machine
==========================================================================

Introduction and Motivation
---------------------------

Running large language models locally comes with one major challenge: **memory**. I learned this firsthand using an NVIDIA RTX 4090 with 24 GB of VRAM. While the 4090’s raw performance is stellar, its limited VRAM quickly became a bottleneck when attempting to run models beyond roughly 30 billion parameters – the largest models simply wouldn’t fit in memory, leading to crashes or severe slowdowns. In other words, *memory size is key – even more important than memory speed or GPU compute power when it comes to huge LLMs*.

This realization led me to explore alternatives that offer more memory per dollar. I discovered that Apple Silicon, specifically the **Apple M1 Ultra** system-on-chip with **128 GB of unified memory**, provides an excellent GB/\$ ratio for local AI work. Unlike a discrete GPU, Apple’s unified memory architecture lets the GPU dynamically use a large pool of system memory at very high bandwidth (around 800 GB/s). Practically, this meant I could load models that a 24 GB GPU could not. For example, the **DeepSeek R1 70B** model (70 billion parameters) requires roughly *43–48 GB* of memory even in quantized form – well above the 4090’s VRAM. On the M1 Ultra (128 GB), I was able to run DeepSeek 70B locally by using 5-bit quantization to reduce the model size to around 43 GB, fitting comfortably in unified memory. The high memory bandwidth helps feed the model data to the GPU cores efficiently, minimizing slowdowns. This was a revelatory experience: a compact desktop (Mac Studio with M1 Ultra) could handle a 70B model that would normally require multi-GPU servers.

It’s worth noting this experiment took place before Apple’s **M3 Ultra** chip was released. The M1 Ultra proved the value of massive memory capacity for LLMs. Since then, newer hardware (like M3 Ultra and upcoming NVIDIA GPUs) has further shifted the landscape by offering even more memory or bandwidth. In the remainder of this article, we’ll compare several consumer-grade hardware options for running large LLMs locally, focusing on memory capacity, bandwidth, quantization support, model size limits, performance (measured in tokens per second), power efficiency, and cost effectiveness. We’ll also mention which popular models (such as LLaMA, DeepSeek, Mistral, Mixtral, etc.) can be run on each platform given these constraints.

Key Considerations for Local LLM Hardware
-----------------------------------------

Before diving into hardware specifics, it’s important to understand the factors that determine whether a given system can run a large LLM:

- **Memory Capacity (VRAM or Unified RAM):**  
  Large models demand *tens or hundreds of gigabytes* of memory. For example, a 65B parameter model in 16-bit floating point requires roughly 130 GB, and even 4-bit quantized versions can be 30–40 GB in size. If the model doesn’t fully fit in GPU memory, it must be offloaded to CPU RAM—which dramatically hurts throughput due to slower data transfer speeds. Apple’s unified memory allows using up to 128–192 GB (or more on newer systems) seamlessly for GPU tasks, whereas PC GPUs are currently limited to 24 GB (4090) or 48 GB (professional cards) per card. **Having enough memory is the primary requirement** to avoid performance issues.

- **Memory Bandwidth:**  
  Once a model fits, how quickly the GPU can access model data influences token generation speed. For instance, the NVIDIA RTX 4090 delivers about 1000 GB/s bandwidth from its VRAM, whereas Apple’s M1, M2, and M3 Ultra provide around 800 GB/s. Higher bandwidth means the model’s parameters and activations can be moved faster, which boosts tokens per second. (By comparison, CPU memory bandwidth is much lower, which is why pure CPU inference is significantly slower.)

- **Quantization and Model Precision:**  
  Running models at lower precision (such as int4/int5 via GGML or GGUF quantization) is a game-changer for local inference. Quantization can reduce a model’s size by 2–4× with minimal quality loss, enabling larger models to fit into limited memory. For example, LLaMA2 70B in 4-bit mode is roughly 35–40 GB—a size that could fit on a 48 GB GPU or in unified memory—whereas the full 16-bit model would be approximately 140 GB. While all the hardware options discussed can run quantized models, not all can handle full-precision models. In each section below, the maximum model size possible in full precision is noted where applicable.

- **Compute Throughput (GPU Cores/Tensor Cores):**  
  Once memory is sufficient, the raw speed of the GPU or accelerator determines how many tokens per second you can generate. NVIDIA’s GPUs excel with thousands of CUDA and Tensor cores, whereas Apple’s GPUs have fewer compute units but aim to compensate with efficiency. A high-end NVIDIA card may generate tokens faster on a given model, but if VRAM is insufficient, it may need to use a smaller quantization scheme or offload to CPU, affecting speed.

- **Power Efficiency:**  
  For users planning continuous inference or using a personal workstation, power consumption is important. A PC with a 4090 may draw 500–600 W under load, while Apple’s SoCs draw a fraction of that—often around 150–200 W for the entire system. Lower power usage can lead to long-term savings, less heat, and quieter operation.

- **Cost (Price per GB and Tokens per Dollar):**  
  Considering the high cost of consumer GPUs and Apple systems, it’s useful to compare cost effectiveness. For example, the M1 Ultra with 128 GB of unified memory offers a lower cost per GB than a single NVIDIA RTX 4090 (which has only 24 GB). However, if you are only working with models that fit in 24 GB, you may achieve higher tokens per second per dollar with an NVIDIA card. The trade-offs are clear: choose based on whether raw speed or maximum memory capacity is more important for your specific LLM use cases.

Comparative Hardware Options for Local LLMs
-------------------------------------------

In this section, we compare five consumer-level hardware setups for running large LLMs locally:

- **NVIDIA GeForce RTX 4090 (24 GB GDDR6X VRAM)** – Current generation flagship GPU.
- **NVIDIA GeForce RTX 5090 (32 GB GDDR7 VRAM, upcoming)** – Next-generation flagship (based on leaked specifications).
- **Apple M1 Ultra (128 GB Unified Memory)** – Apple Silicon (2022) with unified CPU/GPU memory.
- **Apple M2 Ultra (192 GB Unified Memory)** – Updated Apple Silicon (2023) with increased cores and memory.
- **Apple M3 Ultra (up to 512 GB Unified Memory)** – Latest high-end Apple Silicon (2025).

For each, the discussion below covers memory capacity and bandwidth, ability to run quantized versus full-precision models, performance (tokens per second), power efficiency, cost, and example model sizes that run well.

### NVIDIA RTX 4090 (24 GB VRAM)

The **RTX 4090** is a powerhouse GPU based on NVIDIA’s Ada Lovelace architecture. It features 16,384 CUDA cores and 24 GB of GDDR6X VRAM on a 384-bit bus, delivering approximately 1000 GB/s of memory bandwidth. Its strength is in raw throughput: for models that fit in 24 GB, the 4090 can generate tokens very quickly. For example, running a 7–13B parameter model (such as LLaMA-13B) with 4-bit quantization can yield generation speeds in the range of 100–130 tokens per second. Even a 30B model (when quantized) can run at high speed on this card.

However, 24 GB of VRAM is insufficient for the largest models. A 65B model quantized to 4-bit (approximately 35–40 GB) cannot fit entirely in a single 4090’s memory. Workarounds include enabling CPU offloading, but that results in significant slowdowns as the GPU waits for data from system RAM. As a result, the RTX 4090 excels for models up to about 30B but is limited when it comes to 65B–70B models (though these can run with compromises).

On the plus side, the RTX 4090 is well supported by frameworks such as exllama and TensorRT. With the right optimizations, users have achieved around 30–35 tokens per second even on a 70B model by streaming weights from the CPU. In summary, the 4090 offers excellent performance per dollar for midsize LLMs, but its 24 GB VRAM creates an upper limit on model size.

### NVIDIA RTX 5090 (Leaked Next-Gen, 32 GB VRAM)

While NVIDIA has not officially released the RTX 5090, credible leaks suggest it will feature **32 GB of GDDR7 VRAM** on a widened 512-bit bus, with a memory bandwidth of roughly 1.8 TB/s. This upgrade will increase the maximum model size that can reside entirely on the GPU. Although a 70B model quantized to 4-bit (around 35–40 GB) may still push the limits, the increased VRAM makes it more feasible to handle larger or moderately quantized models with minimal offloading.

Performance improvements are expected from increased core counts and higher bandwidth. Extrapolations indicate token throughput could be 1.5–2× that of the 4090 on models that fit entirely on GPU, meaning significantly faster speeds on midsize models and improved performance on larger ones when partial offloading is needed. However, the RTX 5090 is also anticipated to come with higher cost and power consumption, making it a premium choice for enthusiasts willing to pay for cutting-edge performance.

### Apple M1 Ultra (128 GB Unified Memory)

The **Apple M1 Ultra** is an entirely different approach. Rather than a discrete GPU, it is a system-on-chip (SoC) that combines CPU, GPU, and memory into one package. With up to **128 GB of unified memory** available to both CPU and GPU, and a memory bandwidth of around 800 GB/s, the M1 Ultra offers an outstanding memory capacity that allows very large models to be loaded entirely on device.

With this memory pool, models like LLaMA2-70B in 4-bit (about 35–40 GB) or even 8-bit quantized models (70–80 GB) can be loaded without needing to offload portions to the CPU. Although the integrated GPU in the M1 Ultra is not as fast as high-end NVIDIA offerings (with a 70B model running at around 8–12 tokens/sec compared to higher speeds on a 4090), the system shines in its energy efficiency and simplicity. A Mac Studio built with M1 Ultra typically draws only about 150 W under load, in contrast to the much higher power draws of high-end discrete GPUs.

In cost terms, while the system price of around \$5,000 is premium, the cost per gigabyte of memory is very competitive compared to building a multi-GPU PC.

### Apple M2 Ultra (192 GB Unified Memory)

The **Apple M2 Ultra** (2023) builds on the strengths of the M1 Ultra. It can be configured with up to **192 GB of unified memory** and features a GPU with up to 76 cores. While the memory bandwidth remains at about 800 GB/s, the M2 Ultra’s more powerful GPU improves performance—making it roughly 20–30% faster than the M1 Ultra on many tasks.

The increased memory capacity allows for even larger models or larger contexts, such as loading a full LLaMA2-70B in FP16 (if carefully optimized) or running two large models simultaneously. Although the token generation speed on very large models is still limited (around 36 tokens/sec on a 13B model, for instance), the M2 Ultra represents the upper limit of single-machine LLM hosting for many real-world applications, while consuming far less power than an equivalent NVIDIA system.

### Apple M3 Ultra (512 GB Unified Memory, 2025)

The latest in Apple’s lineup, the **M3 Ultra**, takes things to a new extreme. With configurations available up to **512 GB of unified memory** and an 80-core GPU, it is designed for tasks that require massive memory capacity. Although the memory bandwidth remains similar at roughly 800 GB/s, the overall architectural improvements and the sheer number of GPU cores significantly increase compute performance.

For LLM practitioners, the 512 GB option means that virtually any open-source model available—even future models with hundreds of billions of parameters—can be loaded on a single machine. In practice, this allows researchers to experiment with extremely large models, long context lengths, or even load multiple models concurrently. While the price for such configurations can be well above \$10,000, the balance of extreme memory, acceptable compute speeds, and exceptional power efficiency makes the M3 Ultra an attractive platform for those who need uncompromised model size within a desktop environment.

Comparative Summary
-------------------

Below is a summary table comparing these hardware options:

+--------------------------+------------------------+----------------+-------------------+---------------------------+
| **Hardware**             | **Memory**             | **Bandwidth**  | **Power (TDP)**   | **Approx Price**          |
+==========================+========================+================+===================+===========================+
| NVIDIA RTX 4090          | 24 GB GDDR6X           | ~1000 GB/s     | 450 W             | ~$1,600 (GPU only)        |
+--------------------------+------------------------+----------------+-------------------+---------------------------+
| NVIDIA RTX 5090          | 32 GB GDDR7            | ~1.8 TB/s      | ~600 W (est.)     | ~$2,000 (est.)            |
+--------------------------+------------------------+----------------+-------------------+---------------------------+
| Apple M1 Ultra           | 64–128 GB Unified      | ~800 GB/s      | ~90 W (chip)      | ~$5,000 (system)          |
+--------------------------+------------------------+----------------+-------------------+---------------------------+
| Apple M2 Ultra           | 64–192 GB Unified      | ~800 GB/s      | ~90 W (chip)      | ~$4,000–$7,000 (system)   |
+--------------------------+------------------------+----------------+-------------------+---------------------------+
| Apple M3 Ultra           | 96–512 GB Unified      | 800+ GB/s      | ~150 W (est.)     | ~$5,000–$10,000+ (system) |
+--------------------------+------------------------+----------------+-------------------+---------------------------+

*Note: Prices for NVIDIA GPUs are for the card only, while Apple system prices reflect full workstation configurations. Bandwidth and power for the RTX 5090 and M3 Ultra are based on early leaks and estimates.*

A Few Key Observations
~~~~~~~~~~~~~~~~~~~~~~~

- **Memory per Dollar:**  
  Apple’s offerings provide a tremendous amount of memory relative to their cost. For users whose primary goal is to run very large models, a Mac Studio with 128–192 GB (or even 512 GB) of unified memory can be more cost-effective than assembling a multi-GPU system to achieve similar memory capacity.

- **Raw Performance:**  
  NVIDIA GPUs lead in raw token generation throughput when the model fits entirely in VRAM. For models that do, the RTX 4090 (and the upcoming RTX 5090) offer faster response times. However, for extremely large models that exceed discrete GPU VRAM, performance may be compromised due to CPU offloading, at which point Apple Silicon’s unified memory systems offer a distinct advantage.

- **Scalability:**  
  NVIDIA’s multi-GPU configurations can further boost performance, although the complexity and power requirements rise. Apple’s integrated systems, while excellent in memory capacity, do not currently support multi-unit scaling in the same manner.

- **Power and Efficiency:**  
  Apple’s SoCs are far more power-efficient than high-end discrete GPUs. This is an important consideration for 24/7 inference tasks or when operating in thermally constrained environments.

- **Use Cases:**  
  For chat, coding, and inference tasks on models up to around 30B parameters, the NVIDIA RTX 4090 (or 5090) is an excellent choice. For models in the 65B–70B range or if absolute memory capacity is required for long-context or multiple simultaneous models, Apple’s M1, M2, and M3 Ultra systems offer a superior value proposition.

Conclusion
----------

By understanding the memory requirements of large LLMs and the strengths of each hardware option, you can make an informed decision on the **GPU (or SoC)** to invest in for local AI work. Whether you choose the brute-force approach of a next-gen NVIDIA GPU or the memory-rich, energy-efficient approach of Apple Silicon, it’s an exciting time—the ability to run models that once required multi-GPU servers is now within reach on a desktop machine.

References
----------

- [llama.cpp Discussions on GitHub](https://github.com/ggml-org/llama.cpp/discussions/4167)