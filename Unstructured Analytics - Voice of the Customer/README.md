# Unstructured Analytics - Voice of the Customer

Analyze customer call transcripts, chat logs, and support tickets using Snowflake Cortex AI functions, then surface insights through an incremental Dynamic Table pipeline, Cortex Search, and a conversational Cortex Agent.

## What This Notebook Does

### 1. Synthetic Data Generation

Generates a realistic retail dataset with 50 customers across five tables:

| Table | Description |
|---|---|
| `VOC_CUSTOMERS` | Customer profiles with loyalty tier, region, and demographics |
| `VOC_PURCHASES` | Purchase transactions across 7 product categories |
| `VOC_CALL_TRANSCRIPTS` | Phone call transcripts (positive and negative) |
| `VOC_CHAT_INTERACTIONS` | Live chat conversations |
| `VOC_SUPPORT_TICKETS` | Support tickets with subjects and bodies |

~20% of customers are flagged with a recent negative experience to create realistic at-risk signals.

### 2. Incremental Dynamic Table Pipeline

An end-to-end pipeline that automatically refreshes as new interaction data arrives:

```
Base Tables --> Sentiment Scoring --> Topic Classification --> Topic Agg --------+
                   |                                                             |
                   +---> Sentiment Agg ----------------------------------> Customer 360 --> Outreach Candidates
                   |                                                             |
             Purchases ---> Purchase Agg ---+                                    |
                   |                        +------------------------------------+
                   +---> Last Purchase -----+
```

**Dynamic Tables created:**

| Dynamic Table | Purpose |
|---|---|
| `VOC_INTERACTION_SENTIMENT` | Sentiment scores (numeric + aspect-level) across all channels |
| `VOC_INTERACTION_TOPICS` | Topic classification (shipping, billing, defect, etc.) |
| `VOC_TICKET_EXTRACTIONS` | Structured extraction from ticket text (product, issue, urgency, resolution) |
| `VOC_SENTIMENT_AGG` | Per-customer sentiment aggregates |
| `VOC_TOPIC_AGG` | Per-customer dominant topic |
| `VOC_PURCHASE_AGG` | Per-customer purchase metrics |
| `VOC_LAST_PURCHASE` | Most recent product per customer |
| `VOC_CUSTOMER_360` | Unified customer view with sentiment trends, topics, and purchase data |
| `VOC_OUTREACH_CANDIDATES` | At-risk customers filtered by declining sentiment |

### 3. Automated Outreach (Stream + Task)

Since `SNOWFLAKE.CORTEX.COMPLETE` cannot run inside a Dynamic Table, a hybrid pattern is used:

1. **Incremental DT** identifies outreach candidates
2. **Stream** (`VOC_OUTREACH_STREAM`) captures new/changed candidates
3. **Task** (`VOC_GENERATE_EMAILS`) runs hourly and uses `SNOWFLAKE.CORTEX.COMPLETE` (llama3.1-70b) to draft personalized recovery emails via MERGE

### 4. Cortex Search Services

Three search services provide semantic (meaning-based) retrieval over unstructured interactions:

- `VOC_CALL_SEARCH` -- Search call transcripts
- `VOC_CHAT_SEARCH` -- Search chat logs
- `VOC_TICKET_SEARCH` -- Search support tickets

### 5. Cortex Agent

A conversational agent (`VOC_CUSTOMER_AGENT`) that combines:
- **Structured data access** via a semantic model over `VOC_CUSTOMER_360`
- **Unstructured search** via the three Cortex Search services

Enables natural-language queries like "Which gold-tier customers had negative sentiment last month?" or "Find all interactions mentioning shipping delays."

## Cortex AI Functions Used

| Function | Purpose |
|---|---|
| `SNOWFLAKE.CORTEX.SENTIMENT()` | Numeric sentiment score (-1 to 1) |
| `AI_SENTIMENT()` | Aspect-level sentiment with custom categories |
| `AI_CLASSIFY()` | Topic classification against custom labels |
| `AI_EXTRACT()` | Structured field extraction from free text |
| `SNOWFLAKE.CORTEX.COMPLETE()` | LLM-generated outreach emails |

## Prerequisites

- Snowflake account with Cortex AI functions enabled
- Warehouse (defaults to `ML_DEMO_WH`)
- Database and schema (defaults to `ML_DEMO.PUBLIC`)

## Getting Started

1. Open `voice_of_the_customer.ipynb` in Snowflake Notebooks
2. Run cells sequentially -- data generation comes first, then the pipeline, then search and agent setup
3. The notebook is self-contained: all tables, dynamic tables, streams, tasks, search services, and the agent are created inline
