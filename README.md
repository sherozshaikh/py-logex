# py_logex

**Simple, powerful logging with built-in exception handling and YAML configuration**

`py_logex` is a lightweight wrapper around [loguru](https://github.com/Delgan/loguru) that adds YAML-based configuration and enhanced exception handling, making it perfect for production environments.

## ✨ Features

- 🚀 **Zero Configuration** - Works out of the box with sensible defaults
- 📝 **YAML Configuration** - Flexible, easy-to-read configuration files
- 🔍 **Enhanced Exception Logging** - See exactly where errors occur (file, line, function)
- 🎯 **Multiple Loggers** - Different log files for different components
- 🔄 **Auto-Discovery** - Finds config files automatically
- 💡 **Simple API** - Just like loguru, but easier
- 🛠️ **CLI Tools** - Manage configurations from command line

## 📦 Installation

```bash
pip install py-logex
```

## 🚀 Quick Start

### Basic Usage

```python
from py_logex import logger

logger.info("Application started")
logger.debug("Debug information")
logger.warning("Warning message")
logger.error("Error occurred")

# Exception handling with full traceback
try:
    result = 10 / 0
except Exception as e:
    logger.exception(e)
```

That's it! `py_logex` automatically creates a `logging.yaml` config file and starts logging.

### Multiple Loggers

```python
from py_logex import get_logger

app_logger = get_logger()           # Logs to app.log
db_logger = get_logger("database")  # Logs to database.log
api_logger = get_logger("api")      # Logs to api.log

app_logger.info("Application started")
db_logger.debug("Database query executed")
api_logger.warning("API rate limit approaching")
```

## ⚙️ Configuration

### Automatic Configuration

When you first import `py_logex`, it automatically creates `logging.yaml` in your project root with sensible defaults:

```yaml
defaults:
  level: "INFO"
  rotation: "500 MB"
  retention: "10 days"
  compression: "zip"
  format: "<green>{time:YYYY-MM-DD HH:mm:ss}</green> | <level>{level: <8}</level> | <cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - <level>{message}</level>"
  console:
    enabled: true
    colorize: true

logger:
  file: "app.log"
  level: "INFO"
```

### Custom Configuration

Create your own `logging.yaml`:

```yaml
defaults:
  level: "DEBUG"
  rotation: "100 MB"
  retention: "7 days"

logger:
  file: "myapp.log"
  level: "INFO"

# Named loggers
loggers:
  database:
    file: "database.log"
    level: "DEBUG"
    rotation: "50 MB"
  
  api:
    file: "api.log"
    level: "WARNING"
    console:
      enabled: false  # Disable console output for this logger
```

### Configuration Discovery

`py_logex` finds your configuration automatically using this priority:

1. **Environment Variable**: `PYLOGEX_CONFIG=/path/to/logging.yaml`
2. **Walk Up**: Searches parent directories for `logging.yaml`
3. **Common Locations**: `./config/logging.yaml`, `./src/config/logging.yaml`, etc.
4. **Create Default**: Creates `logging.yaml` in current directory

## 🎯 Exception Handling

`py_logex` makes debugging production errors easy:

```python
try:
    data = process_data(input_file)
except Exception as e:
    logger.exception(e)
```

**Output in log file:**
```
2024-12-26 10:30:45 | ERROR    | main.py:process_data:23 - ValueError: Invalid input
Location: main.py:process_data:23
Code: data = int(value)

Traceback (most recent call last):
  File "/app/main.py", line 23, in process_data
    data = int(value)
ValueError: invalid literal for int() with base 10: 'abc'
```

## 🛠️ CLI Tools

`py_logex` includes command-line tools for managing configurations:

```bash
# Show current configuration
py_logex config show

# Create new configuration file
py_logex config init

# Create with custom name
py_logex config init -n myapp

# Validate configuration
py_logex config validate

# Show version
py_logex version
```

## 📚 Advanced Usage

### Environment-Specific Configuration

```python
import os
from pathlib import Path
from py_logex import get_logger

env = os.environ.get("ENVIRONMENT", "development")
config_file = f"./config/{env}-logging.yaml"

logger = get_logger(config_path=Path(config_file))
logger.info(f"Running in {env} mode")
```

### Reconfiguring at Runtime

```python
from py_logex import logger

logger.info("Using original config")

# Switch to different config
logger.set_config(Path("./new_config.yaml"))
logger.info("Using new config")
```

### Docker/Production Setup

```dockerfile
# Dockerfile
ENV PYLOGEX_CONFIG=/app/config/production-logging.yaml
```

```python
# app.py - no changes needed!
from py_logex import logger

logger.info("Production logging configured automatically")
```

## 🤝 Why py_logex?

| Feature | Standard Logging | loguru | py_logex |
|---------|-----------------|--------|-------|
| Simple API | ❌ | ✅ | ✅ |
| YAML Config | ❌ | ❌ | ✅ |
| Auto-discovery | ❌ | ❌ | ✅ |
| Exception Details | ❌ | ⚠️ | ✅ |
| Multiple Loggers | ⚠️ | ⚠️ | ✅ |
| Zero Config | ❌ | ⚠️ | ✅ |

## 📖 Examples

Check out the [examples](examples/) directory for more:

- [basic_usage.py](examples/basic_usage.py) - Simple logging examples
- [multiple_loggers.py](examples/multiple_loggers.py) - Using multiple loggers
- [custom_config.py](examples/custom_config.py) - Custom configurations

## 🧪 Testing

```bash
# Install development dependencies
pip install -e ".[dev]"

# Run tests
pytest

# Run tests with coverage
pytest --cov=py_logex --cov-report=html

# Run specific test file
pytest tests/test_logger.py
```

## 📝 Requirements

- Python >= 3.8
- loguru >= 0.7.0
- pyyaml >= 6.0

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 🐛 Bug Reports

Please report bugs on the [GitHub Issues](https://github.com/sherozshaikh/py_logex/issues) page.

## ⭐ Show Your Support

If you find this project useful, please consider giving it a star on GitHub!

---

Made with ❤️ for the Python community
