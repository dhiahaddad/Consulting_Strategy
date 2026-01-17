# Testing Guide for Research Software

*A comprehensive guide to testing strategies for academic research code*

---

## Why Test Research Code?

### Common Misconceptions
- ❌ "My code only runs once, no need to test"
- ❌ "Testing is for software engineers, not researchers"
- ❌ "I don't have time to write tests"
- ❌ "My code is too complex to test"

### The Reality
- ✅ **Catch bugs before publication** - Avoid retractions and corrections
- ✅ **Reproducibility** - Tests document expected behavior
- ✅ **Confidence in results** - Sleep better knowing your analysis is correct
- ✅ **Refactoring safety** - Change code without breaking results
- ✅ **Documentation** - Tests show how code should be used
- ✅ **Collaboration** - Others can contribute safely
- ✅ **Career advancement** - Professional code quality

### Real Research Failures Due to Lack of Testing
- Retracted papers due to data processing bugs
- Protein structure predictions invalidated by sign errors
- Climate models with unit conversion errors
- Gene sequencing pipelines with off-by-one errors
- Economic models with incorrect formulas

**Testing saves careers and reputations!**

---

## Types of Tests

### The Testing Pyramid

```
        /\
       /  \        ← Few, slow, expensive
      /E2E \       End-to-End Tests
     /______\
    /        \
   /Integration\   ← Some, moderate speed
  /____________\
 /              \
/   Unit Tests   \ ← Many, fast, cheap
/__________________\
```

---

## 1. Unit Tests

### What Are Unit Tests?

Test individual functions or methods in isolation.

**Characteristics**:
- Test single function/method
- Fast to run (milliseconds)
- No external dependencies
- Easy to write and maintain

### Example: Testing a Simple Function

```python
# src/analysis.py
def calculate_mean(values):
    """Calculate arithmetic mean of values."""
    if not values:
        raise ValueError("Cannot calculate mean of empty list")
    return sum(values) / len(values)

def calculate_std(values):
    """Calculate standard deviation."""
    if len(values) < 2:
        raise ValueError("Need at least 2 values for std")
    mean = calculate_mean(values)
    variance = sum((x - mean) ** 2 for x in values) / (len(values) - 1)
    return variance ** 0.5
```

```python
# tests/test_analysis.py
import pytest
from src.analysis import calculate_mean, calculate_std

class TestCalculateMean:
    """Test suite for calculate_mean function."""
    
    def test_mean_simple(self):
        """Test mean with simple values."""
        assert calculate_mean([1, 2, 3, 4, 5]) == 3.0
    
    def test_mean_single_value(self):
        """Test mean with single value."""
        assert calculate_mean([42]) == 42
    
    def test_mean_negative_values(self):
        """Test mean with negative values."""
        assert calculate_mean([-1, -2, -3]) == -2.0
    
    def test_mean_empty_list(self):
        """Test that empty list raises ValueError."""
        with pytest.raises(ValueError, match="empty list"):
            calculate_mean([])
    
    def test_mean_floats(self):
        """Test mean with floating point values."""
        result = calculate_mean([1.5, 2.5, 3.5])
        assert abs(result - 2.5) < 1e-10  # Float comparison

class TestCalculateStd:
    """Test suite for calculate_std function."""
    
    def test_std_simple(self):
        """Test std with known values."""
        # [2, 4, 4, 4, 5, 5, 7, 9] has std = 2.0
        result = calculate_std([2, 4, 4, 4, 5, 5, 7, 9])
        assert abs(result - 2.0) < 0.01
    
    def test_std_insufficient_data(self):
        """Test that single value raises error."""
        with pytest.raises(ValueError, match="at least 2"):
            calculate_std([1])
```

### When to Write Unit Tests

✅ **Always test**:
- Mathematical calculations
- Data transformations
- Utility functions
- Algorithm implementations
- Edge cases (empty, zero, negative, very large)

