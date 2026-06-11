# SemEval Task Proposals 2027 Reviews for Submission #20

Title: SemEval-2027 Task Proposal: Coherence-Aware Compositional Fine Grained Natural Language Inference (CoCo-NLI)
Authors: Jon Felix Apaolaza Larraya, Aitor Soroa, Rodrigo Agerri and Inigo Lopez-Gazpio


##                            META-REVIEW


Comments: 

> Overall, reviewers are positive about this proposal. The task appears to be novel and interesting; data and pilot experiments exist;
> reviewers believe the proposal is well-structured and evaluation setup is strong; organizing team are strong too.
> Both reviewers expressed some concerns over the conceptual framing of the task and some other technical details,
> but these do not undermine the strengths of the submission.


##                            REVIEWER #1


Detailed Comments
-------------------

This proposal takes the reversal curse and reframes it as a paired fine-grained NLI task on PhrasIS,
with a deterministic label-reversal operator and two coherence-based metrics (SoftCoh, HardCoh) on top of weighted F1, across EN/ES/EU.

The team is strong, the pilot exists, the infrastructure plan is credible, and the evaluation section is the cleanest part of the proposal.
I want to like this more than I do, and I'll try to explain why.

> (R1.1) My main concern is conceptual. The reversal curse as introduced by Berglund et al. is a claim about parametric knowledge: 
> the model has seen "A is B" in training and fails to produce "B is A" at inference. 
> It's a failure of weight-level symmetry. What CoCo-NLI measures is something different: given both phrases in context, does the model assign mutually compatible directional labels when the pair is flipped?
> That's in-context compositional consistency, and it's a perfectly worthwhile thing to measure, but it is not the same phenomenon.

**Answer R1.1** We agree with the reviewer that DiCo-NLI is not identical to the original Reversal Curse setup of Berglund et al., which targets parametric factual reversal: a model exposed to a statement such as `A is B` fails to retrieve or generate the reverse form `B is A`. DiCo-NLI instead operates at the level of fine-grained NLI relation prediction.

However, the task is closer to the Reversal Curse motivation than a purely test-time pair-flipping consistency check. In our LREC pilot, we explicitly distinguished two directional evaluation regimes. In the **seen/easy** condition, models were fine-tuned/model-selected on the original directional constructs and then evaluated on their reversed manipulated counterparts. This tested whether exposure to one directional formulation supported correct behavior under reversal. In the **unseen/hard** condition, both the original and reversed phrase-pair constructs came from the held-out evaluation split, testing directional generalization to novel pairs.

The LREC experiments showed that the unseen/hard setting was substantially more challenging than the seen/easy setting across model families. For the SemEval task, we therefore decided to focus the official evaluation directly on this harder and cleaner scenario. This keeps the task simpler for participants, avoids introducing an additional seen/unseen split distinction in the competition setup, and makes the benchmark target clearer: systems must generalize directional NLI decisions to held-out phrase pairs and their reversed counterparts.

We have revised the framing to avoid claiming that the task directly measures Berglund-style parametric knowledge reversal. The task is now presented as **DiCo-NLI: Directional Consistency in Fine-Grained Natural Language Inference**. Berglund et al. motivate the use of reversal as a stress test for directional generalization, while DiCo-NLI operationalizes that idea in a supervised fine-grained NLI setting through a deterministic label-reversal operator.

> (R1.2) A model can be perfectly consistent on CoCo-NLI and still fail Berglund-style probes, or vice versa.
> The proposal elides this ("the inability of LLMs... to preserve inference when relation direction is reversed") and uses the gravity of the RC literature to motivate a task that doesn't actually test the RC.

**Answer R1.2** We agree that DiCo-NLI scores and Berglund-style Reversal Curse scores are not logically interchangeable at the level of individual systems. A model may be consistent when both phrases are provided in an NLI input pair and still fail a parametric factual-reversal probe; conversely, a model may retrieve reversed factual associations while still failing to assign fine-grained entailment labels consistently. We therefore do not treat DiCo-NLI as a direct proxy measurement of the original Reversal Curse.

However, the connection to Berglund et al. is not merely rhetorical. The closest point of contact is the **seen/easy** condition in our LREC pilot. In that setting, models were fine-tuned/model-selected on original directional phrase-pair constructs and then evaluated on their reversed manipulated counterparts. This is structurally analogous to Berglund et al. in the sense that exposure to one directional formulation is tested against behavior in the reverse direction. The crucial difference is the target of prediction: Berglund et al. evaluate parametric factual recall/generation, whereas DiCo-NLI evaluates fine-grained NLI relation prediction.

| Berglund et al. Reversal Curse | LREC seen/easy DiCo-NLI setup |
|---|---|
| Model is trained/fine-tuned on `A is B` | Model is fine-tuned/model-selected on an original directional phrase construct `A -> B` |
| Model is queried on the reverse form `B -> A` | Model is evaluated on the reversed manipulated phrase pair `B -> A` |
| Tests whether one directional exposure supports reverse factual retrieval | Tests whether one directional NLI construct supports reverse-label prediction |
| Output is factual recall or generation | Output is a fine-grained NLI label |

The SemEval task itself focuses on the harder **unseen** condition, because our LREC experiments showed that this setting is more challenging and cleaner for participants: both the original and reversed phrase-pair constructs are held out from training. This design choice avoids adding a separate seen/unseen competition distinction while retaining the core reversal-based diagnostic.

We have revised the motivation accordingly. Berglund et al. are now presented as conceptual and methodological motivation for reversal-based evaluation, not as a claim that DiCo-NLI directly measures weight-level factual reversal. DiCo-NLI contributes to the broader line of work on directional generalization by operationalizing reversal at the level of fine-grained NLI labels and deterministic label reversal.

