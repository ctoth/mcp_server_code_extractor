[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/ctoth-mcp-server-code-extractor-badge.png)](https://mseep.ai/app/ctoth-mcp-server-code-extractor)

# MCP Server Code Extractor

A Model Context Protocol (MCP) server that provides precise code extraction tools using tree-sitter parsing. Extract functions, classes, and code snippets from 30+ programming languages without manual parsing.

## Why MCP Server Code Extractor?

When working with AI coding assistants like Claude, you often need to:
- Extract specific functions or classes from large codebases
- Get an overview of what's in a file without reading the entire thing
- Retrieve precise code snippets with accurate line numbers
- Avoid manual parsing and grep/sed/awk gymnastics

MCP Server Code Extractor solves these problems by providing structured, tree-sitter-powered code extraction tools directly within your AI assistant.

## Features

- **🎯 Precise Extraction**: Uses tree-sitter parsing for accurate code boundary detection
- **🔍 Semantic Search**: Search for function calls and code patterns across files and directories
- **🌍 30+ Languages**: Supports Python, JavaScript, TypeScript, Go, Rust, Java, C/C++, and many more
- **📍 Line Numbers**: Every extraction includes precise line number information
- **🗂️ Directory Search**: Search entire codebases with file pattern filtering and exclusions
- **📊 Depth Control**: Extract at different levels (top-level only, classes+methods, everything)
- **🌐 URL Support**: Fetch and extract code from GitHub, GitLab, and direct file URLs
- **🔄 Git Integration**: Extract code from any git revision, branch, or tag
- **⚡ Fast & Lightweight**: Efficient caching and minimal dependencies
- **🤖 AI-Optimized**: Designed specifically for use with AI coding assistants

## Installation

### Quick Start with uvx (Recommended)

```bash
# Install and run directly with uvx
uvx mcp-server-code-extractor
```

### Alternative Installation Methods

#### Using UV
```bash
# Install UV if you haven't already
curl -LsSf https://astral.sh/uv/install.sh | sh

# Run as package with UV
uv run mcp-server-code-extractor
```

#### Using pip
```bash
pip install mcp-server-code-extractor
mcp-server-code-extractor
```

#### Development Installation
```bash
# Clone this repository
git clone https://github.com/ctoth/mcp_server_code_extractor
cd mcp_server_code_extractor

# Install development dependencies
uv add --dev pytest black flake8 mypy

# Run as Python module
uv run python -m code_extractor
```

### Configure with Claude Desktop

Add to your Claude Desktop configuration:

#### Using uvx (Recommended)
```json
{
  "mcpServers": {
    "mcp-server-code-extractor": {
      "command": "uvx",
      "args": ["mcp-server-code-extractor"]
    }
  }
}
```

#### Using UV
```json
{
  "mcpServers": {
    "mcp-server-code-extractor": {
      "command": "uv",
      "args": ["run", "mcp-server-code-extractor"]
    }
  }
}
```

#### Using pip installation
```json
{
  "mcpServers": {
    "mcp-server-code-extractor": {
      "command": "mcp-server-code-extractor"
    }
  }
}
```

### Testing with MCP Inspector

```bash
# Test the server with MCP Inspector
npx @modelcontextprotocol/inspector uvx mcp-server-code-extractor

# Or with other installation methods
npx @modelcontextprotocol/inspector uv run mcp-server-code-extractor
npx @modelcontextprotocol/inspector mcp-server-code-extractor
```

## Available Tools

### 1. `get_symbols` - Discover Code Structure
List all functions, classes, and other symbols in a file with depth control.

```
Parameters:
- path_or_url: Path to source file or URL
- git_revision: Optional git revision (branch, tag, commit)
- depth: Symbol extraction depth (0=everything, 1=top-level only, 2=classes+methods)

Returns:
- name: Symbol name
- type: function/class/method/etc
- start_line/end_line: Line numbers
- preview: First line of the symbol
- parent: Parent class name (for methods)
```

### 2. `search_code` - Semantic Code Search
Search for code patterns using tree-sitter parsing. Supports both single-file and directory-wide searches.

```
Parameters:
- search_type: Type of search ("function-calls")
- target: What to search for (e.g., "requests.get", "logger.error", "validateData")
- scope: File path, directory path, or URL to search in
- language: Programming language (auto-detected if not specified)
- git_revision: Optional git revision (commit, branch, tag) - not supported for URLs
- max_results: Maximum number of results to return (default: 100)
- include_context: Include surrounding code lines for context (default: true)
- file_patterns: File patterns for directory search (e.g., ["*.py", "*.js"])
- exclude_patterns: File patterns to exclude (e.g., ["*.pyc", "node_modules/*"])
- max_files: Maximum number of files to search in directory mode (default: 1000)
- follow_symlinks: Whether to follow symbolic links in directory search (default: false)

Returns:
- file_path: Path to file containing the match
- start_line/end_line: Line numbers of the match
- match_text: The matching code
- context_before/context_after: Surrounding code lines
- language: Detected programming language
- metadata: Additional search information
```

### 3. `get_function` - Extract Complete Functions
Extract a complete function with all its code.

```
Parameters:
- path_or_url: Path to source file or URL
- function_name: Name of the function to extract
- git_revision: Optional git revision (branch, tag, commit)

Returns:
- code: Complete function code
- start_line/end_line: Precise boundaries
- language: Detected language
```

### 4. `get_class` - Extract Complete Classes
Extract an entire class definition including all methods.

```
Parameters:
- path_or_url: Path to source file or URL
- class_name: Name of the class to extract
- git_revision: Optional git revision (branch, tag, commit)

Returns:
- code: Complete class code
- start_line/end_line: Precise boundaries
- language: Detected language
```

### 5. `get_lines` - Extract Specific Line Ranges
Get exact line ranges when you know the line numbers.

```
Parameters:
- path_or_url: Path to source file or URL
- start_line: Starting line (1-based)
- end_line: Ending line (inclusive)
- git_revision: Optional git revision (branch, tag, commit)

Returns:
- code: Extracted lines
- line numbers and metadata
```

### 6. `get_signature` - Get Function Signatures
Quickly get just the function signature without the body.

```
Parameters:
- path_or_url: Path to source file or URL
- function_name: Name of the function
- git_revision: Optional git revision (branch, tag, commit)

Returns:
- signature: Function signature only
- start_line: Where the function starts
```

## Usage Examples

### Example 1: Exploring Local Files

```python
# First, see what's in the file
symbols = get_symbols("src/main.py")
# Returns: List of all functions and classes with line numbers

# Extract a specific function
result = get_function("src/main.py", "process_data")
# Returns: Complete function code with line numbers

# Get just a function signature
sig = get_signature("src/main.py", "process_data")
# Returns: "def process_data(input_file: str, output_dir: Path) -> Dict[str, Any]:"
```

### Example 2: Working with URLs and Git Revisions

```python
# Explore a GitHub file (current version)
symbols = get_symbols("https://raw.githubusercontent.com/user/repo/main/src/api.py")

# Extract function from GitLab
result = get_function("https://gitlab.com/user/project/-/raw/main/utils.py", "helper_func")

# Work with git revisions (local files only)
symbols_old = get_symbols("src/api.py", git_revision="HEAD~1")
function_from_branch = get_function("src/utils.py", "helper_func", git_revision="feature-branch")
class_from_tag = get_class("src/models.py", "User", git_revision="v1.0.0")

# Get lines from any URL
lines = get_lines("https://example.com/code/script.py", 10, 25)
```

### Example 3: Progressive Code Discovery

```python
# 1. Start with overview - just see the main structure
overview = get_symbols("models/user.py", depth=1)
# Shows: class User, class Admin, def create_user, etc.

# 2. Explore a specific class and its methods
class_methods = get_symbols("models/user.py", depth=2)
# Shows: class User with its methods like __init__, validate, save

# 3. Extract the full class when you need implementation details
user_class = get_class("models/user.py", "User")
# Returns: Complete User class with all methods

# 4. Or get just a specific method signature for quick reference
init_sig = get_signature("models/user.py", "__init__")
# Returns: "def __init__(self, username: str, email: str, **kwargs):"

# 5. Extract specific lines when you know exactly what you need
lines = get_lines("models/user.py", 10, 25)
# Returns: Lines 10-25 of the file
```

### Example 4: Semantic Code Search

```python
# Search for specific function calls in a single file
results = search_code(
    search_type="function-calls",
    target="requests.get",
    scope="src/api.py"
)
# Returns: All requests.get() calls with line numbers and context

# Search across an entire directory
results = search_code(
    search_type="function-calls", 
    target="logger.error",
    scope="src/",
    file_patterns=["*.py"],
    exclude_patterns=["test_*", "__pycache__/*"]
)
# Returns: All logger.error() calls across Python files, excluding tests

# Cross-language search in frontend code
results = search_code(
    search_type="function-calls",
    target="fetchData", 
    scope="frontend/",
    file_patterns=["*.js", "*.ts", "*.jsx"],
    max_results=50
)
# Returns: All fetchData() calls in JavaScript/TypeScript files
```

### Example 5: Multi-Language Support

```javascript
// Works with JavaScript/TypeScript
symbols = get_symbols("app.ts")
func = get_function("app.ts", "handleRequest")
```

```go
// Works with Go
symbols = get_symbols("main.go")
method = get_function("main.go", "ServeHTTP")
```

## Supported Languages

- Python, JavaScript, TypeScript, JSX/TSX
- Go, Rust, C, C++, C#, Java
- Ruby, PHP, Swift, Kotlin, Scala
- Bash, PowerShell, SQL
- Haskell, OCaml, Elixir, Clojure
- And many more...

## Best Practices

### Progressive Discovery Workflow
1. **Start with `search_code`** to find relevant functions and patterns across the codebase
2. **Use `get_symbols`** with `depth=1` to see file structure of interesting files
3. **Use depth control** - `depth=2` for classes+methods, `depth=0` for everything
4. **Extract specific items** with `get_function/get_class` for implementation details
5. **Use `get_signature`** for quick API exploration without full code
6. **Use `get_lines`** when you know exact line numbers

### Semantic Search Tips
- Use **directory search** to find patterns across your entire codebase
- Apply **file patterns** to focus on specific languages or file types
- Use **exclusion patterns** to skip test files, build artifacts, and dependencies
- Set appropriate **max_results** and **max_files** limits for large codebases
- Enable **context** to understand the surrounding code

### Git Integration Tips
- Use git revisions to compare implementations across versions
- Extract from feature branches to review changes
- Use tags to get stable API versions

### URL Usage
- GitHub/GitLab URLs work great for exploring open source code
- Combine with local git revisions for comprehensive analysis
- Note: git revisions only work with local files, not URLs

## Advantages Over Traditional Tools

**Traditional file reading:**
- Reads entire files (inefficient for large files)
- Requires manual parsing to find functions/classes
- Manual line counting for extraction
- Complex syntax edge cases

**MCP Server Code Extractor:**
- ✅ Extracts exactly what you need
- ✅ Provides structured data with metadata
- ✅ Handles complex syntax automatically
- ✅ Works across 30+ languages consistently
- ✅ Depth control for efficient exploration
- ✅ Git integration for version comparison

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see LICENSE file for details.

## Acknowledgments

- Built on [tree-sitter](https://tree-sitter.github.io/) for robust parsing
- Uses [tree-sitter-languages](https://github.com/grantjenks/py-tree-sitter-languages) for language support
- Implements the [Model Context Protocol](https://modelcontextprotocol.io/) specification
