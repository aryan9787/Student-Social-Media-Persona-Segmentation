# Student Social Media Persona Segmentation

Clustering ~15,000 U.S. high-school students' self-written social media profiles into interpretable, interest-based personas using word-frequency signals across 36 topics (sports, fashion, religion, music, and a few at-risk language terms).

## Why This Matters

Schools and youth-marketing teams can't read every profile individually, but they can act on a small number of interpretable segments — for example, targeting a "fashion & pop culture" group differently than a "sports" group, or flagging a small distress-language cluster for a counselor to review in aggregate, not as individual predictions.

## Approach

Data cleaning -> interest-share feature engineering -> K-Means clustering (k chosen via elbow + silhouette) -> PCA visualization -> cluster profiling & persona naming.

## Dataset

Young People Social Media ("Teens") survey dataset — 15,000 rows x 40 columns, including graduation year, gender, age, friend count, and 36 interest-word counts.

## Data Cleaning

Two issues needed fixing before the data was usable:

1. **Mangled age values** : Excel had auto-converted some decimal ages into date strings (e.g. 16.6 became "16. Jun"). About 280 rows had this problem. Instead of discarding these rows or truncating to just "16," the day/month pair was parsed back into the original decimal value.
2. **Implausible ages** : some recorded ages were as low as 4 or over 100. These were clipped to missing before imputation, since leaving them in would have skewed the median used to fill other missing values. Remaining missing ages were imputed using the median within the same graduation year (more accurate than a single global median, since age correlates strongly with graduation year). Missing gender values were kept as an explicit "U" category rather than imputed, to avoid fabricating ~9% of the dataset's gender labels.

## Feature Engineering

Raw interest-word counts were converted into interest *shares* (proportion of a student's total interest mentions belonging to each topic) rather than raw counts, so that students who wrote more overall don't dominate the clustering. These shares were combined with standardized age, friend count, and one-hot encoded gender.

## Choosing k

Rather than picking a round number, k was chosen by scanning a range (2–10) and comparing both the elbow curve and silhouette score, then selecting the k that maximizes silhouette score, since the elbow curve alone is often ambiguous.

## Persona Profiling

For each cluster, the average interest share is compared against the overall population average ("affinity"). Clusters are described by which interests are most *over-represented* relative to the rest of the population, rather than by raw popularity, which would just surface globally common interests like "sports" everywhere.

## Results

| Cluster | Size | % Female | Top Affinities | Persona |
|---|---|---|---|---|
| 0 | ~1.6% | 75% | death, die, drunk, jesus | Distress-language / at-risk signal — a small group whose profiles skew toward mortality and substance-related language. In a school-safety context, this segment would be handed to a counselor for aggregate awareness, never used for individual-level flagging. |
| 1 | ~17% | <1% | baseball, football, sports, basketball, bible | Male sports & faith community |
| 2 | ~8% | 0% | jesus, cheerleading, hollister, mall, dance | Faith-and-fashion, small mixed group |
| 3 | ~73% | 100% | softball, shopping, cheerleading, blonde, cute | Mainstream female fashion & sport — the largest, most "typical" segment |

**Note on cluster balance:** Cluster 3 is large relative to the others. This is a realistic outcome for a mostly-homogeneous survey population, not a modeling flaw.

## Tech Stack

Python, pandas, NumPy, scikit-learn (KMeans, PCA, StandardScaler, silhouette_score), matplotlib, seaborn

## Outputs

- `df_with_clusters.csv` — original data with assigned cluster labels
- `cluster_demographics.csv` — per-cluster size, average age, average friend count, % female
- `cluster_interest_affinity.csv` — per-cluster interest affinity scores relative to overall average

## How to Run

1. Install dependencies: `pip install pandas numpy matplotlib seaborn scikit-learn`
2. Place `03_Clustering_Marketing.csv` in the project directory
3. Run the notebook `Student_Persona_Segmentation.ipynb` top to bottom
