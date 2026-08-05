# Slice docs the way Google wishes you would

> **How you'll submit this lab**
>
> This repo is your lab. Fork it, do the work described below in your fork, then open a pull
> request back into this repository. An AI reviewer will check your PR against `rubric.md` and
> leave feedback directly on the PR. See `README.md` for the full workflow.

## Learning Goal

You will build or evaluate one Different Ways to Chunk Podcast and PDF path and prove the answer is grounded in inspectable evidence.

## Working Context

This lab focuses on the chain between source material, tool behavior, and final answer.
You keep the system small enough to trace one query, one retrieval or tool step, and one final output.
The proof should show grounding, not just a successful response.

## Proof of Learning

Submit one `lab_proof.md` with the code or workflow, one query, retrieved or tool evidence, final output, and one failure or limitation.

## Pseudocode

```text
INPUT: source documents, tool result, or user query
DECIDE: retrieval, tool, agent, or guardrail path
BUILD: prepare source -> run path -> capture output
VERIFY: inspect evidence that supports the answer
EXPLAIN: identify one grounding risk or failure mode
```

## Prompt Discipline

Use AI for review, debugging, or critique. Do not ask it to produce the final artifact or final answer.

```text
Role: Retrieval or agent reviewer
Task: Review my plan for grounding and verification.
Inputs: source material, query, tool path, and proof evidence.
Constraints: Do not produce the final answer. Identify weak grounding and missing traces.
Output format: three grounding risks, two trace checks, and one simplification.
Verification: Name the evidence that ties the answer to the source.
```

## Build

## Step-by-Step Instructions

### Step 1: Setup and Data Loading

Do: Set up your environment and load both content sources.

Do:
1. Create a new Jupyter notebook: `chunking_strategies.ipynb`
2. Install required packages:
3. Load environment variables for API keys if needed
4. Load the podcast audio file
5. Transcribe the audio file (save it to a local file)
6. Load the PDF document (from URL or local file)

**Expected first output:** package installation completes with no errors and your notebook can import the required text splitter classes.

---

### Step 2: Implement Fixed-Size Chunking

Do: Create fixed-size chunks for both documents.

Do:
1. Use `CharacterTextSplitter` with fixed chunk size
2. Experiment with different sizes: 500, 1000, 2000 characters
3. Try different overlap values: 0, 50, 100 characters
4. Compare results between podcast and PDF

**Analysis questions:**
- Does fixed-size chunking break sentences in the middle?
- How does it handle paragraph boundaries?
- Which content type handles fixed-size chunking better?

---

### Step 3: Implement Recursive Character Chunking

Do: Use recursive character splitting, which tries to preserve semantic boundaries.

Do:
1. Use `RecursiveCharacterTextSplitter` (recommended by LangChain)
2. Experiment with chunk sizes: 500, 1000, 2000 tokens
3. Try different separators priority
4. Compare with fixed-size results

**Analysis questions:**
- Does recursive chunking preserve sentence boundaries better?
- How does it handle the podcast's conversational structure?
- Does it respect PDF section headers?

---

### Step 4: Implement Token-Based Chunking

Do: Chunk based on token count (more accurate for LLM context windows).

Do:
1. Use `TokenTextSplitter` or calculate tokens manually
2. Chunk to specific token counts (e.g., 500, 1000 tokens)
3. Compare token-based vs character-based chunking

---

### Step 5: Semantic Chunking (Optional - Advanced)

Note: this should be run on Colab for maximum compatibility.

Do: Explore semantic chunking that splits based on meaning rather than size.

Do:
1. Use sentence transformers to find semantic boundaries
2. Split when semantic similarity drops significantly
3. Compare with size-based approaches

---

### Step 6: Visualize and Compare Results

Do: Create visualizations to compare chunking strategies.

Do:
1. Build a comparison table of chunk statistics for each strategy:

| Strategy | Avg chunk size (chars) | Min | Max | # of chunks |
|----------|----------------------|-----|-----|-------------|
| Fixed-500 | ? | ? | ? | ? |
| Recursive-1000 | ? | ? | ? | ? |
| Token-500 | ? | ? | ? | ? |

