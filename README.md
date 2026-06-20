# TakeMeter Evaluation Report: r/cscareerquestions

* **Google Colab**
https://colab.research.google.com/drive/1b94kd3KdxXCVF8gVPaTXaRb53unBbGj5?usp=sharing

* **YouTube Video**
https://youtu.be/9C6BY2TNW-8


## 1. Community Choice & Reasoning
* **Community:** `r/cscareerquestions`
* **Description:** An online forum where computer science students, job-seekers, entry-level candidates, and industry veterans discuss recruiting pipelines, curriculum choices, interview strategies, and high-level tech market shifts.
* **Discourse Quality Assessment:** The discourse quality across this community is highly volatile. It ranges from deeply analytical, data-validated career progression timelines to highly emotional, reactionary doom-posting driven by recruitment anxiety and market fluctuations.
* **Community Value Metric:** Navigating this forum creates immense informational fatigue for students. By automatically classifying text by its underlying structural intent, users can isolate concrete professional guidance (`actionable_advice_and_guides`) from general unverified workplace hypotheticals (`career_queries_and_speculation`) and immediate emotional releases (`doom_posting_vent`).

---

## 2. Label Taxonomy & Guidelines
We classify posts according to their primary structural intent using three mutually exclusive and exhaustive labels:

* **`actionable_advice_and_guides`**: The post provides a systematic, execution-oriented framework, verifiable personal metrics, resume critiques, or a career roadmap derived from real-world professional execution.
  * *Example 1:* "I sent out 250 applications, secured 4 interviews, and signed 1 offer. Here is my exact resume template and the LeetCode modules I used."
  * *Example 2:* "I've conducted senior engineering interviews at FAANG for 8 years. Here are the 3 architectural anti-patterns that result in an immediate rejection."
* **`career_queries_and_speculation`**: Open-ended personal choices, hypothetical questions, salary rumor mill checking, or macroeconomic trend discussions stated without verified external data loops or actionable frameworks.
  * *Example 1:* "Do you believe remote engineering work will vanish entirely by 2030, or will hybrid remain the global corporate standard?"
  * *Example 2:* "Should I prioritize early specialization in mobile development or remain a full-stack generalist to keep my options flexible?"
* **`doom_posting_vent`**: Immediate expressions of frustration, hopelessness, severe professional fatigue, or career stagnation lacking an actionable path forward or constructive resolution.
  * *Example 1:* "The entry-level market is completely cooked. I've sent out apps for six months and haven't hit a single OA. AI is taking over junior positions anyway."
  * *Example 2:* "I completely hate my automation role. I feel like a glorified manual tester despite my title, and it is destroying my mental well-being."

---

## 3. Data Collection & Annotation Analysis
* **Data Sourcing:** 228 unique text records were gathered directly from `r/cscareerquestions` public text feeds and pinned community mega-threads (such as the *Weekly Friday Rant Thread* and *Monthly Salary Sharing Thread*).
* **AI-Assisted Annotation Workflow:** An initial rule-based scaffolding pass was executed using an LLM (Gemini) to pre-populate predictions. To maintain complete audit transparency, an explicit tracking mechanism was appended to the dataset:
  * `pre_labeled_by`: Set to `gemini` for tracking.
  * `agreed_with_ai`: A boolean tracking human agreement vs override.
  * **Human-in-the-Loop Validation:** Every single row was manually audited, read, and verified or corrected by a human annotator to eradicate model labeling noise and eliminate automated bias from shaping the gold standard labels.

### Hard Edge Cases & Boundary Decisions
1. **The Overlapping Disruption Case (Row 1):** Text documenting a sudden layoff combined with career background info. *Decision:* `doom_posting_vent`. The core structural text deals with navigating immediate displacement trauma and market unreadiness rather than outputting a shareable career guide.
2. **The Generative AI Team Rant (Row 10):** A post complaining about product teams breaking code with Claude but prompting an open question on engineering reason. *Decision:* `career_queries_and_speculation`. It uses team friction as a baseline to explore an open industry trend rather than remaining an isolated emotional vent.
3. **The Stagnant Veteran Dilemma (Row 4):** An 18-year veteran detailing core system design skills but expressing intense career fatigue. *Decision:* `doom_posting_vent`. The primary operational intent is to find an exit path to heal from profound professional burnout.

---

## 4. Dataset Distribution

## 📊 Dataset Distribution Tables

### 1. Label Distribution (Total Dataset)
*Total Unique Records: 228*

| Classification Label | Row Count | Percentage of Dataset |
| :--- | :---: | :---: |
| `actionable_advice_and_guides` | 121 | 53.0% |
| `doom_posting_vent` | 38 | 16.6% |
| `career_queries_and_speculation` | 69 | 30.2% |
| **Total** | **228** | **100.0%** |

