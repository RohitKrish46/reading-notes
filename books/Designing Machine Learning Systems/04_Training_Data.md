Here's the transformed content in the requested style:

# ML System Design - Data & Processing Notes

## Data Challenges in ML Development

Despite data's critical role in ML model development, most curricula disproportionately focus on modeling: the "fun" part perceived by practitioners. While building cutting-edge models is engaging, data wrangling with malformatted, memory-exceeding datasets remains a frustrating reality.

### Core Data Characteristics

- **Inherent Complexity**: Data is messy, unpredictable, and potentially treacherous: improper handling can sink entire ML operations
- **Dynamic Nature**: Production data is neither finite nor stationary
- **Iterative Evolution**: Training data evolves alongside models throughout project lifecycles
- **Bias Risks**: Data contains multiple bias types from collection, sampling, labeling, and historical human biases that models may perpetuate

## Sampling Methodologies

Sampling is fundamental across ML workflows, occurring at multiple stages:
- Creating training sets from real-world data
- Generating train/validation/test splits
- Monitoring system event sampling

**Strategic Importance**:
- Prevents sampling biases
- Optimizes sampling efficiency

### Nonprobability Sampling Techniques

| Method | Description | Use Case Example | Limitations |
|--------|-------------|------------------|-------------|
| **Convenience Sampling** | Selection based on availability | Common default approach | High selection bias |
| **Snowball Sampling** | Chain-based selection from existing samples | Scraping social networks without API access | Amplifies existing biases |
| **Judgment Sampling** | Expert-curated sample selection | Domain-specific data collection | Subjectivity risks |
| **Quota Sampling** | Fixed-quota selection without randomization | Demographic-balanced surveys | Non-representative distributions |

**Industry Reality**: Despite bias risks, convenience sampling dominates many applications (e.g., language models trained on easily available Wikipedia/Reddit data)

### Probability-Based Sampling Methods

**Simple Random Sampling**
- **Mechanism**: Equal selection probability for all population members
- **Advantage**: Implementation simplicity
- **Drawback**: Rare category underrepresentation

**Stratified Sampling**
- **Mechanism**: Population division into strata with separate sampling
- **Advantage**: Guarantees minority class representation
- **Limitation**: Impractical for overlapping/multilabel cases

**Weighted Sampling**
- **Mechanism**: Sample selection probability weighted by domain importance
- **Use Case**: Prioritizing recent data for time-sensitive models

**Reservoir Sampling Algorithm** (for streaming data)
1. Initialize reservoir with first k elements
2. For nth element:
   - Generate random i (1 ≤ i ≤ n)
   - If i ≤ k: replace ith reservoir element
3. **Mathematical Guarantee**: All elements have equal k/n selection probability

**Importance Sampling**
- **Core Concept**: Sample from accessible distribution Q to estimate target distribution P
- **ML Application**: Policy-based reinforcement learning updates

## Labeling Strategies

Most production ML systems rely on supervised learning, making label quality and quantity critical performance factors.

### Hand Labeling Challenges

- **Privacy Risks**: Requires human data exposure
- **Time Cost**: Creates model iteration bottlenecks
- **Label Multiplicity**: Common annotator disagreements require:
  - Clear problem definitions
  - Data lineage tracking for bias detection

### Natural Label Utilization

**Behavioral Label Types**:
- **Explicit**: Direct user feedback (ratings, downvotes)
- **Implicit**: Inferred from lack of response (unclicked recommendations)

**Feedback Loop Characteristics**:
| Metric | Short Loop | Long Loop |
|--------|------------|-----------|
| **Speed** | Minutes/hours | Weeks/months |
| **Signal Strength** | Weaker (clicks) | Stronger (purchases) |
| **Use Case** | Real-time monitoring | Quarterly reporting |

### Label Acquisition Techniques

**Weak Supervision**
- **Mechanism**: Heuristic-based labeling functions (LFs)
- **LF Types**: Keywords, regex patterns, database lookups, model outputs
- **Advantages**:
  - Cost-effective expertise reuse
  - Privacy preservation
  - Rapid scaling (1K → 1M samples)

**Semi-Supervision**
- **Self-Training**: Model-generated high-confidence labels expand training set
- **Similarity-Based**: Shared-label assumption for clustered data
- **Perturbation**: Label-preserving data modifications

**Transfer Learning**
- **Workflow**: Base model pre-training → downstream task adaptation
- **Efficiency**: Reduces labeling requirements
- **Trend**: Larger base models generally yield better downstream performance

**Active Learning**
- **Selection Criteria**:
  - Prediction uncertainty
  - Model committee disagreement
  - Potential loss reduction
- **Data Regimes**: Synthetic, stationary pool, or real-time streams

## Class Imbalance Solutions

Class imbalance: substantial inter-class sample count differences: affects both classification and regression tasks.

### Imbalance Challenges

1. **Signal Scarcity**: Insufficient minority class examples
2. **Heuristic Traps**: Models exploit simple majority-class rules
3. **Asymmetric Costs**: Minority class errors often more critical

### Mitigation Approaches

**Evaluation Metrics**
- **Avoid**: Accuracy (misleading with imbalance)
- **Prefer**: F1, Precision-Recall curves, AUC-ROC

**Data-Level Methods**
| Technique | Process | Advantage | Limitation |
|-----------|---------|-----------|------------|
| **Random Oversampling** | Minority class duplication | Simple implementation | Overfitting risk |
| **SMOTE** | Synthetic minority sample generation | Improved decision boundaries | High-dimension ineffectiveness |
| **Random Undersampling** | Majority class reduction | Computational efficiency | Information loss |
| **Tomek Links** | Borderline majority sample removal | Cleaner decision boundaries | Low-dimension only |

**Algorithm-Level Methods**
- **Cost-Sensitive Learning**: Loss function incorporates misclassification costs
- **Class-Balanced Loss**: Inverse class frequency weighting
- **Focal Loss**: Emphasis on hard-to-classify examples

## Data Augmentation Techniques

Augmentation increases effective training data volume and improves model robustness.

### Computer Vision Approaches

| Type | Methods | Characteristics |
|------|---------|-----------------|
| **Label-Preserving** | Rotation, flipping, cropping | Computationally inexpensive |
| **Adversarial** | Noise injection, DeepFool attacks | Improves noise resistance |
| **Synthetic** | Mixup interpolation | Enhances generalization |

### NLP Approaches

- **Synonym Replacement**: Word swaps with embedding-space neighbors
- **Template Generation**: Structured sentence patterns
- **Perturbation**: Minor syntactic changes preserving semantics

### Multimodal Considerations

- **Cross-Modal Augmentation**: Image-text pair transformations
- **Modality-Specific**: Requires format-aware techniques
- **Performance Impact**: Often yields >2x effective dataset size