❌ **Don't need unit tests for**:
- Simple getters/setters
- One-line pass-throughs
- Configuration files

---

## 2. Integration Tests

### What Are Integration Tests?

Test how multiple components work together.

**Characteristics**:
- Test multiple units working together
- May involve file I/O, databases
- Slower than unit tests
- Test realistic scenarios

### Example: Testing Data Pipeline

```python
# src/pipeline.py
import pandas as pd
from pathlib import Path

def load_data(filepath):
    """Load CSV data."""
    return pd.read_csv(filepath)

def clean_data(df):
    """Remove missing values and outliers."""
    df = df.dropna()
    # Remove outliers (>3 std from mean)
    for col in df.select_dtypes(include='number').columns:
        mean = df[col].mean()
        std = df[col].std()
        df = df[abs(df[col] - mean) <= 3 * std]
    return df

def analyze_data(df):
    """Perform statistical analysis."""
    return {
        'mean': df.mean().to_dict(),
        'std': df.std().to_dict(),
        'count': len(df)
    }

def run_pipeline(input_path, output_path):
    """Complete analysis pipeline."""
    df = load_data(input_path)
    df_clean = clean_data(df)
    results = analyze_data(df_clean)
    
    # Save results
    pd.DataFrame([results]).to_csv(output_path, index=False)
    return results
```

```python
# tests/test_pipeline.py
import pytest
import pandas as pd
from pathlib import Path
import tempfile
from src.pipeline import load_data, clean_data, analyze_data, run_pipeline

@pytest.fixture
def sample_data():
    """Create sample dataset for testing."""
    return pd.DataFrame({
        'value1': [1, 2, 3, 4, 5],
        'value2': [10, 20, 30, 40, 50],
        'category': ['A', 'B', 'A', 'B', 'A']
    })

@pytest.fixture
def sample_csv(tmp_path, sample_data):
    """Create temporary CSV file."""
    filepath = tmp_path / "test_data.csv"
    sample_data.to_csv(filepath, index=False)
    return filepath

class TestPipeline:
    """Integration tests for complete pipeline."""
    
    def test_load_data(self, sample_csv):
        """Test data loading."""
        df = load_data(sample_csv)
        assert len(df) == 5
        assert 'value1' in df.columns
    
    def test_clean_data(self, sample_data):
        """Test data cleaning."""
        # Add some NaN values
        dirty_data = sample_data.copy()
        dirty_data.loc[2, 'value1'] = None
        
        clean = clean_data(dirty_data)
        assert len(clean) == 4  # One row removed
        assert not clean.isna().any().any()
    
    def test_analyze_data(self, sample_data):
        """Test analysis function."""
        results = analyze_data(sample_data)
        assert 'mean' in results
        assert 'std' in results
        assert results['count'] == 5
        assert results['mean']['value1'] == 3.0
    
    def test_full_pipeline(self, sample_csv, tmp_path):
        """Test complete pipeline end-to-end."""
        output_path = tmp_path / "results.csv"
        
        results = run_pipeline(sample_csv, output_path)
        
        # Check results
        assert results is not None
        assert results['count'] > 0
        
        # Check output file exists
        assert output_path.exists()
        
        # Verify output content
        output_df = pd.read_csv(output_path)
        assert len(output_df) == 1
```

---

## 3. Functional Tests

### What Are Functional Tests?

Test complete features from user perspective.

### Example: Testing Analysis Script

```python
# tests/test_functional.py
import subprocess
import pytest
from pathlib import Path

def test_analysis_script_success(tmp_path, sample_csv):
    """Test that analysis script runs successfully."""
    output = tmp_path / "output.csv"
    
    # Run the script as a user would
    result = subprocess.run(
        ['python', 'scripts/analyze.py', 
         '--input', str(sample_csv),
         '--output', str(output)],
        capture_output=True,
        text=True
    )
    
    # Check success
    assert result.returncode == 0
    assert output.exists()
    assert "Analysis complete" in result.stdout

def test_analysis_script_missing_input():
    """Test script handles missing input gracefully."""
    result = subprocess.run(
        ['python', 'scripts/analyze.py', 
         '--input', 'nonexistent.csv'],
        capture_output=True,
        text=True
    )
    
    assert result.returncode != 0
    assert "File not found" in result.stderr
```

