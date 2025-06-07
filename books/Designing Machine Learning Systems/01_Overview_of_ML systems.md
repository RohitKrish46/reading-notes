# Overview of ML systems

## 1. Machine Learning Use Cases

ML exploded in mid-2010s, enabling previously impossible services. Enterprise applications serve both internal (cost reduction, insights, automation) and external (customer experience) use cases.

### Critical Use Cases

**Fraud Detection**
- Oldest ML enterprise application.
- Systems learn from historical fraud patterns to predict future fraudulent transactions.

**Price Optimization** 
- Estimate optimal pricing to maximize margin/revenue/growth
- Best for high-volume, fluctuating demand scenarios (ads, flights, rideshare)

**Customer Acquisition & Retention**
- Average app user acquisition cost(Lyft): $86.61 (2019).
- Even higher for enterprise customers.
- Small reduction in acquisition costs = large profit increase.
- ML enables better targeting, timing, and customer identification.

**Churn Prediction**
- Predict when customers (or employees) will leave.
- Allows proactive retention efforts.
- Critical given high acquisition costs.

**Automated Ticket Classification**
- Reduces response time by routing tickets to correct departments.
- Improves customer satisfaction.
- Works for both customer support and internal IT.

**Brand Monitoring/Sentiment Analysis**
- Track explicit and implicit brand mentions.
- Monitor sentiment trends.
- Early warning system for reputation issues.

## 2. Understanding Machine Learning Systems

ML in production ≠ ML in research. Academic background creates steep learning curve for real-world deployment.

### Machine Learning in Research Versus in Production

**1. Stakeholders & Requirements**
- Research: Single objective (model performance on benchmarks)
- Production: Multiple conflicting requirements from different stakeholders
- Small improvements can mean millions in revenue (0.2% CTR boost) or be completely unnoticeable
- Simple models often beat complex ones if performance gain doesn't justify complexity

**2. Computational Priorities**
- Research: Fast training, high throughput
- Production: Fast inference, low latency
- **Latency vs Throughput paradox**: Batching can increase both simultaneously, but online batching adds wait time
- Google stat: 50%+ mobile users leave if page takes >3 seconds
- Latency is a distribution, not a single number—use percentiles (p50), not averages

**3. Data Reality**
- Research: Clean, static, well-formatted datasets
- Production: Messy, noisy, constantly shifting, biased data with sparse/incorrect labels
- Research uses historical data; production deals with real-time user-generated data

**4. Fairness**
- Research: Often deferred as "we'll fix it in production"
- Production: Too late—biases are already encoded
- **Critical insight**: "ML algorithms don't predict the future, but encode the past"
- Biases scale with deployment—one algorithm can discriminate against millions instantly

**5. Interpretability**
- Research: Not prioritized (single performance metric)
- Production: Required, not optional
- Hinton's dilemma: 90% cure rate black box AI vs 80% cure rate human surgeon
- Needed for trust, bias detection, and debugging

### Machine Learning Systems Versus Traditional Software

**Fundamental Architecture Difference**
- Traditional SWE: Code and data separated, modularity is king
- ML: Code + data + artifacts fused together, inseparable trinity

**Data-Driven Reality**
- Last decade trend: Best data wins, not best algorithms
- Companies focus on data improvement over algorithm optimization
- Data changes quickly → need faster development/deployment cycles

**Testing & Versioning**
- Traditional: Only code needs testing/versioning
- ML: Must test and version data too (this is the hard part)

**Model Size Problem**
- 2022: Models routinely have hundreds of millions to billions of parameters
- Requires gigabytes of RAM just to load
- Production deployment, especially on edge devices, is massive engineering challenge
- Speed bottleneck: Autocompletion is useless if suggestion takes longer than typing

**Monitoring & Debugging**
- Complex models + lack of visibility = hard to diagnose problems
- Difficult to get fast alerts when things go wrong

**BERT Case Study**
- 2018: "Too big, too complex, too slow to be practical" (340M parameters, 1.35GB)
- 2020: Used in almost every English Google search
- Engineering challenges being solved at breakneck pace

## Summary:

- While most people are familiar with ML in consumer-facing applications, the majority of ML use cases are for enterprise.
- Even though ML can solve many problems very well, it can’t solve all the problems and it’s certainly not appropriate for all the problems.
- However, for problems that ML can’t solve, it’s possible that ML can be one part of the solution.
- This chapter also highlighted the differences between ML in research and ML in production. The differences include the stakeholder involvement, computational priority, the properties of data used, the gravity of fairness issues, and the requirements for interpretability.
- ML systems are complex, consisting of many different components. Data scientists and ML engineers working with ML systems in production will likely find that focusing only on the ML algorithms part is far from enough.
- It’s important to know about other aspects of the system, including the data stack, deployment, monitoring, maintenance, infrastructure, etc.