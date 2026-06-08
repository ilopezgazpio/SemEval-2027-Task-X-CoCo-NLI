============================================================================ 
SemEval Task Proposals 2027 Reviews for Submission #20
============================================================================ 

Title: SemEval-2027 Task Proposal: Coherence-Aware Compositional Fine Grained Natural Language Inference (CoCo-NLI)
Authors: Jon Felix Apaolaza Larraya, Aitor Soroa, Rodrigo Agerri and Inigo Lopez-Gazpio

============================================================================
                            META-REVIEW
============================================================================ 

Comments: 

Overall, reviewers are positive about this proposal. The task appears to be novel and interesting; data and pilot experiments exist;
reviewers believe the proposal is well-structured and evaluation setup is strong; organizing team are strong too.
Both reviewers expressed some concerns over the conceptual framing of the task and some other technical details,
but these do not undermine the strengths of the submission.

============================================================================
                            REVIEWER #1
============================================================================

Detailed Comments
-------------------

This proposal takes the reversal curse and reframes it as a paired fine-grained NLI task on PhrasIS,
with a deterministic label-reversal operator and two coherence-based metrics (SoftCoh, HardCoh) on top of weighted F1, across EN/ES/EU.

The team is strong, the pilot exists, the infrastructure plan is credible, and the evaluation section is the cleanest part of the proposal.
I want to like this more than I do, and I'll try to explain why.

My main concern is conceptual. The reversal curse as introduced by Berglund et al. is a claim about parametric knowledge: 
the model has seen "A is B" in training and fails to produce "B is A" at inference. 
It's a failure of weight-level symmetry. What CoCo-NLI measures is something different: given both phrases in context, does the model assign mutually compatible directional labels when the pair is flipped?
That's in-context compositional consistency, and it's a perfectly worthwhile thing to measure, but it is not the same phenomenon. 

A model can be perfectly consistent on CoCo-NLI and still fail Berglund-style probes, or vice versa.
The proposal elides this ("the inability of LLMs... to preserve inference when relation direction is reversed") and uses the gravity of the RC literature to motivate a task that doesn't actually test the RC.
I'd push the authors to either (a) rename and remotivate the task as directional inferential coherence, which is what it actually is!
and which I think is defensible on its own terms, or (b) include a component that correlates CoCo-NLI performance with a parametric-reversal probe and argues empirically that the two are connected.

Second concern, on the metric. HardCoh is defined as coherent-under-Rev AND both predictions correct. But Rev is deterministic and the gold is consistent under Rev by construction.
So if both predictions are correct, coherence is automatic. HardCoh therefore reduces to paired accuracy "both items in the minimal pair labeled correctly" which is a stricter accuracy variant, not a distinct evaluation axis.

SoftCoh is the metric doing real work: it separates self-consistency from correctness and can catch a model that's reliably wrong in a coherent way.
I think the paper would be stronger if it led with the F1 vs SoftCoh gap as the diagnostic signature and presented HardCoh honestly as paired accuracy.
As written, HardCoh is oversold.

Third, and this is the one that most affects my score: there are no numbers. 
The proposal lists eight baseline architectures, cites a pilot, asserts there is clear room for improvement, and then asks reviewers to take all of that on faith while the pilot paper is in press and not accessible.
A compact three-row table one encoder, one decoder, one enc-dec, with F1/SoftCoh/HardCoh would have made the case concrete and would have let me verify the "room for improvement" claim myself.
Its absence is particularly awkward given that the whole argument for running the task is that current systems fail on it.

A few more things worth raising.
Contamination is not discussed. PhrasIS has been public since 2024 and any frontier model trained through 2025-26 has plausibly ingested it. 
For a task whose scientific payload is measuring an LLM reasoning failure, the proposal needs at least a paragraph on how the test split is constructed unreleased items, newly written, perturbed, canary-checked, something.
Otherwise the leaderboard will reflect memorization as much as reasoning.
The translation protocol is underspecified in a way that matters for this specific task.
"Semiautomatic translation by deep MT and bilingual annotators" doesn't say which system, how many annotators per item, or crucially, how the reversal operator is validated after translation.
Basque case marking and Spanish clitic/scope interactions can plausibly shift entailment direction on short phrase pairs.