---

## 4. Regression Tests

### What Are Regression Tests?

Ensure changes don't break existing functionality.

**Strategy**: Save known-good results and compare against them.

### Example: Testing Scientific Calculations

```python
# tests/test_regression.py
import numpy as np
import pytest
from src.simulation import run_simulation

@pytest.fixture
def reference_results():
    """Load reference results from previous verified run."""
    return np.load('tests/data/reference_results.npy')

def test_simulation_matches_reference(reference_results):
    """Ensure simulation produces same results as before."""
    # Use fixed random seed for reproducibility
    np.random.seed(42)
    
    results = run_simulation(
        n_particles=100,
        n_steps=1000,
        dt=0.01
    )
    
    # Results should match reference within tolerance
    np.testing.assert_allclose(
        results, 
        reference_results,
        rtol=1e-5,  # Relative tolerance
        atol=1e-8   # Absolute tolerance
    )

def test_simulation_statistical_properties():
    """Test that simulation has expected statistical properties."""
    np.random.seed(42)
    
    results = run_simulation(
        n_particles=1000,
        n_steps=10000,
        dt=0.01
    )
    
    # Check mean is near expected value
    assert abs(np.mean(results) - 0.0) < 0.1
    
    # Check standard deviation
    assert abs(np.std(results) - 1.0) < 0.1
```

---

## 5. Property-Based Tests

### What Are Property-Based Tests?

Test properties that should always be true, using random inputs.

**Tool**: Hypothesis library

### Example: Testing Mathematical Properties

```python
# tests/test_properties.py
from hypothesis import given, strategies as st
import hypothesis.extra.numpy as npst
from src.statistics import normalize, standardize

@given(st.lists(st.floats(min_value=-1e6, max_value=1e6), min_size=1))
def test_normalize_range(values):
    """Normalized values should be between 0 and 1."""
    normalized = normalize(values)
    assert all(0 <= x <= 1 for x in normalized)

@given(st.lists(st.floats(min_value=-1e6, max_value=1e6), min_size=2))
def test_standardize_mean_std(values):
    """Standardized values should have mean≈0, std≈1."""
    standardized = standardize(values)
    mean = sum(standardized) / len(standardized)
    assert abs(mean) < 1e-10
    
    variance = sum((x - mean) ** 2 for x in standardized) / len(standardized)
    std = variance ** 0.5
    assert abs(std - 1.0) < 1e-10

@given(npst.arrays(dtype=np.float64, shape=st.integers(10, 100)))
def test_matrix_operations(matrix):
    """Test matrix operation properties."""
    # Transpose of transpose should equal original
    np.testing.assert_array_almost_equal(
        matrix,
        matrix.T.T
    )
```

---

## 6. Performance Tests

### What Are Performance Tests?

Ensure code meets performance requirements.

### Example: Benchmarking

```python
# tests/test_performance.py
import pytest
import time
import numpy as np
from src.algorithms import fast_algorithm, naive_algorithm

def test_algorithm_performance():
    """Test that optimized algorithm is faster."""
    data = np.random.rand(10000)
    
    # Benchmark naive version
    start = time.time()
    result_naive = naive_algorithm(data)
    time_naive = time.time() - start
    
    # Benchmark fast version
    start = time.time()
    result_fast = fast_algorithm(data)
    time_fast = time.time() - start
    
    # Fast should be at least 2x faster
    assert time_fast < time_naive / 2
    
    # Results should be equivalent
    np.testing.assert_allclose(result_fast, result_naive)

@pytest.mark.slow
def test_scalability():
    """Test that algorithm scales linearly."""
    times = []
    sizes = [1000, 2000, 4000, 8000]
    
    for size in sizes:
        data = np.random.rand(size)
        start = time.time()
        fast_algorithm(data)
        times.append(time.time() - start)
    
    # Should be roughly linear (within 50% margin)
    for i in range(1, len(sizes)):
        ratio = times[i] / times[i-1]
        size_ratio = sizes[i] / sizes[i-1]
        assert 0.5 * size_ratio < ratio < 1.5 * size_ratio
```

