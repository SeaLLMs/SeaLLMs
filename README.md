<p align="center">
  <img src="seal_logo.png" width="200" />
</p>

# SeaLLMs - Large Language Models for Southeast Asia


<p align="center">
🤗 <a href="https://huggingface.co/spaces/SeaLLMs/SeaLLM-Chat-13b">Hugging Face DEMO</a>
</p>

We introduce SeaLLM - a family of language models optimized for Southeast Asian (SEA) languages. The SeaLLM-base models (to be released) were pre-trained from [Llama-2](https://huggingface.co/meta-llama/Llama-2-13b-hf), on a tailored publicly-available dataset, which comprises mainly Vietnamese 🇻🇳, Indonesian 🇮🇩 and Thai 🇹🇭 texts, along with those in English 🇬🇧 and Chinese 🇨🇳. The pre-training stage involves multiple stages with dynamic data control to preserve the original knowledge base of Llama-2 while gaining new abilities in SEA languages.

The [SeaLLM-chat](https://huggingface.co/spaces/SeaLLMs/SeaLLM-Chat-13b) model underwent supervised finetuning (SFT) on a mix of public instruction data (e.g. [OpenORCA](https://huggingface.co/datasets/Open-Orca/OpenOrca)) and a small internally-collected amount of natural queries from SEA native speakers, which **adapt to the local cultural norms, customs, styles and laws in these regions**, as well as other SFT enhancement techniques (to be revealed later). 

Our customized SFT process helps enhance our models' ability to understand, respond and serve communities whose languages are often neglected by previous [English-dominant LLMs](https://arxiv.org/abs/2307.09288), while outperforming existing polyglot LLMs, like [BLOOM](https://arxiv.org/abs/2211.05100) or [PolyLM](https://arxiv.org/pdf/2307.06018.pdf).

Our [first released SeaLLM](https://huggingface.co/spaces/SeaLLMs/SeaLLM-Chat-13b) supports Vietnamese 🇻🇳, Indonesian 🇮🇩 and Thai 🇹🇭. Future verions endeavor to cover all languages spoken in Southeast Asia.

- DEMO: [SeaLLMs/SeaLLM-Chat-13b](https://huggingface.co/spaces/SeaLLMs/SeaLLM-Chat-13b)
- Model weights: To be released.
- Technical report: To be released.

<blockquote style="color:red">
<p><strong style="color: red">Terms of Use</strong>: By using our released weights, codes and demos, you agree and comply with the following terms and conditions:</p>
<ul>
<li>Follow LLama-2 <a rel="noopener nofollow" href="https://ai.meta.com/llama/license/">License</a> and <a rel="noopener nofollow" href="https://ai.meta.com/llama/use-policy/">Terms of Use</a>.</li>
<li>Strictly comply with the local regulations where you operate at and not attempt to generate or illicit our models to generate locally and internationally illegal and inappropriate content.</li>
</ul>
</blockquote>

> **Disclaimer**:
> We must note that even though the weights, codes and demos are released in an open manner, similar to other pre-trained language models, and despite our best effort in red teaming and safety finetuning and enforcement, our models come with potential risks influenced by complex factors, including but not limited to over-diversified, inaccurate, misleading or potentially harmful generation.
> Developers and stakeholders should perform their own red teaming and provide related security measures before deployment, and they must abide by and comply with local governance and regulations.
> In no event shall the authors be held liable for any claim, damages, or other liability arising from the use of the released weights, codes or demos.

> The logo was generated by DALL-E 3.

The following sections summarize the technical specifications and performance evaluations.

## Pre-training

### Vocabulary Expansion
Like many English/Latin-dominant LLMs, Llama-2's BPE tokenizer breaks non-european and non-latin linguistic texts into unsustainably long byte-level sequences that cover much shorter semantic meanings, leading to [degraded performance](https://arxiv.org/abs/2306.11372). For instance, it takes 4.3x more tokens to encode the same sentence in Thai compared to that in English. This leads to the models failing to perform summarization and comprehension tasks without exceeding the context length.

Our goal for vocabulary expansion is threefold: (1) the number of newly-added tokens must be minimal and only cover the new languages, (2) the tokens should bring the compression ratios of new languages close to that of English, and (3) minimize the disruption of existing European tokens to preserve Llama-2 knowledge. In the end, we obtain **~11K** new tokens for Vi, Id, Th and Zh to augment the original 32000-token vocabulary. Details of our expansion technique will be revealed in our upcoming technical report.

As seen in the below table, our new vocabulary reduce the compression ratio from 4.29 to 1.57 for Thai, meaning it can now encode 2.7x longer Thai text given the same context length. Meanwhile, English is only compressed by 0.3%, thus preserving its integrity.

|Language | Llama's ratio | Our ratio | # New tokens
| --- | --- | --- | --- |
| Vi | 2.91 | 1.2488 | 2304
| Zh | 1.99 | 1.1806 | 3456
| Th | 4.29 | 1.5739 | 1536
| Id | 1.76 | 1.1408 | 3840
| En | 1.00 | 0.9976


### Pre-training Data

**Pending Lixin's**

### Pre-training Strategies

We conduct pre-training in 4 different stages. Each stage serves different specific objectives and involves dynamic control of data mixture, both unsupervised and supervised, and data specification and categorization. We also employ a novel sequence construction and masking techniques during these stages. More details are to be provided in the technical report.

As our goal is for Llama-2 to learn new languages with the least number tokens and computing resources, we control appropriate data mix of new (Vi, Id & Th) and old (En, Zh) languages so that the new vocabulary and knowledge is trained quickly, while relatively maintaining the performance of the original Llama-2 model and establishing a knowledge bridge between new and existing languages.

We pre-train our SeaLLM-base in ~4 weeks on 32gpus, clocking ~150B tokens.

## Supervised Finetuning (SFT)

### SFT Data

Our supervised finetuning (SFT) data consists of many categories. The largests of them are public and open-source, such as [OpenORCA](https://huggingface.co/datasets/Open-Orca/OpenOrca) and [Platypus](https://huggingface.co/datasets/garage-bAInd/Open-Platypus). As the aforementioned is monolingual, we employ several established or novel automatic techniques to gather more instruction data for SEA languages. 

More importantly, we engaged native speakers to collect a small amount of natural queries and responses data, which adapts to the local cultural customs, norms and laws. We also collect country-relevant safety data that covers many culturally and legally sensitive topics in each of these countries, which are often ignored, or even in conflict, with western safety data. Therefore, we believe our models are more local-friendly and abide by local rules to a higher degree.

### SFT Strategies

We conduct SFT with a relatively balanced mix of SFT data from different categories. We make use of the system prompt during training, as we found it helps induce a prior which conditions the model to a behavioral distribution that focus on safety and usefulness.


## Evaluation

### Peer Comparison

One of the most reliable ways to compare chatbot models is peer comparison. With the help of native speakers, we built an instruction test set that focus on various aspects expected in a user-facing chatbot, namely (1) NLP tasks (e.g. translation & comprehension), (2) Reasoning, (3) Instruction-following and (4) Natural and Informal questions. The test set also covers all languages that we are concerned with.

**Pending peer comparison**
<!-- ! Add the stack chart better -->
| vs ChatGPT | win | lose | tie
| --- | --- | --- | --- |
| Polylm-13b-chat           | 204 | 1517 | 122
| Qwen-14b-chat             | 433 | 1128 | 306
| SeaLLM-13bChat/SFT/v1     | 454 | 1185 | 209

### M3Exam - World Knowledge in Regional Languages


[M3Exam](https://arxiv.org/pdf/2306.05179.pdf) is a collection of real-life and native official human exam questions benchmarks. This benchmark cover questions from multiple countries in the SEA region, which require strong multilingual proficiency and cultural knowledge across various critical educational periods, from primary- to high-school levels of difficulty.

As shown in the table, our SeaLLM model outperforms most 13B baselines and reaches closer to ChatGPT's performance. Notably, for Thai - a seemingly low-resource language, our model is just 1% behind ChatGPT despite the large size difference.


| M3Exam / 3-shot (Acc) | En | Zh | Vi | Id | Th
|-----------| ------- | ------- |  ------- | ------- | ------- | 
| Random                | 25.00 | 25.00 | 25.00 | 23.00 | 23.00
| ChatGPT               | 75.46 | 60.20 | 58.64 |  48+? | 37.41
| Llama-2-13b           | 59.88 | 43.40 | 41.70 | 34.80 | 23.18
| Llama-2-13b-chat      | 61.17 | 43.29 | 39.97 | 35.50 | 23.74
| Polylm-13b-chat       | 32.23 | 29.26 | 29.01 | 25.36 | 18.08
| Qwen-PolyLM-7b-chat   | 53.65 | 61.58 | 39.26 | 33.69 | 29.02
| SeaLLM-13b/78k-step   | 58.19 | 41.95 | 46.56 | 37.63 | 31.00
| SeaLLM-13bChat/SFT/v1 | 63.53 | 45.47 | 50.25 | 39.85 | 36.07
| SeaLLM-13bChat/SFT/v2 | 62.35 | 45.81 | 49.92 | 40.04 | 36.49


<!-- ! Considering removing zero-shot from the main article -->
<!-- | Random                | 25.00 | 25.00 | 25.00 | 23.00 | 23.00 -->
<!-- | M3-exam / 0-shot | En | Zh | Vi | Id | Th
|-----------| ------- | ------- |  ------- | ------- | ------- | 
| ChatGPT               | 75.98 | 61.00 | 57.18 | 48.58 | 34.09
| Llama-2-13b           | 19.49 | 39.07 | 35.38 | 23.66 | 12.44
| Llama-2-13b-chat      | 52.57 | 39.52 | 36.56 | 27.39 | 10.40
| Polylm-13b-chat       | 28.74 | 27.71 | 25.77 | 22.01 | 13.65
| Qwen-PolyLM-7b-chat   | 52.51 | 56.14 | 32.34 | 25.49 | 24.64
| SeaLLM-13b/78k-step   | 36.68 | 36.58 | 41.98 | 25.87 | 20.11
| SeaLLM-13bChat/SFT/v1 | 64.30 | 45.58 | 48.13 | 37.76 | 30.77
| SeaLLM-13bChat/SFT/v2 | 62.23 | 41.00 | 47.23 | 35.10 | 30.77 -->


### MMLU - Preserving English-based knowledge

On the 5-shot [MMLU](https://arxiv.org/abs/2009.03300), our SeaLLM models not only preserve but also slightly outperform 13B LLama-2 and Llama-2-chat, despite the fact that we never intent to optimize for this English and Chinese dominant test set.

| MMLU (Acc) | STEM | Humanities | Social | Others | Average
|-----------| ------- | ------- |  ------- | ------- | ------- | 
| Llama-2-13b             | 44.10 | 52.80 | 62.60 | 61.10 | 54.80
| Llama-2-13b-chat        | 43.70 | 49.30 | 62.60 | 60.10 | 53.50
| SeaLLM-13bChat/SFT/v2   | 43.67 | 52.09 | 62.69 | 61.20 | 54.70
| SeaLLM-13bChat/SFT/v3   | 43.30 | 52.80 | 63.10 | 61.20 | 55.00


### NLP tasks

We also test our models on many different NLP tasks.

#### Reading Comprehension (XQUAD & IndoQA)

[XQUAD](https://github.com/google-deepmind/xquad) is a popular multilingual variant of [SQUAD](https://www.aclweb.org/anthology/D16-1264/) benchmark, which evaluates models on reading comprehension ability. As XQUAD does not support Indonesian, we substitute it with [IndoQA](https://huggingface.co/datasets/jakartaresearch/indoqa), which was built for the same purpose.

As shown in the table below, the 1-shot reading comprehension performance is significantly better than Llama-2 for the SEA languages, while preserving the high performance in existing languages (En & Zh).

| XQUAD/IndoQA (F1) | En | Zh | Vi | Id | Th | ALL | SEA-lang
|-----------| ------- | ------- |  ------- | ------- | ------- | ------- | ------- | 
| Llama-2-13b       | 83.22 | 78.02 | 71.03 | 59.31 | 30.73 | 64.46 | 59.77
| Llama-2-13b-chat  | 80.46 | 70.54 | 62.87 | 63.05 | 25.73 | 60.93 | 51.21
| SeaLLM-13b-chat-v1 | 83.12 | 73.95 | 74.16 | 61.37 | 60.94 | 70.71 | 65.49
| SeaLLM-13b-chat-v2 | 81.51 | 76.10 | 73.64 | 69.11 | 64.54 | 72.98 | 69.10


#### Translation

For translation tasks, we evaluate our models with the [FloRes-200](https://github.com/facebookresearch/flores/blob/main/flores200/README.md) using [chrF++](https://aclanthology.org/W15-3049/) scores in 4-shot settings.

Similarly observed, our SeaLLM models outperforms Llama-2 significantly in the new languages.

| FloRes-200 (chrF++) | En-Zh | En-Vi | En-Id | En-Th | En->X | Zh-En | Vi-En | Id-En | Th-En | X->En
|-------- | ---- | ---- |  ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | 
| Llama-2-13b | 24.36 | 53.20 | 60.41 | 22.16 | 45.26 | 53.20 | 59.10 | 63.42 | 38.48 | 53.55
| Llama-2-13b-chat | 19.58 | 51.70 | 57.14 | 21.18 | 37.40 | 52.27 | 54.32 | 60.55 | 30.18 | 49.33
| SeaLLM-13b-chat-v1 | 22.77 | 58.96 | 64.78 | 42.38 | 55.37 | 53.20 | 60.29 | 65.03 | 57.24 | 60.85
| SeaLLM-13b-chat-v2 | 22.75 | 58.78 | 65.90 | 42.60 | 55.76 | 53.34 | 60.80 | 65.44 | 57.05 | 61.10

Our models are also performing competitively with ChatGPT for translation between SEA languages without English-pivoting.

| FloRes-200 (chrF++) | Vi-Id | Id-Vi | Vi-Th | Th-Vi | Id-Th | Th-Id
|-------- | ---- | ---- |  ---- | ---- | ---- | ---- | 
| ChatGPT                     | 56.75 | 54.17 | 40.48 | 46.54 | 40.59 | 51.87
| SeaLLM-13b-base mixed SFT   | 54.56 | 54.76 | 36.68 | 51.88 | 39.36 | 47.99
| SeaLLM-13b-Chat/SFT/v2      | 53.75 | 52.47 | 32.76 | 49.20 | 40.43 | 50.03

#### Summarization

Lastly, in 2-shot [XL-sum summarization tasks](https://aclanthology.org/2021.findings-acl.413/), our models also achieve better performance, with substantial gains in Thai.

| XL-Sum (rouge-L) | En | Zh | Vi | Id | Th
|-------- | ---- | ---- |  ---- | ---- | ---- |
| Llama-2-13b        | 32.57 | 34.37 | 18.61 | 25.14 | 16.91
| Llama-2-13b-chat   | 25.11 | 31.13 | 18.29 | 22.45 | 17.51
| SeaLLM-13b-chat-v2 | 27.00 | 33.31 | 20.31 | 25.69 | 21.97


## Citation

If you find our project useful, hope you can star our repo and cite our work as follows:

```
@article{damonlpsg2023seallm,
  author = {???},
  title = {SeaLLMs - Large Language Models for Southeast Asia},
  year = 2023,
}
```
