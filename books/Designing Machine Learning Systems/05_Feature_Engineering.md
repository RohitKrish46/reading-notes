
# Feature Engineering: Comprehensive Reading Notes

## Introduction
Feature engineering is a cornerstone of machine learning, often delivering greater performance improvements than advanced algorithmic techniques like hyperparameter tuning. A well-crafted set of features can significantly enhance even state-of-the-art model architectures, while inadequate features can undermine model effectiveness, regardless of the model's complexity.

## Learned Features vs. Engineered Features
- **Deep Learning's Promise**: Deep learning, often referred to as feature learning, automates feature extraction, reducing the need for manual feature engineering. For instance, in natural language processing, raw text can be tokenized into words, converted into a vocabulary, and represented as one-hot vectors, allowing models to learn relevant features. Similarly, in computer vision, raw images can be directly input into deep learning models, bypassing manual feature extraction.
- **Feature Engineering's Role**: Despite automation, feature engineering remains vital, particularly for domain-specific tasks. For example, building a fraud detection system requires banking expertise to identify features like unusual transaction patterns. In complex applications like TikTok's video recommendation system, models may leverage millions of features, necessitating careful engineering to ensure relevance and usability.

## Common Feature Engineering Operations
Feature engineering involves selecting and transforming data into formats suitable for machine learning models. Below are key operations to streamline this process:

1. **Handling Missing Values**
   - **Types of Missing Data**:
     - **Missing Not at Random (MNAR)**: Missingness relates to the value itself (e.g., high-income individuals not disclosing income).
     - **Missing at Random (MAR)**: Missingness depends on another variable (e.g., a specific gender not reporting age).
     - **Missing Completely at Random (MCAR)**: No pattern in missingness (rare, e.g., random omission of job titles).
   - **Strategies**:
     - **Deletion**:
       - **Column Deletion**: Remove variables with excessive missing values (e.g., >50% missing marital status). Risks losing critical information, like marital status correlating with homeownership.
       - **Row Deletion**: Remove samples with missing values, suitable for MCAR with minimal missingness (<0.1%). Avoid if missingness is MNAR, as it may remove valuable signals (e.g., missing income indicating higher earnings).
     - **Imputation**: Fill missing values with defaults (e.g., empty string for job), mean, median, or mode (e.g., median July temperature for missing temperature). Avoid imputing with possible values (e.g., age=0) to prevent misinterpretation. Multiple imputation strategies may be combined, but all risk introducing bias or noise, potentially causing data leakage if not handled carefully.

2. **Scaling**
   - **Purpose**: Ensures features have comparable ranges, improving model performance, particularly for algorithms like logistic regression or gradient-boosted trees. Unscaled features can lead to erratic predictions.
   - **Methods**:
     - **Min-Max Scaling**: Transform features to a [0,1] range, ideal for arbitrary distributions.
     - **Standardization**: Adjust features to zero mean and unit variance, suitable for normally distributed data.
     - **Log Transformation**: Mitigates skewness in feature distributions.
   - **Considerations**: Use train split statistics for scaling to prevent data leakage. Recalculate statistics periodically to adapt to data shifts, ensuring robust inference.

3. **Discretization**
   - **Definition**: Converts continuous features into discrete categories (e.g., grouping incomes into low, middle, high). Also known as binning or quantization.
   - **Benefits**: Simplifies model learning by reducing value complexity, especially with limited data.
   - **Challenges**: Introduces discontinuities at boundaries (e.g., $34,999 vs. $35,000). Boundary selection requires histograms, quantiles, or domain expertise.
   - **Applications**: Extends to discrete features like age, grouping into ranges for easier modeling.