> (R1.3) I'd push the authors to either (a) rename and remotivate the task as directional inferential coherence, which is what it actually is!
> and which I think is defensible on its own terms, or (b) include a component that correlates CoCo-NLI performance with a parametric-reversal probe and argues empirically that the two are connected.

**Answer R1.3** We follow the reviewer's recommendation and adopt option (a): the task has been renamed and remotivated around directional consistency rather than presented as a direct Reversal Curse benchmark. The revised task name is **DiCo-NLI: Directional Consistency in Fine-Grained Natural Language Inference**.

We intentionally use the term **consistency** rather than **coherence**. Although the reviewer's phrase "directional inferential coherence" captures the intended evaluation target, "coherence" has a well-established meaning in discourse analysis and may suggest discourse-level textual organization, discourse connectedness, or interpretive continuity across larger text units. During discussions of our LREC work, linguists also pointed out that using "coherence" for our label-level reversal metric could be misleading from a discourse-analysis perspective. This feedback made clear that "consistency" is the more precise term for the property we evaluate: whether predictions over a phrase pair and its reversed counterpart remain mutually compatible under a deterministic label-reversal operator.

We do not plan to add a separate parametric-reversal probe as an official component of the SemEval task. Such a component would require a different experimental design, including controlled factual exposure, factual-recall prompts, and assumptions about whether systems have or have not seen particular facts during training. This would blur the scope of the shared task and make the evaluation harder to interpret for participants. Instead, the official task focuses on a single, well-defined problem: fine-grained NLI label prediction under directional reversal.

We agree that the empirical relationship between DiCo-NLI and Berglund-style parametric reversal is an interesting research question. We therefore now present it as a possible follow-up analysis rather than as an assumption built into the shared-task definition.

> (R1.4) Second concern, on the metric. HardCoh is defined as coherent-under-Rev AND both predictions correct. But Rev is deterministic and the gold is consistent under Rev by construction.
> So if both predictions are correct, coherence is automatic. HardCoh therefore reduces to paired accuracy "both items in the minimal pair labeled correctly" which is a stricter accuracy variant, not a distinct evaluation axis.

**Answer R1.4** We agree with the reviewer's mathematical observation. Because the reversed gold label is generated by a deterministic reversal operator, correctness in both directions entails consistency automatically. Thus, for the reversible subset, `HardCons` should be understood as **paired directional correctness**: a pair receives credit only when both the original item and its reversed counterpart are predicted correctly.

We have revised the terminology and explanation accordingly. We no longer present the hard metric as an independent consistency condition beyond correctness. Instead, we describe it as the strict paired counterpart to item-level label evaluation. This distinction is still important because `HardCons` is not equivalent to ordinary item-level accuracy or F-measure.

For example, consider two reversible pairs, i.e. four ordered instances:

| Pair | System A: original correct? | System A: reverse correct? | System B: original correct? | System B: reverse correct? |
|---|---|---|---|---|
| 1 | yes | no | yes | yes |
| 2 | no | yes | no | no |

Both systems obtain the same item-level accuracy:

```text
System A accuracy = 2/4 = 0.50
System B accuracy = 2/4 = 0.50
```

However, their paired hard scores differ:

```text
System A HardCons = 0/2 = 0.00
System B HardCons = 1/2 = 0.50
```

System A gets one direction correct in each pair, but never solves a complete reversible pair. System B solves one reversible relation in both directions. Thus, `HardCons` captures whether correctness is paired under reversal, rather than merely accumulated over isolated ordered instances.

More generally, if item-level correctness were independent with probability `p`, the expected paired hard score would be `p^2`, not `p`. This illustrates why the metric is stricter than item-level accuracy: partial success on one direction of a reversible relation is not enough.

In the revised task description, we therefore report `F-measure` for standard label prediction, `SoftCons` for self-consistency independent of correctness, and `HardCons` for paired directional correctness over reversible instances.

> (R1.5) SoftCoh is the metric doing real work: it separates self-consistency from correctness and can catch a model that's reliably wrong in a coherent way.
> I think the paper would be stronger if it led with the F1 vs SoftCoh gap as the diagnostic signature and presented HardCoh honestly as paired accuracy.
> As written, HardCoh is oversold.

**Answer R1.5** We agree with the reviewer that `SoftCons` is the metric that most directly isolates the novel diagnostic dimension of the task. `F-measure` evaluates whether individual ordered instances are labeled correctly, whereas `SoftCons` evaluates whether a model's predictions are mutually compatible under reversal, independently of whether those predictions are correct. The contrast between these two metrics is therefore central: a model may obtain reasonable label-level performance while still producing directionally inconsistent predictions, or it may be internally consistent but systematically wrong.

We have revised the task description to foreground this point. In the updated framing, `F-measure` is the standard label-prediction metric, `SoftCons` is the main diagnostic metric for directional self-consistency, and `HardCons` is described more conservatively as paired directional correctness over reversible instances.

We also agree that the previous wording oversold the hard metric. Since the reversed gold label is deterministic, `HardCons` is not an independent consistency axis beyond correctness; it is a strict paired-correctness measure. We keep it because it answers a different practical question from `F-measure`: whether a system solves both directions of a reversible relation as a unit. However, the revised proposal will no longer present `HardCons` as conceptually independent from correctness. Instead, the task analysis will emphasize the joint interpretation of the three scores:

- `F-measure`: item-level label correctness;
- `SoftCons`: prediction-level directional consistency, regardless of correctness;
- `HardCons`: paired correctness under reversal.

This revision should make the diagnostic role of `SoftCons` explicit while preserving `HardCons` as a useful stricter evaluation summary.

> (R1.6) Third, and this is the one that most affects my score: there are no numbers. 
> The proposal lists eight baseline architectures, cites a pilot, asserts there is clear room for improvement, and then asks reviewers to take all of that on faith while the pilot paper is in press and not accessible.
> A compact three-row table one encoder, one decoder, one enc-dec, with F1/SoftCoh/HardCoh would have made the case concrete and would have let me verify the "room for improvement" claim myself.
> Its absence is particularly awkward given that the whole argument for running the task is that current systems fail on it.

**Answer R1.6** We agree with the reviewer. The proposal should have included a compact baseline table rather than relying on a citation to an in-press pilot paper. At the time of submission, the LREC paper was not yet externally available, but this does not remove the need to provide enough empirical evidence inside the proposal itself. The pilot paper is now available here: https://lrec.elra.info/lrec2026-main-423

We have now added representative pilot results to the task documentation and will include them in the revised proposal/task description. We include two summaries below: first, one representative high-performing model from each architecture family; second, the mean and sample standard deviation across all evaluated models in each family. This avoids relying on a single cherry-picked run while keeping the table compact. `F-measure` corresponds to standard label prediction on the positive combined test track. `SoftCons` and `HardCons` correspond to the unseen/hard directional-consistency evaluation on the positive combined track, which is the setting closest to the planned SemEval evaluation.

Representative models:

| Architecture family | Representative model | F-measure | SoftCons | HardCons |
|---|---|---:|---:|---:|
| Encoder-only | DeBERTa v3-base | 0.75 | 0.84 | 0.79 |
| Decoder-only | OPT-125M | 0.61 | 0.65 | 0.58 |
| Encoder-decoder | BART-large | 0.68 | 0.80 | 0.72 |

Family-level mean ± sample standard deviation:

| Architecture family | F-measure | SoftCons | HardCons |
|---|---:|---:|---:|
| Encoder-only | 0.713 ± 0.031 | 0.770 ± 0.049 | 0.721 ± 0.048 |
| Decoder-only | 0.583 ± 0.038 | 0.568 ± 0.060 | 0.498 ± 0.070 |
| Encoder-decoder | 0.655 ± 0.024 | 0.768 ± 0.025 | 0.693 ± 0.019 |

These results support the feasibility of the task while also showing clear room for improvement. Encoder models perform strongest overall, but even the best representative encoder does not solve the hard/unseen directional setting. Decoder-only models lag substantially, especially under `HardCons`, and encoder-decoder models are competitive but still below the strongest encoders. The family-level means show that these are not isolated model choices: architecture family matters systematically in the pilot.

We will release the corresponding baseline scripts, documented hyperparameters, and scorer as part of the starter kit. At minimum, the starter kit will include one tuned encoder baseline and one decoder or encoder-decoder baseline; our goal is to include representative models from all three architecture families.


A few more things worth raising.

> (R1.7) Contamination is not discussed. PhrasIS has been public since 2024 and any frontier model trained through 2025-26 has plausibly ingested it.

**Answer R1.7** We agree that contamination must be treated explicitly. PhrasIS has been described in the literature since 2024, and we cannot assume that large frontier models have not been exposed to some form of the resource or to examples from the paper. We will therefore not present the benchmark as contamination-free by default.

At the same time, the official DiCo-NLI evaluation set is not simply the public PhrasIS dataset reused as-is. The SemEval data will be a task-specific construction: we select the reversible labels, generate paired reversed instances through the deterministic reversal operator, add `NEGATIVE_OTHER` samples from the non-reversible PhrasIS labels as noise, create new Spanish and Basque versions for the task, and define new trial/final splits. The multilingual and mixed-language tracks in particular will be newly produced for the shared task and will not correspond to a pre-existing public benchmark.

To reduce contamination risk, we will document the split construction in the `data/` folder and apply several safeguards: hidden final test labels during the competition, delayed release of gold labels, a held-out final evaluation split generated after task specification, provenance metadata for all items, and an audit of exact or near-exact overlap with examples that have appeared in papers or public documentation.

Finally, we will make the contamination risk explicit in the task documentation. Since DiCo-NLI evaluates not only item-level `F-measure` but also paired directional consistency over reversed instances, memorization of isolated labels is less sufficient than in a standard static classification benchmark. Nevertheless, we agree with the reviewer that contamination can affect leaderboard interpretation, and we will report the construction protocol and contamination-control decisions together with the official release.

> (R1.8) For a task whose scientific payload is measuring an LLM reasoning failure, the proposal needs at least a paragraph on how the test split is constructed unreleased items, newly written, perturbed, canary-checked, something.

**Answer R1.8** This concern is related to contamination but concerns the concrete construction of the final evaluation split, so we address it explicitly. The official DiCo-NLI test set will be built as a held-out grouped split, not as a random list of ordered instances.

The grouping unit will be the underlying source phrase pair. For every source pair `(A,B)`, all derived instances will be assigned to the same split: the original ordered item `(A,B)`, the reversed item `(B,A)`, the corresponding Spanish and Basque translations, and any mixed-language variants derived from that source pair. This prevents leakage where a model could see one direction, language, or variant of a pair during training and be evaluated on another variant during testing.

Concretely, if a reversible pair appears in the final test set, neither its original direction nor its reversed counterpart will appear in the released training or development data. Likewise, multilingual versions of that same source pair will not be split across train/dev/test. This implements the hard/unseen setting from our LREC pilot as the official SemEval setup.