---

## 7. Statistical Tests

### Testing Stochastic Code

Research code often involves randomness - how to test it?

### Example: Testing Random Processes

```python
# tests/test_statistical.py
import numpy as np
from scipy import stats
from src.monte_carlo import run_monte_carlo

def test_monte_carlo_distribution():
    """Test that Monte Carlo produces correct distribution."""
    np.random.seed(42)
    
    # Run simulation
    results = run_monte_carlo(n_samples=10000)
    
    # Should follow normal distribution N(0, 1)
    # Use Kolmogorov-Smirnov test
    statistic, pvalue = stats.kstest(results, 'norm', args=(0, 1))
    
    # Should not reject null hypothesis (is normal)
    assert pvalue > 0.05

def test_monte_carlo_mean_convergence():
    """Test that mean converges to expected value."""
    np.random.seed(42)
    
    expected_mean = 5.0
    results = run_monte_carlo(n_samples=100000, expected_value=expected_mean)
    
    # Mean should be close to expected (law of large numbers)
    sample_mean = np.mean(results)
    
    # 99.7% confidence interval (3 sigma)
    std_error = np.std(results) / np.sqrt(len(results))
    margin = 3 * std_error
    
    assert abs(sample_mean - expected_mean) < margin

def test_random_seed_reproducibility():
    """Test that setting seed gives reproducible results."""
    # First run
    np.random.seed(42)
    results1 = run_monte_carlo(n_samples=1000)
    
    # Second run with same seed
    np.random.seed(42)
    results2 = run_monte_carlo(n_samples=1000)
    
    # Should be identical
    np.testing.assert_array_equal(results1, results2)
```

---

## Test Organization & Structure

### Directory Structure

```
project/
├── src/
│   ├── __init__.py
│   ├── analysis.py
│   ├── preprocessing.py
│   └── visualization.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py              # Shared fixtures
│   ├── test_analysis.py         # Unit tests
│   ├── test_preprocessing.py    # Unit tests
│   ├── test_pipeline.py         # Integration tests
│   ├── test_regression.py       # Regression tests
│   └── data/
│       ├── sample_input.csv     # Test data
│       └── expected_output.csv  # Reference results
├── pytest.ini                   # Pytest configuration
└── requirements-dev.txt         # Testing dependencies
```

### conftest.py - Shared Fixtures

```python
# tests/conftest.py
import pytest
import pandas as pd
import numpy as np
from pathlib import Path

@pytest.fixture
def sample_dataframe():
    """Fixture providing sample DataFrame."""
    return pd.DataFrame({
        'x': np.random.rand(100),
        'y': np.random.rand(100),
        'category': np.random.choice(['A', 'B', 'C'], 100)
    })

@pytest.fixture
def temp_output_dir(tmp_path):
    """Fixture providing temporary output directory."""
    output_dir = tmp_path / "output"
    output_dir.mkdir()
    return output_dir

@pytest.fixture(scope="session")
def test_data_dir():
    """Fixture providing path to test data directory."""
    return Path(__file__).parent / "data"
```

---

## pytest Configuration

### pytest.ini

```ini
[tool:pytest]
# Minimum Python version
minversion = 6.0

# Test discovery patterns
python_files = test_*.py
python_classes = Test*
python_functions = test_*

# Additional options
addopts = 
    --verbose
    --strict-markers
    --cov=src
    --cov-report=html
    --cov-report=term-missing

# Markers for organizing tests
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks tests as integration tests
    regression: marks tests as regression tests
    gpu: marks tests requiring GPU
    network: marks tests requiring network access

# Test paths
testpaths = tests

# Coverage settings
[coverage:run]
source = src
omit = 
    */tests/*
    */test_*.py
    */__init__.py

[coverage:report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise AssertionError
    raise NotImplementedError
    if __name__ == .__main__.:
```

