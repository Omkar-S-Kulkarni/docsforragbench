# Contributing to RAGBench

Thank you for your interest in contributing to RAGBench! This guide will help you get started.

## Quick Start

```bash
# 1. Fork and clone
git clone https://github.com/YOUR_USERNAME/ragbench.git
cd ragbench

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install in editable mode with dev dependencies
pip install -e ".[dev]"

# 4. Install pre-commit hooks
pre-commit install
```

## Ways to Contribute

### 1. 🐛 Report Bugs
Found a bug? [Open an issue](https://github.com/Omkar-S-Kulkarni/ragbench/issues) with:
- Clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- System info (OS, Python version, RAGBench version)

### 2. 💡 Suggest Features
Have an idea? [Start a discussion](https://github.com/Omkar-S-Kulkarni/ragbench/discussions) or open a feature request issue.

### 3. 📝 Improve Documentation
- Fix typos or unclear explanations
- Add examples and tutorials
- Improve API documentation
- Translate documentation

### 4. 🔧 Add New Methods
RAGBench welcomes new:
- **Embedding methods** (Layer 3)
- **Chunking strategies** (Layer 2)
- **Retrieval methods** (Layer 5)
- **Reranking methods** (Layer 6)
- **Vector stores** (Layer 4)
- **Document connectors** (Layer 1)
- **LLM providers** (Layer 7)

See [Adding New Methods](#adding-new-methods) below.

### 5. 🧪 Write Tests
- Add unit tests for new features
- Improve test coverage
- Add integration tests
- Add benchmarks

## Development Workflow

### 1. Code Style

We use:
- **Black** for code formatting
- **isort** for import sorting
- **flake8** for linting
- **mypy** for type checking (optional)

```bash
# Format code
black .
isort .

# Lint
flake8 .

# Type check (optional)
mypy ragbench
```

### 2. Testing

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_embedding.py

# Run with coverage
pytest --cov=ragbench --cov-report=html

# Run live tests (requires API keys)
RAGBENCH_TEST_LIVE=1 pytest
```

### 3. Commit Messages

Format: `<type>: <description>`

**Types:**
- `feat`: New feature
- `fix`: Bug fix  
- `docs`: Documentation
- `style`: Formatting
- `refactor`: Code restructuring
- `test`: Tests
- `chore`: Maintenance

**Examples:**
```
feat: add voyage-3 embedder
fix: handle empty chunks in retrieval
docs: update installation guide
```

## Adding New Methods

### Add an Embedding Method

1. **Create method file** in `ragbench/embedding/methods/`:

```python
# embedding/methods/my_embedder.py
from typing import List
from ragbench.embedding.base import BaseEmbedder

class MyEmbedder(BaseEmbedder):
    """My custom embedder."""
    
    def __init__(self, model: str = "default", **kwargs):
        super().__init__(**kwargs)
        self.model = model
        # Initialize your model here
    
    def embed_texts(self, texts: List[str]) -> List[List[float]]:
        """Embed texts and return vectors."""
        # Your embedding logic here
        return embeddings
    
    @classmethod
    def is_available(cls) -> bool:
        """Check if dependencies are installed."""
        try:
            import my_package
            return True
        except ImportError:
            return False
```

2. **Register the method** in `embedding/__init__.py`:

```python
REGISTRY.register(
    name="my_embedder",
    class_ref=MyEmbedder,
    category="custom",
    tags=["local", "fast"],
    description="My custom embedding method"
)
```

3. **Write tests** in `tests/test_embedding_methods.py`:

```python
def test_my_embedder():
    embedder = Embedder("my_embedder")
    vectors = embedder.embed(["hello", "world"])
    assert len(vectors) == 2
    assert len(vectors[0].vector) > 0
```

4. **Update documentation** in `docs/layers/03-embedding.md`

### Add a Chunking Method

Similar process in `chunking/methods/` directory.

### Add a Retrieval Method

Similar process in `retrieval/methods/` directory.

## Pull Request Guidelines

### Before Submitting

```bash
# Format code
black .
isort .

# Run linter
flake8 .

# Run tests
pytest

# Type check (optional)
mypy ragbench
```

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement

## Testing
Describe testing performed

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] All tests pass
```

## Code of Conduct

- Be respectful and inclusive
- Welcome newcomers
- Focus on what's best for the community
- Show empathy towards others

## Questions?

- **GitHub Issues**: [Report bugs or request features](https://github.com/Omkar-S-Kulkarni/ragbench/issues)
- **Discussions**: [Ask questions](https://github.com/Omkar-S-Kulkarni/ragbench/discussions)
- **Email**: Contact maintainers

## Detailed Guides

For more detailed information, see the [Comprehensive Guide - Part 2](../guides/COMPREHENSIVE_GUIDE_PART2.md#18-contributing-to-ragbench) for complete contributing documentation.

---

**Thank you for contributing to RAGBench!** 🎉
