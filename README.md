# RAG Implementation from Scratch

This repository demonstrates a comprehensive implementation of Retrieval-Augmented Generation (RAG) from scratch, including various chunking strategies and evaluation using RAGAS library.

## Technical Overview

### RAG Pipeline Components

1. **Document Processing**
   - PDF document ingestion using PyMuPDF
   - Text extraction and preprocessing
   - Various chunking strategies implemented

2. **Chunking Strategies Explored**
   - Fixed-size chunking
   - Semantic chunking using sentence transformers
   - Recursive chunking with content-aware splitting
   - Structural chunking based on document layout
   - LLM-based intelligent chunking
   
3. **Embedding & Retrieval**
   - Sentence transformers for text embeddings
   - Vector similarity search for relevant chunk retrieval
   - Storage and indexing of embeddings for efficient retrieval

4. **LLM Integration**
   - Local GPU-powered LLM implementation
   - Context augmentation with retrieved chunks
   - Prompt engineering for improved responses

### Evaluation Framework

The project includes a sophisticated evaluation framework using the RAGAS library:

1. **Metrics Measured**
   - Answer Faithfulness
   - Answer Relevancy
   - Context Relevancy
   - Context Precision

2. **LLM-as-Judge Evaluation**
   - Using a separate LLM model to evaluate responses
   - Automated scoring of response quality
   - Comparison across different chunking strategies

### Key Features

- Complete RAG pipeline implementation from scratch
- Extensive comparison of different chunking strategies
- GPU-optimized local execution
- Quantitative evaluation framework
- RAGAS-based automated assessment

## Repository Structure

```
├── dataset/
│   └── text_chunks_and_embeddings_df.csv
├── scr/
│   ├── LLM_Prod_RAG.ipynb        # Main RAG implementation
│   └── RAG_Chunking_Strategies.ipynb # Chunking experiments
```

## Technical Details

### Chunking Implementations

1. **Fixed-size Chunking**
   - Character and token-based splitting
   - Configurable overlap between chunks

2. **Semantic Chunking**
   - Sentence transformer embeddings
   - Semantic similarity-based grouping
   - Dynamic chunk boundaries

3. **Recursive Chunking**
   - Hierarchical document splitting
   - Content-aware boundary detection
   - Nested chunk relationships

4. **Structural Chunking**
   - Document layout analysis
   - Section-based splitting
   - Hierarchy preservation

5. **LLM-based Chunking**
   - Intelligent content understanding
   - Context-aware splitting
   - Semantic coherence preservation

### Evaluation Details

- Implementation of comprehensive evaluation metrics
- Automated scoring using LLM-as-judge approach
- Comparative analysis of chunking strategies
- Quantitative assessment of RAG performance

The evaluation framework provides objective measures of:
- Answer quality and accuracy
- Context relevance and precision
- Information retrieval effectiveness
- Overall system performance
