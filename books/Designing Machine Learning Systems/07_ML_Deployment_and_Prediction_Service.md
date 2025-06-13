# ML Deployment and Prediction Service

## Core Concepts
- **Deploy**: Making your model accessible outside development environments (staging/production)
- **Export/Serialization**: Converting models to formats usable by other applications
- **Inference**: The process of generating predictions

## Common Deployment Myths
1. **Single Model Myth**: Companies actually deploy many models (each feature may need its own model)
2. **Static Performance Myth**: ML models degrade over time due to data distribution shifts
3. **Infrequent Updates Myth**: Models need frequent updates as performance decays
4. **Scale Irrelevance Myth**: Most ML engineers need to handle scale (hundreds of queries/second, millions of users)

## Prediction Modes

### Batch vs Online Prediction
| Aspect | Batch (Asynchronous) | Online (Synchronous) |
|--------|---------------------|---------------------|
| **Frequency** | Periodical (e.g., every 4 hours) | As requests arrive |
| **Use Case** | Recommender systems | Fraud detection |
| **Optimized For** | High throughput | Low latency |

**Practical Examples:**
- **Batch**: Netflix movie recommendations generated every 4 hours
- **Online**: DoorDash delivery time estimation using real-time data
- **Hybrid**: DoorDash uses batch for restaurant recommendations, online for food items

### Feature Types
- **Batch Features**: From historical data (restaurant's mean prep time)
- **Streaming Features**: From real-time data (current order volume, available drivers)

## Model Compression Techniques

### 1. Low-Rank Factorization
- Replace high-dimensional tensors with lower-dimensional ones
- **Example**: SqueezeNet achieves AlexNet accuracy with 50x fewer parameters

### 2. Knowledge Distillation
- Train smaller "student" model to mimic larger "teacher" model
- **Example**: DistilBERT is 40% smaller, 60% faster, retains 97% of BERT's capabilities

### 3. Pruning
- Remove uncritical parameters or set them to 0
- Can reduce nonzero parameters by 90%+ without accuracy loss

### 4. Quantization
- Use fewer bits to represent parameters (32-bit → 16-bit → 8-bit)
- **Benefits**: Reduces memory (100M parameters: 400MB → 200MB with 16-bit)
- **Trade-off**: Faster computation but potential rounding errors

## Cloud vs Edge Deployment

### Cloud Advantages
- Easy deployment via managed services (AWS, GCP)
- No hardware constraints

### Edge Advantages
- **Cost Control**: Reduces cloud compute bills
- **Connectivity**: Works without stable internet
- **Latency**: Eliminates network transfer delays
- **Privacy**: Data stays on device (GDPR compliance)

**Practical Examples:**
- **Edge-only**: Face unlock, voice assistants
- **Critical online**: Autonomous vehicles, fraud detection, high-frequency trading

## Model Optimization

### Local Optimization Techniques
- **Vectorization**: Process multiple elements simultaneously
- **Parallelization**: Divide work into independent chunks
- **Loop Tiling**: Optimize memory access patterns for hardware
- **Operator Fusion**: Combine multiple operations to reduce memory access

### Global Optimization
- Optimize entire computation graph end-to-end
- ML-based optimization to explore execution paths efficiently

## Browser Deployment
- **WebAssembly (WASM)**: Run models in any browser-supported device
- **Trade-off**: Universal compatibility but slower than native execution
- **Use Case**: Cross-platform deployment without hardware-specific compilation

## Key Takeaways
1. **Hybrid Approaches**: Most applications use both batch and online prediction
2. **Performance Decay**: All models degrade over time and need updates
3. **Compression is Essential**: Edge deployment requires smaller, faster models
4. **Hardware Matters**: Optimization techniques are hardware-dependent
5. **Scale Planning**: Consider latency, throughput, and cost from the start