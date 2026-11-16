# Self-Reports Do Not Identify Self-Models An Identifiability Test for Counterfactual Reports

> Markdown version of the project page at https://aiml-sdu.github.io/self-reports-page/. Interactive figures are on the web page. Every number here also appears there and comes from the paper or its stored results.

Phongsakon Mark Konrad, Toygar Tanyel and Serkan Ayvaz · Centre for Industrial Software, University of Southern Denmark · Promake · ICML 2026 Workshop on Philosophy of Machine Learning, Oral

[OpenReview](https://openreview.net/forum?id=iBq4yYwYfd) [arXiv](https://arxiv.org/abs/2609.32449) [PDF](https://arxiv.org/pdf/2609.32449) [Markdown](index.md) Cite

The claim

For every grounded report mechanism there is a prompt-bound twin that never reads the state and yields the same distribution of reports.

So accuracy alone cannot show that a self-report reads the model's hidden state. We hold the named intervention fixed and change only the examples in the prompt. The reports drift toward whichever answers the prompt makes authoritative.

Fix *I = i*. For any grounded mechanism inducing the kernel K_c,d(i) = P(R | C=c, D=d, I=i), there is a prompt-bound mechanism f(c,d,i,η) with η ~ U[0,1] that does not read *H* yet reproduces K_c,d(i) for every (c,d). The two are observationally equivalent and disagree under any intervention on *H* to which the grounded mechanism is sensitive.

Claim

Observation cannot separate a report that reads the hidden state from one that reads only the prompt. Proposition 2.1 builds the twin.

Evidence

With the steering target fixed, four demonstrations from a different concept moved reports toward that source in all three models, by +0.030 in Qwen2.5-7B, +0.044 in Gemma-2-9B and +0.114 in Llama-3.1-8B, each over 240 rows with a 95% bootstrap interval above zero. Without demonstrations all three stayed below zero. Eight shuffled demonstrations raised the pull to +0.112, +0.113 and +0.177.

Limit

Telling the model that the evidence came from the source brought every interval down to one that overlaps zero. A false label brought the pull back in Gemma (+0.094) and Llama (+0.041), but not in Qwen. The claim covers four affect-like directions in three models.

Two self-models illustration

1Setup

## What we observe, and what could produce it

Language models write fluent reports about their own states, and these reports are starting to serve as evidence in interpretability and oversight. Philosophers of introspection have long doubted that human self-reports track the states they describe. We ask the matching question for models, and we ask it as an identification problem.

Fix a context *C*, a demonstration environment *D* and a named intervention *I*. We observe only P(R | C, D, I), the distribution of reports. A grounded mechanism produces the report from the counterfactual hidden state, R = g(H_I, C, D, ε). A prompt-bound mechanism produces it from the prompt alone, R = φ(C, D, I-as-label, η). Both are allowed to depend on *D*. The question is which of *H_I* and *D* decides the report when both vary.

**Self-report *R*.** The unsteered model's answer to the question of what it would say under a named intervention. It is compared with what the steered model actually says.

**Target *τ* and source *σ*.** The target is the concept the intervention names, for example joy. The source is a different concept, for example anger, whose steered answers we place in the prompt. *Y_τ* and *Y_σ* are the answers the model gives when it is actually steered toward each.

**Demonstration environment *D*.** The prompt and answer examples that precede the question. We change *D* and keep the intervention *I* fixed.

**Source pull Δσ = S(R,Y_σ) − S(R,Y_τ).** Positive when the report is closer to the source answers than to the target answers. Zero means no lean either way.

**Content-word F1 *S*.** Word overlap between two answers after lowercasing and removing stopwords, generic affect words and tokens shorter than three characters. It measures closeness of answer family and is not a direct readout of mechanism.

**Grounded and prompt-bound.** A grounded report covaries with the counterfactual hidden state *H_I* when we intervene. A prompt-bound report depends only on the context, the demonstrations and the name of the intervention.

**Mechanism audit.** The model first states which mechanism produced the evidence, then answers in JSON. Only the answer field is scored, so the same evidence can be tested under different labels.

2Agreement

## Two models that agree on every report

We state the problem as one of identification. The thing we observe is the distribution of reports given a context, a set of demonstrations and a named intervention, P(R | C, D, I). A grounded mechanism that reads the hidden state and a prompt-bound mechanism that reads only the prompt can produce exactly this distribution. Proposition 2.1 builds the prompt-bound twin.

The proof builds *f* by inverse-transform sampling from the grounded kernel. It matches every observation by construction and never looks at *H*. So a benchmark that only scores whether reports are right, in one prompt context, cannot separate the two. Behaviour under an intervention on *H* can separate them, and so can behaviour under a sparse change in *D* that leaves *I* alone, because only there do the grounded mechanism and its twin come apart.

*Figure.* **The two mechanisms side by side.** (illustration) Under observation both produce the same distribution of reports. Intervene on the hidden state and only the grounded mechanism responds. Change the demonstrations and a prompt-bound mechanism follows them. Bar heights are schematic and carry no results.

3The test

## The test that separates them

Our diagnostic therefore varies the two things separately. Activation steering sets *I*. The prompt sets *D*. A report that stays with *I* while *D* changes is evidence of binding. A report that follows *D* is evidence that the prompt chooses the mechanism.

We built steering vectors for joy, anger, fear and calm in Qwen2.5-7B-Instruct, Gemma-2-9B-it and Llama-3.1-8B-Instruct, and added them at a middle layer of the residual stream (layers 14 of 28, 21 of 42 and 16 of 32).

For each target *τ*, each source *σ ≠ τ* and each of 20 held-out introspection prompts, we first generate the steered answers *Y_τ* and *Y_σ*. Then the unsteered model is asked what it would answer under the target steering, with different evidence in front of it. That gives 240 rows per condition. Intervals come from 5000 bootstrap resamples of rows. They are descriptive, since rows share prompts and concepts.

We run the test on three open instruction models with steering vectors for joy, anger, fear and calm. The target intervention stays fixed while the demonstrations change. Wrong-source demonstrations moved reports sourceward in all three models, by +0.055, +0.066 and +0.126 relative to no demonstrations.

4The data

## What the data does

Each dot is one of 240 rows (20 introspection prompts × 12 target and source pairs) from the stored ablation results. Axes are square-root scaled. Rows with identical scores are spread in a small cluster so each stays visible. The line marks equal overlap with both answers.

### Wrong demonstrations pull the report toward the source

With four unlabeled demonstrations taken from the source, every model moved sourceward and every interval sat above zero. Redacting the answers attenuated the pull. Removing the demonstrations left all three targetward. Shuffling and paraphrasing the wrong answers kept the pull in place.

*Figure.* **Seven prompt environments, one fixed intervention.** Tap a row to read what the prompt contained. Each mark is the mean source pull over 240 rows with its 95% bootstrap interval. The interval is brick when it lies above zero, teal when below and grey when it crosses zero. Values are the stored data behind Figure 2A of the paper.

The causal role written on the evidence matters as much as the evidence. Honest source and contrast framing pull Qwen and Gemma back near zero, and contrast framing does the same for Llama. Mixing target and source examples moves all three models targetward even though source text is present. Source text alone does not produce the pull.

Demonstration dose works like a dial. Qwen and Gemma cross zero between one and four examples and reach about +0.11 at eight. Llama is already sourceward at one.

*Figure.* **Source pull under shuffled wrong demonstrations.** Slide the dose and watch the bars. Aggregate only, as reported in the paper's dose table. Intervals were not tabulated for this sweep.

A mean of +0.114 compresses 240 reports. The paper prints two Llama rows under wrong-source demonstrations. The first sits near that mean. The second is an extreme case that shows the mechanism at full strength.

*Figure.* **Rows quoted from the paper's qualitative table.** Forced correct binding on row (b) brings its pull to −0.02, and false binding puts it back above +0.4.

### The label on the evidence decides the pull

We audit the binding. The model first states which mechanism produced the evidence, and only its answer is scored. Correct labels bring the pull to intervals overlapping zero in all three models. False labels restore it in Gemma and Llama.

The audit holds the answer evidence fixed and changes one thing, the causal role the prompt assigns to it. The model states whether the examples belong to the source or to the target mechanism, and then answers. We score only the answer.

This is the behavioural form of an interchange intervention on the binding variable. With the correct label, the pull falls to an interval that overlaps zero in all three models. With a false label, it comes back above zero in Gemma and Llama. Gemma shows the gradient most clearly, from +0.094 under the false label to −0.016 under the correct one.

*Figure.* **One set of evidence under three labels.** Switch the label and watch each model's interval move. A condition fails the diagnostic when its 95% interval lies strictly above zero and passes when the interval overlaps zero. Templates are the paper's illustrative renderings, lightly edited there for readability.

Qwen's false label left the pull at −0.010 with an interval of [−0.026, +0.005]. We do not count that as correct binding. A weaker steering signal at the chosen layer or lower sensitivity to the prompt environment could explain it.

For scale, a report that simply is the target-steered output scores a pull near −0.957, −0.751 and −0.900 in Qwen, Gemma and Llama. Those values bound the span from target to source. Llama's shift from no demonstrations to wrong ones covers nearly 14% of it. The shift is small next to direct steering, and it is exactly the part the environment adds under a fixed intervention.

### Cleaner geometry does not remove the pull

One rival explanation is that the steering vectors are poor, so the model has no clear state to report and falls back on the prompt. If so, the pull should be largest where the concept geometry is worst.

We compared the distances between concept centroids in activation space with the distances between the behaviour they produce, over the six concept pairs. The match was strongest in Llama (Pearson +0.961, Spearman +0.886), weaker in Qwen (+0.663, +0.371) and Gemma (+0.652, +0.543). Llama also had the largest wrong-demonstration pull. The simplest version of the rival explanation does not fit the Llama case, though this check cannot rule out off-manifold effects in general.

*Figure.* **Activation distance against behaviour distance for the six concept pairs.** Point at a dot to see the pair. The number under each panel is that model's wrong-demonstration pull at four examples.

As a second control we projected each steering vector onto a local 16-dimensional PCA subspace at the chosen layer, which keeps the intervention near the local activation manifold. The wrong-demonstration pull stayed above zero in Qwen and Llama, and reports without demonstrations stayed targetward. In Llama the projected and raw pulls differ by 0.001. The Gemma run is deferred.

*Figure.* **Raw against tangent-projected steering.** Switch the vector and compare the two prompt conditions. Values and 95% intervals from the paper's main and tangent tables. This does not establish that the interventions respect the manifold fully.

### The pull has a direction

If the pull came from generic salience, every pair of concepts would show it about equally. It does not. Joy with anger as the source is the strongest pair in Qwen (+0.145) and Gemma (+0.179) and reaches +0.127 in Llama. Llama's strongest pair is fear with calm as the source, at +0.150.

*Figure.* **Source pull for every target and source pair at four demonstrations.** Tap a cell. Rows are targets and columns are sources. Brick cells lean to the source and teal cells lean to the target. Outlined cells are the extremes the paper reports.

Some pairs bind in one direction and not the other. The asymmetry is one of the reasons we keep the claim to these four affect-like directions.

5Scope

## What would have refuted it, and where it stops

Three outcomes would have counted against us. If wrong demonstrations had left the pull at zero, reports would look bound to the intervention. That happened in none of the three models.

If the pull had appeared only where steering is weak or concept geometry is poor, it would point to bad interventions. It was largest in Llama, where activation distances tracked behaviour most closely (Pearson +0.961), and it survived a tangent projection of the steering vector in Qwen and Llama.

If correct labels had left the pull in place, the audit would say nothing about binding. Correct labels brought every interval down to one that overlaps zero. One result stays open. Qwen's false label did not restore the pull (−0.010, interval [−0.026, +0.005]), and we do not read that as correct binding.

- That observational accuracy cannot separate a grounded report from a prompt-bound one (Proposition 2.1).

- That in three open instruction models, with four affect-like concepts, reports under a fixed intervention move with the demonstrations (step 4).

- That the causal role assigned to the evidence gates the movement (step 4).

- That these models lack a self-model. It shows that these reports do not identify one in this setting.

- That the interventions respect the manifold fully. The tangent control covers Qwen and Llama only (step 4).

- Anything beyond four affect-like directions in three models. The bootstrap intervals describe the rows and are not tests of their independence.

Self-report benchmarks should pair accuracy with a test of invariance under environment shift at a fixed intervention. A model can pass on accuracy and fail on binding. Next we want interventions that respect the manifold in all three models, concepts outside affect and larger systems.

## Cite this article

```bibtex
@inproceedings{konrad2026selfreports,
  title         = {Self-Reports Do Not Identify Self-Models: An Identifiability Test for Counterfactual Reports},
  author        = {Konrad, Phongsakon Mark and Tanyel, Toygar and Ayvaz, Serkan},
  booktitle     = {ICML 2026 Workshop on Philosophy of Machine Learning},
  year          = {2026},
  eprint        = {2609.32449},
  archivePrefix = {arXiv},
  url           = {https://openreview.net/forum?id=iBq4yYwYfd}
}
```

Plotted values come from the paper and from its stored result files, the expanded demonstration ablation (240 rows per model and condition), the environment switch summary, the pairwise pull table and the geometry summary. Nothing on this page is simulated. The two-model schematics are marked as illustrations.

A project of the [Applied and Interpretable Machine Learning Research Group](https://aiml-sdu.github.io) at the University of Southern Denmark.
