.. title: Apple silicon limitations with usage on local LLM 
.. slug: apple-silicon-limitations-with-usage-on-local-llm 
.. date: 2025-04-09 21:08:05 UTC 
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Maximizing LLM Usage on a 128GB M1 Ultra (Apple Silicon Mac Studio)
===================================================================

Understanding Unified Memory and the 96GB “VRAM” Limit
------------------------------------------------------

Apple’s M1 Ultra chip uses *unified memory*, meaning the CPU and GPU
share the same 128 GB RAM pool. However, macOS does **not** allow the
GPU to use all 128 GB for graphics/compute tasks by default. In
practice, about **75% of the physical memory** is the recommended
maximum for GPU usage. This is why tools like Ollama (which uses Apple’s
Metal Performance Shaders for acceleration) report roughly **96 GB** as
available “GPU memory” on a 128 GB system – the remaining 25% is
reserved for the OS and CPU tasks.

In Apple’s Metal API, the property
```recommendedMaxWorkingSetSize`` <https://developer.apple.com/documentation/metal/mtldevice/%20recommendedmaxworkingsetsize>`__
reflects this limit. It’s essentially the **largest memory footprint**
the GPU can use “without affecting performance” (i.e. without causing
memory pressure or swaps). For a 128 GB Mac, this value comes out to
about 96 GB. (By comparison, a 64 GB Mac has ~48 GB usable for GPU, and
a 32 GB Mac only ~21–24 GB for GPU, which is ~65–75% of RAM.) In short,
**Ollama showing 96 GB is not a bug** – it’s reflecting an Apple-imposed
limit on how much unified memory the GPU backend (Metal/MPS) will
utilize.

Why is Only 75% of Memory Usable by the GPU?
--------------------------------------------

This behavior appears to be a **deliberate system design**. Apple likely
reserves a portion of unified memory to ensure the system remains
responsive and to avoid GPU allocations evicting critical data needed by
the OS or CPU. Apple’s documentation calls this a *“recommended”*
working set size, but in practice it functions as a **hard cap** for GPU
memory in user-space programs. Developers on Apple’s forums note that
the limit is roughly 75% of physical RAM, and it’s *hard-coded* in
current macOS drivers. Unfortunately, Apple hasn’t been very public
about this constraint (leading some to call it *“false advertising”*
when touting unified memory sizes).

**Is this a Metal API limitation or Ollama’s fault?** It’s fundamentally
a limitation of the **Metal drivers / macOS GPU memory manager**, not
something specific to Ollama. Any software using GPU acceleration on
macOS (be it Ollama’s ggml/Metal backend, PyTorch with MPS, TensorFlow
Metal, etc.) will see a similar ceiling. Ollama is built on
``llama.cpp`` with Metal support, so it inherits the same constraint. In
other words, by default **no single process can use the full 128 GB for
GPU computations** – about 32 GB will be left unused by the GPU (though
the CPU can still use that portion for other things).

Apple’s own guidance to developers suggests breaking up tasks to fit
within the recommended working set or stream data in/out as needed. For
**LLMs**, however, the entire model often needs to reside in memory for
fast inference, so “just split the job” isn’t straightforward. If an LLM
model exceeds the GPU allotment, one of two things typically happens:

-  **Partial CPU Offload:** Frameworks may load whatever fits on the GPU
   and offload the remainder to CPU. This allows the model to run, but
   **slows performance** since the CPU is much slower for matrix
   multiplies. (For example, on Apple M2 Max, GPU inference for a 65B
   model was nearly *2× faster* than CPU inference. So keeping as much
   on the GPU as possible is key to performance.) In Ollama/llama.cpp,
   if your model is slightly too large for GPU memory, you might notice
   the last few layers or the KV cache running on CPU, causing a
   slowdown in token generation speed.
-  **Out-of-Memory Error or Eviction:** If the model *far* exceeds the
   GPU limit, you may simply get an error or the process will use macOS
   virtual memory (disk swap), leading to **severe slowdowns**. MacOS
   does not automatically “expand” the GPU memory beyond the recommended
   limit, so an oversized model can fail to load under Metal. For
   instance, a user found that a LLaMA2 70B 6-bit model (~52.5 GB) could
   not fully load on a 64 GB Mac until they reduced the GPU use or split
   some of it to CPU, since only ~48 GB was available to the GPU by
   default.

In summary, the **96 GB cap on a 128 GB M1 Ultra** is a safety mechanism
in macOS’s Metal driver. It’s not easily changed via normal settings,
and it affects all apps using the GPU. Next, we’ll explore how you *can*
override or work around this limit to better utilize your high-memory
Mac for local LLMs.

