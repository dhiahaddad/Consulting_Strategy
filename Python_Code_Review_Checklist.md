# Python Code Review Checklist

**Client**: _________________________________  
**Date**: _________________________________  
**Files Reviewed**: _________________________________

---

## How to Use This Checklist

1. **Prioritize**: Focus on High Priority items first, especially for time-limited sessions
2. **Context matters**: Adjust recommendations based on client's skill level and project needs
3. **Be constructive**: Frame issues as learning opportunities
4. **Pick battles**: Don't overwhelm with too many changes at once
5. **Celebrate strengths**: Always start with what they're doing well

**Priority Levels:**
- 🔴 **Critical**: Bugs, security issues, data loss risks
- 🟠 **High**: Performance problems, maintainability issues, reproducibility concerns
- 🟡 **Medium**: Code quality, readability, best practices
- 🟢 **Low**: Style preferences, minor optimizations

---

## 1. First Impressions & Structure

### Project Organization 🟡
- [ ] Is there a clear entry point (main script)?
- [ ] Is the directory structure logical?
  ```
  project/
  ├── README.md
  ├── requirements.txt (or environment.yml)
  ├── src/ (or project_name/)
  │   ├── __init__.py
  │   ├── main.py
  │   └── modules/
  ├── tests/
  ├── data/ (if applicable)
  └── docs/
  ```
- [ ] Are data, code, and outputs separated?
- [ ] Is there a README with basic information?

**Notes:**




### Dependencies 🟠
- [ ] Are dependencies documented?
  - [ ] `requirements.txt` for pip
  - [ ] `environment.yml` for conda
  - [ ] Versions specified?
- [ ] Are dependencies appropriate (not over-complicated)?
- [ ] Any unnecessary dependencies?

**Notes:**




---

## 2. Code Quality & Correctness

### Bugs & Logic Errors 🔴
- [ ] Run the code - does it execute without errors?
- [ ] Check edge cases:
  - Empty inputs
  - Null/None values
  - Zero or negative numbers where unexpected
  - Very large inputs
- [ ] Off-by-one errors in loops or indexing?
- [ ] Type mismatches or assumptions?
- [ ] Uncaught exceptions that could crash the program?

**Issues found:**




### Error Handling 🟠
- [ ] Are try/except blocks used appropriately?
- [ ] Are exceptions too broad? (avoid bare `except:`)
  ```python
  # ❌ Bad
  try:
      risky_operation()
  except:
      pass
  
  # ✅ Good
  try:
      risky_operation()
  except FileNotFoundError as e:
      logger.error(f"File not found: {e}")
      raise
  ```
- [ ] Are errors logged or reported helpfully?
- [ ] Are exceptions re-raised when appropriate?

**Notes:**




### Data Handling 🔴
- [ ] Input validation present?
- [ ] Data type assumptions verified?
- [ ] Risk of data loss or corruption?
- [ ] File operations properly closed (use context managers)?
  ```python
  # ✅ Good - file automatically closed
  with open('data.txt', 'r') as f:
      data = f.read()
  ```
- [ ] Large files handled efficiently (streaming vs loading all)?

**Notes:**




---

## 3. Performance & Efficiency

### Algorithmic Efficiency 🟠
- [ ] Obvious inefficiencies?
  - Nested loops that could be avoided
  - Repeated calculations
  - Searching unsorted data
- [ ] Appropriate data structures?
  - Lists vs sets vs dicts
  - Pandas DataFrames for tabular data
  - NumPy arrays for numerical operations
- [ ] Vectorization opportunities?
  ```python
  # ❌ Slow - Python loop
  result = [x * 2 for x in large_list]
  
  # ✅ Fast - NumPy vectorization
  result = large_array * 2
  ```

**Performance issues:**




### Memory Usage 🟠
- [ ] Unnecessary data copies?
- [ ] Large data loaded into memory when could stream?
- [ ] Variables/objects deleted when no longer needed?
- [ ] Generator expressions used where appropriate?
  ```python
  # ❌ Memory intensive
  sum([x**2 for x in range(1000000)])
  
  # ✅ Memory efficient
  sum(x**2 for x in range(1000000))
  ```

**Notes:**




### I/O Operations 🟡
- [ ] File operations efficient (batch reads/writes)?
- [ ] Unnecessary disk I/O?
- [ ] Caching opportunities for repeated reads?