---

## Testing Best Practices

### 1. AAA Pattern (Arrange-Act-Assert)

```python
def test_data_processing():
    # Arrange - Set up test data
    input_data = [1, 2, 3, 4, 5]
    expected_output = [2, 4, 6, 8, 10]
    
    # Act - Run the function
    result = double_values(input_data)
    
    # Assert - Check the result
    assert result == expected_output
```

### 2. One Assertion Per Test (Usually)

```python
# ❌ Bad - hard to debug which assertion failed
def test_statistics():
    assert mean([1, 2, 3]) == 2.0
    assert std([1, 2, 3]) > 0
    assert median([1, 2, 3]) == 2.0

# ✅ Good - clear what failed
def test_mean():
    assert mean([1, 2, 3]) == 2.0

def test_std():
    assert std([1, 2, 3]) > 0

def test_median():
    assert median([1, 2, 3]) == 2.0
```

### 3. Descriptive Test Names

```python
# ❌ Bad
def test_1():
    assert calculate(5) == 25

# ✅ Good
def test_calculate_squares_positive_integer():
    assert calculate(5) == 25
```

### 4. Test Edge Cases

```python
def test_mean_edge_cases():
    # Empty list
    with pytest.raises(ValueError):
        mean([])
    
    # Single value
    assert mean([42]) == 42
    
    # All same values
    assert mean([5, 5, 5, 5]) == 5
    
    # Very large numbers
    assert mean([1e10, 1e10]) == 1e10
    
    # Negative numbers
    assert mean([-1, -2, -3]) == -2
```

### 5. Use Fixtures for Setup

```python
@pytest.fixture
def large_dataset():
    """Generate large dataset once, reuse in tests."""
    return np.random.rand(10000, 100)

def test_processing_speed(large_dataset):
    """Test runs fast because dataset is reused."""
    result = process(large_dataset)
    assert result.shape == large_dataset.shape

def test_processing_accuracy(large_dataset):
    """Another test reuses same fixture."""
    result = process(large_dataset)
    assert np.all(np.isfinite(result))
```

### 6. Parameterize Repetitive Tests

```python
import pytest

# Instead of multiple similar tests
@pytest.mark.parametrize("input,expected", [
    ([1, 2, 3], 2.0),
    ([10, 20, 30], 20.0),
    ([0, 0, 0], 0.0),
    ([-5, 0, 5], 0.0),
])
def test_mean_values(input, expected):
    assert mean(input) == expected
```

---

## Testing NumPy/Scientific Code

### Float Comparisons

```python
import numpy as np

# ❌ Bad - exact comparison fails due to floating point
def test_calculation():
    result = 0.1 + 0.2
    assert result == 0.3  # Fails!

# ✅ Good - use approximate comparison
def test_calculation():
    result = 0.1 + 0.2
    assert abs(result - 0.3) < 1e-10

# ✅ Better - use NumPy testing
def test_calculation():
    result = 0.1 + 0.2
    np.testing.assert_almost_equal(result, 0.3)

# ✅ Best - use allclose for arrays
def test_array_calculation():
    result = complex_calculation()
    expected = np.load('expected_results.npy')
    np.testing.assert_allclose(result, expected, rtol=1e-5, atol=1e-8)
```

### Testing Array Properties

```python
def test_array_properties():
    result = generate_data()
    
    # Check shape
    assert result.shape == (100, 50)
    
    # Check dtype
    assert result.dtype == np.float64
    
    # Check no NaN or Inf
    assert np.all(np.isfinite(result))
    
    # Check range
    assert np.all(result >= 0)
    assert np.all(result <= 1)
    
    # Check statistical properties
    assert abs(np.mean(result) - 0.5) < 0.1
```

