
## Model Development and Training

### Evaluating ML Models
- **Model Selection**: Choose based on problem type (e.g., text classification, anomaly detection). Consider tradeoffs like accuracy, training cost, and interpretability.
- **Neural Networks vs. Classical Algorithms**:
  - Neural networks: Ideal for unstructured data (e.g., images, text), but resource-intensive.
  - Classical algorithms: Better for structured data, interpretable, efficient with limited data.
  - Hybrid approaches: Combine BERT embeddings with logistic regression for balance.
- **Six Principles for Model Selection**:
  1. Avoid overhyped state-of-the-art models; simpler models (e.g., Random Forest) may suffice.
  2. Use simple baselines for quick validation and debugging.
  3. Ensure fair comparisons by equalizing tuning efforts.
  4. Plan for scalability (data growth, hardware upgrades).
  5. Balance tradeoffs (e.g., precision vs. recall, performance vs. explainability).
  6. Validate assumptions (e.g., IID data, feature independence).

### Ensembles
- **Definition**: Combine multiple base learners for improved predictions (e.g., majority vote for classification, average for regression).
- **Types**:
  - **Bagging**: Reduces variance, improves stability (e.g., Random Forest). Best for unstable methods like neural networks.
  - **Boosting**: Iteratively trains weak learners, weighting misclassified samples (e.g., XGBoost, LightGBM).
  - **Stacking**: Combines base learners’ outputs via a meta-learner (e.g., logistic regression).
- **Considerations**: Ensembles boost performance but increase deployment complexity. Use diverse models to reduce correlation.

### Experiment Tracking and Versioning
- **Tracking**: Monitor loss curves, performance metrics (e.g., accuracy, F1), system metrics (e.g., CPU/GPU usage), and hyperparameters to debug issues like overfitting or dead neurons.
- **Versioning**: Log code and data to enable reproducibility. Data versioning is challenging due to size; tools like DVC track changes via checksums.
- **Best Practices**: Use third-party tools for dashboards, track critical metrics, and avoid nondeterminism from frameworks/hardware.

### Distributed Training
- **Data Parallelism**: Split data across machines, accumulate gradients. Synchronous SGD avoids stragglers; asynchronous SGD handles sparse updates but risks staleness.
- **Model Parallelism**: Distribute model components across machines. Pipeline parallelism enables concurrent execution.
- **Challenges**: Balance batch sizes, scale learning rates carefully, and optimize resource allocation.

### AutoML
- **Hyperparameter Tuning**: Automate tuning of parameters (e.g., learning rate, batch size) for optimal performance. Use validation splits, not test splits, to avoid overfitting.

### Debugging ML Models
- **Common Issues**:
  - Violated model assumptions (e.g., non-linear data with linear models).
  - Implementation bugs (e.g., incorrect gradient updates).
  - Poor hyperparameter choices or feature selection.
  - Data issues (e.g., noisy labels, incorrect preprocessing).

## Model Offline Evaluation
- **Baselines**:
  - Random: Predict using label distribution.
  - Simple Heuristic: Non-ML rules (e.g., reverse chronological ranking).
  - Zero Rule: Predict most common class.
  - Human: Compare to human performance.
  - Existing Solutions: Benchmark against current systems.
- **Evaluation Methods**:
  - **Perturbation Tests**: Add noise to test robustness.
  - **Invariance Tests**: Ensure outputs are unaffected by irrelevant changes (e.g., race, gender).
  - **Directional Expectation Tests**: Verify logical input-output relationships (e.g., larger lot size increases house price).
  - **Model Calibration**: Ensure predicted probabilities match actual outcomes.
  - **Confidence Measurement**: Assess per-sample reliability.
  - **Slice-Based Evaluation**: Analyze performance on data subsets to detect biases or uneven performance (e.g., Simpson’s paradox).

## Development Phases
1. **Non-ML Heuristics**: Start with simple rules.
2. **Simple ML Models**: Use interpretable models (e.g., logistic regression, k-NN).
3. **Optimize Simple Models**: Tune hyperparameters, engineer features, use ensembles.
4. **Complex Models**: Experiment with advanced models if needed.