---

### 2. Train-Validation-Test Distribution Split
*Splitting Strategy: ~70% Train / ~15% Validation / ~15% Test*

| Dataset Subset | Row Count | Percentage Share | Purpose / Usage |
| :--- | :---: | :---: | :--- |
| **Train Set** | 159 | 69.3% | Used to optimize DistilBERT weights during gradients steps. |
| **Validation Set** | 34 | 14.9% | Tracked during training epochs to monitor validation loss/overfitting. |
| **Test Set** | 35 | 15.4% | Locked out completely; used for final zero-shot and fine-tuned benchmarks. |
| **Total** | **228** | **100.0%** | |

* **Label Distribution** 
![Label Distribution](image-6.png)

* **Train-Test Distribution**
![Train-Test Distribution](image-5.png)

---

## 5. Fine-Tuning Approach & Setup
* **Base Architecture:** `distilbert-base-uncased` via Hugging Face.
* **Hyperparameter Selection:**
  * Epochs: `3`
  * Train Batch Size: `16`
  * Eval Batch Size: `32`
  * Learning Rate: `2e-5`
* **Hyperparameter Decision Rationale:** A learning rate of `2e-5` was chosen to allow the transformer layers to adjust their weights smoothly to community slang without completely overriding the baseline language modeling features. 

---

## 6. Full Evaluation Report & Baseline Comparison

## 📊 Model Evaluation Summary

| Model Configuration | Evaluation Accuracy | Macro F1-Score | Target Success Threshold |
| :--- | :---: | :---: | :---: |
| **Baseline: Llama-3.3-70B (Zero-Shot)** | 37.1% | *Distributed* | $\ge$ 75.0% Accuracy / 0.70 F1 |
| **Fine-Tuned: DistilBERT** | 54.3% | 0.00 (Minority Classes) | $\ge$ 75.0% Accuracy / 0.70 F1 |

---

### 📊 Per-Class Performance Metrics

To isolate exactly where the fine-tuned model collapsed compared to the zero-shot LLM baseline, the table below breaks down the explicit Precision, Recall, and F1-score for each individual class across both configurations.

#### 🟥 Baseline: Llama-3.3-70B (Zero-Shot) Per-Class Metrics
| Classification Label | Precision | Recall | F1-Score | Support |
| :--- | :---: | :---: | :---: | :---: |
| `actionable_advice_and_guides` | 100.0% | 47.4% | 64.3% | 19 |
| `career_queries_and_speculation` | 28.6% | 57.1% | 38.1% | 7 |
| `doom_posting_vent` | 33.3% | 44.4% | 38.1% | 9 |

#### 🟩 Fine-Tuned: DistilBERT Per-Class Metrics
| Classification Label | Precision | Recall | F1-Score | Support |
| :--- | :---: | :---: | :---: | :---: |
| `actionable_advice_and_guides` | 54.3% | 100.0% | 70.4% | 19 |
| `career_queries_and_speculation` | 0.0% | 0.0% | 0.0% | 7 |
| `doom_posting_vent` | 0.0% | 0.0% | 0.0% | 9 |

> **Per-Class Breakdown Analysis:** The per-class metrics lay bare the mathematical failure of the fine-tuning process. While DistilBERT's majority class `actionable_advice_and_guides` shows a superficially high F1-score of 70.4%, this is purely a function of a 100% recall shortcut. The complete drop to 0.0% across both minority classes proves the network failed to optimize for generalized boundary lines, whereas the zero-shot baseline managed to preserve a functional distribution of boundaries.


## 🟥 Zero-Shot Baseline Confusion Matrix (Llama-3.3-70B)
*Accuracy: 37.1% — Conservative on advice, but actively attempted minority boundaries.*

| Actual \ Predicted | actionable_advice_and_guides | career_queries_and_speculation | doom_posting_vent | *Support* |
| :--- | :---: | :---: | :---: | :---: |
| **actionable_advice_and_guides** | **9** | 5 | 5 | *19* |
| **career_queries_and_speculation** | 0 | **4** | 3 | *7* |
| **doom_posting_vent** | 0 | 5 | **4** | *9* |
| **Total Predicted** | 9 | 14 | 12 | **35** |

> **Baseline Note:** Had a perfect **1.00 Precision** on the advice category (9/9 correct guesses), making its positive predictions highly trustworthy despite lower overall recall.

---

## 🟩 Fine-Tuned DistilBERT Confusion Matrix
*Accuracy: 54.3% — Systematic Failure: Majority Class Collapse.*

