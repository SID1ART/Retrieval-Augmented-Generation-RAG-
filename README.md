# Retrieval-Augmented-Generation-RAG-
A Retrieval-Augmented Generation (RAG) system that answers questions about the PDF uploaded  using Supabase vector store, Cohere embeddings (768-dim), and Cohere LLM.


# RAG System

A Retrieval-Augmented Generation (RAG) system built to answer questions about the ingested PDF using vector similarity search and large language models.

## 🎯 Overview

This project implements an RAG pipeline that ingests PDF documentation and enables intelligent question-answering through semantic search. The system leverages Supabase's vector database for efficient similarity matching and Cohere's AI models for embeddings and response generation.

## 🛠️ Tech Stack

- **Vector Store**: Supabase (PostgreSQL with pgvector extension)
- **Embeddings**: Cohere Embed (1536 dimensions)
- **LLM**: Cohere Language Model
- **Database**: PostgreSQL with vector similarity search

## 📋 Features

- **PDF Document Ingestion**: Processes PDF and stores content with vector embeddings
- **Semantic Search**: Vector similarity search using cosine distance
- **Context-Aware Responses**: Retrieves relevant chunks and generates accurate answers
- **Configurable Similarity Threshold**: Adjustable matching parameters for query precision

## 🗄️ Database Schema

The system uses a `documents` table with the following structure:

```sql
CREATE TABLE documents (
  id bigserial PRIMARY KEY,
  title text,
  content text,
  metadata jsonb,
  embedding vector(1536)
);
```

### Vector Search Function

```sql
CREATE OR REPLACE FUNCTION match_documents(
  query_embedding vector(768),
  match_threshold float DEFAULT 0.5,
  match_count int DEFAULT 10,
  filter jsonb DEFAULT '{}'
)
RETURNS TABLE (
  id bigint,
  title text,
  content text,
  metadata jsonb,
  similarity float
)
LANGUAGE plpgsql
AS $$
BEGIN
  RETURN QUERY
  SELECT
    documents.id,
    documents.title,
    documents.content,
    documents.metadata,
    1 - (documents.embedding <=> query_embedding) AS similarity
  FROM documents
  WHERE 1 - (documents.embedding <=> query_embedding) > match_threshold
  ORDER BY documents.embedding <=> query_embedding
  LIMIT match_count;
END;
$$;
```

## 🚀 Getting Started

### Prerequisites

- Supabase account
- Cohere API key
- Cloud or locally installed n8n

### Installation

1. Download the repository
```bash
Download the json file of the workflow
```

2. Set up environment variables in supabase
```bash
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_key
COHERE_API_KEY=your_cohere_api_key
```

4. Initialize the database
```bash
# Run the SQL schema in your Supabase SQL editor
# Then run the vector search function
```

5. Ingest the PDF inside the n8n workflow


### Usage

Trigger the event and start by ingesting the PDF file into the Supabase vector storage
After ingesting into the vector storage, start asking the queries/questions you want to ask from the content inside the PDF

## 📊 How It Works

1. **Document Processing**: PDF is split into chunks and processed
2. **Embedding Generation**: Each chunk is converted to a 1536-dimensional vector using Cohere Embed
3. **Storage**: Vectors are stored in Supabase with metadata
4. **Query Processing**: User questions are embedded and matched against stored vectors
5. **Response Generation**: Top matching chunks are passed to Cohere LLM for answer synthesis

## 🎮 Example Queries (PDF regarding the rules of CHESS)

- "What are the basic rules of chess?"
- "How does a pawn move?"
- "What is en passant?"
- "Can you explain the castling rules?"
- "What happens when a pawn reaches the opposite end?"

## 📝 Testing

The system has been tested on chess rules documentation with various query types including:
- Rule explanations
- Move validations
- Special scenarios
- Game mechanics

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.


## 🙏 Acknowledgments

- Supabase for vector database capabilities
- Cohere for embeddings and LLM
- Chess rules documentation source

## 📧 Contact

Your Name - [@yourhandle](https://twitter.com/yourhandle)

Project Link: [https://github.com/yourusername/chess-rules-rag](https://github.com/yourusername/chess-rules-rag)

---

⭐ Star this repo if you find it helpful!