We will also include `NEGATIVE_OTHER` items from non-reversible PhrasIS labels as noise in the label-prediction evaluation. These items will be split using the same grouped protocol and will be excluded from `SoftCons` and `HardCons`, which are defined only over reversible pairs.

The final data release will include a `data/README.md` documenting the split construction, source provenance, label distributions, and the relation between source pairs and derived instances. Before release, we will audit exact and near-exact overlap with examples appearing in papers, task documentation, and public-facing material. Gold test labels will remain hidden during the competition and released only after the official evaluation period.

This protocol is designed to make the leaderboard reflect generalization to held-out directional NLI constructs rather than memorization of isolated phrase-pair labels.

> (R1.9) The translation protocol is underspecified in a way that matters for this specific task.

"Semiautomatic translation by deep MT and bilingual annotators" doesn't say which system, how many annotators per item, or crucially, how the reversal operator is validated after translation.
Basque case marking and Spanish clitic/scope interactions can plausibly shift entailment direction on short phrase pairs.

**Answer R1.9** We agree that the original proposal underspecified the translation protocol. For this task, translation quality cannot be evaluated only as phrase-level fidelity: the translated pair must also preserve the intended directional NLI relation and its reversed counterpart under the label-reversal operator.

We have therefore revised the multilingual data protocol as follows. First, candidate Spanish and Basque translations will be generated using multiple independent translation systems, including neural MT and LLM-based translation. The exact systems used will be documented in the data release. Each phrase will be translated without exposing translation systems to the source-side relation metadata, so that the translation step itself is not biased toward the expected label.

Second, automatic agreement between candidate translations will be used as a triage signal, not as the final validation criterion. We will compute surface and normalized overlap signals, such as token/lemma Jaccard or related string-similarity measures, to identify high-disagreement cases that require closer inspection. However, we agree that such automatic scores are insufficient for Spanish and Basque, where morphology, case marking, clitics, and scope can affect the resulting relation.

Third, validation will be performed by bilingual annotators at the phrase-pair level. We will have two bilingual annotators per target language. For each language, both annotators will independently validate a pilot audit of 100 source pairs. This audit will check: (i) phrase-level translation adequacy, (ii) preservation of the expected NLI label, and (iii) preservation of the deterministic reversed label after swapping premise and hypothesis. We will report raw agreement and Cohen's kappa on this pilot validation, together with the proportion of translation-induced label flips or unstable cases.

For the full release, each translated item will be reviewed by one bilingual annotator. Items flagged as low-confidence by automatic agreement scores, judged ambiguous by the first annotator, or suspected of changing the directional relation will be reviewed or adjudicated by the second annotator. A translated pair will only be retained if the original and reversed target-language instances remain compatible with the expected label-reversal mapping.

If the problem is a bad translation, the item will be corrected. If the target-language construction naturally changes or destabilizes the entailment relation, the item will be removed from the official evaluation split rather than silently kept with the English-derived label.

Finally, the data documentation will report the translation systems used, the validation protocol, annotator agreement on the pilot audit, adjudication counts, and translation-induced label-flip statistics. This makes the multilingual extension auditable and ensures that the Spanish and Basque tracks evaluate directional consistency rather than artifacts of translation.


> (R1.10) The "Everyone is hungry / Someone is hungry" example is fine in English under a generic reading, but generic/specific scope is exactly the kind of thing that translation destabilizes.
> With 200 expert hours budgeted for ES+EU combined over roughly 4k 2 items, I'd want the protocol pinned down and a translation-induced label-flip audit on a pilot sample.

**Answer R1.10** We agree with the reviewer that the "Everyone is hungry / Someone is hungry" example is deceptively simple. Quantification, generic/specific readings, plurality, definiteness, and scope are precisely the types of phenomena that can be destabilized by translation, especially across typologically different languages such as English, Spanish, and Basque.

We therefore will not assume that short phrase translations are automatically relation-preserving. Instead, the validation protocol described in R1.9 explicitly treats translation as a label-preservation problem. The translated target-language pair must preserve both the original NLI label and the deterministic reversed label. Quantifier- and scope-sensitive pairs will be treated as high-priority cases in the translation audit.

Concretely, the 100-source-pair pilot audit for each target language will include, where available, examples involving quantification, plurality, specificity/generality, determiners, and related scope-sensitive constructions. The audit will estimate how often such items preserve the English label after translation and how often they induce a label flip or unstable interpretation. We will report these counts together with raw agreement and Cohen's kappa.

If a translated item changes the entailment direction or becomes ambiguous in the target language, it will not be kept with the English-derived label. The item will either be corrected by choosing a better target-language formulation or removed from the official evaluation split. This applies equally to apparently simple examples such as `Everyone is hungry / Someone is hungry`.

Thus, the multilingual tracks will not rely on the assumption that translation is trivial. They will rely on explicit bilingual validation of directional label preservation.

> (R1.11) The cross-lingual evaluation story is also ambiguous. Are the three tracks independent, or is EN-EU transfer a first-class submission? 
> This affects how participants build systems and whether the macro-average is meaningful.

**Answer R1.11** We agree that the original proposal did not define the multilingual evaluation structure clearly enough. We have revised the task description to make the tracks explicit.

The shared task will include four tracks:

| Track | Description |
|---|---|
| English | Monolingual English premise-hypothesis pairs |
| Spanish | Monolingual Spanish premise-hypothesis pairs |
| Basque | Monolingual Basque premise-hypothesis pairs |
| Mixed multilingual | Premise and hypothesis may appear in different languages among EN/ES/EU |

