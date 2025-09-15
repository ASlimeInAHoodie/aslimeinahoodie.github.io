---
title: BitNet - The Untapped Future of AI
date: 2025-09-15
tags:
  - blog
  - research
  - technology
  - quantum-computing
  - artificial-intelligence
---
# BitNet
On May 1$^{st}$ 2025, Microsoft released the first open-source, native 1-bit LLM called [BitNet b1.58 2B4T](https://huggingface.co/microsoft/bitnet-b1.58-2B-4T). This LLM is unique with its use of a new weighting system, allowing (in theory) usage on less powerful hardware (in terms of AI processing). This release is not only a stepping stone for smaller AI infrastructure, but also for the future, with the unknown un-utilised speciality of this underdog - quantum computing.
## Disclaimer
I am not a quantum physicist nor quantum computing researcher. My predictions I discuss here about quantum computing are of my own opinion and should not be perceived as fact. 
# Training AI
An AI you can chat to is called a Large Language Model (LLM). To create an LLM, developers must train it first on words, sentences, and other data, so that it can respond correctly to what words, sentences, and other data you input. This training consists of inputting certain data, checking how close the AI was at reaching the correct response, then adding or subtracting to a list of numbers called weights for next time. These weights correlate to a specific neuron that the AI has, and when an input is received, the weights make the AI pick the optimal neuron path from the weights to process the input and generate an output. This is, in essence, how AI is trained.

# Weight Representation and the Obesity of Current LLMs
The most popular AI LLMs (ChatGPT, Claude, Gemini, Llama, Grok) use 32-bit floats for their weighting representation system. This data type allows AI to have very tiny weights without reaching zero (E.g. `0.00000001` is not `0`), but they take up a lot of space in memory. A single 32-bit floating point number takes as much space in memory as 4 characters you can type, which also means that it takes longer to compute the weights. Therefore, to run current LLMs, you need a lot of space and fast hardware to run them, not to mention the environmental impact.

This is like an LLM being a truck driving over a bridge, with every 32-bit float as a tonne of cargo. With every tonne of cargo you add, the more engine power the truck needs to cross the bridge, and the more max weight the bridge needs to hold - other vehicles (processes) are driving on the bridge too:
- If the truck doesn't have enough engine power, it takes too long to cross the bridge, and a lot of fuel is used.
- If the bridge isn't strong enough, it collapses.

This is also what happens with systems running AI:
- If the system can't process fast enough, the AI takes too long to respond, or stops, and a lot of power is consumed.
- If the system doesn't have enough memory, the AI stops, or the system crashes.

Most LLMs utilise GPUs (Graphical Processing Units), which are really good at computing numbers, but not all machines have this hardware, so they are unable to run LLMs, or run them very poorly.
# BitNet's New Weight Representation
As outlined in the [BitNet White Paper](https://arxiv.org/pdf/2310.11453), BitNet swaps out existing training framework for their new one, and trains with 1-bit sized weights. Experiments training LLMs with this new framework achieved reduced memory usage and energy consumption while maintaining a similar performance level to existing training frameworks.
## Quantized Weights
Instead of using the large 32-bit floating point numbers as weights, this new framework makes quantized number weights. A quantized number can be only one of the following: `1`, `0` and `-1` - a 95% decrease in memory size. In addition to this smaller size, a new training framework makes the LLM adapt to a less precise weight system, allowing it to provide accurate and performant results.
# BitNet & Quantum Computing
Quantum computers run on Qubits, which are like regular computers, which use Bits, where both have are measured as a `1` or a `0`. However, these Qubits can exist in a superposition of states before being measured, allowing them to represent multiple values simultaneously. This increases the number of calculations a quantum computer can perform at once, greatly increasing processing power. However, when measuring a Qubit, it collapses into one of its two basic states: alpha (`α`) or beta (`β`).

Qubits are, like bits, a 2-level system, meaning they only have 2 results. Therefore, I don't see Qubits as the secret weapon BitNet could use. Instead, I would like to introduce to you: **Qutrits**.
## Qutrits - The Secret Weapon
A quantum trit (or qutrit) is like a Qubit, but is a 3-level system, allowing it to have three states instead of 2: alpha (`α`), beta (`β`) and gamma (`γ`). This means that they can quantize weights used for LLMs via collapsing, and collapsing requires no computing power to do.

With Qutrits and the BitNet framework, I predict that Quantum Computers will be able to train larger LLMs faster than ever compared to traditional computers, making BitNet deadly towards traditional training frameworks, killing them off one by one in exchange for Quantum-Only frameworks. That being said, I have little faith that my prediction will come true in the near future, with [estimates of 2035-2040](https://introtoquantum.org/essentials/timelines/) at the earliest for quantum computers, and the complexities/sizes of qutrits compared to qubits.
# See Also
## External Sources
### BitNet
[BitNet b1.58 2B4T LLM](https://huggingface.co/microsoft/bitnet-b1.58-2B-4T)
[BitNet b1.58 2B4T Technical Report White Paper](https://arxiv.org/pdf/2504.12285)
[BitNet White Paper](https://arxiv.org/pdf/2310.11453)
[Microsoft researches say they've developed a hyper-efficient AI model that can run on CPUs](https://techcrunch.com/2025/04/16/microsoft-researchers-say-theyve-developed-a-hyper-efficient-ai-model-that-can-run-on-cpus/)
[Microsoft researches create super efficient AI that uses up to 96 less energy](https://arstechnica.com/ai/2025/04/microsoft-researchers-create-super%E2%80%91efficient-ai-that-uses-up-to-96-less-energy/)
### Quantum Computing
[When can we expect a useful quantum computer?](https://introtoquantum.org/essentials/timelines/)
[High-fidelity Qutrit entangling gates for superconducting circuits](https://www.nature.com/articles/s41467-022-34851-z)
[Going beyond Qubits: New study demonstrates key components for a Qutrit-based quantum computer](https://newscenter.lbl.gov/2021/04/26/going-beyond-quibits/)
[Quantum Computing - IBM](https://www.ibm.com/think/topics/quantum-computing)