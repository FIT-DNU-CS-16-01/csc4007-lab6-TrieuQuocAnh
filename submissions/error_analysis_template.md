# Lab 6 – Error Analysis Report

## 1. Task definition

- Selected task:
- Input:
- Output schema:
- LLM used:
- Prompt execution method: Web UI / API / local model

## 2. Testset

- Number of reviews:
- Number of positive reviews:
- Number of negative reviews:
- Number of easy reviews:
- Number of mixed reviews:
- Number of ambiguous reviews:
- Number of keyword-trap reviews:
- Number of long reviews:

## 3. Prompt v1 – Baseline Prompt

Prompt v1 file: `prompts/prompt_v1.txt`

```text
You are CineSense, a movie review analysis assistant specialized in sentiment analysis of IMDB reviews.

Your task is to analyze the sentiment of a movie review with precision and clarity.

Instructions:
1. Read and analyze the provided IMDB movie review carefully
2. Classify the sentiment into one of these categories:
   - positive: predominantly favorable opinion
   - negative: predominantly unfavorable opinion
   - neutral: objective or balanced without clear preference
   - mixed: contains both positive and negative elements with equal weight
3. Extract exact phrases from the review that support your sentiment classification
4. Provide a concise explanation (one or two sentences) for your classification
5. Return your analysis in valid JSON format

JSON Response Format:
{
  "sentiment": "<positive|negative|neutral|mixed>",
  "evidence": ["exact phrase 1 from review", "exact phrase 2 from review", "exact phrase 3 from review"],
  "short_explanation": "Brief explanation of sentiment classification"
}

Review:
{review_text}

Note:
- Ensure all evidence phrases are directly quoted from the review
- The explanation should justify the sentiment classification based on the evidence
- Be precise with sentiment detection, especially for mixed or nuanced reviews
```

Briefly explain:

- What does this prompt ask the LLM to do?
  - Analyze the sentiment of a single IMDB movie review, extract supporting evidence, and provide a short justification.
- What output format does it require?
  - It requires valid JSON with fields `sentiment`, `evidence`, and `short_explanation`.

## 4. Prompt v2 – Improved Prompt

Prompt v2 file: `prompts/prompt_v2.txt`

```text
You are CineSense, a careful movie review analysis assistant.

Task:
Analyze one IMDB movie review using only the information explicitly stated in the review.

Rules:
1. Do not infer facts that are not present in the review.
2. If the review contains both positive and negative opinions, use "mixed".
3. Evidence must be exact phrases copied from the review.
4. Return valid JSON only. Do not add any explanation outside JSON.
5. If the sentiment is unclear, use "neutral" or "mixed" and set confidence to "low".

Output schema:
{
  "sentiment": "positive | negative | neutral | mixed",
  "evidence": ["exact phrase from the review"],
  "short_explanation": "one sentence",
  "confidence": "high | medium | low"
}

Review:
{review_text}
```

Briefly explain:

- What weaknesses of Prompt v1 does Prompt v2 try to fix?
  - Prompt v1 did not explicitly forbid inference beyond the review, it did not define mixed with sufficient precision, it allowed non-JSON content, and it had no confidence indication.
- What rules or constraints are added?
  - Use only information from the review, require exact phrase evidence, enforce valid JSON only, define mixed sentiment clearly, and add a confidence field for unclear cases.

## 5. Prompt v3 – CoT-inspired Prompt

Prompt v3 file: `prompts/prompt_v3.txt`

```text
You are CineSense, a careful movie review analysis assistant.

Goal:
Analyze one IMDB movie review with careful internal reasoning and return the final output in JSON only.

Think internally before answering:
1. Identify positive clues in the review.
2. Identify negative clues in the review.
3. Decide whether the review is positive, negative, neutral, or mixed.
4. If the review expresses both positive and negative opinions, choose "mixed".
5. If the review is balanced or unclear, choose "neutral".
6. Ensure every evidence phrase is copied exactly from the review.

Rules:
1. Use only information explicitly stated in the review.
2. Do not infer facts not present in the review.
3. Do not reveal your full chain-of-thought.
4. Return valid JSON only. Do not include markdown fences or extra text.
5. If the sentiment is uncertain, set confidence to "medium" or "low".

Output schema:
{
  "sentiment": "positive | negative | neutral | mixed",
  "evidence": ["exact phrase from the review"],
  "short_explanation": "one sentence",
  "confidence": "high | medium | low"
}

Example:
Review: "I loved the acting but the plot was weak and uneven."
Output:
{
  "sentiment": "mixed",
  "evidence": ["loved the acting", "the plot was weak and uneven"],
  "short_explanation": "The review praises the acting while also criticizing the plot, so it is mixed.",
  "confidence": "medium"
}

Review:
{review_text}
```

Briefly explain:

- What step-by-step checking behavior does Prompt v3 encourage?
  - It explicitly asks the model to identify positive clues, identify negative clues, determine the overall category, and verify exact evidence before output.
- Does it reveal full reasoning or only return final JSON?
  - It requires only final JSON output while keeping reasoning internal, so it does not reveal the full chain-of-thought.

## 6. Quantitative comparison

| Metric | Prompt v1 | Prompt v2 | Prompt v3 CoT | Comment |
|---|---:|---:|---:|---|
| Accuracy | | | | |
| Valid JSON rate | | | | |
| Evidence exactness rate | | | | |
| Hallucination count | | | | |
| Outside knowledge count | | | | |
| Overconfidence count | | | | |

## 7. Error buckets

| Error bucket | Count v1 | Count v2 | Count v3 CoT | Example review_id | Comment |
|---|---:|---:|---:|---|---|
| wrong_sentiment | | | | | |
| invalid_json | | | | | |
| hallucinated_evidence | | | | | |
| outside_knowledge | | | | |
| missed_positive_aspect | | | | | |
| missed_negative_aspect | | | | | |
| keyword_trap | | | | | |
| mixed_review_failure | | | | | |
| overconfident | | | | | |
| cot_not_helpful | | | | | |

## 8. Three interesting errors

### Error 1

- Review ID:
- Review type: easy / mixed / ambiguous / keyword_trap / long_review
- Gold sentiment:
- Prompt version:
- What happened:
- Why it matters:
- How Prompt v2 or Prompt v3 tries to fix it:

### Error 2

- Review ID:
- Review type: easy / mixed / ambiguous / keyword_trap / long_review
- Gold sentiment:
- Prompt version:
- What happened:
- Why it matters:
- How Prompt v2 or Prompt v3 tries to fix it:

### Error 3

- Review ID:
- Review type: easy / mixed / ambiguous / keyword_trap / long_review
- Gold sentiment:
- Prompt version:
- What happened:
- Why it matters:
- How Prompt v2 or Prompt v3 tries to fix it:

## 9. Reflection

Answer briefly:

- What did the LLM do well?
- What kind of review was difficult?
- Did Prompt v2 improve over Prompt v1? Give evidence.
- Did Prompt v3 CoT improve over Prompt v2? Give evidence.
- Did CoT ever make the output worse, longer, or harder to parse?
- What would you improve next?

## 10. Final conclusion

Write 5–7 sentences answering:

> Which prompt is most reliable for CineSense, and why?

Your conclusion must refer to metrics and error examples, not only personal feeling.