The three monolingual tracks are independent evaluation tracks. Participants may submit to any subset of them, and results will be reported separately per language. Cross-lingual transfer is not implicitly mixed into those tracks.

The cross-lingual setting will be evaluated explicitly through the fourth mixed multilingual track. This track is intended for systems that can handle language combinations across the premise and hypothesis, for example EN→ES, ES→EU, or EU→EN. This makes cross-lingual transfer a first-class evaluation condition without making it mandatory for teams participating only in monolingual tracks.

For reporting, we will prioritize per-track leaderboards. We may additionally report macro-averages, but only over clearly specified groups: a monolingual macro-average over EN/ES/EU and, separately, an overall macro-average including the mixed multilingual track if appropriate. This avoids ambiguity about what a macro-average represents and lets participants interpret their results according to the setting they targeted.

> (R1.12) Minor: the access-regime tag (open-weight vs API) is a nice analytical addition but you don't say how it's verified or whether it's required.

**Answer R1.12** We agree that the access-regime tag must be specified more clearly. In the revised task documentation, this information will be mandatory run metadata, but it will not define separate official leaderboards. The purpose is post-task analysis: we want to compare whether performance patterns differ across open-weight models, API-based systems, private closed models, and hybrid systems.

Participants will be required to report the access regime of each official run in their submission metadata and system description paper. The planned categories are: `open-weight`, `commercial/API-based`, `private/closed`, and `hybrid/ensemble`. We will also ask teams to report architecture family, model size when known, prompting/fine-tuning strategy, multilingual capability, and external data use.

Verification will necessarily be partial, especially for commercial/API-based systems. We will therefore not claim full independent verification of all access-regime tags. Instead, the tag will be checked against the system description paper, model names, API provider information, released code/configuration files where available, and organizer sanity checks. Inconsistent or missing metadata may lead to a run being marked as `undisclosed` or excluded from access-regime-based analysis.

The official ranking will remain based on task metrics, not on access regime. The access-regime tag is an analytical variable intended to make the overview paper more informative and to avoid conflating very different system types in the post-task discussion.


> (R1.13) The reference to Apaolaza et al. (2026) as in-press limits what reviewers can check, and for a proposal that leans this heavily on the pilot, 
> a preprint link or appendix would have helped.

**Answer R1.13** We agree. The proposal relied too heavily on an in-press pilot paper without giving reviewers enough access to verify the empirical basis of the task. We should have included either a preprint link, an appendix, or a compact table of pilot results in the proposal itself.

This has now been corrected. The LREC paper is publicly available here: https://lrec.elra.info/lrec2026-main-423. We have also added representative baseline results directly to the response/task documentation, including one model from each architecture family and family-level mean and standard deviation values. This ensures that the feasibility and difficulty of the task can be assessed without relying only on an inaccessible citation.

In the revised proposal and repository documentation, the pilot study will be linked explicitly, and the task description will include the relevant metric definitions, baseline summaries, and the relation between the LREC hard/unseen evaluation setting and the planned SemEval setup.

To be clear about what I do like: the paired-instance construction with a deterministic reversal operator is a genuinely good design choice,
the formal definitions in the evaluation section are clean, HiTZ has a real track record on this kind of benchmark, 
and the Basque track is the sort of multilingual contribution the field claims to want and rarely gets.

If the conceptual framing is sharpened and a baseline table appears in the camera-ready, this is a solid shared task.



Questions for Authors
---------------------------------------------------------------------------

> Is CoCo-NLI measuring the reversal curse (parametric, weight-level) or directional inferential coherence (in-context, compositional)?

**Answer** DiCo-NLI measures directional consistency in fine-grained NLI. It is not a direct measurement of Berglund-style parametric, weight-level knowledge reversal. We have revised the framing and renamed the task accordingly: **DiCo-NLI: Directional Consistency in Fine-Grained Natural Language Inference**. Berglund et al. are used as conceptual and methodological motivation for reversal-based evaluation, not as a claim that the shared task directly measures the same parametric factual-reversal phenomenon.

> Would you expect performance on the two to correlate, and if so, can you show it?

**Answer** We do not assume that performance on DiCo-NLI and performance on parametric-reversal probes must correlate at the level of individual systems. A model can plausibly succeed in one setting and fail in the other. However, our LREC pilot includes a seen/easy setting that is structurally close to Berglund et al.: models are exposed to one directional phrase-pair construct and evaluated on the reversed manipulated counterpart. The SemEval task focuses on the harder unseen setting, where both original and reversed constructs are held out. The relationship between DiCo-NLI scores and parametric reversal probes is therefore an empirical follow-up question, not an assumption of the task design.

> Given that Rev is deterministic and gold labels are consistent under Rev, doesn't HardCoh collapse to paired accuracy?

**Answer** Yes, for the reversible subset, because the reversed gold label is deterministic, correctness in both directions entails consistency automatically. In that strict mathematical sense, `HardCons` is equivalent to paired accuracy over reversible pairs. We now describe it explicitly as **paired directional correctness** rather than as an independent consistency condition beyond correctness.

Its value is that the pairing is not arbitrary: it is defined by the label-reversal operator and by the original/reversed structure of the benchmark. `HardCons` changes the unit of evaluation from isolated ordered instances to reversible semantic units. A system receives credit only if it solves both directions of the same relation, and `NEGATIVE_OTHER` items are excluded because they do not have a deterministic reversed counterpart. Thus, although `HardCons` coincides numerically with paired accuracy in this setup, it operationalizes the task-specific notion of correctness under reversal and makes the comparison with `SoftCons` explicit: `SoftCons` asks whether predictions are directionally compatible regardless of truth, while `HardCons` asks whether the reversible relation is correctly solved in both directions.

