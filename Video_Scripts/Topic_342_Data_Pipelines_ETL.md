# Data Pipelines: ETL and ELT Basics

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your app has data. User signups. Orders. Clicks. It's in MySQL. In Redis. In log files. Scattered. Raw. A product manager asks: "What's our conversion rate last week?" You're stuck. The data's everywhere. You need to move it. Transform it. Put it somewhere you can query. That's a **data pipeline**. Extract from sources. Transform. Load into a warehouse. ETL. Or maybe ELT. Let me break it down.

---

## The Story

A **data pipeline** moves data from source to destination, often with transformation in between. Your **sources** might be: databases (MySQL, Postgres), APIs, log files, event streams. Your **destination** might be: a **data warehouse** (Snowflake, BigQuery, Redshift) for analytics, or a **data lake** for raw storage. The pipeline is the plumbing. It makes data useful.

**ETL: Extract → Transform → Load.** You pull data from sources. You **transform** it—clean it, aggregate it, join it—before loading. Transformation happens in a separate engine (Spark, dbt, custom code). Then you load the transformed data into the warehouse. **Why ETL?** You control transformation. You load only what you need. Warehouse stays lean. Good when: sources are messy, or you want to reduce storage cost.

**ELT: Extract → Load → Transform.** You pull data and load it **raw** into the warehouse. Transformation happens *inside* the warehouse. Tools like **dbt** run SQL in the warehouse. **Why ELT?** Modern warehouses (Snowflake, BigQuery) are fast. They scale. Transform there. Simpler pipeline—just move data. Good when: warehouse is powerful, you want flexibility to transform later. ELT is increasingly popular. Less pipeline complexity.

**Batch vs streaming:** **Batch**—run on a schedule. Hourly. Daily. Move data in chunks. Tools: **Apache Airflow**, **AWS Glue**, **dbt**. **Streaming**—data flows continuously. Real-time. Tools: **Kafka**, **Kafka Streams**, **Flink**. Use batch for reporting, analytics. Use streaming for real-time dashboards, event-driven systems. Many systems do both—batch for historical, streaming for live.

---

## Another Way to See It

Think of a **water treatment plant**. Water comes from rivers (sources). It gets filtered, cleaned, treated (transform). Then it goes to reservoirs and taps (destination). ETL is like that—treat before delivery. ELT is like storing raw water in a big tank, then treating it on demand. Different order. Same goal: clean, usable data (water) where you need it.

---

## Connecting to Software

- **Data pipeline** = move data from source to destination. Often with transformation.
- **ETL** = Extract → Transform → Load. Transform before loading. Use when sources are messy.
- **ELT** = Extract → Load → Transform. Load raw, transform in warehouse. Simpler pipeline. Modern warehouses make this viable.
- **Batch** = scheduled. Hourly, daily. Airflow, Glue, dbt. **Streaming** = real-time. Kafka, Flink.
- **Tools:** Apache Spark, Airflow, dbt, Kafka Streams, AWS Glue. Warehouses: Snowflake, BigQuery, Redshift.
- **Use cases:** Analytics, reporting, ML feature stores, dashboards.
- **Real-world:** Uber (MySQL → analytics), Netflix. Every data-driven company has pipelines.

---

## Let's Walk Through the Diagram

```
    ETL vs ELT

    ETL: Extract → Transform → Load
    ──────────────────────────────

    ┌────────┐    ┌────────────┐    ┌────────────┐    ┌──────────┐
    │ MySQL  │───►│  Extract   │───►│  Transform │───►│  Load    │───► Warehouse
    │ APIs   │    │            │    │  (Spark,   │    │          │
    │ Logs   │    │            │    │   dbt)     │    │          │
    └────────┘    └────────────┘    └────────────┘    └──────────┘
                        │                   │
                        │    Transform      │
                        │    BEFORE load    │
                        └───────────────────┘

    ELT: Extract → Load → Transform
    ──────────────────────────────

    ┌────────┐    ┌────────────┐    ┌──────────┐    ┌────────────┐
    │ MySQL  │───►│  Extract   │───►│  Load    │───►│  Transform │
    │ APIs   │    │            │    │  (raw)   │    │  (in WH,   │
    │ Logs   │    │            │    │          │    │   dbt SQL) │
    └────────┘    └────────────┘    └──────────┘    └────────────┘
                        │                   │               │
                        │    Load raw       │   Transform   │
                        │    first          │   IN warehouse│
                        └───────────────────┘               └──► Analytics
```