The "Everyone is hungry / Someone is hungry" example is fine in English under a generic reading, but generic/specific scope is exactly the kind of thing that translation destabilizes.
With 200 expert hours budgeted for ES+EU combined over roughly 4k 2 items, I'd want the protocol pinned down and a translation-induced label-flip audit on a pilot sample.

The cross-lingual evaluation story is also ambiguous. Are the three tracks independent, or is EN-EU transfer a first-class submission? 
This affects how participants build systems and whether the macro-average is meaningful.

Minor: the access-regime tag (open-weight vs API) is a nice analytical addition but you don't say how it's verified or whether it's required.
The reference to Apaolaza et al. (2026) as in-press limits what reviewers can check, and for a proposal that leans this heavily on the pilot, 
a preprint link or appendix would have helped.

To be clear about what I do like: the paired-instance construction with a deterministic reversal operator is a genuinely good design choice,
the formal definitions in the evaluation section are clean, HiTZ has a real track record on this kind of benchmark, 
and the Basque track is the sort of multilingual contribution the field claims to want and rarely gets.

If the conceptual framing is sharpened and a baseline table appears in the camera-ready, this is a solid shared task.



Questions for Authors
---------------------------------------------------------------------------

- Is CoCo-NLI measuring the reversal curse (parametric, weight-level) or directional inferential coherence (in-context, compositional)? 

- Would you expect performance on the two to correlate, and if so, can you show it?

- Given that Rev is deterministic and gold labels are consistent under Rev, doesn't HardCoh collapse to paired accuracy?
  
- If so, what does it add over F1 or paired accuracy that SoftCoh doesn't already provide?

-  Can the camera-ready include a small baseline table at minimum one model per architecture family, reporting F1, SoftCoh, HardCoh?
  
- How is contamination handled? Is the test set held-out from public PhrasIS, newly authored, perturbed, or canary-checked?
  
- For ES/EU translation: which MT system, how many annotators per item, what's the adjudication protocol, and have you audited how often translation flips the gold label on a pilot sample?

- Are cross-lingual submissions (e.g., train EN, evaluate EU) part of the evaluation, or is each track independent?

- How will the open-weight vs API tag be verified, and is it mandatory?




============================================================================
                            REVIEWER #2
============================================================================

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

The closest related work in the NLI consistency literature is BECEL (Jang et al., COLING 2022), a benchmark that evaluates language model consistency across four logical properties,
including symmetric consistency for NLI.
However, BECEL is not a shared task, and its symmetric NLI test is conceptually distinct from CoCo-NLI's design:
BECEL evaluates symmetry only for restricted contradiction and selected neutral cases in standard 3-class NLI, whereas CoCo-NLI explicitly models directional entailment reversal using MacCartney's natural logic label-reversal operator,
under which FORWARD-ENTAILMENT deterministically maps to BACKWARD-ENTAILMENT and EQUIVALENCE maps to itself. 
This is a richer and more formally grounded formulation than the symmetry setting explored in BECEL.
More recent work on NLI meta-inferential properties (Blanck et al., arXiv 2026) and transitive self-consistency (Wu and Last, EMNLP 2025) similarly remains in the research-paper paradigm 
and does not directly address directional reversal coherence in a shared-task framework.
The authors could strengthen the related work section further by citing these studies explicitly to sharpen the novelty argument.

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

- Translation quality and linguistic risk.
  The proposal states that the Spanish and Basque tracks will be created through semi-automatic translation followed by bilingual annotator adjudication.
  It would be important to ensure that the adjudication process verifies not only translation fidelity, but also whether the logical coherence of the reversed pairs is preserved in the target languages.
  This is particularly relevant for Basque, whose morphosyntactic structure differs substantially from English and Spanish and may affect directional entailment relationships.
  A brief inter-annotator agreement protocol for the translated tracks would strengthen confidence in the multilingual extension.