Increasing GPU Memory Utilization (Beyond the 96 GB Default)
------------------------------------------------------------

While Apple doesn’t provide an official toggle to use more of the
unified memory, advanced users have discovered a way to **manually raise
the GPU memory limit** on Apple Silicon Macs. This involves using a
**``sysctl`` command** to adjust a hidden kernel parameter. *Use these
tweaks at your own risk:* they are not officially supported by Apple
(and may require disabling System Integrity Protection in some cases).

**How to override the VRAM limit on macOS:**

1. **Determine a safe memory split.** Decide how much RAM to allocate to
   GPU tasks vs. leave for the OS/CPU. It’s wise *not* to give 100% of
   RAM to the GPU, or you risk starving the OS. A common recommendation
   is to leave at least **8–16 GB for the system**. For a 128 GB Mac
   Studio, you might aim to allocate around **120 GB** to the GPU and
   keep ~8 GB for the OS. (120 GB is 93.75% of 128 GB – users have
   reported success with this, effectively raising the limit from 96 GB
   to 120 GB.)

2. **Run the ``sysctl`` command to set the new limit.** Open the
   Terminal app and enter:

   .. code:: bash

      sudo sysctl iogpu.wired_limit_mb=<desired_MB>

   Replace ``<desired_MB>`` with the amount of memory (in MB) you want
   the GPU to be allowed to use. For example, to set ~120 GB, use
   ``iogpu.wired_limit_mb=122880`` (since 120×1024 = 122880 MB). For
   96 GB (the default on 128), it would be 98304, and for 128 GB (not
   recommended), 131072. On **macOS Sonoma (14.x)** and later, use the
   ``iogpu.wired_limit_mb`` key. *(On older macOS versions (e.g. Ventura
   or Monterey), the key was named ``debug.iogpu.wired_limit`` and took
   a value in bytes. Sonoma simplified it to MB and changed the name.)*

   After running the command with ``sudo``, enter your password if
   prompted. No reboot is required – the setting takes effect
   immediately. For example, one user with a 64 GB M1 Max ran
   ``sudo sysctl iogpu.wired_limit_mb=57344`` (56 GB) and saw the Metal
   ``recommendedMaxWorkingSetSize`` jump accordingly in the logs,
   allowing the GPU to use 56 GB instead of the default ~48 GB.

3. **Verify the new GPU memory availability.** You can check that the
   limit changed by looking at your LLM application’s logs or using
   system tools:

   -  In **Ollama**, run a model and then check the Ollama server log.
      You should see a line like
      ``ggml_metal_init: recommendedMaxWorkingSetSize = XXXX MB``. After
      the sysctl tweak, this number should reflect your new setting
      (e.g. ~120000 MB) rather than the old ~98304 MB.
   -  You can also use ``sysctl iogpu.wired_limit_mb`` without ``=`` to
      read the current value and confirm it’s set.
   -  The ``ollama ps`` command will show the GPU memory usage for a
      loaded model (e.g. “25 GB – 100% GPU” for a 25 GB model). After
      raising the limit, you’ll be able to load larger models before
      that hits 100%.

4. **(Optional) Persist the setting across reboots.** By default, the
   change made by ``sysctl`` will reset on reboot (the parameter goes
   back to ``0``, which means “use default 75%”). If you want this
   change to apply automatically at startup, you can add the setting to
   **``/etc/sysctl.conf``** (create the file if it doesn’t exist) in the
   format:

   ::

      iogpu.wired_limit_mb=<desired_MB>

   Keep in mind that to modify this file, you might need to **disable
   System Integrity Protection (SIP)** on your Mac, since altering
   certain kernel parameters permanently is restricted. Many users
   simply re-run the ``sudo sysctl`` command when needed, to avoid
   disabling SIP. Given that it’s a one-liner and only needed when you
   plan to do heavy ML work, manually setting it is usually fine. (If
   you do add it to sysctl.conf, and later want to revert to defaults,
   remove the line and reboot, or set it back to 0 which tells macOS to
   use the default 75% limit.)

**Important cautions:** Pushing the GPU memory limit closer to 100% of
RAM can cause instability if macOS or other apps suddenly need more
memory. Monitor **Memory Pressure** in Activity Monitor – if it stays
green while running your model, you’re okay; if it goes red or the
system starts swapping, consider dialing back the GPU allocation. In
practice, leaving a buffer for the OS (and for things like the LLM’s
CPU-based components) is crucial. Many folks find leaving ~8–16 GB for
system use keeps things stable even under heavy load.

