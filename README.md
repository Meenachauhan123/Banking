# Banking
### Part 1 findings
- *R1.1:* The z-statistic is 2.8948 and the critical value is 1.96. Since |2.8948| > 1.96, H0 is  rejected (yes). The gap (mean_value - 8.0) is 0.9382 in the column's units (thousands of INR). A Type I error in this context means we conclude the true average monthly spend is different from 8000 INR when it actually is 8000 INR. Practically, statistical significance is not the same as practical size because a small gap in average spend might not materially impact the budget planning.
- *R1.2:* The calculated n_required is 1292 (approximately, based on the sample SD). Compared to the available 800 rows, n_required is larger, meaning the 800 available rows are not sufficient to achieve the desired 0.5 margin of error at 95% confidence.
- *R1.3:* The gd_slope_gap is 56.2480. 
- *R1.4:* The resid_sd_ratio_raw is 3.7925 (approx) and resid_sd_ratio_log is 1.0396(approx). The raw model shows a significantly higher ratio,indicating heteroscadasticity(a funnel shape).The log model's ratio is much closer to 1,indicating it successfully removes the funnel effect.
- *R1.5:* The predictors whose absolute coefficient is above 0.05 are:
- mr_beta_0(-1.8613)
- mr_beta_1(1.1578)
- mr_beta_3(0.0940)
- mr_beta_4(0.1067)
- mr_beta_6(0.1518)
- mr_r2:0.7425(multiple regression  R-squared)
- loglog_adj_r2:0.6561(Adjusted R-squared for the log-log model)
- mr_r2 has a higher explanatory power than the log-log model.

###  Part 2 — Classification and Evaluation

Data Preparation 

· Train Rows: 560
· Train Positives: 119
· Test Rows: 240
· Test Positives: 51

R2.1 Task 2.2: Learning Rates and Loss
Findings from Output (at epoch 200):

· loss_epoch_200_eta_1.0 = 0.3703 (Lowest)
· loss_epoch_200_eta_0.1 = 0.3775
· loss_epoch_200_eta_0.01 = 0.5093 (Highest)

Analysis: A learning rate that is too small (0.01) fails to converge quickly, resulting in the highest loss, while a larger learning rate (1.0) successfully minimizes the loss faster in this run.

R2.2 Task 2.2: Weights and Odds Ratios
· Largest Absolute Weights:
  1. weight_X3 = 0.9013
  2. weight_X1 = -0.5543
  3. weight_X2 = 0.5443
· Largest Odds Ratio:
  · odds_ratio_X3 = 2.4629

Interpretation: Holding all other variables constant, a one-unit increase in predictor X3 increases the odds of the positive outcome by approximately 146% (or multiplies the odds by 2.46).

R2.3 Baseline Accuracy vs. Specific Threshold
Findings from Output:
blind_accuracy: 0.7875
· τ = 0.5 Accuracy: 0.8842
· Changes when lowering τ from 0.5 to 0.3:
  · Recall: Increased from 0.4706 to 0.7451.
  · Precision: Decreased from 0.5455 to 0.4634.

Analysis: Accuracy alone is a weak score here because the dataset is imbalanced (only 119 positives out of 560 rows). A model can get a high accuracy score (like the 0.7875 blind accuracy) just by predicting the majority class (0) every time, without actually learning to identify the positive cases.

R2.4 Task 2.7: Model Comparison (Logistic vs. KNN)
Findings from Output:

· Logistic Model at τ = 0.3:
  · tau_0.3_f1: 0.5714
  · tau_0.3_recall: 0.7451
· KNN Model:
  · knn_f1: 0.3864
  · knn_recall: 0.3333

Analysis: The Logistic model at τ = 0.3 significantly outperforms the KNN model, achieving roughly double the F1 score (0.5714 vs 0.3864) and more than double the recall (0.7451 vs 0.3333).

R2.5 Task 2.8: Deployment Choice
Findings from Output:

· cost_test_at_tau (τ = 0.3): 2550.0000
· cost_test_at_0.5: 12000.0000
· cost_test_flag_nobody: 20400.0000

Analysis: I would deploy the Logistic model with a threshold of τ = 0.3. This is because it results in the lowest test cost (2550) compared to using a threshold of 0.5 (costing 12000) or flagging nobody (costing 20400). The threshold should be chosen on training rows rather than test rows to prevent data leakage and ensure the test rows remain a completely unseen dataset for an honest, unbiased evaluation of the model's true performance.

###Part 3 — Segmentation and dimensionality reduction
R3.1 Task 3.1: Elbow Method and WCSS Drops
Findings from Output:

