---
title:  "Papers Illustrated: Found In Translation"
mathjax: true
illustrated: true
layout: post
categories: misc
---

<!--
  VISUAL TOOLKIT — reference / cheat sheet for this post.
  Delete this whole comment block once you're done using it as a guide;
  it isn't part of the published post.

  SYMBOL -> COLOR MAP (every symbol always uses its color; subscripts stay
  inside the parent's tile, in the parent's color)
    \mathcal{E}    evaluator            tok-purple
    x              prompt (intent)      tok-blue
    x_\ell         prompt in language   tok-babyblue (x_\text{en} too)
    \ell           language             tok-orange
    \mathcal{LLM}  multilingual LLM     tok-gray     (paper writes \mathcal{M})
    r              model response       tok-pink
    \hat{r}        translated response  tok-cyan
    \mathcal{T}    translation model    tok-teal
    \mathcal{C}    consistency score    tok-red
    \mathbb{E}     expectation          tok-yellow
    \mathcal{X}, \mathcal{L}  (sets)    NO tile, plain math
    operators (= \approx := \in \sum |.|)  NO tile
  Functions ENCLOSE their arguments: the whole application sits in one tile of
  the function's color, with the argument tiles nested inside it
  (arguments are not subscripts, so they keep their own colors):
    r_\ell(x)       = [pink: r_\ell ( [blue x] ) ]
    LLM(x_\ell)     = [gray: LLM ( [babyblue x_\ell] ) ]
    E_x[E(r, r)]    = [yellow: E_x [ [purple: E ( [pink ...] , [pink ...] ) ] ] ]

  Writing math in a tile:
    - inside a raw HTML block (<div ...>):   <span class="tok tok-purple">\(\mathcal{E}\)</span>
    - inside a markdown paragraph/list:     <span class="tok tok-purple">$$\mathcal{E}$$</span>
      (kramdown would strip the backslash from \( in markdown text)

  Colored inline text:
    <span class="hl hl-blue">some phrase</span>

  Tiles in a wrapping row, with arrows:
    <div class="tok-row">
      <span class="tok tok-pink">чат</span>
      <span class="arrow arrow-blue">&rarr;</span>
      <span class="tok tok-blue">cat</span>
    </div>

  Tile with a small note/example underneath (use tok-row-top on the row):
    <div class="tok-row tok-row-top">
      <span class="tok-stack">
        <span class="tok tok-blue">\(x\)</span>
        <span class="tok-note">an example</span>
      </span>
    </div>

  A function application as one tile (in markdown text, use $$...$$ and plain
  parentheses instead of the arrow spans):
    <span class="tok-group">
      <span class="tok tok-pink tok-group">\(r_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
    </span>

  Outlined, unfilled tile for things that aren't math symbols:
    <span class="tok tok-plain">text A</span>

  Colored arrows on their own (also work inline in a sentence):
    <span class="arrow arrow-green">&rArr;</span>
    <span class="arrow arrow-purple">&mapsto;</span>

  Callout / aside box:
    <div class="box box-orange">
      <p>Some explanatory aside.</p>
    </div>

  Caption under a figure:
    <p class="caption">Figure 1: some diagram.</p>

  Available color names: red, orange, yellow, green, teal, blue, babyblue, purple, pink, cyan, gray
  (use as hl-NAME / tok-NAME / arrow-NAME / box-NAME)
-->

This post walks through Section 2, *A Framework for Cross-Lingual Consistency*, of [Found in Translation: Measuring Multilingual LLM Consistency as Simple as Translate then Evaluate](https://aclanthology.org/2025.ijcnlp-long.185.pdf) (Gupta et al., IJCNLP-AACL 2025).

## Section 2

Let's start with the notion of an **Evaluator** <span class="tok tok-purple">$$\mathcal{E}$$</span>. An evaluator is a function that takes two pieces of text and returns a **compatibility score**. For example, BLEU ([Papineni et al., 2002](https://aclanthology.org/P02-1040/)) can be an evaluator, or an LLM-based metric like FActScore ([Min et al., 2023](https://aclanthology.org/2023.emnlp-main.741/)) can be an evaluator.


<div class="tok-row tok-row-top">
  <span class="tok-stack">
    <span class="tok tok-plain">text A</span>
    <span class="tok-note">"… the three main laws of Gregor Mendel are: separation, independent assortment, dominance"</span>
  </span>
  <span class="arrow">+</span>
  <span class="tok-stack">
    <span class="tok tok-plain">text B</span>
    <span class="tok-note">"Mendel's laws are of two types of formal formulae …"</span>
  </span>
  <span class="arrow arrow-purple">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-purple">\(\mathcal{E}\)</span>
    <span class="tok-note">compares them</span>
  </span>
  <span class="arrow arrow-purple">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-plain">score</span>
    <span class="tok-note">0.4: not much agreement</span>
  </span>
</div>

Next, we define a prompt <span class="tok tok-blue">$$x$$</span>: an **abstract, language-agnostic** communicative intent. Let <span class="tok tok-orange">$$\ell$$</span> $$\in \mathcal{L}$$ be a language belonging to the set of languages $$\mathcal{L}$$.

<span class="tok tok-babyblue">$$x_\ell$$</span> is an actual sentence: the **realization** of the intent <span class="tok tok-blue">$$x$$</span> in language <span class="tok tok-orange">$$\ell$$</span>. Let <span class="tok tok-babyblue">$$x_\text{en}$$</span> be the English version.

<div class="tok-row tok-row-top">
  <span class="tok-stack">
    <span class="tok tok-blue">\(x\)</span>
    <span class="tok-note"><em>intent:</em> learn what Mendel's laws of inheritance are</span>
  </span>
  <span class="tok-stack">
    <span class="arrow arrow-orange">&rarr;</span>
    <span class="tok-note"><span class="tok tok-orange">\(\ell = \text{en}\)</span></span>
  </span>
  <span class="tok-stack">
    <span class="tok tok-babyblue">\(x_\text{en}\)</span>
    <span class="tok-note">"What are Mendel's Laws of Inheritance?"</span>
  </span>
</div>
<p class="caption">Figure 2: The intent, realized in the English language. Picking a different <span class="tok tok-orange">\(\ell\)</span> would give the same question worded in, say, Hindi.</p>

$$\mathcal{X}$$ is the set of all these abstract prompts. We assume every prompt in $$\mathcal{X}$$ has a valid translation, with the same meaning, in every language <span class="tok tok-orange">$$\ell$$</span> $$\in \mathcal{L}$$.

For a multilingual <span class="tok tok-gray">$$\mathcal{LLM}$$</span>, we define its **response** (i.e: generation) to prompt <span class="tok tok-blue">$$x$$</span> in language <span class="tok tok-orange">$$\ell$$</span> as <span class="tok tok-babyblue">$$x_\ell$$</span>:

<div class="tok-row">
  <span class="tok-group">
    <span class="tok tok-pink tok-group">\(r_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
  </span>
  <span class="arrow">=</span>
  <span class="tok-group">
    <span class="tok tok-gray tok-group">\(\mathcal{LLM}\)<span class="arrow">(</span><span class="tok tok-babyblue">\(x_\ell\)</span><span class="arrow">)</span></span>
  </span>
</div>

The same intent, asked in two languages, goes through the same model, but can come back as two very different answers:

<div class="tok-row tok-row-top">
  <span class="tok-stack">
    <span class="tok tok-babyblue">\(x_\text{en}\)</span>
    <span class="tok-note">"What are Mendel's Laws of Inheritance?"</span>
  </span>
  <span class="arrow">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-gray">\(\mathcal{LLM}\)</span>
  </span>
  <span class="arrow arrow-pink">&rarr;</span>
  <span class="tok-stack">
    <span class="tok-group">
      <span class="tok tok-pink tok-group">\(r_\text{en}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
    </span>
    <span class="tok-note">365 words: the three laws, each explained</span>
  </span>
</div>
<div class="tok-row tok-row-top">
  <span class="tok-stack">
    <span class="tok tok-babyblue">\(x_\text{hi}\)</span>
    <span class="tok-note">"मेंडल के वंशागति के नियम क्या हैं?"</span>
  </span>
  <span class="arrow">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-gray">\(\mathcal{LLM}\)</span>
  </span>
  <span class="arrow arrow-pink">&rarr;</span>
  <span class="tok-stack">
    <span class="tok-group">
      <span class="tok tok-pink tok-group">\(r_\text{hi}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
    </span>
    <span class="tok-note">85 words: "two types of formal formulae", some of it wrong</span>
  </span>
</div>
<p class="caption">Figure 3: One intent <span class="tok tok-blue">\(x\)</span>, two languages, one model, two responses. The word counts and content are from the paper's Figure 1 (Llama-3 8B).</p>

This paper aims to measure the gap between <span class="tok tok-gray">$$\mathcal{LLM}$$</span> generations in English <span class="tok tok-pink">$$r_\text{en}$$</span> vs. other languages <span class="tok tok-pink">$$r_\ell$$</span>. The **consistency** of the model in language <span class="tok tok-orange">$$\ell$$</span>, as judged by evaluator <span class="tok tok-purple">$$\mathcal{E}$$</span>, is:

<div class="tok-row">
  <span class="tok-group">
    <span class="tok tok-red tok-group">\(\mathcal{C}_{\mathcal{LLM},\mathcal{E}}\)<span class="arrow">(</span><span class="tok tok-orange">\(\ell\)</span><span class="arrow">)</span></span>
  </span>
  <span class="arrow">:=</span>
  <span class="tok tok-yellow tok-group">\(\mathbb{E}_x\)<span class="arrow">[</span><span class="tok tok-purple tok-group">\(\mathcal{E}\)<span class="arrow">(</span><span class="tok tok-pink tok-group">\(r_\text{en}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">,</span><span class="tok tok-pink tok-group">\(r_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">)</span></span><span class="arrow">]</span></span>
</div>


But comparing <span class="tok tok-pink tok-group">$$r_\text{en}$$(<span class="tok tok-blue">$$x$$</span>)</span> with <span class="tok tok-pink tok-group">$$r_\ell$$(<span class="tok tok-blue">$$x$$</span>)</span> directly is hard, because most model-based evaluators are monolingual and optimized for English. 

So we add a **translation model** <span class="tok tok-teal">$$\mathcal{T}_{\ell \to \text{en}}$$</span>, which maps responses from the language of generation <span class="tok tok-orange">$$\ell$$</span> into English:

<div class="tok-row tok-row-top">
  <span class="tok-stack">
    <span class="tok-group">
      <span class="tok tok-pink tok-group">\(r_\text{hi}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
    </span>
    <span class="tok-note">in Hindi</span>
  </span>
  <span class="arrow arrow-teal">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-teal">\(\mathcal{T}_{\text{hi} \to \text{en}}\)</span>
    <span class="tok-note">translates</span>
  </span>
  <span class="arrow arrow-teal">&rarr;</span>
  <span class="tok-stack">
    <span class="tok tok-plain">English text</span>
    <span class="tok-note">now the evaluator can score it faithfully</span>
  </span>
</div>
<p class="caption">Figure 5: Translate-Then-Evaluate</p>

Let's call the translated response <span class="tok tok-cyan tok-group">$$\hat{r}_\ell$$(<span class="tok tok-blue">$$x$$</span>)</span>:

<div class="tok-row">
  <span class="tok tok-cyan tok-group">\(\hat{r}_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span>
  <span class="arrow">:=</span>
  <span class="tok tok-teal tok-group">\(\mathcal{T}_{\ell \to \text{en}}\)<span class="arrow">(</span><span class="tok tok-pink tok-group">\(r_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">)</span></span>
</div>

If the evaluator gives about the same score before and after translation, we can swap the translated response in for the original one. Now both inputs to <span class="tok tok-purple">$$\mathcal{E}$$</span> are in English:

<div class="tok-row">
  <span class="tok tok-red tok-group">\(\mathcal{C}_{\mathcal{LLM},\mathcal{E}}\)<span class="arrow">(</span><span class="tok tok-orange">\(\ell\)</span><span class="arrow">)</span></span>
  <span class="arrow">\(\approx\)</span>
  <span class="tok tok-yellow tok-group">\(\mathbb{E}_x\)<span class="arrow">[</span><span class="tok tok-purple tok-group">\(\mathcal{E}\)<span class="arrow">(</span><span class="tok tok-pink tok-group">\(r_\text{en}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">,</span><span class="tok tok-cyan tok-group">\(\hat{r}_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">)</span></span><span class="arrow">]</span></span>
</div>

We compute the above expectation <span class="tok tok-yellow tok-group">$$\mathbb{E}_x$$</span> as the empirical mean for LLM generations across a set of prompts $$\mathcal{X}$$:

<div class="tok-row">
  <span class="tok tok-red tok-group">\(\mathcal{C}_{\mathcal{LLM},\mathcal{E}}\)<span class="arrow">(</span><span class="tok tok-orange">\(\ell\)</span><span class="arrow">)</span></span>
  <span class="arrow">\(\approx \displaystyle \frac{1}{|\mathcal{X}|} \sum_{x \in \mathcal{X}}\)</span>
  <span class="tok tok-purple tok-group">\(\mathcal{E}\)<span class="arrow">(</span><span class="tok tok-pink tok-group">\(r_\text{en}\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">,</span><span class="tok tok-cyan tok-group">\(\hat{r}_\ell\)<span class="arrow">(</span><span class="tok tok-blue">\(x\)</span><span class="arrow">)</span></span><span class="arrow">)</span></span>
</div>
<p class="caption">Equation 1 of the paper: sum the evaluator's scores over every prompt in \(\mathcal{X}\), then divide by the number of prompts \(|\mathcal{X}|\). That is the average from Step 2, written out.</p>

That's the whole framework. To use it, you only need to pick two things: a translation model <span class="tok tok-teal">$$\mathcal{T}$$</span> and an English evaluator <span class="tok tok-purple">$$\mathcal{E}$$</span>.

## Results

For the translation model <span class="tok tok-teal">$$\mathcal{T}$$</span>, the paper uses NLLB, a 54B-parameter translation model ([Costa-jussà et al., 2022](https://arxiv.org/abs/2207.04672)). Native speakers of ten languages rated how well its translations kept the original information, and gave it 4.26 out of 5 on average. For the evaluator <span class="tok tok-purple">$$\mathcal{E}$$</span>, the paper tries two:

- <span class="tok tok-purple">$$\mathcal{E}_\text{FAct}$$</span> measures **information consistency**: does the translated response state the same facts as the English one? It is built on FActScore. It checks claims in both directions: are the translated response's claims supported by the English one, and are the English response's claims covered by the translated one? The two directions are then combined into one F-score.
- <span class="tok tok-purple">$$\mathcal{E}_\text{Emp}$$</span> measures **empathy consistency**: when someone describes a struggle, does the model respond with the same kinds of empathy in every language? Three classifiers check each response for emotional reactions, interpretations and explorations (from the EPITOME framework of Sharma et al., 2020). The score is 1 if both responses show the same mix, and 0 otherwise.

The paper tests 12 LLMs on 30 languages. All the numbers below are average consistency scores from Table 4 of the paper.

- **Models are far from consistent.** Average information consistency ranges from 0.32 (`Llama-3.1-8B`) to 0.68 (`gpt-4o`).
- **Proprietary models are more consistent than open-weight ones.** For information, `gpt-4o` scores 0.68 against 0.59 for the best open-weight model, `gemma-2-27b`. For empathy, the gap is wider: 0.82 for `gpt-4o` against 0.67 for `aya-exp-32b`.
- **The writing system matters.** Models are most consistent on languages written in the Latin script, and much less on scripts like the Indic ones. The gap is largest for `aya-exp-8b`:

<div class="tok-row">
  <span class="tok tok-red tok-group">\(\mathcal{C}\)<span class="arrow">(</span><span class="tok tok-orange">\(\ell \in \text{Latin script}\)</span><span class="arrow">)</span></span>
  <span class="arrow">=</span>
  <span class="tok tok-plain">0.59</span>
</div>
<div class="tok-row">
  <span class="tok tok-red tok-group">\(\mathcal{C}\)<span class="arrow">(</span><span class="tok tok-orange">\(\ell \in \text{Indic scripts}\)</span><span class="arrow">)</span></span>
  <span class="arrow">=</span>
  <span class="tok tok-plain">0.11</span>
</div>
<p class="caption">Information consistency of <code>aya-exp-8b</code>, averaged over all languages <span class="tok tok-orange">\(\ell\)</span> written in each script.</p>

- **Lower-resource language families do worse.** For the same model, information consistency is 0.61 for Romance languages but 0.06 for Dravidian languages (Kannada, Malayalam, Tamil, Telugu).
- **Languages seen in training do better.** Models are more consistent on languages their training data explicitly covers. The Gemini models hold up best on languages outside their training data.
- **One dimension isn't enough.** `aya-exp-8b` has one of the lowest information consistency scores (0.42), yet on empathy (0.65) it beats `gemini-2.0` (0.60). A model can be consistent in one way and inconsistent in another.

## Takeaway

Take away the colors, and Equation 1 says something simple: ask the model the same question in English and in another language, translate the second answer back into English, score how well the two answers agree, and average over many questions. Rather than building a new evaluator for every language, you reuse the good evaluators that already exist for English. And because the two answers are compared *with each other*, not against a correct answer, it works for open-ended questions with no single right answer.

The weak point is <span class="tok tok-teal">$$\mathcal{T}$$</span>. If the translation drops or adds information, <span class="tok tok-red">$$\mathcal{C}$$</span> ends up measuring the translator instead of the model. The paper checks this for information consistency in two ways. One is the native-speaker ratings above. The other is a round-trip test: English answers translated into another language and back scored 0.86 to 0.94 against the originals. The paper hasn't yet checked translation quality for empathy, and languages without a good translation system can't be evaluated at all. Within those limits, it's a cheap and general way to ask whether a model says the same thing in every language, and right now the answer is often no.