> If so, what does it add over F1 or paired accuracy that SoftCoh doesn't already provide?

**Answer** `HardCons` is not intended to replace `SoftCons`. `SoftCons` is the main diagnostic metric for directional self-consistency independent of correctness. `HardCons` adds a stricter paired-correctness view that ordinary item-level `F-measure` does not provide. Two systems can have the same item-level score while differing in whether they solve complete reversible pairs. This distinction also appears empirically in our pilot system results: architecture families do not collapse to the same profile under the three metrics. For example, encoder-decoder models obtain lower average `F-measure` than encoder-only models on the positive combined track, but their average `SoftCons` is nearly identical, while decoder-only models drop much more sharply under `HardCons`. Thus the metrics expose different system behaviors rather than adding only redundant summaries. We therefore report the three metrics with distinct roles: `F-measure` for item-level label prediction, `SoftCons` for prediction-level directional consistency, and `HardCons` for paired correctness under reversal.

>  Can the camera-ready include a small baseline table at minimum one model per architecture family, reporting F1, SoftCoh, HardCoh?

**Answer** Yes. We have added representative baseline results from the LREC pilot and will include them in the revised task documentation. The LREC paper is now available at https://lrec.elra.info/lrec2026-main-423. We report one representative model per architecture family and family-level mean ± sample standard deviation for encoder-only, decoder-only, and encoder-decoder systems, using `F-measure`, `SoftCons`, and `HardCons`.

> How is contamination handled? Is the test set held-out from public PhrasIS, newly authored, perturbed, or canary-checked?

**Answer** The official test set will be built as a held-out grouped split, not as a random list of ordered instances. The grouping unit is the underlying source phrase pair: original direction, reversed direction, Spanish/Basque translations, and mixed-language variants derived from the same source pair are assigned to the same split. Thus, if a pair appears in the final test set, neither its original nor reversed form appears in released train/dev data. We also add `NEGATIVE_OTHER` noise items from non-reversible PhrasIS labels, hide final test labels during evaluation, delay gold release, document provenance in `data/README.md`, and audit exact/near-exact overlap with public examples and documentation.

> For ES/EU translation: which MT system, how many annotators per item, what's the adjudication protocol, and have you audited how often translation flips the gold label on a pilot sample?

**Answer** The multilingual protocol now treats translation as label-preserving transfer, not only phrase-level translation. Candidate Spanish and Basque translations will be produced with multiple MT/LLM systems and automatically compared for triage. Two bilingual annotators per target language will independently validate a 100-source-pair pilot audit, checking translation adequacy, preservation of the NLI label, and preservation of the reversed label. We will report raw agreement, Cohen's kappa, and translation-induced label-flip counts. For the full release, each item is reviewed by one bilingual annotator, with second-annotator adjudication for low-confidence, ambiguous, or relation-changing cases. Items that cannot preserve the intended relation are corrected or removed.

> Are cross-lingual submissions (e.g., train EN, evaluate EU) part of the evaluation, or is each track independent?

**Answer** The task now has four explicit tracks: English, Spanish, Basque, and Mixed multilingual. The first three are independent monolingual tracks. Cross-lingual transfer is handled explicitly in the Mixed multilingual track, where premise and hypothesis may appear in different languages among EN/ES/EU. Participants may submit to any subset of tracks. We will report per-track results and only compute macro-averages over clearly specified groups, such as monolingual EN/ES/EU or all four tracks.

> How will the open-weight vs API tag be verified, and is it mandatory?

**Answer** The access-regime tag will be mandatory run metadata, but it will not define separate official leaderboards. Participants must report whether each run is `open-weight`, `commercial/API-based`, `private/closed`, or `hybrid/ensemble`, together with architecture family, model size when known, prompting/fine-tuning strategy, multilingual capability, and external data use. Verification will be partial and based on system papers, model/API names, released code/configuration files where available, and organizer sanity checks. Missing or inconsistent metadata may be marked as `undisclosed` or excluded from access-regime-based analysis.




##                            REVIEWER #2


Detailed Comments
---------------------------------------------------------------------------
This is a well-motivated and carefully prepared proposal.
CoCo-NLI targets a recognised gap in the current evaluation landscape for Large Language Models (LLMs):
the inability of models to preserve logical inference when the direction of a relation is reversed, the so-called Reversal Curse.
The proposal reframes this within a Natural Language Inference (NLI) setting, rather than the narrow factual-retrieval setup in which it was originally demonstrated,
and extends it to a fine-grained, multilingual shared task.

The Reversal Curse has attracted substantial attention since Berglund et al. (2023) demonstrated that autoregressive LLMs trained on "A is B" systematically fail to infer "B is A."
Subsequent work has explored its theoretical underpinnings (NeurIPS 2024), proposed mitigation strategies such as reverse training Golovnevaet al. (2024), and examined its manifestation across question-answering tasks Lin et al., (2024).
Much of this work, however, has remained close to factual recall, question answering, or mechanistic analyses of reversal failures,
rather than operationalising the phenomenon within a fine-grained NLI shared-task setting.
To my knowledge, no prior shared task has combined the Reversal Curse framing with fine-grained directional NLI labels and a formal label-reversal coherence metric.