**Notes:**




---

## 4. Code Readability & Maintainability

### Naming Conventions 🟡
- [ ] Variable names descriptive?
  ```python
  # ❌ Bad
  x = 42
  d = datetime.now()
  
  # ✅ Good
  max_iterations = 42
  analysis_timestamp = datetime.now()
  ```
- [ ] Functions named as verbs (actions)?
- [ ] Classes named as nouns?
- [ ] Constants in UPPER_CASE?
- [ ] Following PEP 8 naming conventions?
  - `snake_case` for functions and variables
  - `PascalCase` for classes
  - `_private` for internal functions

**Naming issues:**




### Function Design 🟠
- [ ] Functions have single, clear purpose?
- [ ] Function length reasonable (< 50 lines ideal)?
- [ ] Too many parameters? (> 5 is a code smell)
- [ ] Parameters have sensible defaults?
- [ ] Return values consistent and documented?

**Refactoring opportunities:**




### Code Duplication 🟡
- [ ] Repeated code blocks?
- [ ] Opportunities for helper functions?
- [ ] Copy-paste programming evident?
- [ ] Could loops/functions reduce repetition?

**DRY violations:**




### Magic Numbers & Hard-coding 🟡
- [ ] Magic numbers should be named constants?
  ```python
  # ❌ Bad
  if len(data) > 100:
      sample = data[:100]
  
  # ✅ Good
  MAX_SAMPLE_SIZE = 100
  if len(data) > MAX_SAMPLE_SIZE:
      sample = data[:MAX_SAMPLE_SIZE]
  ```
- [ ] File paths hard-coded? (should be parameters/config)
- [ ] Configuration values extracted to config file?

**Notes:**




---

## 5. Documentation

### Code Comments 🟡
- [ ] Complex logic explained?
- [ ] Comments describe "why", not "what"?
  ```python
  # ❌ Bad - describes what
  # Loop through items
  for item in items:
  
  # ✅ Good - describes why
  # Process items in batches to avoid memory overflow
  for item in items:
  ```
- [ ] Outdated comments removed?
- [ ] TODO/FIXME comments have context?

**Documentation needs:**




### Docstrings 🟠
- [ ] All functions have docstrings?
- [ ] Docstrings describe parameters and return values?
- [ ] Example usage provided for complex functions?
  ```python
  def calculate_statistics(data, method='mean'):
      """
      Calculate statistical summary of data.
      
      Parameters
      ----------
      data : array-like
          Input data to analyze
      method : str, optional
          Statistical method to use ('mean', 'median', 'mode')
          Default is 'mean'
      
      Returns
      -------
      float
          Calculated statistic
      
      Examples
      --------
      >>> calculate_statistics([1, 2, 3, 4, 5])
      3.0
      """
      pass
  ```
- [ ] Classes have docstrings explaining purpose?
- [ ] Module-level docstring present?

**Docstring improvements:**




### README & External Documentation 🟠
- [ ] README exists with:
  - [ ] Project description
  - [ ] Installation instructions
  - [ ] Usage examples
  - [ ] Dependencies
  - [ ] Contact/contribution info
- [ ] Complex algorithms explained in separate docs?
- [ ] Data formats/schemas documented?

**Documentation gaps:**




---

## 6. Python Best Practices

### Imports 🟡
- [ ] All imports at top of file?
- [ ] Imports organized (standard library, third-party, local)?
- [ ] No wildcard imports (`from module import *`)?
- [ ] No unused imports?
- [ ] Relative imports used appropriately in packages?

**Import issues:**




### Pythonic Code 🟡
- [ ] Using list/dict/set comprehensions appropriately?
- [ ] Context managers for resources (`with` statements)?
- [ ] Enumerate instead of range(len())?
  ```python
  # ❌ Un-Pythonic
  for i in range(len(items)):
      print(i, items[i])
  
  # ✅ Pythonic
  for i, item in enumerate(items):
      print(i, item)
  ```
- [ ] Using `zip` for parallel iteration?
- [ ] Using `join` for string concatenation?
- [ ] Using `in` for membership testing?

**Pythonic improvements:**




