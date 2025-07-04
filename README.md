[![CI](https://github.com/AlphaTok-Singapore/VerseMind-RAG/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/AlphaTok-Singapore/VerseMind-RAG/actions/workflows/ci.yml)

# AlphaMind

**Where Poetry Meets AI**

<p align="center">
  <a href="README.md">English</a> |
  <a href="README_CN.md">简体中文</a>
</p>

AlphaMind is a document intelligence and semantic generation system, providing a complete RAG (Retrieval-Augmented Generation) solution that supports document loading, chunking, parsing, vector embedding, indexing, semantic search, and text generation.

## Features

- **Document Processing**: Load, chunk, and parse document content
- **Vector Embedding**: Generate semantic vectors for retrieval
- **Semantic Search**: Retrieve relevant content based on natural language queries
- **Text Generation**: Generate responses using advanced large models
- **Modular Design**: Flexible extension with multiple models and vector databases
- **Dynamic Model Configuration**: Centralized model management, consistent across frontend and backend

## System Architecture

- **Frontend**: React + Vite + TailwindCSS
- **Backend**: FastAPI + Python services
- **Storage**: File system + FAISS/Chroma vector database
- **Models**: Ollama (local) + DeepSeek/OpenAI APIs

## Quick Start

### Prerequisites

- Node.js 18+ (for frontend)
- Python 3.12.9 (for backend, recommended to use Conda)
- Conda (or Miniconda/Anaconda)
- SWIG (needed for Faiss, can be installed via Conda: `conda install -c anaconda swig`)
- Ollama (optional, for local model support)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/AlphaTok-Singapore/VerseMind-RAG.git
   cd VerseMind-RAG
   ```

2. Set up Conda environment:
   ```bash
   # Create and activate the environment
   conda create -n versemind-rag python=3.12.9
   conda activate versemind-rag
   ```

3. Install frontend dependencies:
   ```bash
   cd frontend
   npm install
   ```

4. Install backend dependencies (choose one method):

   **Option A: Using pip with requirements.txt**
   ```bash
   cd backend
   pip install -r requirements.txt
   ```

   **Option B: Using Poetry**
   ```bash
   cd backend
   # Install Poetry if not already installed
   # pip install poetry
   poetry install
   ```

   **Option C: Using uv (faster Python package installer)**
   ```bash
   cd backend
   # Install uv if not already installed
   # pip install uv
   uv pip install -r requirements.txt
   ```

5. Configure environment variables:
   ```bash
   # Create a config.toml file based on the example
   cp config/config.example.toml config/config.toml
   # Edit the config.toml file to set your API keys and preferences
   ```

### Running the Project

1. **Start backend:**
   ```bash
   # Using the convenience script (Windows):
   start-backend.bat
   
   # Using the convenience script (macOS/Linux):
   chmod +x ./start-backend.sh  # Make executable (first time only)
   ./start-backend.sh
   
   # OR manually:
   cd backend
   conda activate versemind-rag  # Or your virtual environment
   uvicorn app.main:app --host 0.0.0.0 --port 8200 --reload
   ```
   
   The backend will be available at:
   - API endpoint: http://localhost:8200
   - API documentation: http://localhost:8200/docs

2. **Start frontend:**
   ```bash
   # Development mode with hot reload:
   cd frontend
   npm run dev
   
   # OR using convenience script (Windows):
   start-frontend.bat dev
   
   # OR using convenience script (macOS/Linux):
   chmod +x ./start-frontend.sh  # Make executable (first time only)
   ./start-frontend.sh dev
   ```
   
   ```bash
   # Production mode:
   cd frontend
   npm run build
   npm run preview
   
   # OR using convenience script (Windows):
   start-frontend.bat
   
   # OR using convenience script (macOS/Linux):
   chmod +x ./start-frontend.sh  # Make executable (first time only)
   ./start-frontend.sh
   ```

3. **Access the application:**
   - Development mode: http://localhost:3200
   - Production preview: http://localhost:4173

### Using Local Models with Ollama

VerseMind-RAG can utilize local AI models through [Ollama](https://ollama.ai) for both text generation and embedding, which:
- Keeps your data private by processing it locally
- Eliminates API costs and rate limits
- Provides offline functionality

**Setup Steps:**

1. Install Ollama from [ollama.ai](https://ollama.ai)

2. Pull the models you want to use:
   ```bash
   # Pull embedding models (choose at least one)
   ollama pull bge-m3        # Smaller, faster embedding model
   ollama pull bge-large     # Larger, more accurate embedding model
   
   # Pull generation models (choose models that fit your hardware)
   ollama pull gemma3:4b     # Smaller, faster model (4B parameters)
   ollama pull deepseek-r1:14b # Better quality, needs more RAM (14B parameters)
   ollama pull phi4          # Microsoft's latest model
   ollama pull mistral       # Good performance/resource balance
   ollama pull llama3.2-vision # For image understanding capabilities
   ```

3. Ensure Ollama is running in the background before starting VerseMind-RAG

4. In the VerseMind-RAG interface, select your preferred models from the Models dropdown menu

### Environment Configuration

The application is configured through `config/config.toml`. You need to copy and modify the example configuration file:

```bash
# Copy the example configuration
cp config/config.example.toml config/config.toml

# Edit the config.toml file according to your needs
# For Ollama users, the default settings should work out of the box
# For API-based models (OpenAI, DeepSeek), you'll need to add your API keys
```

Basic configuration options:

```toml
# Main LLM configuration - selects your default model
[llm]
api_type = "ollama"  # Use "ollama" for local models, "openai" for OpenAI/DeepSeek APIs
model = "gemma3:4b"  # Your preferred model
base_url = "http://localhost:11434"  # Ollama API URL (default)

# Server settings
[server]
host = "0.0.0.0"
port = 8200

# Vector database settings
[vector_store]
type = "faiss"  # Options: "faiss", "chroma"
persist_directory = "./storage/vector_db"
```

See the `config/config.example.toml` file for more configuration options.

## Advanced Usage

### Custom Model Configuration

VerseMind-RAG allows you to customize models through the `config/config.toml` file. You can configure:

- Multiple embedding models with different dimensions and providers
- Custom generation models with specific parameters (temperature, max_tokens, etc.)
- Provider-specific configurations for different AI services

See the model configuration section in `config/config.example.toml` for examples.

### Vector Database Optimization

VerseMind-RAG supports multiple vector database backends for storing and retrieving document embeddings:

- **FAISS**: Optimized for high-performance vector similarity search
  - Excellent for large-scale datasets (millions of vectors)
  - Multiple index types (Flat, HNSW32, HNSW64)
  - Various distance metrics (cosine, L2, inner product)

- **Chroma**: Feature-rich vector database with metadata filtering
  - Rich metadata filtering capabilities
  - Easy-to-use API and simple configuration
  - Excellent for complex document retrieval requirements

Configuration is managed through the `config/config.toml` file using this structure:

```toml
[vector_store]
type = "faiss"  # Options: "faiss", "chroma"
persist_directory = "./storage/vector_db"

[vector_store.faiss]
index_type = "HNSW32"  # Options: "Flat", "HNSW32", "HNSW64"
metric = "cosine"      # Options: "cosine", "l2", "ip"

[vector_store.chroma]
collection_name = "versemind_docs"
distance_function = "cosine"  # Options: "cosine", "l2", "ip"
```

For detailed configuration options including index types, distance metrics, and performance tuning recommendations, please refer to the [User Guide](./Temp/user_guide.md#向量数据库配置).

### Logging Configuration

VerseMind-RAG supports dynamic logging level configuration through environment variables, allowing you to control the verbosity of logs without modifying the code:

```
# In your .env file:
LOG_LEVEL=INFO  # Options: DEBUG, INFO, WARNING, ERROR, CRITICAL
```

Available log levels:

- `DEBUG`: Show all messages, including detailed debugging information
- `INFO`: Show informational messages, warnings, and errors (default)
- `WARNING`: Show only warnings and errors
- `ERROR`: Show only errors
- `CRITICAL`: Show only critical errors

For more details, see [Logging Configuration](./docs/logging_configuration.md).

### Advanced Document Processing

VerseMind-RAG provides flexible document processing configurations that can be adjusted to optimize for different document types and use cases.

For detailed configuration options, please refer to the configuration example file (`config/config.example.toml`).

## How to Use VerseMind-RAG

VerseMind-RAG provides a complete document processing and retrieval pipeline:

1. **Upload Documents** via the UI
2. **Process Documents** (chunking, parsing, embedding, indexing)
3. **Query Documents** through the Chat interface
4. **View Results** with source references and visualizations

For detailed usage instructions, sample queries, and best practices, please refer to the [User Guide](./docs/user_guide.md).

## Documentation

For detailed documentation, refer to:
- [System Architecture Design](./docs/VerseMind-RAG%20系统架构设计.md)
- [User Guide](./docs/user_guide.md)

## Contributing

Contributions to VerseMind-RAG are welcome! Here's how you can contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

Please ensure your code passes all tests before submitting a pull request.

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

## Acknowledgements

- [React](https://reactjs.org/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Tailwind CSS](https://tailwindcss.com/)
- [Ollama](https://ollama.ai/)
- [FAISS](https://github.com/facebookresearch/faiss)
- [Chroma](https://www.trychroma.com/)