---

## Real-World Examples (2-3)

**1. Uber.** Hundreds of services. Data in MySQL, Kafka, S3. They built massive pipelines to move data into their data warehouse. Batch for historical. Streaming for real-time (e.g., driver location). They use **Apache Kafka** for streaming, **Spark** for batch. The pipeline is the backbone of their analytics—rider behavior, driver efficiency, pricing models.

**2. Netflix.** Data from viewing events, signups, recommendations. Pipelines into a data warehouse. They've talked about using **Apache Airflow** for orchestration. Batch jobs. dbt for transformation. The result: product and content teams get the data they need. Pipelines make it happen.

**3. Stripe.** Payment data. Sensitive. They have pipelines for analytics—aggregated, anonymized. ETL with strict governance. Transform before load. Don't put raw card data in the warehouse. The pipeline enforces that. Security and compliance are built into the design.

---

## Let's Think Together

**Question:** You have 10 MySQL databases. Each has a "users" table. Slightly different schemas. You want one "users" table in the warehouse. Do you use ETL or ELT?

**Pause. Think about it...**

**Answer:** Either can work. **ETL:** Extract from all 10. Transform (normalize, merge, dedupe) in Spark or dbt. Load one clean table. Pro: warehouse gets clean data. Con: more pipeline logic. **ELT:** Load all 10 raw into the warehouse. Use dbt to transform—UNION, normalize, dedupe—in SQL. Pro: simpler extract/load. Warehouse does the work. Con: you store raw + transformed. More storage. For schema normalization across sources, ETL gives you control. But dbt in ELT can do it too. Many teams choose ELT for simplicity—load raw, transform in dbt. The warehouse is powerful. Use it.

---

## What Could Go Wrong? (Mini Disaster Story)

A company built an ETL pipeline. Daily batch. Pull from MySQL. Transform. Load to warehouse. It worked. Then data volume grew 10x. The transform step—running on a single server—took 12 hours. The pipeline missed its window. Data was stale. Reports were wrong. They hadn't designed for scale. The fix? Move transform to **Spark** (distributed). Or switch to **ELT**—load raw, let the warehouse (Snowflake, BigQuery) do the heavy lifting. Modern warehouses scale. The lesson: **design for growth**. Your pipeline will need to handle 10x, 100x more data. Plan for it.

---

## Surprising Truth / Fun Fact

**ETL** has been around since the 1970s—mainframe era. Extract from tapes. Transform. Load to another system. **ELT** emerged when **cloud data warehouses** (Snowflake, BigQuery) got cheap and fast. They can scan petabytes. Transform in SQL. Why do it elsewhere? The industry shifted. **dbt**—"data build tool"—exploded. It's SQL-based transformation. Runs in the warehouse. ELT made dbt possible. dbt made ELT popular. A virtuous cycle. Today, many new pipelines are ELT-first.

---

## Quick Recap (5 bullets)

- **Data pipeline** = move data from source to destination. Extract, optionally transform, load.
- **ETL** = Extract → Transform → Load. Transform before load. Good for messy sources.
- **ELT** = Extract → Load → Transform. Load raw, transform in warehouse. Simpler. Modern.
- **Batch** = scheduled (Airflow, Glue). **Streaming** = real-time (Kafka, Flink).
- **Tools:** Spark, Airflow, dbt, Kafka. Warehouses: Snowflake, BigQuery, Redshift.

---

## One-Liner to Remember

> **ETL: transform before load. ELT: load raw, transform in the warehouse. Both move data. Choose based on your warehouse and sources.**

---

## Next Video

That wraps up our data pipeline deep dive. You now have the complete toolkit—from your first "what is a system" to Staff-level design. Go build. Go design. Go ship.