### Modern Python Features 🟡
- [ ] Using f-strings for formatting (Python 3.6+)?
  ```python
  # ❌ Old style
  print("Value: %s" % value)
  
  # ✅ Modern
  print(f"Value: {value}")
  ```
- [ ] Type hints used? (especially helpful in research code)
  ```python
  def process_data(data: list[float], threshold: float = 0.5) -> dict:
      pass
  ```
- [ ] Pathlib for file paths (instead of os.path)?
- [ ] Dataclasses for simple data structures (Python 3.7+)?

**Modernization opportunities:**




---

## 7. Testing & Validation

### Testing 🟠
- [ ] Any tests present?
- [ ] Critical functions tested?
- [ ] Test coverage reasonable for project stage?
- [ ] Tests actually run and pass?
- [ ] Edge cases tested?

**Testing recommendations:**




### Assertions & Validation 🟡
- [ ] Assertions for sanity checks?
- [ ] Input validation in functions?
  ```python
  def calculate_mean(values):
      assert len(values) > 0, "Cannot calculate mean of empty list"
      assert all(isinstance(v, (int, float)) for v in values), "All values must be numeric"
      return sum(values) / len(values)
  ```
- [ ] Intermediate results validated?

**Validation needs:**




---

## 8. Scientific/Research-Specific

### Reproducibility 🟠
- [ ] Random seeds set for stochastic processes?
  ```python
  import numpy as np
  np.random.seed(42)
  ```
- [ ] Versions of packages recorded?
- [ ] Data provenance tracked?
- [ ] Analysis parameters documented?
- [ ] Enough information to reproduce results?

**Reproducibility issues:**




### Data Analysis Patterns 🟡
- [ ] Using appropriate libraries (NumPy, Pandas, SciPy)?
- [ ] Vectorized operations instead of loops?
- [ ] Proper statistical methods applied?
- [ ] Data cleaning steps documented?
- [ ] Results saved with metadata?

**Analysis improvements:**




### Visualization 🟡
- [ ] Plots have labels (axes, title, legend)?
- [ ] Figure sizes appropriate?
- [ ] Colors accessible (colorblind-friendly)?
- [ ] Plots saved in publication-quality format?
- [ ] Visualization code separated from analysis?

**Visualization notes:**




---

## 9. Security & Safety (If Applicable)

### Security Basics 🔴
- [ ] No passwords or API keys in code?
- [ ] Sensitive data properly handled?
- [ ] User input sanitized if applicable?
- [ ] SQL queries parameterized (no injection risks)?

**Security concerns:**




---

## 10. Git & Version Control

### Version Control Usage 🟡
- [ ] Project in version control?
- [ ] Meaningful commit messages?
- [ ] `.gitignore` properly configured?
  ```gitignore
  # Python
  __pycache__/
  *.pyc
  .pytest_cache/
  
  # Data files
  *.csv
  *.h5
  data/
  
  # Environment
  venv/
  .env
  
  # IDE
  .vscode/
  .idea/
  ```
- [ ] Large data files excluded?
- [ ] Sensitive information excluded?

**Version control improvements:**




---

## Summary & Prioritization

### Strengths (Always start positive!)
1. 
2. 
3. 

### Critical Issues (Fix first) 🔴
Priority | Issue | Location | Recommendation
---------|-------|----------|---------------
1        |       |          |
2        |       |          |
3        |       |          |

### High-Priority Improvements 🟠
Priority | Issue | Location | Recommendation
---------|-------|----------|---------------
1        |       |          |
2        |       |          |
3        |       |          |

### Nice-to-Have Improvements 🟡🟢
- 
- 
- 

### Quick Wins (High impact, low effort)
1. 
2. 
3. 

### Recommended Next Steps
1. 
2. 
3. 

---

## Resources to Share

Based on this review, recommend:
- [ ] PEP 8 Style Guide
- [ ] Python testing tutorial
- [ ] Pandas best practices
- [ ] NumPy performance tips
- [ ] Git workflow guide
- [ ] Documentation guide
- [ ] Specific tool/library tutorial: _______________
- [ ] Other: _______________

---

**Review completed**: _________________________________  
**Time spent**: _________________________________  
**Follow-up needed**: ☐ Yes ☐ No

**Overall code quality**: ☐ Excellent ☐ Good ☐ Fair ☐ Needs Work

**Notes for client discussion:**
