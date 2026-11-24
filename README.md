# GraphMind AI — Knowledge Graph-Based RAG

A Python and Streamlit application for knowledge graph-based Retrieval-Augmented Generation (GraphRAG), multi-hop retrieval and citation-backed question answering using Neo4j and local LLM inference through Ollama.

Based on **CodinzHub GraphRAG**, originally developed by **Coder Coder** ([CodinzHub Projects](https://www.codinzhub.com/projects)).

## Key Features

- Knowledge graph-based Retrieval-Augmented Generation (RAG)
- Entity and relationship extraction from documents
- Multi-hop graph traversal for indirect associations
- Context-aware question answering with source citations
- Local LLM inference through Ollama
- Interactive Streamlit interface

## Technology Stack

| Component       | Technology                             |
|-----------------|----------------------------------------|
| Language        | Python                                 |
| User interface  | Streamlit                              |
| Graph database  | Neo4j                                  |
| Local inference | Ollama (Llama or Mistral)              |
| Default model   | Llama 3.2                              |
| Integration     | Neo4j Python Driver, Ollama Python SDK |
| Database setup  | Docker                                 |

## Architecture

GraphMind AI uses structured entities and relationships to retrieve connected facts that may be difficult to recover through text similarity alone. The workflow maps retrieved context to source snippets to support citation checking.

### Document ingestion

``` mermaid
flowchart TD
    Doc[Text or PDF document] --> LLM[Ollama entity extraction]
    LLM --> Parser[JSON nodes and edges parser]
    Parser --> DB[(Neo4j knowledge graph)]
```

### Retrieval and generation

``` mermaid
flowchart LR
    Q[User question] --> S[Text index matching]
    S --> G[Graph traversal up to depth 2]
    G --> C[Retrieved context and source mapping]
    C --> L[LLM generation]
    L --> A[Answer and citations]
```

## Installation and Setup

### Prerequisites

Install Python, Docker and Ollama. Open a terminal in the project directory containing `requirements.txt`.

### 1. Download the local model

``` bash
ollama pull llama3.2
```

Ensure Ollama is running locally.

### 2. Start Neo4j

``` bash
docker run -d \
  --name neo4j \
  -p 127.0.0.1:7474:7474 -p 127.0.0.1:7687:7687 \
  -e NEO4J_AUTH=neo4j/your_password \
  neo4j:latest
```

Replace `your_password` with a strong password and use the same credentials in the application configuration. Do not commit credentials to GitHub.

### 3. Install dependencies

``` bash
python -m pip install -r requirements.txt
```

### 4. Launch the application

``` bash
streamlit run codinzhub-graph-rag.py
```

This command retains the original entry-point filename. If you rename the file to `app.py`, run `streamlit run app.py` instead.

## Configuration

| Setting        | Default                 |
|----------------|-------------------------|
| Neo4j Bolt URI | `bolt://127.0.0.1:7687` |
| Neo4j user     | `neo4j`                 |
| Local LLM      | `llama3.2`              |

## Example Workflow

The following fictional financial services example demonstrates entity extraction and multi-hop retrieval.

```text
Aster Capital manages the Horizon Growth Fund. The fund invests in
Vertex Energy, which operates in the renewable energy sector.
Maya Rao is the portfolio manager responsible for the Horizon Growth Fund.
```

Example graph entities:

- `Aster Capital` — ORGANIZATION
- `Horizon Growth Fund` — INVESTMENT_FUND
- `Vertex Energy` — ORGANIZATION
- `Renewable Energy` — SECTOR
- `Maya Rao` — PERSON

Example relationships:

- `Aster Capital` — MANAGES → `Horizon Growth Fund`
- `Horizon Growth Fund` — INVESTS_IN → `Vertex Energy`
- `Vertex Energy` — OPERATES_IN → `Renewable Energy`
- `Maya Rao` — PORTFOLIO_MANAGER_OF → `Horizon Growth Fund`

An example two-hop question is: **Which company does the fund managed by Aster Capital invest in?** The expected answer from the sample document is Vertex Energy, retrieved through the MANAGES and INVESTS_IN relationships. This is an illustrative example, not a verified application output or measured evaluation result.

## Future Enhancements

The following are proposed extensions, not currently claimed as implemented:

- Sentence embeddings and hybrid vector/graph retrieval
- Retrieval and answer evaluation using RAGAS
- REST API endpoints with FastAPI
- Automated unit and integration tests using Pytest
- Authentication and secure deployment

## Acknowledgements and License

GraphMind AI is a renamed adaptation of **CodinzHub GraphRAG**, originally developed by **Coder Coder**. Visit [CodinzHub Projects](https://www.codinzhub.com/projects) for the original project collection.

This README update does not establish new functional contributions. The original project’s license and copyright notices govern reuse; retain them and verify that redistribution and modification are permitted before publishing. The original repository URL and license terms were not supplied with this README.
