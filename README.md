# CRAG Notebook

This repository contains a Jupyter notebook implementation of Corrective Retrieval-Augmented Generation (CRAG), a structured pipeline for building more reliable retrieval-enhanced QA systems.

## What is CRAG?

CRAG extends standard retrieval-augmented generation by adding corrective evaluation and contextual refinement steps. Instead of simply retrieving documents and generating a response, the system:

- retrieves candidate documents
- evaluates whether those documents are correct and relevant
- refines context by filtering to the most useful sentences
- generates the final answer only from the curated context

This notebook implements a CRAG-style pipeline using local LLM and embedding services plus LangChain tooling.

## Key Components

- **Document ingestion**: load and chunk source documents (PDF/text)
- **Vector search**: index chunks with FAISS and retrieve top candidates
- **Retrieval evaluation**: score each retrieved chunk for relevance and correctness
- **Context refinement**: keep only the sentences that directly help answer the question
- **Web augmentation**: optionally search the web and add external evidence
- **Answer generation**: use only the refined context to produce a final response

## Architecture

The notebook follows this flow:

```
User question
     |
     v
Retrieval -> Candidate docs
     |
     v
Doc evaluation -> Correct / Wrong / Ambiguous
     |
     v
Web query rewrite + Web search (if needed)
     |
     v
Context refinement via a sentence-filtering agent
     |
     v
Answer generation using curated context
```

Web calls happen when document evaluation is wrong or ambiguous. In those cases, the pipeline rewrites the question into a search query, performs web search, and adds external evidence to the candidate pool before refinement.

The final context refinement step uses a separate agent that decomposes candidate text into sentences and keeps only those sentences directly helpful for the question.

## Notebook Features

- Local embedding model setup (`OpenAIEmbeddings` pointing to a local endpoint)
- FAISS vector store for similarity search
- `langchain` + `langgraph` orchestration
- Structured prompt outputs for document evaluation and filtering
- Sentence-level relevance filtering
- Web query rewriting and search integration using Tavily
- Controlled final generation with a strong "I don't know" fallback

## Requirements

The notebook installs the following packages:

- `pydantic`
- `langchain`
- `langgraph`
- `dotenv`
- `langchain-community`
- `langchain-openai`
- `langchain-text-splitters`
- `pypdf`
- `faiss-cpu`

A local model endpoint is expected at `http://127.0.0.1:1234/v1` with API key `lm-studio`.

## Usage

1. Activate the virtual environment.
2. Run the notebook `crag.ipynb`.
3. Configure `.env` or local endpoint variables if needed.
4. Execute cells in order to load documents, build embeddings, and run the CRAG pipeline.

## Notes

- The implementation is designed for experimentation with retrieval quality and correctness checks.
- The answer generator is intentionally restricted to the curated context to reduce hallucinations.
- The pipeline is modular, so you can replace embedding models, vector stores, or search services.

## Diagram

A simple ASCII diagram of the CRAG pipeline:

```
+-------------------+      +-------------------+      +--------------------+
|   User Question   | ---> | Retrieval + Score | ---> |  Context Refinement|
+-------------------+      +-------------------+      +--------------------+
                                         |                   |
                                         v                   v
                                +------------------+   +------------------+
                                |  Web Augmentation|   |  Answer Generator|
                                +------------------+   +------------------+
```

## Outcome

This notebook shows how to build a more robust retrieval-augmented system by adding corrective evaluation and context filtering, which aligns with the CRAG concept from the referenced article.
