# Human or AI? · Group 36

The team retained Abishek’s **stack_nbsvm_stylo.csv**, the highest public AUROC in the team’s submission history (**0.92847**). Its original training implementation and submitted CSV are unavailable in this branch. Its validation and private AUROC are unreported.

Abishek’s available comparison notebook evaluates a five-model ensemble. Four logistic classifiers use Naive Bayes weights on different text representations. A fifth classifier uses gradient boosting on 67 writing-style features. In the saved experiment, the four text models each receive weight 1 and the style model receives weight 3. Their weighted average ranks passages by the likelihood of AI authorship.

| Contributor | Model | Held-out AUROC | Experiments |
| --- | --- | ---: | --- |
| Abishek Chozhan (ac3388) | Five-model rank average | 0.989949 | [Rank-average notebook](abishek_ensemble/rank_average.ipynb) · [Model comparison](abishek_ensemble/results/model_comparison.csv) |
| Liyan Yang (ly462) | Random Forest on 21 writing-style features | 0.989913 | [Experimental report](alma_rf/results/EXPERIMENT_REPORT.md) · [Run instructions](alma_rf/README.md) |
| Jianing Li (jl4837) | Word and character TF-IDF, chunked MiniLM and linear SVM | 0.974103 | [Experiments and run instructions](jianing_svm/README.md) · [Learning curve](jianing_svm/results/learning_curve/learning_curve.png) |

All three approaches use the same stratified partition of 8,000 training and 2,000 validation passages, with seed 42. The public scores in [the submission history](team_results/public_scores.csv) are keyed by filename. Jianing’s binary TF-IDF SVM scored 0.90977 on the public leaderboard and 0.977117 on validation.

## Reproduce Abishek’s rank-average comparison

From the directory containing this README, create an environment and open the notebook:

~~~sh
cd abishek_ensemble
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab rank_average.ipynb
~~~

Place the competition files **train.csv**, **test.csv** and **sample_submission.csv** in the **data/** directory under **abishek_ensemble/**. Select the Python kernel from this environment and run all cells. The default rank-average setting reproduces the method recorded in Abi’s saved notebook outputs. The final refit uses all 10,000 labeled passages and writes [rank_average_final.csv](abishek_ensemble/submissions/rank_average_final.csv) in sample-submission ID order. Its scores are averaged normalized ranks in (0, 1]. The probability-average switch writes a separate CSV.

## Experimental evidence

- **Abishek.** The text models use word n-grams, within-word character n-grams, grammatical skeletons and cross-word character n-grams. The style model uses gradient boosting. Five-fold predictions on training data selected the style weight. Specialist vocabularies use the 8,000 training texts before internal CV; the supervised NB ratios and regressions are refitted within each fold. The held-out validation texts are excluded from training. [Weight search](abishek_ensemble/results/style_weight_search.csv) · [Leave-one-model-out results](abishek_ensemble/results/ablation.csv) · [Validation predictions](abishek_ensemble/results/validation_scores.csv) · [ROC curves](abishek_ensemble/results/validation_roc.png)
- **Alma.** Representation and Random Forest settings were compared by training-only cross-validation. The report includes baseline comparisons, length subgroups and example errors. [RF results](alma_rf/results/)
- **Jianing.** Experiments compare term frequencies, frozen MiniLM and feature fusion. The learning-curve experiment refits vocabularies and IDF on each balanced subset. Validation AUROC increased from 0.890852 at 500 passages to 0.974103 at 8,000. [Training-size results](jianing_svm/results/learning_curve/learning_curve.csv)

[Experiment history](EXPERIMENT_HISTORY.md) · [Data split](jianing_svm/data/splits/seed42_stratified_8000_2000.csv)

## Unlabeled-data experiment

[Experiment and reproduction instructions](abi_unlabeled/README.md). Adding 3,000 unlabeled passages to vocabulary fitting in the reconstructed stack changed fixed-validation AUROC from **0.988825 to 0.988813**. [Supervised reconstruction and separate SVM control](group36_evidence/) · [Paired validation comparison](abi_unlabeled/results/comparison.json).

The team reports **0.92117 public AUROC** for the unlabeled-vocabulary submission. The retained historical submission has public AUROC 0.92847.