> (R2.1) The closest related work in the NLI consistency literature is BECEL (Jang et al., COLING 2022), a benchmark that evaluates language model consistency across four logical properties,
> including symmetric consistency for NLI.
> However, BECEL is not a shared task, and its symmetric NLI test is conceptually distinct from CoCo-NLI's design:
> BECEL evaluates symmetry only for restricted contradiction and selected neutral cases in standard 3-class NLI, whereas CoCo-NLI explicitly models directional entailment reversal using MacCartney's natural logic label-reversal operator,
> under which FORWARD-ENTAILMENT deterministically maps to BACKWARD-ENTAILMENT and EQUIVALENCE maps to itself. 
> This is a richer and more formally grounded formulation than the symmetry setting explored in BECEL.
> More recent work on NLI meta-inferential properties (Blanck et al., arXiv 2026) and transitive self-consistency (Wu and Last, EMNLP 2025) similarly remains in the research-paper paradigm 
> and does not directly address directional reversal coherence in a shared-task framework.
> The authors could strengthen the related work section further by citing these studies explicitly to sharpen the novelty argument.

**Answer R2.1** We thank the reviewer for these references. We agree that they are directly relevant and will include them in the revised related work section to sharpen the novelty claim.

In particular, we will discuss BECEL as the closest benchmark in the NLI consistency literature. BECEL evaluates several logical consistency properties, including symmetric consistency for NLI, but its NLI setting is based on standard 3-class labels and restricted symmetry cases. DiCo-NLI differs by explicitly modeling directional entailment reversal with fine-grained Natural Logic-style labels: `FORWARD_ENTAILMENT` maps deterministically to `BACKWARD_ENTAILMENT`, while `EQUIVALENCE` maps to itself. Thus, the task is not only checking invariance under symmetry but evaluating direction-sensitive label reversal.

We will also cite Blanck et al. on NLI meta-inferential properties and Wu and Last on transitive self-consistency. These works are relevant because they study consistency properties of NLI systems beyond isolated accuracy. However, they remain research-paper benchmarks rather than shared tasks and do not define a multilingual fine-grained directional-reversal evaluation with an official scorer and participant submissions.

The revised related work will therefore position DiCo-NLI as complementary to this literature: it brings consistency-based NLI evaluation into a shared-task setting, focuses on fine-grained directional entailment rather than only 3-way NLI labels, and evaluates both item-level correctness and paired directional consistency across monolingual and mixed multilingual tracks.

The evaluation design is one of the strongest aspects of the proposal. The three-metric framework is well conceived:

- Weighted F: provides a standard and comparable label-prediction metric that connects the task to the broader NLI literature.

- SoftCoh isolates directional self-consistency from correctness, allowing the community to distinguish models that are internally coherent (even if incorrect) from those that produce incompatible predictions under reversal.

- HardCoh is the most task-aligned primary metric, as it rewards predictions that are both correct and mutually consistent under the label-reversal operator.

The formal definitions of SoftCoh and HardCoh are clear and sufficiently specified for reproducible evaluation.
The decision to report both per-language scores and a multilingual macro-average is also sensible. 
Finally, the plan to run the task on CodaBench with an official scorer and format validator is fully consistent with current SemEval best practices.

Data:
======
The data plan is realistic, though the risk profile differs between the English and multilingual tracks. 
The English track appears relatively low-risk from a data and infrastructure perspective: the pilot data already exists, the metrics are defined,
and baseline systems have already been evaluated.
The Spanish and Basque tracks, however, remain partly prospective.
They require semi-automatic translation followed by bilingual expert adjudication, estimated at under 200 expert hours.
While the resulting dataset of approximately 10k ordered instances across three languages is appropriate in scale, preserving directional entailment relationships across languages,
particularly in Basque, whose morphosyntactic structure differs substantially from English and Spanish which introduces a moderate linguistic and organizational risk that the organizers will need to manage carefully.

The CC BY 4.0 license and commitment to long-term archiving on Zenodo are strong and fully aligned with current open-science expectations.
The ethics section is brief but adequate: the data contains no personally identifiable information, no medical decision-making content,
and no sensitive records.

The organizers also correctly identify benchmark overfitting to shallow cues as the primary methodological risk,
which is precisely the behaviour the task is designed to expose.

Organizers:
============
The team of four organizers is well-suited to the task. The roles are clearly defined and the team has the institutional infrastructure. 

Baselines
==========
The pilot study is strong.
Having already evaluated encoder-only models (DeBERTa-v3, ModernBERT, RoBERTa),
decoder-only models (OPT, Pythia, GPT-2), 
and encoder-decoder models (BART, Flan-T5) on the English reversal-aware benchmark,
the organizers are able to ground the task design in preliminary empirical findings rather than purely hypothetical motivations.

The pilot suggests several useful design lessons: 
(1) coherence should be treated as an explicit evaluation target rather than relying solely on aggregate label metrics; 
(2) the reversible entailment/equivalence subset appears to provide the clearest diagnostic signal;
(3) encoder-based models performed more strongly in the reported experiments, although they still show confusion between entailment and high semantic similarity;
and (4) performance appears sensitive to hyperparameter configuration. 

The commitment to release baseline systems and training scripts should help lower the barrier to participation and improve reproducibility.

Suggestions for Improvement
============================

While the proposal is strong overall, the following points deserve attention in the final task design:

> (R2.2) Translation quality and linguistic risk.
> The proposal states that the Spanish and Basque tracks will be created through semi-automatic translation followed by bilingual annotator adjudication.
> It would be important to ensure that the adjudication process verifies not only translation fidelity, but also whether the logical coherence of the reversed pairs is preserved in the target languages.
> This is particularly relevant for Basque, whose morphosyntactic structure differs substantially from English and Spanish and may affect directional entailment relationships.
> A brief inter-annotator agreement protocol for the translated tracks would strengthen confidence in the multilingual extension.

