# ML Training Data - Key Points

## Core Principles

- **Data > Models**: Training data is more critical than model architecture; poor data will sink ML operations regardless of model sophistication
- **Production Reality**: Data in production is neither finite nor stationary - it continuously evolves
- **Iterative Process**: Training data creation is iterative and evolves with the model lifecycle
- **Bias Awareness**: Data contains biases from collection, sampling, and labeling processes that ML models will perpetuate

## Sampling Methods

### Nonprobability Sampling
- **Convenience**: Select based on availability (easy but biased)
- **Snowball**: Future samples based on existing ones (e.g., Twitter account scraping)
- **Judgment**: Expert-driven selection
- **Quota**: Fixed quotas per category without randomization
- **Risk**: Creates selection biases; common in language modeling (Wikipedia, Reddit data)

### Probability-Based Sampling
- **Simple Random**: Equal probability for all samples (risk: rare categories may not appear)
- **Stratified**: Sample from predefined groups separately (ensures rare class representation)
- **Weighted**: Assign probabilities based on domain expertise (prioritize valuable subpopulations)
- **Reservoir**: For streaming data - maintains equal probability selection without knowing total stream size
- **Importance**: Sample from one distribution when only having access to another (used in reinforcement learning)

## Labeling Strategies

### Hand Labels
- **Challenges**: Expensive, privacy concerns, slow iteration, requires expertise
- **Label Multiplicity**: Multiple annotators create conflicting labels; need clear problem definitions
- **Data Lineage**: Track origin and quality of labels for debugging and bias detection

### Natural Labels
- **Definition**: Labels inferred from system feedback (clicks, purchases, user behavior)
- **Examples**: Recommender systems (clicks = positive), fraud detection, newsfeed ranking
- **Feedback Loop Length**: Time from prediction to feedback affects model iteration speed
- **Implicit vs Explicit**: Inferred from lack of action vs direct user feedback

### Handling Label Scarcity

#### Weak Supervision
- **Concept**: Use heuristics encoded as Labeling Functions (LFs) instead of hand labels
- **LF Types**: Keywords, regex, database lookups, other model outputs
- **Benefits**: Cost-effective, privacy-preserving, fast scaling, adaptive to changes
- **Process**: Create LFs on small data subset, apply programmatically to larger dataset

#### Semi-Supervision
- **Self-Training**: Use model predictions with high confidence as new labels
- **Similarity-Based**: Samples with similar characteristics share labels
- **Perturbation-Based**: Small changes shouldn't alter labels (add noise/perturbations)
- **Strategy**: Balance between evaluation set size and training set augmentation

#### Transfer Learning
- **Process**: Pretrain on abundant data task, fine-tune for target task
- **Base Tasks**: Language modeling (no labels needed), ImageNet for vision
- **Applications**: Zero-shot learning, few-shot scenarios
- **Trend**: Larger pretrained models generally perform better on downstream tasks

#### Active Learning
- **Concept**: Model queries which samples to label next for maximum learning benefit
- **Uncertainty Sampling**: Label examples model is least confident about
- **Query-by-Committee**: Multiple models vote on most valuable samples to label
- **Other Heuristics**: Highest gradient updates, maximum loss reduction

## Class Imbalance

### Problem Characteristics
- **Definition**: Substantial difference in sample numbers across classes
- **Examples**: Fraud detection (6.8¢/$100), medical diagnosis, resume screening
- **Causes**: Inherent to problem, sampling bias, or labeling errors

### Challenges
- **Insufficient Signal**: Few examples of minority class (few-shot learning)
- **Trivial Solutions**: Model exploits majority class heuristic (99% accuracy predicting "no cancer")
- **Asymmetric Costs**: Minority class errors often more expensive than majority class errors

### Solutions

#### Evaluation Metrics
- **Avoid**: Overall accuracy (dominated by majority class)
- **Use**: Precision, Recall, F1 for positive class focus
- **ROC Curves**: True positive rate vs false positive rate across thresholds
- **Precision-Recall Curves**: Better for heavy imbalance than ROC

#### Data-Level Methods (Resampling)
- **Undersampling**: Remove majority class instances (Tomek links for low-dim data)
- **Oversampling**: Add minority class instances (SMOTE for synthetic samples)
- **Advanced**: Two-phase learning, dynamic sampling during training
- **Warning**: Never evaluate on resampled data (causes overfitting to resampled distribution)

#### Algorithm-Level Methods
- **Cost-Sensitive Learning**: Manually define different misclassification costs
- **Class-Balanced Loss**: Weight classes inversely proportional to sample count
- **Focal Loss**: Higher weight for samples with lower prediction confidence
- **Ensembles**: Multiple models help with imbalance problems

## Data Augmentation

### Purpose
- **Traditional**: Increase training data for limited datasets
- **Modern**: Improve robustness even with abundant data, defend against adversarial attacks

### Techniques by Domain

#### Computer Vision
- **Label-Preserving**: Crop, flip, rotate, invert, erase (rotated dog still a dog)
- **Computational**: Generated on CPU while GPU trains (effectively free)

#### Natural Language Processing
- **Synonym Replacement**: Replace words with similar meaning (dictionary or embeddings)
- **Constraint**: Must preserve sentence meaning and sentiment

### Advanced Methods

#### Perturbation
- **Adversarial Augmentation**: Add noise to find model weak spots
- **DeepFool**: Minimum noise needed for misclassification
- **Goal**: Improve decision boundary robustness

#### Data Synthesis
- **NLP Templates**: Bootstrap conversational AI with structured patterns
- **Mixup**: Combine examples with interpolated labels (continuous between classes)
- **Benefits**: Improved generalization, reduced memorization, adversarial robustness

## Best Practices

1. **Start Simple**: Use convenience sampling for initial exploration, upgrade to probability-based for production
2. **Embrace Natural Labels**: Design systems to capture user feedback when possible
3. **Track Everything**: Implement data lineage for debugging and bias detection
4. **Choose Right Metrics**: Match evaluation metrics to business impact and class distribution
5. **Iterate Deliberately**: Treat data creation as iterative process aligned with model development
6. **Plan for Scale**: Consider computational and privacy constraints in labeling strategy