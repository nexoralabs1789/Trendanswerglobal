# UNVERIFIED AI DRAFT — NOT APPROVED

Primary publisher: AWS Machine Learning

Source: https://aws.amazon.com/blogs/machine-learning/full-fine-tuning-fft-on-sagemaker-ai-serverless-model-customization/

Source publication date: 2026-11-19

Human editor: read the original full article, verify each claim, check sources, and approve a separate public HTML page before publishing.

---

VERDICT: READY_FOR_HUMAN_REVIEW

SEO_TITLE: Full Fine-Tuning on SageMaker AI Serverless

META_DESCRIPTION: Learn how Amazon SageMaker AI Serverless Model Customization now supports Full Fine-Tuning (FFT) for large language models.

PRIMARY_KEYWORD: Full Fine-Tuning SageMaker AI

READER_QUESTION: How does Full Fine-Tuning work on Amazon SageMaker AI Serverless Model Customization?

SOURCE_URL: https://aws.amazon.com/blogs/machine-learning/full-fine-tuning-fft-on-sagemaker-ai-serverless-model-customization/

EVIDENCE_GAPS: Specific numerical values for the exact performance outcomes on benchmarks other than the 90.0 percent LLM-as-Judge score and MATH score drops (2–3 percent) are cut off or omitted in the source text. Complete details on all hyperparameter combinations are limited.

ARTICLE_DRAFT:
## Introduction to Full Fine-Tuning on SageMaker AI

According to AWS Machine Learning, fine-tuning allows businesses to adapt pretrained large language models (LLMs) to specific domains, tasks, or styles. Amazon SageMaker AI Serverless Model Customization now supports Full Fine-Tuning (FFT), enabling users to achieve high model quality without needing to manage infrastructure, set up clusters, configure distributed training, or provision GPUs. 

## How Full Fine-Tuning Compares to LoRA

Parameter-efficient fine-tuning (PEFT) methods, such as Low-Rank Adaptation (LoRA), update only a small subset of LLM parameters. While LoRA reduces GPU capacity requirements, the low-rank nature of its weight updates limits its representation power on complex tasks. As datasets scale beyond tens of thousands of examples, LoRA can hit an accuracy plateau due to rank deficiency.

In contrast, Full Fine-Tuning updates model parameters simultaneously. By modifying the model's weight matrix uniformly across the full representational capacity, FFT avoids the localized distortions of low-rank methods. According to the source article, FFT’s quality advantages scale with dataset size, achieving a 90.0 percent score on LLM-as-Judge semantic correctness on code generation tasks with 100K+ training examples.

## Experimentation and Evaluation on SageMaker AI

The publisher highlights experiments using the Supervised Fine-Tuning (SFT) technique on a Qwen3-4B base model with the Magicoder-Evol-Instruct-110K dataset. Using SageMaker AI Serverless Model Customization, developers can specify a base model, dataset, and hyperparameters while the service handles compute allocation and checkpoint management automatically.

SageMaker AI Serverless Model Customization includes three built-in evaluation methods:
- **LLM-as-Judge:** Uses a frontier model (Amazon Nova Pro) to evaluate the logical correctness of generated code.
- **PRIMECODE:** Measures text-similarity metrics such as BLEU, ROUGE-1/2/L, and F1 to evaluate code generation fidelity.
- **MMLU (Massive Multitask Language Understanding):** Tests across 57 academic subjects to monitor general knowledge retention and detect catastrophic forgetting.

## Practical Hyperparameter Guidelines

Because FFT modifies every parameter in the model—including those encoding pre-trained general knowledge—careful hyperparameter tuning is required to mitigate catastrophic forgetting:
- **Learning Rate:** Aggressive learning rates (such as $\text{LR} \ge 5\text{e-}5$) can permanently overwrite pretrained knowledge and cause scores on tasks like MATH to drop to 2–3 percent. Controlled tests indicated an $\text{LR}$ of $2\text{e-}5$ on large datasets worked as a safer zone.
- **Epoch Count:** Extending training beyond 3 epochs degraded performance variants in the publisher's experiments due to increased forgetting.
- **Batch Size:** Smaller batches can produce noisy updates that increase forgetting risk, while larger batches reduce noise but risk underfitting. A baseline configuration with a 5 percent warmup, batch size 8, and $\text{LR} = 2\text{e-}5$ best preserved general knowledge according to MMLU evaluations.

## What remains uncertain

The source article does not provide exhaustive numerical performance comparisons across all evaluated base models (such as DeepSeek-R1-1.5B, Nemotron-30B, and Gemma-4-26B) beyond the specific Qwen3-4B code generation experiments. Additionally, the exact details of every hyperparameter sweep configuration are generalized rather than fully exhaustively listed.