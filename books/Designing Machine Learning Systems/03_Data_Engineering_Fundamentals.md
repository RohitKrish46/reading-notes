# ML System Design - Data & Processing Notes

## Data Sources in ML Systems

Machine learning systems work with data from multiple sources, each with distinct characteristics requiring different processing approaches. Understanding these sources helps optimize data usage and system efficiency.

### User Input Data
User input data represents information explicitly provided by users, including text, images, videos, and uploaded files. The fundamental challenge with user input is its unreliability—if users can input incorrect data, they will. This reality makes user input data particularly prone to malformatting issues.

**Key Challenges:**
- Text may be too long, too short, or contain unexpected characters
- Numerical fields often receive text input accidentally
- File uploads frequently use wrong formats or corrupted files
- **Solution**: Implement robust validation and heavy-duty processing pipelines

### System-Generated Data
This category encompasses data automatically created by system components, including various types of logs, model predictions, and system outputs. Unlike user input, system-generated data is significantly more reliable and consistent.

**Characteristics:**
- Much less likely to be malformed compared to user input
- Essential for debugging ML systems (common practice: log everything possible)
- **Volume Challenge**: Log data grows extremely rapidly, creating storage cost issues
- **Storage Strategy**: Use low-access, cost-effective storage for infrequently accessed logs

### User Behavior Data
Systems automatically capture user interaction patterns such as clicks, suggestion selections, scrolling behavior, zoom actions, pop-up responses, and time spent on specific pages. While technically system-generated, this data represents user actions and may be subject to privacy regulations.

### Internal Enterprise Databases
Companies maintain various databases for managing business assets including inventory systems, customer relationship management (CRM), user databases, and operational data. This structured data can be consumed directly by ML models or integrated into broader ML system components.

### Third-Party Data Ecosystem
The data marketplace offers different types of external data sources:

- **First-party data**: Your company's own collected user/customer data
- **Second-party data**: Another company's customer data made available (typically purchased)
- **Third-party data**: Public data collected by companies on non-direct customers

**Modern Data Collection Reality:**
The proliferation of internet and smartphones revolutionized data collection capabilities. Previously, unique advertiser IDs (Apple's IDFA, Android's AAID) enabled comprehensive activity aggregation across devices. Today, data from apps, websites, and check-in services is collected, anonymized, and processed to create detailed activity histories.

**Commercial Applications:**
Third-party data enables highly granular demographic targeting (e.g., "men, age 25-34, working in tech, living in Bay Area") and behavioral insights ("people who like brand A also like brand B"). This processed, cleaned data particularly benefits recommendation systems for generating relevant user suggestions.

## Data Formats and Serialization

Data serialization converts data structures into storable/transmittable formats. Format selection depends on human readability requirements, access patterns, and file size considerations (text vs. binary).

### Critical Storage Considerations
- **Multimodal Data**: How to store samples containing both images and text
- **Cost vs. Speed**: Balancing storage costs with access speed requirements  
- **Cross-Platform Compatibility**: Ensuring models load correctly across different hardware

### Format Comparison Matrix

| Format | Type | Human-Readable | Primary Use Cases | Key Characteristics |
|--------|------|---------------|-------------------|-------------------|
| **JSON** | Text | Yes | Universal application | Language-independent, powerful key-value paradigm |
| **CSV** | Text | Yes | General purpose | Row-major format, precision loss risk |
| **Parquet** | Binary | No | Big data (Hadoop, Redshift) | Column-major, highly efficient |
| **Avro** | Binary | No | Hadoop ecosystem | Schema evolution support |
| **Protobuf** | Binary | No | Google services, TensorFlow | Compact, fast serialization |
| **Pickle** | Binary | No | Python ecosystem | Python-specific, PyTorch default |

### JSON: The Universal Standard
JSON's ubiquity makes it indispensable despite significant drawbacks. While language-independent and human-readable with a flexible key-value structure, JSON creates substantial pain points:
- **Schema Rigidity**: Retrospective schema changes are extremely difficult
- **Storage Inefficiency**: Text format requires significantly more space than binary alternatives

### Row-Major vs Column-Major Paradigms

The distinction between CSV (row-major) and Parquet (column-major) represents fundamentally different data access philosophies.

**Row-Major Format (CSV):**
- Consecutive row elements stored adjacently in memory
- **Performance Advantage**: Faster row access due to sequential data processing efficiency
- **Write Optimization**: Significantly faster for adding individual records
- **Use Case**: Systems with frequent data writes and row-based operations

**Column-Major Format (Parquet):**
- Consecutive column elements stored adjacently in memory
- **Performance Advantage**: Faster column access and aggregation operations
- **Read Optimization**: Flexible column-based reads, especially for wide tables with thousands of features
- **Use Case**: Analytical workloads requiring column aggregations

**Performance Trade-off**: Row-major optimizes writes, column-major optimizes analytical reads.

