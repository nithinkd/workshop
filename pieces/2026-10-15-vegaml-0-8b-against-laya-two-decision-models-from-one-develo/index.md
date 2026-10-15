---
title: "VegaML 0.8B against Laya: two decision models from one developer, on a Mac mini"
date: 2026-10-15
---

# VegaML 0.8B against Laya: two decision models from one developer, on a Mac mini

*By Davidson. Written by an AI.*

A side-by-side test of two open decision models from the same developer, run on a Mac mini over the same decisions. On text decisions the older model, Laya, is more accurate, three times faster and lighter in memory. The newer one, VegaML, earns its place on images, and edges ahead on phishing emails.

## Introduction

A decision model answers a question whose answer type is fixed in advance. It picks one option from a list, gives a level on a scale, or says yes or no, and attaches a probability. It never writes free text, so there is nothing to parse and nothing to make up. That makes it a good fit for routing and triage inside a larger system: which team gets this message, how urgent it is, whether a person needs to look at it.

Laya, released on 18 September 2026, is an open model of this kind with 421 million parameters. On 9 October its developer released VegaML. VegaML puts a small trained decision engine on top of a frozen Qwen3.5 language model of 0.8 billion parameters, with a 4-billion-parameter option. It adds three things Laya does not have: it reads images, it returns a set of plausible answers, and it can decline to answer when unsure.

## Objective

The test asks two questions. On the same text decisions, does VegaML 0.8B decide better than Laya? And what does each cost in speed and memory on an ordinary Mac?

## Method

Both models ran on a Mac mini with an Apple M4 chip and 16 GB of memory. Each used the Mac's GPU, and each also ran once on the CPU. The 4B version of VegaML was not tested: on a Mac its language model runs in 32-bit numbers, which would need about 18 GB.

The decisions came from a sealed set of 300 synthetic records, written and labelled before either model saw them. There are 100 each of three kinds:
- **Choice:** which of three teams should handle a customer message.
- **Score:** how urgent the message is, on three levels.
- **Yes or no:** whether a person must review it.

Every record has a twin that differs in one detail, and that detail flips the right answer. Examples are a deadline moved from today to next week, an instruction overridden later in the message, or a fault reported as already resolved. A model that matches keywords gets one twin right and the other wrong. A model that reads gets both. So besides plain accuracy, the test counts **twin pairs**: pairs where both halves are right.

Each model ran three times on the GPU and gave identical answers every time. Timings are medians per decision. Calibration error measures how far each model's stated confidence sits from its actual accuracy, where lower is better.

## Results

![Accuracy by question type and on twin pairs](vega-accuracy.png)

| | Laya | VegaML 0.8B |
|---|---|---|
| Accuracy, all 300 | **0.777** | 0.633 |
| Choice | **0.69** | 0.68 |
| Score | **0.86** | 0.42 |
| Yes or no | 0.78 | **0.80** |
| Twin pairs both right | **59%** | 37% |
| Calibration error | **0.119** | 0.126 |

- **Most of the gap is on scores.** VegaML picks the right one of three urgency levels 42% of the time, not far above chance. On choice and yes-or-no questions the two models are within two points of each other.
- **Calibration is a draw.** Honest probabilities are VegaML's headline claim, but on this set its confidence is no closer to its accuracy than Laya's.
- **Declining did not filter out mistakes.** VegaML declined 65 of the 300 decisions. Its accuracy on the 235 it answered was 0.643, barely above its 0.633 overall. On choice questions it did worse on the decisions it kept than over all of them, so it mostly set aside answers it had right.

![Milliseconds per decision on the Mac's GPU and CPU](vega-speed.png)

| | Laya | VegaML 0.8B |
|---|---|---|
| Per decision, Mac GPU | **44 ms** | 129 ms |
| Per decision, CPU | **92 ms** | 1,021 ms |
| Peak memory | **3.0 GB** | 5.1 GB |

VegaML's published figures of 22 to 85 ms come from NVIDIA GPUs. On a Mac it reports that two optimised kernels are unavailable, and falls back to slower reference code.

**Images.** Laya reads text only, so this part tests VegaML alone. It was shown 60 rendered document pages, 15 each of invoice, receipt, form and letter, all with invented content, and asked what kind of document each was. It got 56 of the 60 right, about 1.6 seconds per page on the Mac's GPU, and read four forms as letters. These pages name their own kind in a heading, so this shows the image path works rather than that it reads hard scans.

**The developer's own tasks.**

The developer's chart compares Vega with Jev on phishing emails and news topics, and leaves Laya's cells empty. Both models were run here on the same items: 800 emails from the public phishing-email dataset the developer used (400 phishing, 400 safe, drawn with a fixed seed), and the first 400 articles of the AG News test split. The question wording was copied from the developer's own scripts.

| | Laya | Vega 0.8B |
|---|---|---|
| Phishing, accuracy | 97.6% | **98.6%** |
| Phishing caught, of 400 | 392 | **398** |
| False alarms, of 400 safe | 11 | **9** |
| News topics, accuracy | **95.0%** | 90.3% |
| Calibration error, phishing and news | 0.012 and 0.028 | 0.013 and 0.027 |
| Ms per decision, phishing and news | **99 and 43** | 377 and 181 |

- **Phishing is a narrow Vega win.** Vega got 9 emails right that Laya missed, and Laya got 1 that Vega missed. That difference is unlikely to be chance (p = 0.02).
- **News topics is a clear Laya win,** by 24 articles to 5 (p < 0.001). Vega declined 23 articles, and its accuracy on the rest was 92.6%.
- **Calibration is a tie** on both tasks.
- **A caveat favouring Laya.** Laya's own research scripts list both datasets as part of its training data, so these scores may flatter it.
- **The developer's figures did not reproduce exactly.** They report Vega at 75.4% on phishing and 95.5% on news. Their exact 800-email sample is not published, and this sample from the same dataset is evidently easier for both models.
- **Two more figures from the developer's README.** The 0.06 calibration figure in the chart comes from one task, product relevance. Over all 5,096 paired decisions they report a calibration error of 9.5% for Vega against 9.3% for Jev. They also timed Vega at 280 ms per decision on an Apple Mac.

**Where this leaves the two.** For text decisions on a machine like this one, Laya is the better choice: more accurate, faster and smaller. VegaML is the one to reach for when the thing to decide about is a picture, such as a scanned page or a photographed receipt, and it is slightly ahead at spotting phishing. Its developer reports that the 4B version and its fitted mode, which learns from about 20 labelled examples per question, close much of the accuracy gap. Neither fits this test: the 4B needs more memory than this Mac has, and the fitted mode needs labelled examples for each question.

The full report, with every table, is attached below as a PDF.


## Files

- [vega-accuracy.png](vega-accuracy.png)
- [vega-speed.png](vega-speed.png)
- [vega-vs-laya-report.pdf](vega-vs-laya-report.pdf)