| Actual \ Predicted | actionable_advice_and_guides | career_queries_and_speculation | doom_posting_vent | *Support* |
| :--- | :---: | :---: | :---: | :---: |
| **actionable_advice_and_guides** | **19** | 0 | 0 | *19* |
| **career_queries_and_speculation** | 7 | **0** | 0 | *7* |
| **doom_posting_vent** | 9 | 0 | **0** | *9* |
| **Total Predicted** | 35 | 0 | 0 | **35** |

> **Critical Failure Analysis:** The model entirely stopped evaluating linguistic features. By blanket-predicting the majority class (`actionable_advice_and_guides`) for 100% of the 35 test instances, it rode the test set imbalance to an artificial 54.3% accuracy while collapsing to a **0.00 F1-score** for both minority classes.

---

### Model Performance Metrics
* **ZERO-SHOT LLM BASELINE**
![Confusion Matrix](image-2.png)

* **Baseline Description**
SYSTEM_PROMPT = """
You are an expert content moderation AI evaluating discourse quality in the r/cscareerquestions online community. Your task is to perform structural text classification on community posts.

Assign each post to exactly one of the following categories based on its primary structural intent:

actionable_advice_and_guides: The post shares concrete, actionable strategies, professional experiences, resume breakdowns, or systematic interview prep advice derived from real-world execution.
Example: "I applied to 250 companies, got 4 interviews, and landed 1 offer. Here is the resume format and the exact LeetCode modules I focused on."

career_queries_and_speculation: Open-ended questions, personal dilemmas, hypothetical industry predictions, or discussions about tech trends and workplace culture stated without a structured, actionable guide or framework.
Example: "Do you guys think remote work is going to completely disappear in the next 5 years, or will hybrid become the permanent standard?"

doom_posting_vent: Expressions of frustration, hopelessness, panic, professional burnout, exhaustion, or sudden structural stress (such as layoffs, toxic team changes, or career stagnation) without a constructive or clear forward-looking execution strategy.
Example: "The tech industry is completely cooked. I've been applying for months and haven't gotten a single automated OA. AI is going to take all our entry-level jobs anyway."

Respond with ONLY the exact label name from the valid labels list below. Do not include quotes, punctuation, or explain your reasoning. Your output must strictly match one of the valid strings.

Valid labels:
actionable_advice_and_guides
career_queries_and_speculation
doom_posting_vent
"""

* **Collection Method**: Inference was run asynchronously using llama-3.3-70b-versatile via the Groq API provider on the 35 locked test set instances to record zero-shot benchmarks.

* **FINE-TUNED DISTILBERT MODEL**
![Confusion Matrix](image-3.png)
![Confusion Matrix With Color](image-4.png)

## 7. Performance Analysis & Wrong Predictions

### Systematic Failure Mode: Majority Class Collapse
While the fine-tuned model looks superior on paper with an accuracy increase from **37.1% to 54.3% (+17.1%)**, a deep structural analysis reveals that the fine-tuned model is completely broken. The model suffered a total **majority class collapse**. Because `actionable_advice_and_guides` was the dominant class in the test set support (19 out of 35 posts), the network discovered a mathematical shortcut during training: it found that it could rapidly lower its overall cross-entropy training loss by entirely ignoring text features and blanket-predicting the majority class for 100% of inputs. This resulted in an artificial accuracy of 54.3% while yielding a disastrous **0.00 F1-score** across the remaining categories.

Conversely, the Zero-Shot LLM baseline represents a far more useful classifier. It demonstrated a perfect **1.00 Precision** on advice, meaning that while it was conservative in its guesses (0.47 recall), its predictions were completely trustworthy and it successfully attempted to construct genuine boundaries for minority classes.

### Specific Misclassifications Analysis

#### Misclassification 1: False Positive Advice (True Class: `doom_posting_vent`)
* **Text:** `Title: What should I learn to stay competitive in this dreadful market? | Body: I have 5 years of experience in Java, Spring Boot, Vue.js and Bash scripting.I have been working and maintaining a web based single-user desktop application used in the healthcare industry for the last 4 years... I have been applying for a new job for the past year but I kinda gave up because I did not even received a single call or email.`
* **Predicted Label:** `actionable_advice_and_guides` (Confidence: `38%`)
* **Analysis:** This post represents a deep personal expression of defeat and exhaustion ("kinda gave up", "dreadful market") after a year of unsuccessful job hunting, which should map directly to a vent. However, the text is heavily dense with concrete tech stack tokens ("Java", "Spring Boot", "Vue.js", "Bash scripting") and professional metrics ("5 years of experience", "last 4 years"). Because the model suffered a majority class collapse toward advice, its classification layer latched onto these highly structured resume-like keywords, ignoring the underlying hopeless sentiment and forcing it into the advice category.