---

## Testing Pandas Code

```python
import pandas as pd
from pandas.testing import assert_frame_equal, assert_series_equal

def test_data_processing():
    # Input
    input_df = pd.DataFrame({
        'A': [1, 2, 3],
        'B': [4, 5, 6]
    })
    
    # Expected output
    expected = pd.DataFrame({
        'A': [2, 4, 6],
        'B': [8, 10, 12]
    })
    
    # Process
    result = double_dataframe(input_df)
    
    # Compare DataFrames
    assert_frame_equal(result, expected)

def test_groupby_operation():
    df = pd.DataFrame({
        'category': ['A', 'B', 'A', 'B'],
        'value': [1, 2, 3, 4]
    })
    
    result = group_and_sum(df)
    
    expected = pd.Series([4, 6], index=['A', 'B'], name='value')
    assert_series_equal(result, expected)
```

---

## Mocking External Dependencies

### When to Mock

Mock external services, APIs, slow operations, or non-deterministic behavior.

```python
# src/data_fetcher.py
import requests

def fetch_data(url):
    """Fetch data from API."""
    response = requests.get(url)
    response.raise_for_status()
    return response.json()

def process_api_data(url):
    """Fetch and process data."""
    data = fetch_data(url)
    return analyze(data)
```

```python
# tests/test_data_fetcher.py
from unittest.mock import Mock, patch
import pytest

def test_fetch_data_success():
    """Test successful API call."""
    with patch('requests.get') as mock_get:
        # Setup mock response
        mock_response = Mock()
        mock_response.json.return_value = {'key': 'value'}
        mock_response.raise_for_status.return_value = None
        mock_get.return_value = mock_response
        
        # Call function
        result = fetch_data('http://example.com/api')
        
        # Verify
        assert result == {'key': 'value'}
        mock_get.assert_called_once_with('http://example.com/api')

def test_fetch_data_error():
    """Test API error handling."""
    with patch('requests.get') as mock_get:
        # Mock an error
        mock_get.side_effect = requests.RequestException("API Error")
        
        # Should raise exception
        with pytest.raises(requests.RequestException):
            fetch_data('http://example.com/api')
```

---

## Test Coverage

### Measuring Coverage

```bash
# Install coverage tool
pip install pytest-cov

# Run tests with coverage
pytest --cov=src --cov-report=html

# View HTML report
open htmlcov/index.html
```

### Coverage Goals

- **75-80%**: Minimum for research code
- **90%+**: Good coverage
- **100%**: Usually not necessary

**Focus on**:
- Critical calculations
- Data processing pipelines
- Edge cases
- Error handling

**Don't stress about**:
- Simple getters/setters
- Print statements
- Configuration parsing

---

## Running Tests

### Basic Commands

```bash
# Run all tests
pytest

# Run specific file
pytest tests/test_analysis.py

# Run specific test
pytest tests/test_analysis.py::test_mean

# Run tests matching pattern
pytest -k "test_mean"

# Run with verbose output
pytest -v

# Stop on first failure
pytest -x

# Run last failed tests only
pytest --lf

# Show print statements
pytest -s
```

### Marking Tests

```python
import pytest

@pytest.mark.slow
def test_long_computation():
    """This test takes a long time."""
    pass

@pytest.mark.gpu
def test_cuda_computation():
    """Requires GPU."""
    pass

# Run only fast tests
# pytest -m "not slow"

# Run only GPU tests
# pytest -m gpu
```

---

## Test-Driven Development (TDD) for Research

### The TDD Cycle

1. **Write test** (it fails - Red)
2. **Write minimal code** to pass (Green)
3. **Refactor** code (Refactor)
4. Repeat

### Example: Developing a New Function