Other Workflows to Maximize Hardware Utilization
------------------------------------------------

Aside from raising the Metal memory cap, here are additional tips and
configurations to fully leverage a 128 GB M1 Ultra for local LLM
inference:

-  **Use Quantized Models to Fit More in Memory:** Take advantage of
   quantization (4-bit, 5-bit, or 8-bit) so that large models consume
   less memory. For example, LLaMA-2 70B in 4-bit precision might use
   ~35–40 GB, which easily fits in the 96 GB GPU limit (even leaving
   room for a large context window). With 128 GB total, you could even
   load a 70B model at 8-bit (~70–80 GB) entirely in GPU memory after
   the sysctl tweak (since 8-bit 70B is too large for 96 GB but could
   fit in ~120 GB). Smaller quantization not only saves memory but also
   reduces memory bandwidth usage, often improving throughput. *Note:*
   Apple’s MPS backend (used by Ollama/llama.cpp) supports 4-bit and
   5-bit quantization via the ggml library, but very *high* quantization
   (2-bit) may not be supported or may degrade quality significantly.
   Choose the smallest model precision that still gives you acceptable
   accuracy.

-  **Optimize Context Length vs. Speed:** If you increase the context
   length (the token window for prompts/history), be aware it linearly
   increases memory usage for the model’s KV cache. A longer context can
   cause the GPU memory usage to balloon and potentially exceed the
   limit, forcing a fallback to CPU memory (which will slow down
   generation). For example, going from 2048 tokens to 8192 tokens
   context will roughly 4× the memory required for the KV cache. If you
   find that long conversations slow down on your model, it might be
   because you’ve exceeded the GPU memory and spilled into CPU RAM. To
   max out performance, try to **keep context length within what your
   GPU portion can handle**. You can monitor this via ``ollama ps`` (it
   shows how much VRAM the model + context is using). If you need
   ultra-long contexts, consider the memory tweak above, or use a
   smaller model that leaves headroom for the KV cache.

-  **Leverage CPU RAM for Overflow (Hybrid Offloading):** The nice thing
   about unified memory is that even when you hit the GPU’s recommended
   limit, the remainder of the model *can* reside in normal RAM.
   Frameworks like llama.cpp will automatically use CPU for the parts
   that don’t fit on GPU. While pure GPU use is fastest, this hybrid
   approach means you **can** technically load models larger than 96 GB
   – up to the full 128 GB – but with some of the work handled by CPU.
   If a model is just slightly over the GPU limit, this is efficient:
   e.g., a 75 GB model on a 128 GB Mac might run 96 GB on GPU and the
   other ~-20 GB on CPU. It will be slower than fully on-GPU, but still
   functional. *Note:* Ollama doesn’t currently expose a manual setting
   for “GPU layers” vs “CPU layers”, but llama.cpp’s CLI does (the
   ``-ngl`` flag specifies number of GPU layers to offload). In
   practice, just attempt to load the model – if it’s over budget, the
   system will offload automatically. If performance is too slow, you
   may need to choose a smaller model or reduce context.

-  **CPU-Only Inference as a Fallback:** In cases where GPU acceleration
   isn’t working (e.g., a model with unsupported operations on MPS, or
   if you want to use **vLLM** which currently lacks MPS support), you
   can run on the 20-core CPU of the M1 Ultra. The CPU can use the
   entire 128 GB for the model, no 75% restriction. The downside is
   speed: CPU inference is much slower for large models. Still, for
   certain large models that simply cannot fit in 96 GB (even
   quantized), CPU mode is an option. Ollama does not have a simple
   switch for CPU-only, but you could use the underlying ``llama.cpp``
   compiled without Metal, or another library’s CPU path. Expect that a
   70B model on CPU will be **several times slower** than on the Apple
   GPU (e.g., <1 token/sec in worst cases). This is really a last resort
   if GPU memory is exhausted or if using a backend like vLLM which, as
   of 2025, runs on CPU on Macs.

-  **Stay Updated with Apple’s Tools:** Apple is actively improving
   their machine learning support on Mac. Ensure you’re on the latest
   macOS (newer versions sometimes raise or optimize memory limits) and
   using the latest version of Ollama/llama.cpp. For instance, macOS
   updates have reportedly adjusted the usable GPU memory fraction for
   some configs (some older systems saw ~65% use, newer ones 75%).
   Likewise, ``llama.cpp`` and Ollama are rapidly evolving – newer
   releases might have better memory management, faster Metal kernels,
   or support for the Apple Neural Engine (ANE) if Apple ever opens it
   up. Keeping these updated will help maximize performance.