4. **Encoding Categorical Features**
   - **Dynamic Categories**: In production, categories evolve (e.g., new brands on Amazon). Static encoding fails when new categories appear.
   - **Hashing Trick**: Uses a hash function to map categories to a fixed-size encoding space, handling new categories without retraining. Large hash spaces minimize collisions, where different categories map to the same index.
   - **Example**: Encoding Amazon brands with a top 99% approach and an "UNKNOWN" category for the rest, adjusting dynamically to new brands. Hashing ensures random collisions, avoiding bias toward unpopular brands.
   - **Industry Adoption**: Widely used in tools like scikit-learn, TensorFlow, and Vowpal Wabbit for its effectiveness in continual learning.

5. **Feature Crossing**
   - **Purpose**: Combines features to capture nonlinear relationships (e.g., marital status and number of children for house purchase prediction).
   - **Relevance**: Essential for linear models (e.g., logistic regression) that struggle with nonlinearity. Less critical but still beneficial for neural networks.
   - **Risks**: Increases feature space exponentially (e.g., 100x100 values yield 10,000 combinations), risking overfitting and requiring more data.
   - **Applications**: Used in models like DeepFM for recommendation systems and click-through rate prediction.

6. **Positional Embeddings**
   - **Context**: Crucial for models like transformers, where word order must be explicitly encoded (e.g., "dog bites child" vs. "child bites dog").
   - **Types**:
     - **Learned Embeddings**: Position embeddings are trained alongside model weights (e.g., BERT’s approach).
     - **Fixed Embeddings**: Use predefined sine/cosine functions (Fourier features) for discrete or continuous positions (e.g., 3D object coordinates).
   - **Applications**: Extends to computer vision and NLP, improving performance in tasks requiring positional context.

## Data Leakage
- **Definition**: Occurs when label information leaks into features, unavailable during inference, leading to inflated performance and production failures.
- **Examples**:
  - A COVID-19 model predicting severity based on patient position (lying down) rather than medical indicators.
  - Models using hospital-specific font styles in scans as predictors of disease severity.
- **Common Causes**:
  1. **Random Splitting of Time-Correlated Data**: Random splits leak future information (e.g., stock prices or music trends). Split by time instead.
  2. **Scaling Before Splitting**: Using test split statistics for scaling leaks global data properties. Scale after splitting, using train split statistics.
  3. **Imputing with Test Split Statistics**: Filling missing values with test split mean/median leaks information. Use train split statistics.
  4. **Data Duplication**: Duplicates across splits (e.g., CIFAR-10 test set images in training) inflate performance. Remove duplicates before splitting.
  5. **Group Leakage**: Correlated samples (e.g., patient scans a week apart) split across train/test. Understand data generation to avoid.
  6. **Data Generation Leakage**: Equipment differences (e.g., CT scan machines) leak unintended signals. Normalize data and consult domain experts.
- **Detection Strategies**:
  - Monitor feature-label correlations for anomalies.
  - Perform ablation studies to assess feature impact.
  - Scrutinize new features causing significant performance boosts.
  - Restrict test split usage to final evaluation to prevent leakage.

## Engineering Good Features
- **Feature Growth**: Models in production often accumulate features, improving performance but increasing complexity.
- **Risks of Excessive Features**:
  - Higher data leakage potential.
  - Overfitting to training data.
  - Increased memory and latency for inference.
  - Technical debt from maintaining irrelevant features.
- **Feature Importance**:
  - **Measurement**: Use XGBoost’s built-in functions or SHAP for model-agnostic analysis. SHAP also explains individual predictions.
  - **Utility**: Identifies critical features and enhances model interpretability.
- **Feature Generalization**:
  - **Coverage**: Features with high coverage (fewer missing values) generalize better. Low-coverage features may still be useful if missingness correlates with labels.
  - **Distribution**: Ensure feature value overlap between train and test splits to maintain performance.
  - **Trade-offs**: General features (e.g., IS_RUSH_HOUR) are robust but less specific than detailed features (e.g., HOUR_OF_THE_DAY). Balance both for optimal results.
- **Management**: Remove outdated features to reduce complexity, storing definitions for reuse. Regularization (e.g., L1) can mitigate useless features, but proactive removal accelerates learning.

