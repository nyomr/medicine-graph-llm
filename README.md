# Conversational Medicine Assistant

**Medicine Assistant** is an intelligent question-answering system that helps users explore medicine-related information using a combination of **Large Language Models (LLMs)** and a **Neo4j Knowledge Graph**.

## 🚀 Overview

This project bridges the gap between complex structured data and natural language by allowing users to ask flexible, human-like questions about medicines. Under the hood, it generates Cypher queries to retrieve data from a graph database and deliver context-aware answers.

Example capabilities:
- 🔍 Find medicines with specific **compositions**, **side effects**, or **uses**
- 🔁 Suggest similar medicines based on a chosen **aspect** (e.g., similar in side effects or ingredients)

## 🧱 Tech Stack

- **Neo4j**: Graph database to store medicine-related entities and relationships.
- **LangChain**: Framework for building LLM-driven applications.
- **Gemini**: To understand natural language questions and generate Cypher queries.
- **Gradio**: Interactive user interface for asking questions.
- **Python**: Core programming language.

## 📌 Features

- Natural language interface for graph-based medical data
- Aspect-specific similarity (e.g., by composition, use, or side effect)
- Dynamic Cypher query generation using `LangChain`
- Case-insensitive matching with `toLower()` for consistent search results
- Interactive UI for querying and visualizing results

## 🧠 Data Modeling (Cypher Seed Query)

The initial data model is created using the following Cypher query, which loads and structures data from a Google Drive CSV:

```cypher
LOAD CSV WITH HEADERS FROM 'source' AS row

MERGE (m:Medicine {name: row.MedicineName})
SET m.image_url = row.ImageURL, m.excellent_review = row.ExcellentReview, m.average_review = row.AverageReview, m.poor_review = row.PoorReview

MERGE (manu:Manufacturer {name: row.Manufacturer})
MERGE (m)-[:MADE_BY]->(manu)

WITH m, row
UNWIND SPLIT(row.Composition, ",") AS comp
WITH m, row, TRIM(comp) AS comp_name
MERGE (c:Composition {name: comp_name})
MERGE (m)-[:HAS_COMPOSITION]->(c)

WITH m, row
UNWIND SPLIT(row.Uses, ",") AS use
WITH m, row, TRIM(use) AS use_name
MERGE (u:Use {name: use_name})
MERGE (m)-[:USED_FOR]->(u)

WITH m, row
UNWIND SPLIT(row.Sideeffects, ",") AS se
WITH m, TRIM(se) AS se_name
MERGE (s:SideEffect {name: se_name})
MERGE (m)-[:CAUSES]->(s)