**Answer R2.2** We agree. This concern is closely related to the issues addressed in **R1.9** and **R1.10**. The multilingual extension must validate preservation of the directional NLI relation, not only translation adequacy. We have therefore revised the protocol so that Spanish and Basque annotation is treated as a label-preservation task.

Candidate translations will be generated using multiple MT/LLM systems, and automatic agreement signals such as token/lemma overlap will be used only for triage. The final decision will be made by bilingual annotators at the phrase-pair level. For each target language, two bilingual annotators will independently validate a pilot audit of 100 source pairs. The audit will check: (i) phrase-level translation adequacy, (ii) preservation of the expected NLI label, and (iii) preservation of the deterministic reversed label after swapping premise and hypothesis. We will report raw agreement, Cohen's kappa, adjudication counts, and translation-induced label-flip rates.

For the full release, each translated item will be reviewed by one bilingual annotator. Items flagged as low-confidence, ambiguous, or relation-changing will be reviewed or adjudicated by the second annotator. If a translated pair changes entailment direction or becomes unstable in the target language, it will be corrected if possible; otherwise, it will be removed from the official evaluation split.

This protocol directly addresses the linguistic risk noted by the reviewer, especially for Basque morphology and case marking, and ensures that the Spanish and Basque tracks evaluate directional consistency rather than artifacts of translation.

> (R2.3) Label balance across languages. The proposal reports that PhrasIS contains 1,946 English source pairs with reversible labels,
> yielding approximately 4k ordered instances for the English track.
> It would be helpful to report the distribution of EQUIVALENCE, FORWARD-ENTAILMENT, and BACKWARD-ENTAILMENT labels in the pilot data.
> If the distribution is highly skewed, the organizers may wish to consider stratified sampling or related balancing strategies to avoid inflating HardCoh scores through majority-class behaviour.

**Answer R2.3** We agree with the reviewer in that we include here and will include statistics of the data in the data folder of the current task

> (R2.4) Starter kit specifics. The proposal mentions that a starter kit will be provided, but does not specify which baseline models will be included.
>  Given the hyperparameter sensitivity noted in the pilot study, providing at least one reasonably tuned encoder-only and one decoder-only baseline with documented hyperparameter settings would likely lower the barrier to participation for teams with limited compute resources.

**Answer R2.4** We will prepare our LREC submission models as starter kits for participating in the task. At least we will include a model of each kind in the architecture types (enoder, decoder and encoder-decoder)

> (R2.5) Scope of the "compositional" claim. The task title includes the term "Compositional," but the proposal does not explicitly elaborate on the compositional dimension beyond the phrase-level granularity of PhrasIS.

**Answer R2.5** Explain the compositional term in the current task framework.

> (R2.6) It would be helpful to clarify whether the task is intended to target specific forms of compositional inference (e.g., adjective-noun or verb-argument compositions), or whether the term is being used more broadly in contrast to sentence-level NLI.

**Answer R2.6** 


# REFERENCES

Berglund, L., Tong, M., Kaufmann, M., Balesni, M., Stickland, A., Korbak, T., & Evans, O. (2024, May). The Reversal Curse: LLMs trained on "A is B" fail to learn "B is A". In International Conference on Learning Representations (Vol. 2024, pp. 18623-18642).

Golovneva, O., Allen-Zhu, Z., Weston, J., & Sukhbaatar, S. (2024). Reverse training to nurse the reversal curse. arXiv preprint arXiv:2403.13799.

Lin, Z., Fu, Z., Liu, K., Xie, L., Lin, B., Wang, W., ... & Ye, J. (2024). Delving into the Reversal Curse: How Far Can Large Language Models Generalize?. Advances in Neural Information Processing Systems, 37, 30686-30726.

Jang, M. E., Kwon, D., & Lukasiewicz, T. (2022, October). BECEL: Benchmark for consistency evaluation of language models. In Proceedings of the 29th international conference on computational linguistics (pp. 3680-3696).

Blanck, R., Noble, B., & Chatzikyriakidis, S. (2026). Reverse-engineering NLI: A study of the meta-inferential properties of Natural Language Inference. arXiv preprint arXiv:2601.05170.

Wu, W., & Last, M. (2025, November). Transitive self-consistency evaluation of NLI models without gold labels. In Proceedings of the 2025 Conference on Empirical Methods in Natural Language Processing (pp. 22637-22653).



Questions for Authors
---------------------------------------------------------------------------
> 1. Translation and adjudication protocol:
>    Will the bilingual annotators for the Spanish and Basque tracks be asked to verify the logical coherence of the reversed pairs in the target language, or only the fidelity of the translation?
>    If a translated phrase pair loses its directional entailment relationship due to morphosyntactic differences (especially in Basque), how will such cases be handled?

**Answer**

> 2. Starter kit baselines: Which specific models will be included in the starter kit?
>   Will you provide pre-trained checkpoints, or only training scripts?
>   Given the hyperparameter sensitivity reported in the pilot, will you provide recommended hyperparameter configurations for each architecture family?

**Answer**

> 3. Label distribution: What is the distribution of EQUIVALENCE, FORWARD-ENTAILMENT, and BACKWARD-ENTAILMENT labels in the pilot dataset?
>    Is it balanced, and if not, how will you address potential class imbalance in the official task data?

**Answer**

>4. Compositional dimension: Could you clarify what "compositional" means in the context of this task? Is the task specifically designed to probe compositional inference (e.g., adjective-noun compositions), or is the term used more broadly?
>

**Answer**
