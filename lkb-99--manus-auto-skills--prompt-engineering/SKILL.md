---
name: prompt-engineering
description: "Use this skill for advanced prompt engineering techniques. Triggers: prompt engineering, chain-of-thought, few-shot learning, prompt optimization, otimização de prompt, engenharia de prompt."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Prompt Engineering

## Overview
This skill provides a comprehensive guide to advanced prompt engineering techniques. It is designed to help users craft effective prompts that maximize the performance of Large Language Models (LLMs). By leveraging techniques such as chain-of-thought, few-shot learning, and prompt optimization, users can unlock the full potential of LLMs for a wide range of tasks, from simple text generation to complex reasoning and problem-solving.

This skill is particularly useful for developers, researchers, and anyone interested in getting the most out of their interactions with LLMs. Whether you are building an AI-powered application or simply want to improve your prompting skills, this guide will provide you with the knowledge and tools you need to succeed.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: prompt, prompt engineering, chain-of-thought, few-shot learning, prompt optimization, LLM, GPT, otimizar prompt, engenharia de prompt, criar prompt
- Phrases: "como criar um bom prompt", "otimizar meu prompt", "melhorar a resposta do LLM", "usar chain-of-thought", "aprendizado few-shot"
- Context: Any discussion about improving the quality of responses from Large Language Models.

**Example user queries that trigger this skill:**
- "Como posso otimizar meu prompt para o GPT-4?"
- "Me ensine a usar a técnica de chain-of-thought."
- "Preciso de ajuda para criar um prompt para gerar código."

## When to Use This Skill
This skill is useful in a variety of situations where you need to interact with a Large Language Model (LLM). Here are a few examples:

*   **Complex Reasoning:** When you need the LLM to solve a problem that requires multiple steps of reasoning, such as a math word problem or a logic puzzle.
*   **Content Creation:** When you want to generate creative and high-quality text, such as a blog post, a poem, or a script.
*   **Code Generation:** When you need to generate code in a specific programming language, with specific requirements for functionality and style.
*   **Data Extraction:** When you want to extract specific information from a large body of text, such as a legal document or a research paper.
*   **Summarization:** When you need to summarize a long document or conversation, while preserving the key information and context.
*   **Translation:** When you need to translate text from one language to another, with a high degree of accuracy and fluency.

## Core Capabilities
This skill provides a comprehensive set of tools and techniques for advanced prompt engineering. The core capabilities include:

### 1. Chain-of-Thought (CoT) Prompting
Chain-of-Thought prompting is a technique that encourages the LLM to break down a complex problem into a series of intermediate reasoning steps. This allows the model to 'think' before giving a final answer, leading to more accurate and reliable results. This skill provides detailed guidance on how to use CoT prompting, including:

*   **Zero-shot CoT:** How to use a simple prompt like "Let's think step by step" to trigger a chain of thought.
*   **Few-shot CoT:** How to provide the LLM with a few examples of chain-of-thought reasoning to improve its performance on a specific task.
*   **Automatic CoT:** How to automate the process of creating chain-of-thought examples, saving you time and effort.

### 2. Few-Shot Learning
Few-shot learning is a technique where you provide the LLM with a small number of examples (or 'shots') of a task to help it understand what you want it to do. This is particularly useful when you want the model to perform a task that it has not been explicitly trained on. This skill will teach you:

*   **How to structure few-shot prompts:** The best way to format your examples to maximize their effectiveness.
*   **The importance of label space and input distribution:** How the choice of examples can significantly impact the model's performance.
*   **The limitations of few-shot learning:** When few-shot learning is not enough and you need to use more advanced techniques.

### 3. Prompt Optimization
Prompt optimization is the process of refining your prompts to get the best possible results from the LLM. This skill will cover a range of optimization techniques, including:

*   **Instructional Prompts:** How to write clear and concise instructions that the LLM can easily understand and follow.
*   **Role Prompting:** How to assign a role to the LLM (e.g., "You are a helpful assistant") to influence its tone and behavior.
*   **Negative Prompts:** How to tell the LLM what *not* to do, to avoid unwanted outputs.
*   **Iterative Refinement:** How to systematically test and refine your prompts to improve their performance over time.

## Step-by-Step Workflow

This section provides a step-by-step guide to using the advanced prompt engineering techniques covered in this skill.

### 1. Define Your Goal

Before you start writing a prompt, it's important to have a clear understanding of what you want to achieve. What is the task you want the LLM to perform? What is the desired output? The more specific you are, the better the results will be.

### 2. Choose the Right Technique

Based on your goal, you can choose the most appropriate prompt engineering technique.

