# ML System Design - Data & Processing Notes

## Data Sources

### User Input Data
- Data explicitly entered by users (text, images, videos, files)
- **Challenge**: Prone to malformatting and errors
- **Requirement**: Heavy-duty validation and processing

### System-Generated Data
- Logs, model predictions, system outputs
- **Advantages**: Less likely to be malformed, useful for debugging
- **Challenges**: Rapid volume growth, storage costs
- **Solution**: Use low-access storage for infrequently accessed logs

### User Behavior Data
- Click patterns, scrolling, time spent on pages
- System-generated but subject to privacy regulations

### Internal Databases
- Company assets: inventory, CRM, user data
- Used directly by ML models or system components

### Third-Party Data
- **First-party**: Company's own user data
- **Second-party**: Another company's customer data (purchased)
- **Third-party**: Public data from non-direct customers
- **Use cases**: Demographic insights, recommendation systems

## Data Formats

### Key Considerations
- Multimodal data storage
- Cost vs. access speed balance
- Cross-hardware model compatibility

### Common Formats

| Format | Type | Human-Readable | Use Cases |
|--------|------|---------------|-----------|
| JSON | Text | Yes | Everywhere |
| CSV | Text | Yes | General purpose |
| Parquet | Binary | No | Hadoop, Redshift |
| Avro | Binary | No | Hadoop |
| Protobuf | Binary | No | Google, TensorFlow |
| Pickle | Binary | No | Python, PyTorch |

### Row-Major vs Column-Major
- **CSV (Row-Major)**:
  - Faster row access
  - Better for frequent writes
  - Adding individual examples is faster

- **Parquet (Column-Major)**:
  - Faster column access
  - Better for analytical queries
  - Flexible column-based reads

### Text vs Binary
- **Text**: Human-readable, larger file size, precision loss risk
- **Binary**: Compact, faster processing, requires specific programs to read
- **AWS Recommendation**: Parquet is 2x faster and uses 6x less storage than text formats

## Data Models

### Relational Model
- **Structure**: Data organized in relations (tables)
- **Query Language**: SQL (declarative)
- **Advantages**: Normalized, consistent structure
- **Disadvantages**: Expensive joins, rigid schema

### NoSQL Models

#### Document Model
- **Structure**: JSON/XML documents with unique keys
- **Advantages**: Schema flexibility, faster single-document queries
- **Disadvantages**: Harder cross-document joins

#### Graph Model
- **Structure**: Nodes and edges representing relationships
- **Advantages**: Fast relationship-based queries
- **Use Case**: When relationships are priority

### Structured vs Unstructured Data

| Structured | Unstructured |
|------------|-------------|
| Predefined schema | No schema required |
| Easy to analyze | Fast data arrival |
| Schema-specific handling | Any source compatibility |
| Schema changes cause issues | Flexible storage |
| Data warehouses | Data lakes |

## Storage Engines & Processing

### OLTP vs OLAP
- **OLTP (Online Transaction Processing)**:
  - Real-time transactions
  - Low latency, high availability
  - Row-major format
  - Individual record processing

- **OLAP (Online Analytical Processing)**:
  - Analytical queries
  - Column aggregations
  - Complex analytical operations
  - Historical data analysis

### ETL vs ELT
- **ETL (Extract, Transform, Load)**:
  - Transform before loading
  - Structured approach
  - Quality control upfront

- **ELT (Extract, Load, Transform)**:
  - Load raw data first
  - Transform as needed
  - More flexible but less efficient searches

## Data Flow Models

### 1. Database-Mediated
- **Method**: Process A writes to DB, Process B reads
- **Limitations**: Shared access required, slower for low-latency needs

### 2. Service-Based (REST/RPC)
- **Method**: Direct network requests between services
- **REST**: Network-optimized, public APIs
- **RPC**: Function-like calls, internal services
- **Challenge**: Complex inter-service dependencies

### 3. Real-Time Transport (Event-Driven)
- **Method**: Services communicate through central broker
- **Advantages**: Decoupled services, asynchronous processing
- **Types**:
  - **Pub-Sub**: Topics with multiple subscribers (Kafka, Kinesis)
  - **Message Queue**: Targeted message delivery (RocketMQ, RabbitMQ)

## Batch vs Stream Processing

### Batch Processing
- **Data**: Historical data
- **Schedule**: Periodic jobs (daily, weekly)
- **Features**: Static/batch features (slowly changing)
- **Tools**: MapReduce, Spark
- **Use Case**: Driver ratings, historical aggregations

### Stream Processing
- **Data**: Real-time streaming data
- **Schedule**: Continuous or frequent (minutes)
- **Features**: Dynamic/streaming features (rapidly changing)
- **Tools**: Apache Flink, Kafka Streams
- **Use Case**: Available drivers, current demand

### Key Insights
- **Latency**: Stream processing enables lower latency
- **Efficiency**: Stream processing can be more efficient for stateful computations
- **Reality**: Most ML systems need both batch and streaming features
- **Architecture**: Stream processors can handle batch processing better than vice versa