```python
# Step 1: Write the test first
def test_normalize_data():
    data = [0, 5, 10]
    result = normalize(data)
    expected = [0.0, 0.5, 1.0]
    assert result == expected

# Step 2: Write minimal implementation
def normalize(data):
    min_val = min(data)
    max_val = max(data)
    range_val = max_val - min_val
    return [(x - min_val) / range_val for x in data]

# Step 3: Test passes! Add more test cases
def test_normalize_edge_cases():
    # All same values
    assert normalize([5, 5, 5]) == [0.0, 0.0, 0.0]
    
    # Single value
    assert normalize([42]) == [0.0]

# Step 4: Refactor to handle edge cases
def normalize(data):
    if not data:
        return []
    if len(data) == 1:
        return [0.0]
    
    min_val = min(data)
    max_val = max(data)
    range_val = max_val - min_val
    
    if range_val == 0:
        return [0.0] * len(data)
    
    return [(x - min_val) / range_val for x in data]
```

---

## Common Testing Mistakes

### 1. Testing Implementation, Not Behavior

```python
# ❌ Bad - tests internal implementation
def test_uses_numpy():
    result = calculate_mean([1, 2, 3])
    assert isinstance(result, np.ndarray)

# ✅ Good - tests behavior/output
def test_mean_calculation():
    result = calculate_mean([1, 2, 3])
    assert result == 2.0
```

### 2. Tests That Don't Test Anything

```python
# ❌ Bad - always passes
def test_function_runs():
    process_data([1, 2, 3])
    # No assertion!

# ✅ Good - actually checks output
def test_process_data():
    result = process_data([1, 2, 3])
    assert len(result) == 3
    assert all(isinstance(x, float) for x in result)
```

### 3. Overly Complex Tests

```python
# ❌ Bad - test is harder to understand than the code
def test_complex():
    data = generate_random_data()
    for i in range(100):
        if i % 2 == 0:
            result = process(data[i])
            if result > 0:
                assert check_positive(result)
    # What are we even testing?

# ✅ Good - simple and clear
def test_process_positive_input():
    result = process(5)
    assert result > 0
    assert check_positive(result)
```

---

## Checklist for Testing Research Code

### Before Publishing Paper
- [ ] All core calculations have unit tests
- [ ] Integration tests for full pipeline
- [ ] Regression tests with known-good results
- [ ] Edge cases tested (empty, zero, extreme values)
- [ ] Random seeds set for reproducibility
- [ ] Test coverage > 75%
- [ ] All tests pass
- [ ] Tests documented in README
- [ ] CI/CD runs tests automatically

### For Each Function
- [ ] Test with typical input
- [ ] Test edge cases
- [ ] Test error conditions
- [ ] Test with different data types
- [ ] Document expected behavior in docstring

---

## Resources

### Testing Frameworks
- [pytest](https://docs.pytest.org/) - Most popular Python testing framework
- [unittest](https://docs.python.org/3/library/unittest.html) - Built-in Python testing
- [hypothesis](https://hypothesis.readthedocs.io/) - Property-based testing

### Learning Resources
- [Python Testing with pytest](https://pragprog.com/titles/bopytest/python-testing-with-pytest/) - Book
- [Real Python Testing Guide](https://realpython.com/python-testing/)
- [Testing Scientific Code](https://carpentries-incubator.github.io/python-testing/)

### Tools
- [pytest-cov](https://pytest-cov.readthedocs.io/) - Coverage plugin
- [pytest-xdist](https://pytest-xdist.readthedocs.io/) - Parallel testing
- [tox](https://tox.readthedocs.io/) - Test multiple environments

---

## Summary

**Start simple**:
1. Write a few unit tests for critical functions
2. Add integration test for main pipeline
3. Set up CI to run tests automatically
4. Gradually increase coverage

**Focus on**:
- Mathematical correctness
- Edge cases
- Reproducibility
- Regression prevention

**Remember**: Even basic testing is infinitely better than no testing. Start small and build up!

**The goal**: Confidence that your published results are correct and reproducible.