- Label balance across languages. The proposal reports that PhrasIS contains 1,946 English source pairs with reversible labels,
  yielding approximately 4k ordered instances for the English track.
  It would be helpful to report the distribution of EQUIVALENCE, FORWARD-ENTAILMENT, and BACKWARD-ENTAILMENT labels in the pilot data.
  If the distribution is highly skewed, the organizers may wish to consider stratified sampling or related balancing strategies to avoid inflating HardCoh scores through majority-class behaviour.

- Starter kit specifics. The proposal mentions that a starter kit will be provided, but does not specify which baseline models will be included.
  Given the hyperparameter sensitivity noted in the pilot study, providing at least one reasonably tuned encoder-only and one decoder-only baseline with documented hyperparameter settings would likely lower the barrier to participation for teams with limited compute resources.

- Scope of the "compositional" claim. The task title includes the term "Compositional," but the proposal does not explicitly elaborate on the compositional dimension beyond the phrase-level granularity of PhrasIS.
- It would be helpful to clarify whether the task is intended to target specific forms of compositional inference (e.g., adjective-noun or verb-argument compositions), or whether the term is being used more broadly in contrast to sentence-level NLI.


# REFERENCES

Berglund, L., Tong, M., Kaufmann, M., Balesni, M., Stickland, A., Korbak, T., & Evans, O. (2024, May). The Reversal Curse: LLMs trained on "A is B" fail to learn "B is A". In International Conference on Learning Representations (Vol. 2024, pp. 18623-18642).

Golovneva, O., Allen-Zhu, Z., Weston, J., & Sukhbaatar, S. (2024). Reverse training to nurse the reversal curse. arXiv preprint arXiv:2403.13799.

Lin, Z., Fu, Z., Liu, K., Xie, L., Lin, B., Wang, W., ... & Ye, J. (2024). Delving into the Reversal Curse: How Far Can Large Language Models Generalize?. Advances in Neural Information Processing Systems, 37, 30686-30726.

Jang, M. E., Kwon, D., & Lukasiewicz, T. (2022, October). BECEL: Benchmark for consistency evaluation of language models. In Proceedings of the 29th international conference on computational linguistics (pp. 3680-3696).

Blanck, R., Noble, B., & Chatzikyriakidis, S. (2026). Reverse-engineering NLI: A study of the meta-inferential properties of Natural Language Inference. arXiv preprint arXiv:2601.05170.

Wu, W., & Last, M. (2025, November). Transitive self-consistency evaluation of NLI models without gold labels. In Proceedings of the 2025 Conference on Empirical Methods in Natural Language Processing (pp. 22637-22653).



Questions for Authors
---------------------------------------------------------------------------
1. Translation and adjudication protocol:
   Will the bilingual annotators for the Spanish and Basque tracks be asked to verify the logical coherence of the reversed pairs in the target language, or only the fidelity of the translation?
   If a translated phrase pair loses its directional entailment relationship due to morphosyntactic differences (especially in Basque), how will such cases be handled?

2. Starter kit baselines: Which specific models will be included in the starter kit?
   Will you provide pre-trained checkpoints, or only training scripts?
   Given the hyperparameter sensitivity reported in the pilot, will you provide recommended hyperparameter configurations for each architecture family?

3. Label distribution: What is the distribution of EQUIVALENCE, FORWARD-ENTAILMENT, and BACKWARD-ENTAILMENT labels in the pilot dataset?
   Is it balanced, and if not, how will you address potential class imbalance in the official task data?

5. Compositional dimension: Could you clarify what "compositional" means in the context of this task? Is the task specifically designed to probe compositional inference (e.g., adjective-noun compositions), or is the term used more broadly?