*   **For simple tasks:** Start with a zero-shot prompt. This is the simplest and most straightforward approach.
*   **For more complex tasks:** If a zero-shot prompt doesn't give you the desired results, try a few-shot prompt. Providing a few examples can significantly improve the model's performance.
*   **For tasks that require reasoning:** If the task requires the LLM to think through a problem step-by-step, use chain-of-thought (CoT) prompting.

### 3. Craft Your Prompt

Once you have chosen a technique, you can start crafting your prompt. Here are some tips for writing effective prompts:

*   **Be clear and concise:** Use simple language and avoid jargon.
*   **Be specific:** Provide as much detail as possible about the task and the desired output.
*   **Use examples:** As we've seen, providing examples is a powerful way to improve the model's performance.
*   **Use instructions:** Tell the LLM exactly what you want it to do.
*   **Use roles:** Assigning a role to the LLM can help to guide its behavior.

### 4. Test and Refine

Once you have written a prompt, it's important to test it to see if it gives you the desired results. If not, you can refine the prompt and try again. This is an iterative process, and it may take a few tries to get the perfect prompt.

Here is an example of how you might use this workflow to solve a math word problem:

**1. Goal:** Solve the following math word problem: "I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?"

**2. Technique:** This problem requires a few steps of reasoning, so we will use chain-of-thought (CoT) prompting.

**3. Prompt:**
```
I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?

Let's think step by step.
```

**4. Test and Refine:** The model should output a step-by-step explanation of how to solve the problem, and the correct answer. If not, we can try to refine the prompt by providing more explicit instructions or examples.

## Best Practices

Here are some best practices to keep in mind when using this skill:

*   **Be specific and clear in your instructions.** The more specific you are, the better the LLM will be able to understand what you want. Avoid ambiguity and use simple, direct language.
*   **Provide context.** If the task requires some background information, be sure to include it in the prompt. This will help the LLM to generate a more relevant and accurate response.
*   **Use the right temperature.** The temperature setting controls the randomness of the LLM's output. A higher temperature will result in more creative and unpredictable responses, while a lower temperature will result in more conservative and predictable responses. Experiment with different temperature settings to find the one that works best for your task.
*   **Experiment with different phrasing.** The way you phrase your prompt can have a big impact on the output. If you're not getting the results you want, try rephrasing your prompt in a few different ways.
*   **Use negative prompts to avoid unwanted content.** If there are certain things you don't want the LLM to include in its response, you can use a negative prompt to tell it what to avoid.
*   **Review and edit the output.** Even with the best prompts, the LLM may not always generate a perfect response. It's important to review the output and edit it as needed to ensure that it meets your standards.

## Examples

Here are some practical examples of how to use the techniques covered in this skill.

### Example 1: Chain-of-Thought for a Logic Puzzle

**Goal:** Solve a logic puzzle.

**Prompt:**
```
There are three friends: Alice, Bob, and Carol. Each of them has a different pet: a cat, a dog, and a fish. We know the following:
1. Alice is not the owner of the cat.
2. Bob is the owner of the dog.

Who owns the fish?

Let's think step by step.
```

**Expected Output:**
```
1. We know that Bob is the owner of the dog.
2. This means that neither Alice nor Carol can be the owner of the dog.
3. We also know that Alice is not the owner of the cat.
4. Since there are only three pets (cat, dog, and fish), and Alice doesn't own the cat or the dog, she must own the fish.
Therefore, Alice owns the fish.
```

### Example 2: Few-Shot Learning for Sentiment Analysis

**Goal:** Classify the sentiment of a movie review as positive or negative.

**Prompt:**
```
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
```

**Expected Output:**
```
Negative
```

### Example 3: Template for a Complex Prompt

**Goal:** Generate a detailed blog post about a specific topic.

**Prompt:**
```
As an expert in [Topic], write a comprehensive and engaging blog post about [Specific Subject].

The blog post should be at least [Word Count] words long and should cover the following key points:

1.  [Key Point 1]
2.  [Key Point 2]
3.  [Key Point 3]

The tone of the blog post should be [Tone] and the target audience is [Target Audience].

Please include at least one practical example or case study to illustrate your points.

Use clear and concise language, and format the blog post with headings, subheadings, and bullet points for readability.
```

## References

*   [Prompt Engineering Guide](https://www.promptingguide.ai/)
*   [Advanced Prompt Engineering Techniques](https://www.mercity.ai/blog-post/advanced-prompt-engineering-techniques)
*   [Chain-of-Thought Prompting](https://www.promptingguide.ai/techniques/cot)
*   [Few-Shot Prompting](https://www.promptingguide.ai/techniques/fewshot)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
