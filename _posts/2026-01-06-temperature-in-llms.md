---
title: 'Temperature in LLMs: From Softmax to Autoregressive Decoding'
date: 2026-01-06
permalink: /posts/2026/01/temperature-in-llms/
tags:
  - LLM
  - Inference
  - Sampling
---

![Temperature in LLMs](/assets/images/posts/temperature-llms.png)
*Figure 1: Visualizing the effect of Temperature on Softmax logits and token probability distributions.*

## Fundamentals of Decoding Strategies in Large Language Models

Most LLMs use decoder-only architectures, and understanding temperature is key to controlling their behavior. **Colder temperature means more deterministic behavior.**

In the context of a language model, the probability of the next token (word or sub-word unit) is given by a distribution $P$, which is a function of the model's internal parameters and the input context. When generating a token, the model samples from this distribution. The temperature parameter scales the logits (pre-softmax activations) of this distribution as follows:

$$\text{Scaled logits} = \frac{\text{Original logits}}{T}$$

After scaling the logits, they are passed through the softmax function to produce the final distribution from which the next token is sampled:

$$P(token_i) = \frac{e^{z_i/T}}{\sum_j e^{z_j/T}}$$

### Temperature Effects on Token Sampling

Here's what happens when you adjust the temperature:

**High Temperature ($T > 1$):** The scaling effectively "flattens" the distribution, making less probable tokens more likely to be sampled. This results in more "random" or "creative" outputs.

**Low Temperature ($0 < T < 1$):** The distribution becomes "sharper," concentrating the probability mass on the most likely tokens. This results in more "deterministic" outputs, where the language model is more likely to pick the most probable word according to its training data.

**$T = 1$:** The logits remain unchanged, and the original probabilities are used for sampling.

Mathematically, as $T \to 0$, the softmax function turns into a kind of "argmax" function, where the token with the highest logit gets a probability of nearly 1, while all other tokens get a probability of nearly 0. In other words, the model becomes completely deterministic, always picking the most likely next token according to its training.

So, in the case of language models, lowering the temperature leads to more deterministic behavior by narrowing the distribution from which the next token is sampled, making it increasingly likely that the model will select the most probable token according to its training data.

## Example: Temperature Approaching Zero

**Q: Can you give me an example with numbers of temperature = 0?**

In practical applications, a temperature of exactly $T = 0$ is not used because this would cause division by zero or undefined behavior. Instead, very small temperatures, close to zero, might be used to approach deterministic behavior.

However, for the sake of this example, let's assume we have a hypothetical system where the temperature can be set to $T = 0$. We'll see what effect this would have on the softmax distribution.

Let's say the logits (pre-softmax activations) outputted by a model for a specific token sequence are $[2, 1, -1]$. These logits represent three potential next tokens in the sequence.

### Without Temperature Scaling ($T = 1$)

The softmax probabilities would be:

$$P = \left[ \frac{e^2}{e^2 + e^1 + e^{-1}}, \frac{e^1}{e^2 + e^1 + e^{-1}}, \frac{e^{-1}}{e^2 + e^1 + e^{-1}} \right]$$

Which, when calculated, gives:

$$P \approx [0.67, 0.25, 0.08]$$

This means that without temperature scaling, the model would predict the first token with a 67% probability, the second with a 25% probability, and the third with an 8% probability.

### Understanding the Exponential Function Behavior

The underlying principle is rooted in the properties of exponential functions:

- **Exponential growth:** As the exponent becomes large, the exponential function grows very rapidly.
- **Exponential decay:** As the exponent becomes more negative, the exponential function approaches zero very rapidly.

As temperature approaches zero, the differences between logits become exponentially amplified in the softmax, making the highest logit overwhelmingly dominant in the probability distribution.