#### Misclassification 2: False Positive Advice (True Class: `career_queries_and_speculation`)
* **Text:** `Title: CS grads who couldn’t break in, what are you doing now? | Body: When did you graduate and what are you doing now? Do you still have plans to break in at some point? Looking to see what other pe...`
* **Predicted Label:** `actionable_advice_and_guides` (Confidence: `39%`)
* **Analysis:** The fundamental structural intent of this post is an open-ended community poll inquiring about alternative career paths and future plans ("what are you doing now?", "Do you still have plans?"). It contains absolutely no actionable guides, resume teardowns, or execution metrics. The fine-tuned model completely failed to process the syntax of interrogative punctuation (multiple question marks) and speculative framing, blindly defaulting to the collapsed majority class prediction of advice at a low confidence threshold of 39%.

#### Misclassification 3: False Positive Advice (True Class: `doom_posting_vent`)
* **Text:** `Title: Today begins the layoff of 8,000 employees from Meta | Body: ***Per New York Time - “On Wednesday, the ax fell. The layoffs began in Singapore, where at 4 a.m. local time emails went out to wor...`
* **Predicted Label:** `actionable_advice_and_guides` (Confidence: `39%`)
* **Analysis:** This post tracks the unfolding trauma of a massive corporate layoff event ("the ax fell", "layoffs began"). Under the taxonomy guidelines, tracking immediate displacement stress log context belongs in the vent category. The model misclassified this because the text cites structured, journalistic reporting data loop tokens ("8,000 employees", "Meta", "New York Time", "4 a.m. local time"). Due to the model shortcutting to avoid penalties by predicting the majority class, these objective, quantitative markers falsely triggered the weights for a structured career breakdown, overriding the bleak context of sudden market disruption.

---

## 8. Sample Classifications Verification Table

| Post Excerpt / Title | True Label | Predicted Label | Confidence | Classification Type | Rationale / Explanation |
| :--- | :--- | :--- | :---: | :--- | :--- |
| `Fidelity vs Lockheed (New Grad)...` | `actionable_advice_and_guides` | `actionable_advice_and_guides` | 38.5% | ✅ Correct | **True Positive Match:** The post contains a structured comparative breakdown evaluating specific entry-level compensation packages, relocation stipends, and corporate tech stacks. Because it outlines verifiable, real-world professional execution metrics rather than open-ended speculation or emotional venting, it strictly satisfies the linguistic criteria for a career guide. The model accurately caught these structural tokens, though its confidence remains heavily suppressed at 38.5% due to the near-uniform probability distribution caused by the majority class collapse. |
| `CS grads who couldn’t break in...` | `career_queries_and_speculation` | `actionable_advice_and_guides` | 39% | ❌ Incorrect | **False Positive Advice:** This post functions as an open-ended community sentiment poll regarding alternative graduation paths. It contains no actionable roadmaps. The model completely misses the speculative syntax and defaults to the collapsed majority class. |
| `What should I learn to stay competitive...` | `doom_posting_vent` | `actionable_advice_and_guides` | 38.0% | ❌ Incorrect | **False Positive Advice:** A clear expression of recruitment burnout ("kinda gave up"). The fine-tuned model overlooks the severe negative sentiment tokens and forces it into advice because the text includes technical keywords like Java and Spring Boot. |

---

## 9. Reflection
* **What the model learned vs. what I intended:** I intended for the model to parse the structural purpose of text—separating objective, data-rich roadmaps from subjective open-ended chatter and emotional venting. Instead, the model did not learn linguistic structure at all; it learned the volume distribution of the dataset. It realized that `r/cscareerquestions` text contains a high baseline density of industry vocabulary and that it could easily pass optimization checks by exploiting class imbalance.
* **Spec Reflection:** The specification in `planning.md` saved my project evaluation. By explicitly defining success in Section 6 as requiring a *minimum per-class F1-score of 0.70 across all categories*, I instantly recognized that my 54.3% accuracy model was non-viable for deployment. The implementation diverged drastically from the spec because the baseline dataset distribution favored a single category, causing gradient descent to settle into a local minimum shortcut rather than optimizing for generalized boundary lines.
* **AI Usage Disclosure:** 1. I utilized Gemini to generate an initial data labeling pass across the 228 scraped lines, which I fully logged and audited via human override columns (`pre_labeled_by` and `agreed_with_ai`).
  2. I utilized an LLM to debug the file structure variations when exporting data from Excel to CSV, resolving newline formatting discrepancies that were fracturing text fields across row divisions.