### pandas and NumPy Architectural Insight
A critical but often overlooked detail: pandas DataFrame is built around columnar format (inspired by R's Data Frame), while NumPy arrays default to row-major. Developers transitioning from NumPy to pandas often experience performance issues by treating DataFrames like ndarrays with row-based access patterns.

### Text vs Binary Format Trade-offs

**Text Formats (CSV, JSON):**
- **Advantages**: Human-readable, editable in standard text editors
- **Disadvantages**: Larger file sizes, potential precision loss (floating-point rounding)
- **Example Issue**: 0.12345678901232323 might serialize as "0.12345678901"

**Binary Formats (Parquet, Protobuf):**
- **Advantages**: Compact storage, faster processing, precise data representation
- **Disadvantages**: Require specific programs for interpretation, not human-readable
- **AWS Performance Data**: Parquet is 2x faster to process and uses 6x less storage than equivalent text formats

## Data Models and Architecture

Data models define how information is conceptually represented and structured, directly impacting both system architecture and problem-solving capabilities.

### Relational Model: The Persistent Foundation
The relational model remains one of computer science's most enduring concepts, organizing data into relations (tables) where each row represents a tuple.

**Core Characteristics:**
- **Unordered Nature**: Row and column order doesn't affect the relation
- **Normalization**: Data typically follows normal forms (1NF, 2NF, etc.) to reduce redundancy
- **Storage Formats**: Usually stored in CSV or Parquet formats
- **Query Language**: SQL provides declarative query capabilities

**SQL's Declarative Power:**
Unlike imperative languages (Python), SQL is declarative—you specify desired outputs rather than execution steps. The database engine determines optimal query execution paths, including join strategies, sorting methods, and operation sequencing. SQL approaches Turing-completeness, theoretically capable of solving any computation problem.

**Normalization Trade-off:**
While normalization reduces data redundancy and ensures consistency, it spreads data across multiple relations. Rejoining data requires expensive operations, particularly for large tables.

### NoSQL Models: Beyond Relational Constraints

#### Document Model Architecture
Built around the document concept—typically single continuous strings encoded in JSON, XML, or binary formats like BSON.

**Advantages:**
- **Schema Flexibility**: No enforced structure requirements (often called "schemaless")
- **Single Document Performance**: Faster queries when data resides in one document
- **Development Speed**: Rapid prototyping without upfront schema design

**Limitations:**
- **Join Complexity**: Cross-document operations are harder and less efficient than table joins
- **Query Example**: Finding all books under $25 requires reading all documents, extracting prices, comparing values, and filtering results
- **Responsibility Shift**: Schema burden moves from write-time to read-time applications

#### Graph Model Architecture
Designed around nodes and edges where relationships are first-class citizens in the data model.

**Core Philosophy**: If document databases prioritize document content, graph databases prioritize relationships between data items.

**Performance Advantage**: Explicit relationship modeling enables faster relationship-based queries compared to traditional join operations in relational databases.

**Use Case Optimization**: Ideal when relationships and connections are central to application logic.

### Structured vs Unstructured Data Philosophy

| Aspect | Structured Data | Unstructured Data |
|--------|----------------|-------------------|
| **Schema** | Predefined, enforced schema | No schema requirements |
| **Analysis** | Easy querying and analysis | Fast data ingestion |
| **Flexibility** | Limited to specific schema | Accepts any data source |
| **Change Management** | Schema changes require system-wide updates | Schema evolution handled downstream |
| **Storage** | Data warehouses | Data lakes |
| **Use Case** | When data format is stable and known | When data sources are diverse or evolving |

**Business Reality**: As requirements evolve, predefined schemas can become restrictive. Unstructured approaches provide flexibility but shift complexity to downstream consumers.

## Storage Engines and Processing Architectures

Storage engines implement the actual mechanisms for data storage and retrieval, typically optimized for either transactional or analytical workloads.

### OLTP vs OLAP Processing Paradigms

**Online Transaction Processing (OLTP):**
Handles real-time business transactions requiring immediate processing with strict performance requirements.

- **Latency Requirement**: Low latency for user-facing operations
- **Availability Requirement**: High availability—system downtime prevents transactions
- **Data Format**: Row-major for individual record processing
- **ACID Compliance**: Some systems use BASE (Basically Available, Soft state, Eventual consistency) for less restrictive requirements
- **Use Cases**: E-commerce purchases, ride bookings, social media posts

**Online Analytical Processing (OLAP):**
Designed for complex analytical queries requiring data aggregation across multiple dimensions.

- **Query Complexity**: Complex aggregations and multi-dimensional analysis
- **Data Format**: Column-major for efficient aggregation operations
- **Performance Focus**: Optimized for "What's the average price for all rides in September in San Francisco?" type queries
- **Use Cases**: Business intelligence, reporting, data analysis

**Convergence Trend**: The traditional OLTP/OLAP separation is diminishing. Modern databases like CockroachDB handle both transactional and analytical queries, while systems like Apache Iceberg and DuckDB blur traditional boundaries.

### ETL vs ELT Processing Philosophies

**ETL (Extract, Transform, Load):**
Traditional approach emphasizing data quality and structure before storage.

- **Extract Phase**: Data retrieval with validation and rejection of malformed data
- **Transform Phase**: Core processing including joins, cleaning, standardization, deduplication, aggregation, and feature derivation
- **Load Phase**: Optimized insertion into target systems (databases, warehouses)
- **Philosophy**: Process and structure data before storage

**ELT (Extract, Load, Transform):**
Modern approach prioritizing flexibility and raw data preservation.

- **Philosophy**: Store raw data first, process as needed
- **Advantage**: Flexibility for schema changes and diverse data sources
- **Challenge**: Inefficient searching through massive raw datasets
- **Evolution**: Led to hybrid "data lakehouse" solutions (Databricks, Snowflake)

## Data Flow Architectures in Distributed Systems

Production ML systems require data passage between multiple processes that don't share memory, creating distinct dataflow patterns.

### Database-Mediated Data Flow
**Mechanism**: Process A writes data to shared database, Process B reads from same database.

**Limitations:**
- **Access Requirements**: Both processes need database access (challenging across company boundaries)
- **Performance**: Database read/write operations too slow for strict latency requirements (consumer-facing applications)

### Service-Based Data Flow (Request-Driven Architecture)
**Mechanism**: Direct network communication through service requests.

**Implementation Styles:**
- **REST (Representational State Transfer)**: Designed for network requests, predominant for public APIs
- **RPC (Remote Procedure Call)**: Makes remote network calls appear like local function calls, focused on internal services within organizations

**Architecture Benefits:**
- **Service Independence**: Components can be developed, tested, maintained separately
- **Microservice Architecture**: Each component becomes an independent service
- **Cross-Organization**: Services can span different companies and applications

**Scalability Challenge**: With hundreds or thousands of services, direct inter-service communication creates complex dependency webs and becomes a system bottleneck.

### Real-Time Transport (Event-Driven Architecture)
**Core Innovation**: Central broker eliminates complex point-to-point service communication.

**Architecture Benefits:**
- **Decoupling**: Services only communicate with broker, not directly with each other
- **Asynchronous Processing**: Producers don't wait for consumer responses
- **Fault Tolerance**: Service downtime doesn't cascade to dependent services
- **Scalability**: Eliminates N-to-N service communication complexity

**Event-Driven Philosophy**: Data-heavy systems benefit more than logic-heavy systems from this architecture.

**Implementation Types:**

**Pub-Sub (Publish-Subscribe) Model:**
- Any service publishes to topics
- Any service subscribes to relevant topics
- Producers don't care about consumers
- **Retention Policy**: Data retained for specified period (e.g., 7 days) then archived
- **Examples**: Apache Kafka, Amazon Kinesis

**Message Queue Model:**
- Events have intended consumers (called messages)
- Queue ensures message delivery to correct consumers
- **Examples**: Apache RocketMQ, RabbitMQ

## Batch vs Stream Processing Paradigms

### Batch Processing: Historical Data Analysis
**Data Source**: Historical data stored in databases, data lakes, or warehouses
**Execution Pattern**: Periodic jobs (daily, weekly, monthly schedules)
**Technology**: Distributed systems like MapReduce and Spark for efficient large-scale processing

**Feature Generation**: Produces batch/static features for slowly changing data
- **Example**: Driver ratings (hundreds of rides make rating changes gradual)
- **Computation Efficiency**: Optimized for large-scale historical analysis

### Stream Processing: Real-Time Data Analysis
**Data Source**: Real-time streaming data from transports like Kafka and Kinesis
**Execution Pattern**: Continuous processing or short intervals (minutes)
**Latency Advantage**: Process data immediately upon generation without database storage delays

**Feature Generation**: Produces streaming/dynamic features for rapidly changing data
- **Examples**: Current driver availability, recent ride requests, upcoming ride completions
- **Business Impact**: Critical for real-time pricing optimization

**Technology Evolution**: Apache Flink and other modern stream processors achieve high scalability with distributed, parallel computation capabilities.

**Stateful Computation Advantage**: Stream processing excels at maintaining ongoing computations.
- **Example**: 30-day user engagement analysis
- **Batch Approach**: Recompute entire 30-day window daily (redundant)
- **Stream Approach**: Incrementally update with new daily data, join with existing computation

### Hybrid Architecture Reality
**Production Requirements**: Most ML systems require both batch and streaming features for comprehensive functionality.

**Infrastructure Needs**: Systems must process both data types and join results for ML model consumption.

**Complexity Management**: ML applications like fraud detection and credit scoring use hundreds or thousands of stream features requiring complex queries with multi-dimensional joins and aggregations.

**Architectural Philosophy**: Stream processors handle batch processing more effectively than batch processors handle streaming (Apache Flink's core argument: batch processing is a special case of stream processing).

**Processing Engine Requirements**: Complex stream feature extraction demands sophisticated stream processing engines beyond simple real-time transport capabilities (Kafka's built-in streaming is limited for complex multi-source scenarios).