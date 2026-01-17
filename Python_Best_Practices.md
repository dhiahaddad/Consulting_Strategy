# Python Best Practices - Quick Reference Guide

*For Research Software Engineers and Academic Researchers*

---

## 📋 Table of Contents

1. [Project Structure](#project-structure)
2. [Code Style](#code-style)
3. [Functions & Classes](#functions--classes)
4. [Data Structures](#data-structures)
5. [Error Handling](#error-handling)
6. [File Operations](#file-operations)
7. [Performance](#performance)
8. [Testing](#testing)
9. [Documentation](#documentation)
10. [Common Pitfalls](#common-pitfalls)

---

## Project Structure

### Recommended Layout
```
my_research_project/
├── README.md                 # Project overview
├── requirements.txt          # Dependencies (pip)
├── environment.yml           # Dependencies (conda)
├── setup.py                  # Package installation (optional)
├── .gitignore               # Git exclusions
├── src/                     # Or use your project name
│   ├── __init__.py
│   ├── main.py              # Entry point
│   ├── analysis.py          # Core analysis code
│   ├── preprocessing.py     # Data preparation
│   └── utils.py             # Helper functions
├── tests/                   # Unit tests
│   ├── __init__.py
│   └── test_analysis.py
├── notebooks/               # Jupyter notebooks
│   └── exploration.ipynb
├── data/                    # Data files (excluded from git)
│   ├── raw/
│   └── processed/
├── results/                 # Output files
│   ├── figures/
│   └── tables/
└── docs/                    # Documentation

```

### Essential Files

**requirements.txt**
```txt
numpy>=1.20.0
pandas>=1.3.0
matplotlib>=3.4.0
scipy>=1.7.0
```

**.gitignore**
```gitignore
# Python
__pycache__/
*.pyc
*.pyo
.pytest_cache/
*.egg-info/

# Environments
venv/
env/
.env

# Data
data/
*.csv
*.h5
*.pkl

# Results
results/
*.png
*.pdf

# IDEs
.vscode/
.idea/
*.swp
```

---

## Code Style

### Follow PEP 8

**Naming Conventions**
```python
# Variables and functions: snake_case
my_variable = 42
def calculate_mean(data):
    pass

# Classes: PascalCase
class DataProcessor:
    pass

# Constants: UPPER_SNAKE_CASE
MAX_ITERATIONS = 1000
DEFAULT_THRESHOLD = 0.05

# Private/internal: leading underscore
def _internal_helper():
    pass
```

**Line Length & Spacing**
```python
# ✅ Good: max 88-100 characters per line
result = calculate_statistics(
    data=my_dataset,
    method='robust',
    confidence_level=0.95
)

# ✅ Good: blank lines between functions
def function_one():
    pass


def function_two():
    pass
```

### Use Modern Formatting

**F-strings (Python 3.6+)**
```python
# ❌ Old
print("Result: %s, Error: %.2f" % (result, error))
print("Result: {}, Error: {:.2f}".format(result, error))

# ✅ Modern
print(f"Result: {result}, Error: {error:.2f}")

# ✅ Multi-line f-strings
message = (
    f"Processing {len(data)} samples\n"
    f"Mean: {mean:.3f}\n"
    f"Std: {std:.3f}"
)
```

---

## Functions & Classes

### Write Clear Functions

```python
# ✅ Good: single purpose, clear name, docstring
def calculate_moving_average(values: list[float], window_size: int = 3) -> list[float]:
    """
    Calculate moving average using a sliding window.
    
    Parameters
    ----------
    values : list[float]
        Input time series data
    window_size : int, default=3
        Size of the sliding window
        
    Returns
    -------
    list[float]
        Smoothed values
        
    Examples
    --------
    >>> calculate_moving_average([1, 2, 3, 4, 5], window_size=2)
    [1.5, 2.5, 3.5, 4.5]
    """
    if window_size < 1:
        raise ValueError("Window size must be positive")
    
    result = []
    for i in range(len(values) - window_size + 1):
        window = values[i:i + window_size]
        result.append(sum(window) / window_size)
    return result
```

### Function Design Principles

```python
# ❌ Bad: too many responsibilities
def process_everything(data, filename, plot=True, save=True):
    cleaned = clean_data(data)
    result = analyze_data(cleaned)
    if plot:
        make_plots(result)
    if save:
        save_results(result, filename)
    return result

# ✅ Good: separate concerns
def clean_data(data):
    """Remove missing values and outliers."""
    pass

def analyze_data(data):
    """Perform statistical analysis."""
    pass

def visualize_results(results):
    """Create publication-quality plots."""
    pass

def save_results(results, filename):
    """Save results to file."""
    pass
```

### Use Type Hints

```python
from typing import Optional, Union
import numpy as np
import pandas as pd

def load_and_process(
    filepath: str,
    columns: Optional[list[str]] = None,
    normalize: bool = True
) -> pd.DataFrame:
    """Load and preprocess data from file."""
    df = pd.read_csv(filepath)
    
    if columns:
        df = df[columns]
    
    if normalize:
        df = (df - df.mean()) / df.std()
    
    return df

def calculate_correlation(
    x: np.ndarray,
    y: np.ndarray
) -> tuple[float, float]:
    """Return (correlation, p_value)."""
    pass
```

---

## Data Structures

### Choose the Right Structure

```python
# Lists: ordered, mutable, allow duplicates
measurements = [23.1, 24.5, 22.8, 23.9]

# Tuples: ordered, immutable
coordinates = (45.5, -122.6)

# Sets: unordered, unique elements, fast membership testing
unique_ids = {101, 102, 103, 104}

# Dictionaries: key-value pairs
parameters = {
    'learning_rate': 0.01,
    'batch_size': 32,
    'epochs': 100
}
```

### Comprehensions

```python
# ✅ List comprehension
squares = [x**2 for x in range(10)]

# ✅ Dictionary comprehension
squared_dict = {x: x**2 for x in range(10)}

# ✅ Set comprehension
unique_squares = {x**2 for x in range(-10, 11)}

# ✅ With conditions
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# ❌ Too complex - use regular loop instead
result = [f(g(x, y), h(z)) for x in a for y in b for z in c if condition(x, y, z)]
```

### Iteration Patterns

```python
# ✅ Use enumerate instead of range(len())
for i, value in enumerate(data):
    print(f"Item {i}: {value}")

# ✅ Use zip for parallel iteration
names = ['Alice', 'Bob', 'Charlie']
scores = [85, 92, 88]
for name, score in zip(names, scores):
    print(f"{name}: {score}")

# ✅ Unpack directly
point = (10, 20)
x, y = point

# ✅ Use dict.items()
for key, value in my_dict.items():
    print(f"{key}: {value}")
```

---

## Error Handling

### Be Specific with Exceptions

```python
# ❌ Too broad
try:
    result = risky_operation()
except:
    pass

# ✅ Specific exception handling
try:
    with open('data.csv', 'r') as f:
        data = f.read()
except FileNotFoundError:
    print("Error: data.csv not found")
    raise
except PermissionError:
    print("Error: no permission to read file")
    raise
except Exception as e:
    print(f"Unexpected error: {e}")
    raise
```

### Validate Inputs

```python
def calculate_statistics(data: list[float]) -> dict:
    """Calculate mean and std of data."""
    
    # Validate inputs
    if not data:
        raise ValueError("Cannot calculate statistics on empty data")
    
    if not all(isinstance(x, (int, float)) for x in data):
        raise TypeError("All data must be numeric")
    
    if len(data) < 2:
        raise ValueError("Need at least 2 values for std calculation")
    
    # Perform calculation
    mean = sum(data) / len(data)
    variance = sum((x - mean)**2 for x in data) / (len(data) - 1)
    std = variance ** 0.5
    
    return {'mean': mean, 'std': std}
```

### Use Custom Exceptions

```python
class DataValidationError(Exception):
    """Raised when data validation fails."""
    pass

class InsufficientDataError(Exception):
    """Raised when not enough data for analysis."""
    pass

def analyze_experiment(data):
    if len(data) < 30:
        raise InsufficientDataError(
            f"Need at least 30 samples, got {len(data)}"
        )
```

---

## File Operations

### Always Use Context Managers

```python
# ✅ Automatic cleanup with context manager
with open('results.txt', 'w') as f:
    f.write('Results:\n')
    f.write(f'Mean: {mean}\n')

# ✅ Multiple files
with open('input.txt', 'r') as infile, open('output.txt', 'w') as outfile:
    data = infile.read()
    processed = process(data)
    outfile.write(processed)
```

### Use Pathlib

```python
from pathlib import Path

# ✅ Modern path handling
data_dir = Path('data')
input_file = data_dir / 'raw' / 'experiment_01.csv'

# Check existence
if input_file.exists():
    data = load_data(input_file)

# Create directories
output_dir = Path('results') / 'figures'
output_dir.mkdir(parents=True, exist_ok=True)

# List files
csv_files = list(data_dir.glob('*.csv'))
all_csv_files = list(data_dir.rglob('*.csv'))  # Recursive

# Get file info
print(f"Size: {input_file.stat().st_size} bytes")
print(f"Modified: {input_file.stat().st_mtime}")
```

---

## Performance

### Use NumPy for Numerical Operations

```python
import numpy as np

# ❌ Slow: Python loop
result = []
for i in range(len(data)):
    result.append(data[i] * 2 + 1)

# ✅ Fast: NumPy vectorization
data = np.array(data)
result = data * 2 + 1

# ✅ Use NumPy functions
mean = np.mean(data)
std = np.std(data)
normalized = (data - mean) / std
```

### Use Pandas for Tabular Data

```python
import pandas as pd

# ✅ Efficient operations
df = pd.read_csv('data.csv')

# Filtering
filtered = df[df['score'] > 80]

# Grouping
grouped = df.groupby('category')['value'].mean()

# Apply functions
df['normalized'] = (df['value'] - df['value'].mean()) / df['value'].std()
```

### Avoid Repeated Calculations

```python
# ❌ Bad: recalculating in loop
for item in data:
    if item > calculate_threshold(data):  # Calculated every iteration!
        process(item)

# ✅ Good: calculate once
threshold = calculate_threshold(data)
for item in data:
    if item > threshold:
        process(item)
```

### Use Generators for Large Data

```python
# ❌ Memory intensive: loads everything
def read_large_file(filename):
    with open(filename) as f:
        return [line.strip() for line in f]

# ✅ Memory efficient: yields one at a time
def read_large_file(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()

# Usage
for line in read_large_file('huge_file.txt'):
    process(line)
```

---

## Testing

### Write Unit Tests

```python
# tests/test_analysis.py
import pytest
from src.analysis import calculate_mean, calculate_std

def test_calculate_mean():
    """Test mean calculation."""
    data = [1, 2, 3, 4, 5]
    assert calculate_mean(data) == 3.0

def test_calculate_mean_empty():
    """Test mean with empty data."""
    with pytest.raises(ValueError):
        calculate_mean([])

def test_calculate_std():
    """Test standard deviation."""
    data = [2, 4, 4, 4, 5, 5, 7, 9]
    result = calculate_std(data)
    assert abs(result - 2.0) < 0.01  # Approximate comparison
```

### Run Tests

```bash
# Install pytest
pip install pytest

# Run all tests
pytest

# Run with coverage
pip install pytest-cov
pytest --cov=src tests/

# Run specific test
pytest tests/test_analysis.py::test_calculate_mean
```

### Use Assertions for Sanity Checks

```python
def process_data(data):
    """Process experimental data."""
    assert len(data) > 0, "Data cannot be empty"
    assert all(x >= 0 for x in data), "All values must be non-negative"
    
    result = perform_analysis(data)
    
    assert len(result) == len(data), "Result size mismatch"
    return result
```

---

## Documentation

### Write Good Docstrings

```python
def fit_linear_model(X, y, regularization=0.01):
    """
    Fit a linear regression model with L2 regularization.
    
    Parameters
    ----------
    X : np.ndarray, shape (n_samples, n_features)
        Training data features
    y : np.ndarray, shape (n_samples,)
        Target values
    regularization : float, default=0.01
        L2 regularization strength. Higher values mean more regularization.
    
    Returns
    -------
    weights : np.ndarray, shape (n_features,)
        Fitted model weights
    intercept : float
        Model intercept term
    
    Raises
    ------
    ValueError
        If X and y have incompatible shapes
    
    Examples
    --------
    >>> X = np.array([[1, 2], [3, 4], [5, 6]])
    >>> y = np.array([1, 2, 3])
    >>> weights, intercept = fit_linear_model(X, y)
    >>> weights.shape
    (2,)
    
    Notes
    -----
    Uses the closed-form solution:
    w = (X^T X + λI)^-1 X^T y
    
    References
    ----------
    .. [1] Hastie, T., et al. (2009). The Elements of Statistical Learning.
    """
    pass
```

### Create a Good README

Note: the example below uses MIT as a common choice; pick the license that fits your project.

````markdown
# My Research Project

Brief description of what this project does.

## Installation

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Usage

```python
from src.analysis import run_analysis

results = run_analysis('data/experiment.csv')
print(results)
```

## Project Structure

- `src/`: Source code
- `tests/`: Unit tests
- `data/`: Data files (not tracked in git)
- `notebooks/`: Jupyter notebooks for exploration

## Citation

If you use this code, please cite:
```
Author et al. (2026). Paper Title. Journal Name.
```

## License

MIT License
````

---

## Common Pitfalls

### Mutable Default Arguments

```python
# ❌ DANGEROUS: mutable default
def add_item(item, items=[]):
    items.append(item)
    return items

add_item(1)  # [1]
add_item(2)  # [1, 2] - Unexpected!

# ✅ Safe: use None
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### Modifying Lists While Iterating

```python
# ❌ Bad: modifying while iterating
numbers = [1, 2, 3, 4, 5]
for num in numbers:
    if num % 2 == 0:
        numbers.remove(num)  # Skips elements!

# ✅ Good: create new list
numbers = [1, 2, 3, 4, 5]
numbers = [num for num in numbers if num % 2 != 0]

# ✅ Also good: iterate over copy
numbers = [1, 2, 3, 4, 5]
for num in numbers[:]:  # Note the [:]
    if num % 2 == 0:
        numbers.remove(num)
```

### Floating Point Comparisons

```python
# ❌ Dangerous: direct comparison
if 0.1 + 0.2 == 0.3:  # False!
    print("Equal")

# ✅ Use tolerance
import math
if math.isclose(0.1 + 0.2, 0.3, rel_tol=1e-9):
    print("Equal")

# ✅ Or numpy
import numpy as np
if np.allclose(0.1 + 0.2, 0.3):
    print("Equal")
```

### String Concatenation in Loops

```python
# ❌ Slow: repeated concatenation
result = ""
for item in large_list:
    result += str(item) + "\n"

# ✅ Fast: join
result = "\n".join(str(item) for item in large_list)
```

### Not Using Virtual Environments

```bash
# ✅ Always use virtual environments!

# Create
python -m venv venv

# Activate
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Install packages
pip install numpy pandas matplotlib

# Save requirements
pip freeze > requirements.txt

# Deactivate when done
deactivate
```

---

## Quick Wins Checklist

Starting a new project? Do these first:

- [ ] Create virtual environment
- [ ] Set up git repository
- [ ] Create `.gitignore`
- [ ] Create `README.md`
- [ ] Create `requirements.txt`
- [ ] Set up basic project structure
- [ ] Write docstrings as you code
- [ ] Add type hints to functions
- [ ] Use f-strings for formatting
- [ ] Use pathlib for file paths
- [ ] Write at least a few basic tests
- [ ] Set random seeds for reproducibility

---

## Tools to Use

### Code Quality
```bash
# Formatting
pip install black
black src/

# Linting
pip install pylint
pylint src/

# Type checking
pip install mypy
mypy src/

# All-in-one
pip install ruff
ruff check src/
```

### Jupyter Notebooks
```bash
# Better notebook experience
pip install jupyter
pip install jupyterlab

# Export to Python
jupyter nbconvert --to python notebook.ipynb

# Clear outputs before committing
jupyter nbconvert --clear-output --inplace notebook.ipynb
```

---

## Resources

- [PEP 8 Style Guide](https://peps.python.org/pep-0008/)
- [Real Python Tutorials](https://realpython.com/)
- [Python Documentation](https://docs.python.org/3/)
- [NumPy Documentation](https://numpy.org/doc/)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [pytest Documentation](https://docs.pytest.org/)

---

**Remember**: Good code is code that others (including future you) can understand and maintain. Prioritize clarity over cleverness!
