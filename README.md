# ⚡ Perplexity Explorer

An interactive, zero-backend, client-side educational playground that demonstrates how autoregressive language models measure predictability and calculate **perplexity** token-by-token.

Built with **HTML, CSS, and plain JavaScript** in a single file (`index.html`) using a **Neo-Brutalist** design theme and powered directly in the browser by [Transformers.js](https://huggingface.co/docs/transformers.js).

---

## 🎯 What is Perplexity?

In Natural Language Processing (NLP), **perplexity** measures how "surprised" or "perplexed" a language model is by a sequence of words.

- **Lower Perplexity**: The sequence is natural, familiar, and predictable according to the model.
- **Higher Perplexity**: The sequence is unnatural, ungrammatical, or contains surprising word choices.

### Mathematical Breakdown

Given a sequence of $N$ tokens $(w_1, w_2, \dots, w_N)$:

1. **Token Probability ($P$)**: For each token $w_i$, the model outputs raw unnormalized scores (*logits*). We compute the token's conditional probability $P(w_i \mid w_{<i})$ using numerically stable softmax:
   $$\text{Softmax}(z_i) = \frac{e^{z_i - \max(z)}}{\sum_j e^{z_j - \max(z)}}$$

2. **Inverse Probability ($1/P$)**: Represents the "branching factor" or surprise at each step. If $P = 0.5$, $1/P = 2$ (the model was picking between roughly 2 equally likely words). If $P = 0.01$, $1/P = 100$ (a 1-in-100 surprise).

3. **Step 1 — Multiply Inverse Probabilities**:
   $$\text{Product} = \prod_{i=1}^{N} \frac{1}{P(w_i \mid w_{<i})}$$

4. **Step 2 — Count Tokens**:
   $$N = \text{number of evaluated tokens}$$

5. **Step 3 — Geometric Mean (Perplexity)**:
   $$\text{Perplexity} = (\text{Product})^{1/N} = \left( \prod_{i=1}^{N} \frac{1}{P_i} \right)^{1/N}$$

To prevent floating-point underflow/overflow on long sentences, the calculation is evaluated in log-space:
$$\text{Perplexity} = \exp\left( \frac{1}{N} \sum_{i=1}^{N} -\ln P_i \right)$$

---

## ✨ Features

### 1. Section 1 – Type a Sentence (Real In-Browser LLM)
- Evaluates real sentences using **DistilGPT-2** (`Xenova/distilgpt2`, 8-bit quantized `q8`).
- **Zero API Keys & Zero Backend**: Runs 100% locally in your browser using WebAssembly / WebGPU via Transformers.js.
- **Lazy Loading**: The model weights (~30 MB) are only downloaded when you first click **Calculate perplexity**.
- Includes preset samples:
  - `“I love Python”` (low perplexity — common English phrase)
  - `“Python love I”` (high perplexity — scrambled word order)
  - `“The capital of France is Paris”` (factual, predictable sentence)
- Pre-pends the standard GPT-2 start token (`50256` / `<|endoftext|>`), ensuring the first token also receives a conditioned probability.

### 2. Section 2 – Probability Table & Step-by-Step Breakdown
- Displays a clean table with:
  - `#` (Token position)
  - `Token` (decoding spaces visually as `␣`)
  - `Probability P`
  - `1/P`
- Visual step cards showing the exact multiplication chain, token count $N$, and the final geometric mean perplexity.

### 3. Section 3 – What Does the Model Predict Next?
- Takes the logits at the sequence's final position.
- Renders the **top 8 most likely candidate tokens** as horizontal progress bars with exact percentage probabilities.
- Highlights the #1 prediction with dedicated brutalist badges.

### 4. Section 4 – Try It by Hand (Simulated Playground)
- An interactive sandbox to build intuition without running the neural network.
- Add and remove tokens, edit token names, and adjust probabilities with live instant recalculation.
- Built-in verification presets:
  - **“I love Python”** $(0.5, 0.8, 0.2) \to$ **Perplexity 2.32**
  - **“All 1/8”** $(8 \text{ tokens at } 0.125 \text{ each}) \to$ **Perplexity 8.00**
  - **“One surprising word”** $(0.9, 0.9, 0.9, 0.01) \to$ **Perplexity 3.42**

---

## 🎨 Design Theme

- **Neo-Brutalist Aesthetic**: Clean white cards, bold $2.5\text{px}$ black borders, high-contrast typography, and tactile offset drop shadows (`4px 4px 0px #121212`).
- **Color Palette**: Light canvas background, purple (`#7c3aed`) and blue (`#3b82f6`) interactive buttons, yellow notification tags, and green/red status banners.
- **Micro-Interactions**: Tactile button presses (`transform: translate(2px, 2px)`).

---

## 🚀 Getting Started

No build tools, bundlers, or Node.js installations are required.

### Quick Start

1. Clone or download this folder:
   ```bash
   cd "Perplexity Explorer"
   ```

2. Open `index.html` in your browser:
   - On macOS:
     ```bash
     open index.html
     ```
   - Or start a quick local web server:
     ```bash
     python3 -m http.server 8000
     ```
     Then navigate to `http://localhost:8000`.

3. Click **“Calculate perplexity”** in Section 1 to download the model and see it in action, or test custom values immediately in Section 4.

---

## 📁 File Structure

```text
Perplexity Explorer/
├── index.html   # Complete application (HTML structure, CSS styling, and JS module)
└── README.md    # Documentation and mathematical explanation
```

---

## 📜 License

MIT License. Created for interactive learning and exploration of modern language models.