-  **High-Power Mode and Cooling (if applicable):** On a Mac Studio, you
   don’t have “Turbo” or “High Power” mode like MacBook Pros do, but you
   should still ensure the machine has adequate cooling (the Mac
   Studio’s fans should ramp up under load – make sure they’re not
   obstructed or overly dusty). The M1 Ultra will thermal throttle if it
   somehow overheats (less likely in a desktop chassis, but worth noting
   for sustained jobs). A cool environment can sustain peak GPU
   frequency for longer, which helps throughput.

-  **Parallelism and Batch Inference:** If you’re serving an LLM with
   Ollama (which acts as a local API server), you might handle multiple
   requests or streams at once. The M1 Ultra has **20 CPU cores** (16
   performance + 4 efficiency) and a 64-core GPU, which can handle some
   parallel work. However, single large model inference is mostly bound
   by the GPU compute for each token. You won’t easily get *more*
   tokens/sec by running two queries concurrently – they’ll just share
   resources. For maximum single-query performance, focus on one model
   at a time. If you need to serve multiple users or models, consider
   running one model on GPU and another on CPU (or using two separate
   Mac machines), to avoid contention. Also, when running a generation,
   try to avoid other heavy GPU tasks (don’t, say, be gaming or running
   a video export) which could also contend for that 96 GB GPU
   allocation.

-  **Use Efficient Serving Backends:** Tools like **vLLM**, **TGI (Text
   Generation Inference)**, or others can optimize prompt handling and
   token outputs for throughput. On Mac these may run in CPU mode (since
   vLLM doesn’t yet use Metal), but they can still leverage
   multi-threading and batch prompts together. If your goal is to
   **maximize token output throughput** for many prompts, a specialized
   server like vLLM might offer better efficiency in how it uses the CPU
   cache and memory. (For example, vLLM is designed to reuse KV cache
   across requests to avoid re-computation, which can be beneficial even
   on CPU.) Just note that any CPU-based approach will be slower per
   token than GPU-based inference.

Conclusion and Further Reading
------------------------------

In summary, the reason your M1 Ultra shows only ~96 GB for LLMs is due
to Apple’s **Metal memory management**, which by default caps GPU memory
usage to about 75% of unified RAM. This is a system-level safeguard, not
a flaw in Ollama. To fully utilize your 128 GB machine for large models,
you can apply the **``sysctl`` tweak** to raise the limit (e.g. up to
~120 GB GPU use), and employ other strategies like quantization and
careful context length management to stay within budget. With these
adjustments, a Mac Studio M1 Ultra becomes a very capable box for local
LLM inference – able to run models in the 70B parameter class (and
beyond, with quantization) entirely from memory, something traditional
GPUs (with fixed VRAM) often cannot do.

For more details and community discussions, you may find the following
resources helpful:

-  Apple Developer Forums – *“recommendedMaxWorkingSetSize – is there a
   way to use all our unified memory for GPU?”* (discussion of the 75%
   limit and Apple’s stance).
-  GitHub: llama.cpp Issue
   `#1870 <https://github.com/ggerganov/llama.cpp/issues/1870>`__ – *GPU
   Memory problem on Apple M2 Max 64GB* (users report the 75% limit and
   share performance observations).
-  Peddal’s Blog – *Optimizing VRAM Settings for Local LLM on macOS*
   (step-by-step guide to using ``sudo sysctl iogpu.wired_limit_mb`` to
   increase usable VRAM, with examples).
-  Reddit: r/LocalLLaMA – various threads (e.g. users running 70B on
   96GB Macs, etc.) where the 75% memory issue is discussed and the
   ``iogpu.wired_limit`` workaround is shared. *(Look for keywords “VRAM
   75%” or “iogpu.wired_limit” in those discussions.)*
-  Hacker News: *High-RAM Apple Silicon for large models?* – contains
   insights into Apple GPU vs NVIDIA, and limitations of the Metal
   backend for ML.
-  **Ollama’s documentation** – for any settings related to performance,
   and **llama.cpp’s README** for tips on Metal and thread tuning.

By combining these techniques, you should be able to **max out** your
Mac Studio’s capabilities, running cutting-edge LLMs locally with as
much of that 128 GB put to work as possible. Good luck, and happy
experimenting with your local AI models!
