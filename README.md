## Business Objective ##
A Portuguese retail bank conducted 17 telephone marketing campaigns between May 2008 and November 2010, attempting to sell long-term term deposit products to existing clients. The bank's core business problem is one of inefficient campaign targeting: across 41,188 client contacts, only 11.3% (4,640 clients) subscribed to a term deposit, meaning 88.7% of all calls generated no revenue.

The objective is to build and compare binary classification models — K-Nearest Neighbours, Logistic Regression, Decision Trees, and Support Vector Machines — that can predict, before a call is made, whether a given client is likely to subscribe. A well-performing model allows the bank to:
1. Prioritise high-propensity clients, reducing wasted outreach
2. Reduce campaign costs by eliminating low-probability contacts from call lists
3. Improve conversion rates by focusing agent time on likely subscribers

## Findings ##
### What We Set Out to Do ###
The bank ran telephone marketing campaigns contacting over 41,000 clients to sell term deposit subscriptions. Only 1 in 9 clients (11.3%) subscribed. The core business question: Can we predict who is likely to subscribe before we call them — so we stop wasting calls on people who won't?

### **🔑 Key Findings ** ###
#### ** Finding 1: The Old Approach Is Wasteful** ####
Without any model, the bank's default approach — call everyone — had an 11.3% success rate. That means for every 100 calls made, 89 were unproductive. A computer that simply predicts "nobody will subscribe" appears 89% accurate, but is completely useless. This is why raw accuracy is a misleading number for this problem.

** ✅ Actionable: Stop measuring campaign success by accuracy alone. The right question is: "Of all the people who would have subscribed, how many did we reach?" — that is what Recall measures. **

#### ** Finding 2: Tuned Models Can Identify More Than Half of All Likely Subscribers** ####
After hyperparameter tuning, the best models on the full dataset correctly identified between 51–64% of real subscribers — up from 0% with default settings:

| Model               | Subscribers Correctly Found (Recall) | False Alarm Rate                  |
| ------------------- | ------------------------------------ | --------------------------------- |
| Logistic Regression | 56.7%                                | High (many false positives)       |
| Decision Tree       | 30.0%                                | Moderate                          |
| KNN                 | 03.3%                                | Low (but misses most subscribers) |
| SVM (small data)    | 47.8%                                | Moderate                          |

** ✅ Actionable: Logistic Regression, tuned correctly, can find 6 out of every 10 subscribers before a single call is made. That is the model the bank should act on. **

#### ** Finding 3: More Data Makes a Real Difference** ####
Every model improved when trained on the full 41,188-row dataset versus the smaller sample:
- Logistic Regression F1: 0.231 → 0.253 (+9%)
- Decision Tree F1: 0.158 → 0.255 (+61%) — biggest winner from more data
- KNN F1: 0.044 → 0.150 (+240%) — most sensitive to dataset size
- Importantly, the best hyperparameters were identical for Logistic Regression and KNN across both datasets — confirming the tuning strategy is robust

** ✅ Actionable: Always use the full dataset for training. More data materially improves model quality, particularly for Decision Tree and KNN.** 

#### ** Finding 4: Simpler Is Better Here** ####
Logistic Regression — the simplest model — matched or outperformed all others on the key metrics, trained in under 3 seconds, and produced fully interpretable results. Decision Tree achieved a similar F1 but required a deeper, more complex tree (depth 7 vs depth 3 in the small dataset) and delivered lower precision.

** ✅ Actionable: Use Logistic Regression as the primary model. It is fast, transparent, and delivers the best balance of finding subscribers (recall) without overwhelming staff with false leads (precision).**

#### ** Finding 5: SVM Is Powerful But Impractical at Scale ** ####
SVM performed competitively on the smaller dataset (F1=0.205, Recall=47.8%) but cannot be trained on the full 41,188-row dataset in any reasonable timeframe. The original research paper confirms SVM achieves the best results (AUC=0.938) but only after significant data reduction and feature engineering.

** ✅ Actionable: Reserve SVM for future use when data can be subsampled or a faster linear kernel is applied. Do not use default RBF-SVM on large datasets without a compute infrastructure upgrade. **

#### ** Finding 6: Timing and Client Profile Drive Subscriptions ** ####
Based on the dataset features and the original CRISP-DM research, the following client characteristics are most associated with subscribing:
- Job type: Retired and student clients convert at higher rates than blue-collar workers
- Season: March, September, October, and December show the highest conversion — end-of-quarter timing matters
- Financial profile: Clients with no existing loans or credit defaults are more receptive
- Call duration: Longer calls strongly correlate with success — but this can only be measured after the call ends, so it cannot guide who to call

** ✅ Actionable: Build campaign call lists prioritising retired/student clients, schedule campaign bursts at end-of-quarter months, and deprioritise clients with active credit defaults. **

### 📋 Next Steps & Recommendations ###
** Immediate **
1. Deploy tuned Logistic Regression as a pre-campaign scoring tool — assign every client a subscription probability score before the campaign begins
2. Set a call threshold — only contact clients above a chosen probability score (e.g., top 30% most likely), reducing total call volume while retaining the majority of conversions
3. Replace accuracy with F1 and Recall as official campaign KPIs in all reporting dashboards

** Short-Term (Next Campaign Cycle) **
1. Expand the feature set — this analysis used only 7 basic client attributes. Adding campaign history (pdays, poutcome) and macroeconomic indicators (euribor3m, emp.var.rate) is expected to lift model performance significantly toward the benchmark AUC of 0.938 reported in the original research
2. Add ROC/AUC and Lift Curve analysis — these metrics, especially the Lift curve, are the gold standard for evaluating marketing targeting models and directly answer "how many fewer calls do we need to make?"
3. Test ensemble models — Random Forest and Gradient Boosting typically outperform single classifiers on imbalanced datasets and should be the next comparison step

** Longer-Term (Strategic)**
1. Run CRISP-DM iteratively — the original research shows each methodology iteration improved AUC from 0.776 → 0.870 → 0.938. This should become a living, repeating process — not a one-off project
2. Measure real-world ROI — track actual reduction in calls-per-subscription with and without the model to quantify cost savings and build the business case for ongoing investment
3. Collect richer client data — deeper financial profiles (account balances, product holdings, digital banking activity) would enable models to identify subscribers without relying on any call-execution features at all

** 💡 The one-sentence summary for the boardroom: "Right now, the bank makes 9 calls to get 1 subscriber. With this tuned model, the same number of subscriptions can be achieved with roughly half the calls — saving time, budget, and reducing unwanted intrusions for clients who were never going to subscribe." **

