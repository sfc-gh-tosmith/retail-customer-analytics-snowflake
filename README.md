# Retail Customer Analytics

End-to-end analytics pipelines built on Snowflake demonstrating how to combine structured transaction data with unstructured customer interactions to drive retention, personalization, and revenue growth.

## Projects

### [Customer Lifetime Value](./Customer%20Lifetime%20Value/)

ML models that predict customer lifetime value using Snowflake's Feature Store and Model Registry.

- **Cold Start Model** -- Predicts CLV for new customers (0-30 days) using acquisition signals, demographics, and early engagement
- **Continuous Model** -- Updates CLV predictions for established customers (90+ days) using RFM metrics, purchase patterns, and engagement features
- **Automated Inference** -- Dynamic Tables that auto-refresh predictions as new data arrives, with no Python execution required

**Key Snowflake features**: Feature Store, Feature Views (Dynamic Tables), ML Registry, XGBoost + Bayesian HPO, WAREHOUSE and SPCS deployment

### [Unstructured Analytics - Voice of the Customer](./Unstructured%20Analytics%20-%20Voice%20of%20the%20Customer/)

Extracts actionable insights from call transcripts, chat logs, and support tickets using Snowflake Cortex AI functions and incremental Dynamic Tables.

- **Sentiment Analysis** -- Numeric scoring and aspect-level sentiment (agent helpfulness, resolution quality, product quality)
- **Topic Classification** -- Automatic categorization of interactions (shipping issues, billing problems, product defects, etc.)
- **Customer 360** -- Unified view joining VoC metrics with purchase history and loyalty data
- **Automated Outreach** -- Stream + Task pattern that drafts personalized recovery emails for at-risk customers using Cortex Complete
- **Semantic Search** -- Cortex Search Services for meaning-based retrieval across all interaction types
- **Cortex Agent** -- Conversational agent that queries structured data and searches unstructured interactions

**Key Snowflake features**: Cortex AI Functions (SENTIMENT, AI_CLASSIFY, AI_EXTRACT, AI_COMPLETE), Incremental Dynamic Tables, Streams, Tasks, Cortex Search, Cortex Agents

## Prerequisites

- Snowflake account with access to Cortex AI functions
- A warehouse (notebooks default to `ML_DEMO_WH`)
- Database and schema (notebooks default to `ML_DEMO.PUBLIC`)

## Getting Started

1. Open the notebooks in Snowflake Notebooks (or any Snowpark-compatible environment)
2. Run the data generation cells first to create the synthetic datasets
3. Proceed through the pipeline steps in order

Each project folder contains its own README with detailed instructions.