2. Plot chunk size distributions using `matplotlib`:
```python
import matplotlib.pyplot as plt

sizes = [len(chunk.page_content) for chunk in chunks]
plt.hist(sizes, bins=20)
plt.xlabel("Chunk size (chars)")
plt.ylabel("Frequency")
plt.title("Chunk Size Distribution — Recursive 1000")
plt.show()
```
3. Identify where chunks break: print the last 50 characters of a few chunks and check whether they end mid-sentence
4. Document trade-offs in a markdown cell

---

### Step 7: Analyze Chunk Quality

Do: Evaluate chunk quality by checking boundary preservation.

Do:
1. Write a function that checks whether each chunk ends with a sentence-ending character (`.`, `?`, `!`):
```python
def pct_clean_boundaries(chunks):
    clean = sum(1 for c in chunks if c.page_content.strip()[-1] in '.?!')
    return clean / len(chunks) * 100

print(f"Fixed-size: {pct_clean_boundaries(fixed_chunks):.1f}% clean endings")
print(f"Recursive:  {pct_clean_boundaries(recursive_chunks):.1f}% clean endings")
```
2. Compare this metric across all strategies and both document types (podcast vs PDF)
3. Write 2-3 sentences explaining which strategy preserves context best and why

---

### Step 8: Make Recommendations

Do: Document your findings and make recommendations.

Do:
1. Create a summary table comparing strategies
2. Write recommendations for each content type
3. Explain trade-offs and when to use each approach

**Deliverable template:**
```markdown
## Chunking Strategy Recommendations

### For PDF Documents:
**Recommended Strategy:** [Your choice]
**Reasoning:**
- [Why this works best for PDFs]
- [Key advantages]
- [Optimal chunk size and overlap]

### For Podcast Transcripts:
**Recommended Strategy:** [Your choice]
**Reasoning:**
- [Why this works best for transcripts]
- [Key advantages]
- [Optimal chunk size and overlap]

### Trade-offs Summary:
| Strategy | Pros | Cons | Best For |
|----------|------|------|----------|
| Fixed-Size | Simple, predictable | Breaks context | Uniform content |
| Recursive | Preserves structure | More complex | Structured docs |
| Token-Based | Accurate for LLMs | Requires tokenizer | LLM integration |
| Semantic | Meaning-based | Computationally expensive | Complex content |
```

---

## Submission Guidelines


## Extension Activities (Optional)

1. **Test with actual RAG retrieval:** Embed chunks and test retrieval quality
2. **Experiment with overlap:** Find optimal overlap percentage for each strategy
3. **Hybrid approach:** Combine multiple strategies (e.g., semantic boundaries with size limits)
4. **Metadata preservation:** Ensure chunk metadata (source, page number, timestamp) is maintained

---

## Resources

- [LangChain Text Splitters Documentation](https://docs.langchain.com/oss/python/integrations/splitters/index#text-splitters)
- [ChunkViz Tool](https://chunkviz.up.railway.app/) - Visualize how different strategies split your text
- [OpenAI Tokenizer](https://platform.openai.com/tokenizer) - Understand token counting
- [Sentence Transformers](https://www.sbert.net/) - For semantic chunking

---

## Troubleshooting

**Issue:** Chunks are too small or too large
- **Solution:** Adjust `chunk_size` parameter. Start with 1000 characters and adjust based on your content.

**Issue:** Important context is being split across chunks
- **Solution:** Increase `chunk_overlap` to preserve context at boundaries.

**Issue:** Recursive chunking isn't respecting structure
- **Solution:** Adjust the `separators` list to prioritize your content's structure (e.g., `["\n\n\n", "\n\n", "\n", ". "]` for PDFs with clear section breaks).

**Issue:** Token-based chunking gives different results than expected
- **Solution:** Verify your tokenizer matches the model you'll use (e.g., use `tiktoken` with `cl100k_base` for GPT-4).

## Verify

The reviewer can trace one answer back to retrieved content, tool output, or guardrail evidence.

## Explain

Where could the system produce a plausible but unsupported answer?

## Submission Sanity Check

Before you submit:
- Keep only files for this lab.
- Name files clearly.
- Remove secrets, API keys, tokens, and unrelated data.
- Make `lab_proof.md` the entry point for the reviewer.

## Optional Stretch

- Add one targeted improvement: better chunking, reranking, a guardrail, a trace view, or a failure-case test.
- Create a small demo UI with vibecoding only if it helps inspect sources and answers. Deploy only if it can run safely without exposed secrets.