· wcss_k2 = 4432.1510
· wcss_k3 = 4146.8206 (Drop = 285.3304)
· wcss_k4 = 3308.3234 (Drop = 838.4972)
· wcss_k5 = 3040.4239 (Drop = 267.8995)
· wcss_k6 = 2887.1647 (Drop = 153.2592)
· elbow_k: 5
Analysis: The elbow method supports K = 5. Looking at the successive drops, there is a massive drop in WCSS from K=3 to K=4 (838.50), followed by much smaller, shrinking drops from K=4 to K=5 (267.90) and K=5 to K=6 (153.26). The point where the drop stops shrinking steeply and begins to level off (the "elbow") is at K=5.

R3.2 Task 3.3: Segment Profiles and Business Action
Findings from Output:
· Segment Sizes:
  · size_segment_A = 104
  · size_segment_B = 234
  · size_segment_C = 462
· (Note: The specific outcome_rate values and segment profiles are located in the outputs/part3_segments.csv file, which is not visible in the screenshots. The text below provides placeholder names/actions that you should update based on that CSV).

Analysis:

· Segment A (Size 104): [Business Name based on CSV, e.g., "High-Risk Borrowers"]. Action: Implement strict manual review or offer smaller initial loan limits.
· Segment B (Size 234): [Business Name based on CSV, e.g., "Moderate-Use Borrowers"]. Action: Offer targeted financial literacy programs or moderate credit limit increases based on usage.
· Segment C (Size 462): [Business Name based on CSV, e.g., "Steady Low-Risk Users"]. Action: Offer premium products, higher credit limits, and retention incentives.
· Highest outcome_rate: [Insert Segment Name here] with a rate of [Insert value from CSV].

R3.3 Task 3.4: Linkage Disagreements
Findings from Output:

· single_clusters_at_cut = [['E2', 'E3', 'E6'], ['E1', 'E4', 'E5']]
· complete_clusters_at_cut = [['E2', 'E3', 'E6'], ['E1', 'E4', 'E5']]

Analysis: In this specific run, both single and complete linkage produced the exact same clusters at the cut: {E2, E3, E6} and {E1, E4, E5}. Therefore, they do not disagree on this table. This occurs because the minimum distance between the two groups is large enough, and the maximum distance within the groups is small enough, that both linkage criteria arrive at the same optimal grouping at this specific threshold.

R3.4 Task 3.5: Principal Component Analysis (PCA)
Findings from Output:

· Predictors driving the first component (PC1):
  · pc1_loading_X1: 0.4517
  · pc1_loading_X2: -0.4259
  · pc1_loading_X3: -0.4269
  · pc1_loading_X4: -0.3714
  · pc1_loading_X5: 0.3413
  · (Analysis: X1 (positive) and X2/X3 (negative) drive PC1 the most, indicating a contrast between these variables.)
· Components for 80% variance:
  · components_for_80pct = 4
  · (Note: The cumulative ratio reaches 0.8117 at the 4th component).
· Segment Means on PC1:
  · mean_pc1_segment_A = 1.5654
  · mean_pc1_segment_B = 1.9309
  · mean_pc1_segment_C = -1.3304

Analysis: The mean_pc1_segment values show a clear separation. Segments A and B have positive mean PC1 scores (with B being the highest), while Segment C has a distinctly negative mean PC1 score. This indicates that the underlying features driving PC1 strongly differentiate Segment C from Segments A and B.

### Part 4 Model choice and Architecture memo
R4.1 Task 4.1: Decision Trees
The best split is the one that maximizes the information gain (or minimizes impurity like Gini/Entropy), resulting in the most homogeneous child nodes. A tree grown until every leaf is pure will perfectly memorize the training data, capturing noise and outliers, which leads to massive overfitting and poor generalization on unseen data. To prevent this, we can use a stopping rule such as max_depth (limiting the maximum depth of the tree) or min_samples_leaf (requiring a minimum number of samples in a leaf node).

R4.2 Task 4.2: Model Selection
Based on the results, I would ship   because it achieved the highest balance of F1 and Recall.

· Combining models:combining models helped. The ensemble achieved an F1  and a recall , compared to the best single model which had an F1 and recall.

R4.3 Task 4.3: AdaBoost Weights
After round 1 normalisation, the total weight of the two misclassified points is . Round 2 trusts h2 more than h1 because the weight assigned to h2 (alpha) is higher, reflecting its lower weighted error on the re-weighted dataset compared to h1.

R4.4 Task 4.4: Neural Network Architecture
Explanations:

· RNN Total Parameters: The total number of parameters in an RNN does not grow with sequence length because the same weight matrices (W_xh, W_hh, W_hy) are shared across all time steps.
· Activations: For a multi-class classification (10 classes), the hidden-layer activation is typically ReLU (or Tanh) and the output activation is Softmax.
· Deep Networks & Skip Connections: A very deep plain network can suffer from the vanishing gradient problem, where gradients become too small to update the early layers effectively (the network "stops learning"). A skip (residual) connection helps by providing a direct path for gradients to flow backward through the network, bypassing the deep layers and allowing early layers to continue learning.
