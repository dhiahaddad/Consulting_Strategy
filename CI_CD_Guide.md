# CI/CD Guide for Research Software

*Continuous Integration and Continuous Deployment for academic research projects*

---

## What is CI/CD?

### Continuous Integration (CI)
Automatically test your code every time you push changes to ensure:
- Code runs without errors
- Tests pass
- Code style is consistent
- Dependencies are up-to-date
- Documentation builds correctly

### Continuous Deployment (CD)
Automatically deploy your application when tests pass:
- Update live demos
- Publish documentation
- Release new versions
- Deploy to hosting platforms

### Why Researchers Should Care

**Benefits for research code**:
- ✅ **Catch bugs early** - Before they affect results
- ✅ **Reproducibility** - Automated testing ensures consistency
- ✅ **Collaboration** - Confident accepting contributions
- ✅ **Documentation** - Auto-build and deploy docs
- ✅ **Quality assurance** - Maintain standards automatically
- ✅ **Time savings** - Less manual testing and deployment
- ✅ **Publication ready** - Confidence in your code

---

## Quick Decision Tree

```
What do you need?
│
├─ Just run tests on every commit
│  └─ GitHub Actions (FREE, easiest) ⭐
│
├─ Test + auto-deploy documentation
│  └─ GitHub Actions + GitHub Pages ⭐
│
├─ Test + deploy app (Streamlit, etc.)
│  └─ GitHub Actions + platform integration ⭐
│
├─ Complex workflows, multiple languages
│  └─ GitLab CI (FREE, powerful) or GitHub Actions
│
├─ Need self-hosted runners
│  └─ GitLab CI or Jenkins (institutional)
│
└─ Using university infrastructure
   └─ Check if Jenkins/GitLab available
```

---

## Platform Comparison

| Platform | Cost | Best For | Difficulty | Minutes/Month (Free) |
|----------|------|----------|------------|---------------------|
| **GitHub Actions** | Free tier | Most research projects | ⭐ Easy | 2,000 |
| **GitLab CI/CD** | Free tier | Complex pipelines | ⭐⭐ Medium | 400 |
| **Travis CI** | Limited free | Open source | ⭐⭐ Medium | Limited |
| **CircleCI** | Free tier | Professional projects | ⭐⭐ Medium | 6,000 credits |
| **Jenkins** | Free (self-host) | Institutional use | ⭐⭐⭐ Hard | Unlimited* |

*Self-hosted = your resources

---

## GitHub Actions ⭐ (Recommended)

### Why GitHub Actions for Research?

- Free for public repositories (2,000 minutes/month)
- Easy to set up (YAML in repo)
- Huge marketplace of pre-built actions
- Integrates perfectly with GitHub
- Great documentation
- Matrix builds (test multiple Python versions)

### Basic Concepts

**Workflow**: Automated process (defined in YAML)
**Job**: Set of steps that run on same runner
**Step**: Individual task (run command, use action)
**Runner**: Server that runs your workflow
**Trigger**: Event that starts workflow (push, pull request, schedule, etc.)

---

### Example 1: Run Python Tests

**File**: `.github/workflows/tests.yml`

```yaml
name: Tests

# When to run
on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    # Checkout code
    - uses: actions/checkout@v4
    
    # Setup Python
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    # Install dependencies
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest pytest-cov
    
    # Run tests
    - name: Run tests
      run: |
        pytest tests/ --cov=src --cov-report=xml
    
    # Upload coverage
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
```

**Result**: Tests run on every push and pull request!

---

### Example 2: Test Multiple Python Versions

```yaml
name: Test Multiple Versions

on: [push, pull_request]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        python-version: ['3.8', '3.9', '3.10', '3.11']
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install pytest
    
    - name: Run tests
      run: pytest tests/
```

**Result**: Tests run on 12 combinations (3 OS × 4 Python versions)!

---

### Example 3: Code Quality Checks

```yaml
name: Code Quality

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    - name: Install linters
      run: |
        pip install black flake8 pylint mypy
    
    # Check formatting
    - name: Check code formatting (Black)
      run: black --check src/
    
    # Linting
    - name: Lint with flake8
      run: flake8 src/ --max-line-length=100
    
    # Type checking
    - name: Type check with mypy
      run: mypy src/ --ignore-missing-imports
      continue-on-error: true  # Don't fail if type hints incomplete
```

---

### Example 4: Auto-Deploy Documentation

```yaml
name: Deploy Docs

on:
  push:
    branches: [ main ]

jobs:
  deploy-docs:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    # Build docs with Sphinx
    - name: Install dependencies
      run: |
        pip install sphinx sphinx-rtd-theme
        pip install -r requirements.txt
    
    - name: Build documentation
      run: |
        cd docs/
        make html
    
    # Deploy to GitHub Pages
    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./docs/_build/html
```

**Result**: Documentation automatically updates on every push to main!

---

### Example 5: Auto-Deploy Streamlit App

```yaml
name: Deploy to Streamlit

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    # Streamlit Cloud auto-deploys from GitHub
    # Just need to trigger via API or webhook
    
    - name: Trigger Streamlit deployment
      run: |
        curl -X POST https://api.streamlit.io/v1/deploy \
          -H "Authorization: Bearer ${{ secrets.STREAMLIT_TOKEN }}" \
          -d '{"repo": "${{ github.repository }}"}'
```

*Note: Many platforms (Streamlit, Render, etc.) auto-deploy from GitHub without needing CI/CD*

---

### Example 6: Scheduled Workflows

```yaml
name: Nightly Tests

on:
  schedule:
    # Run at 2 AM UTC every day
    - cron: '0 2 * * *'
  workflow_dispatch:  # Allow manual trigger

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    - name: Run extensive tests
      run: |
        pip install -r requirements.txt
        pytest tests/ --runslow  # Include slow tests
    
    - name: Send notification on failure
      if: failure()
      uses: dawidd6/action-send-mail@v3
      with:
        server_address: smtp.gmail.com
        server_port: 465
        username: ${{ secrets.EMAIL_USERNAME }}
        password: ${{ secrets.EMAIL_PASSWORD }}
        subject: Nightly tests failed
        to: your-email@example.com
        from: GitHub Actions
        body: The nightly test run failed. Check the logs.
```

---

### Example 7: Release Automation

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'  # Trigger on version tags (v1.0.0, etc.)

jobs:
  release:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    # Build package
    - name: Build package
      run: |
        pip install build
        python -m build
    
    # Publish to PyPI
    - name: Publish to PyPI
      uses: pypa/gh-action-pypi-publish@release/v1
      with:
        password: ${{ secrets.PYPI_API_TOKEN }}
    
    # Create GitHub Release
    - name: Create Release
      uses: softprops/action-gh-release@v1
      with:
        files: dist/*
        generate_release_notes: true
```

**Usage**: `git tag v1.0.0 && git push --tags` → automatic release!

---

## GitLab CI/CD

### Why GitLab CI/CD?

- Free for public and private repos
- Built into GitLab (no setup needed)
- Powerful pipeline features
- Better for complex workflows
- Self-hosted runners supported
- Good for institutional use

### Basic Structure

**File**: `.gitlab-ci.yml` (in repository root)

```yaml
# Define stages
stages:
  - test
  - build
  - deploy

# Define what to cache between jobs
cache:
  paths:
    - .cache/pip

# Run before every job
before_script:
  - pip install --cache-dir .cache/pip -r requirements.txt

# Test job
test:
  stage: test
  image: python:3.9
  script:
    - pip install pytest pytest-cov
    - pytest tests/ --cov=src
  coverage: '/TOTAL.*\s+(\d+%)$/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml

# Code quality job
lint:
  stage: test
  image: python:3.9
  script:
    - pip install black flake8
    - black --check src/
    - flake8 src/
  allow_failure: true

# Build documentation
docs:
  stage: build
  image: python:3.9
  script:
    - pip install sphinx sphinx-rtd-theme
    - cd docs && make html
  artifacts:
    paths:
      - docs/_build/html
  only:
    - main

# Deploy to Pages
pages:
  stage: deploy
  script:
    - mv docs/_build/html public
  artifacts:
    paths:
      - public
  only:
    - main
```

---

### GitLab: Test Multiple Versions

```yaml
test:
  stage: test
  image: python:${PYTHON_VERSION}
  parallel:
    matrix:
      - PYTHON_VERSION: ['3.8', '3.9', '3.10', '3.11']
  script:
    - pip install -r requirements.txt
    - pytest tests/
```

---

### GitLab: Docker Build & Push

```yaml
build-docker:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG
  only:
    - tags
```

---

## Jenkins (Institutional/Advanced)

### When to Use Jenkins

- University has Jenkins instance
- Need complete control
- Complex build requirements
- Integration with existing infrastructure
- Self-hosted preference

### Basic Jenkinsfile

**File**: `Jenkinsfile`

```groovy
pipeline {
    agent any
    
    stages {
        stage('Setup') {
            steps {
                sh 'python -m venv venv'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }
        
        stage('Test') {
            steps {
                sh '. venv/bin/activate && pytest tests/'
            }
        }
        
        stage('Lint') {
            steps {
                sh '. venv/bin/activate && flake8 src/'
            }
        }
        
        stage('Build Docs') {
            steps {
                sh '. venv/bin/activate && cd docs && make html'
            }
        }
    }
    
    post {
        always {
            junit 'test-reports/*.xml'
            publishHTML([
                reportDir: 'docs/_build/html',
                reportFiles: 'index.html',
                reportName: 'Documentation'
            ])
        }
        failure {
            mail to: 'your-email@example.com',
                 subject: "Build Failed: ${env.JOB_NAME}",
                 body: "Build failed. Check Jenkins for details."
        }
    }
}
```

---

## Common CI/CD Patterns for Research

### Pattern 1: Test on Every Commit

**Goal**: Catch bugs immediately

```yaml
# Minimal .github/workflows/test.yml
name: Test
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.9'
      - run: pip install -r requirements.txt
      - run: pytest
```

---

### Pattern 2: Only Deploy from Main Branch

**Goal**: Keep main branch production-ready

```yaml
on:
  push:
    branches: [ main ]  # Only run on main

jobs:
  deploy:
    # ... deployment steps
```

---

### Pattern 3: Require Tests to Pass Before Merge

**Setup in GitHub**:
1. Settings → Branches → Add rule
2. Select "Require status checks to pass"
3. Select your test workflow
4. Save

**Result**: PRs can't merge if tests fail!

---

### Pattern 4: Version-Based Releases

```yaml
on:
  push:
    tags:
      - 'v*.*.*'  # v1.0.0, v1.2.3, etc.

jobs:
  release:
    # ... create release, publish package
```

**Usage**: 
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

---

### Pattern 5: Nightly Integration Tests

```yaml
on:
  schedule:
    - cron: '0 3 * * *'  # 3 AM daily

jobs:
  integration-tests:
    # Run comprehensive, slow tests
    # Test against latest dependencies
    # Check for dependency updates
```

---

### Pattern 6: Auto-Update Dependencies

```yaml
name: Update Dependencies

on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly on Sunday
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Update requirements
        run: |
          pip install pip-tools
          pip-compile --upgrade requirements.in
      
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v5
        with:
          commit-message: Update dependencies
          title: 'chore: Update Python dependencies'
          body: Automated dependency updates
          branch: update-dependencies
```

---

## Setting Up CI/CD: Step-by-Step

### GitHub Actions Setup

**Step 1**: Create workflow directory
```bash
mkdir -p .github/workflows
```

**Step 2**: Create workflow file
```bash
touch .github/workflows/test.yml
```

**Step 3**: Add workflow configuration (see examples above)

**Step 4**: Commit and push
```bash
git add .github/workflows/test.yml
git commit -m "Add CI workflow"
git push
```

**Step 5**: Check Actions tab on GitHub

That's it! Workflows run automatically.

---

### GitLab CI Setup

**Step 1**: Create `.gitlab-ci.yml` in repo root

**Step 2**: Add configuration (see examples above)

**Step 3**: Commit and push
```bash
git add .gitlab-ci.yml
git commit -m "Add CI/CD pipeline"
git push
```

**Step 4**: Check CI/CD → Pipelines in GitLab

GitLab automatically detects and runs the pipeline.

---

## Secrets Management

### GitHub Secrets

**Adding secrets** (for API keys, tokens, etc.):
1. Repo Settings → Secrets and variables → Actions
2. New repository secret
3. Name: `PYPI_API_TOKEN`
4. Value: your-secret-token
5. Save

**Using in workflows**:
```yaml
- name: Use secret
  run: echo ${{ secrets.PYPI_API_TOKEN }}
  env:
    TOKEN: ${{ secrets.PYPI_API_TOKEN }}
```

**Common secrets**:
- `PYPI_API_TOKEN` - For publishing packages
- `CODECOV_TOKEN` - For coverage reports
- `SLACK_WEBHOOK` - For notifications
- `AWS_ACCESS_KEY_ID` - For cloud deployments

---

### GitLab CI Variables

**Adding variables**:
1. Settings → CI/CD → Variables
2. Add variable
3. Key: `PYPI_TOKEN`
4. Value: your-token
5. Check "Mask variable" (hides in logs)
6. Check "Protect variable" (only on protected branches)

**Using in pipelines**:
```yaml
deploy:
  script:
    - echo $PYPI_TOKEN
    - twine upload -u __token__ -p $PYPI_TOKEN dist/*
```

---

## Advanced Features

### Caching Dependencies

**GitHub Actions**:
```yaml
- name: Cache pip packages
  uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
```

**GitLab CI**:
```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - .cache/pip
    - venv/
```

**Benefits**: Faster builds (don't reinstall every time)

---

### Conditional Workflows

**Only run on certain paths**:
```yaml
on:
  push:
    paths:
      - 'src/**'
      - 'tests/**'
      - 'requirements.txt'
```

**Skip CI with commit message**:
```bash
git commit -m "docs: update README [skip ci]"
```

---

### Matrix Builds

**Test combinations**:
```yaml
strategy:
  matrix:
    python: ['3.8', '3.9', '3.10']
    os: [ubuntu-latest, windows-latest]
    include:
      - python: '3.11'
        os: ubuntu-latest
        experimental: true
```

---

### Artifacts & Reports

**Save test results**:
```yaml
- name: Upload test results
  uses: actions/upload-artifact@v3
  with:
    name: test-results
    path: test-reports/
    
- name: Upload coverage
  uses: codecov/codecov-action@v3
```

---

## Badges for README

### Add Status Badges

**GitHub Actions**:
```markdown
![Tests](https://github.com/username/repo/workflows/Tests/badge.svg)
```

**GitLab CI**:
```markdown
[![pipeline status](https://gitlab.com/username/project/badges/main/pipeline.svg)](https://gitlab.com/username/project/-/commits/main)
```

**Codecov**:
```markdown
[![codecov](https://codecov.io/gh/username/repo/branch/main/graph/badge.svg)](https://codecov.io/gh/username/repo)
```

**Example README**:
```markdown
# My Research Project

![Tests](https://github.com/user/repo/workflows/Tests/badge.svg)
[![codecov](https://codecov.io/gh/user/repo/branch/main/graph/badge.svg)](https://codecov.io/gh/user/repo)
[![Documentation](https://img.shields.io/badge/docs-latest-blue.svg)](https://user.github.io/repo)

Description of project...
```

---

## Common Issues & Solutions

### Issue: Workflow doesn't trigger

**Solution**: Check:
- Workflow file in `.github/workflows/`
- Valid YAML syntax
- Correct trigger events
- Branch protection rules

### Issue: Tests pass locally but fail in CI

**Solution**: 
- Different Python versions
- Missing system dependencies
- Environment variables
- File paths (use relative paths)

### Issue: Running out of CI minutes

**Solution**:
- Cache dependencies
- Run fewer matrix combinations
- Use self-hosted runners
- Optimize test suite

### Issue: Secrets not working

**Solution**:
- Check secret name matches exactly
- Verify secret is set in correct scope
- Check branch protection rules
- Secrets aren't available in forked PRs (security)

---

## Best Practices

### 1. Start Simple

```yaml
# Start with just testing
name: Test
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
      - run: pip install pytest
      - run: pytest
```

Add complexity gradually!

### 2. Use Pre-built Actions

Don't reinvent the wheel:
- `actions/checkout@v4` - Check out code
- `actions/setup-python@v5` - Set up Python
- `actions/cache@v3` - Cache dependencies
- `codecov/codecov-action@v3` - Upload coverage

### 3. Pin Action Versions

```yaml
# Good - specific version
- uses: actions/checkout@v4

# Risky - latest version
- uses: actions/checkout@main
```

### 4. Fail Fast

```yaml
strategy:
  fail-fast: true  # Stop all jobs if one fails
  matrix:
    python: ['3.8', '3.9', '3.10']
```

### 5. Keep Workflows Fast

- Cache dependencies
- Parallelize when possible
- Don't test everything on every commit
- Use matrix builds wisely

### 6. Add Status Checks

Require CI to pass before merging:
- Settings → Branches → Branch protection
- Require status checks
- Select workflows

---

## Templates for Common Research Scenarios

### Template 1: Python Package

```yaml
name: Python Package CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.8', '3.9', '3.10', '3.11']
    
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
    - run: pip install -e .[dev]
    - run: pytest tests/ --cov
    - run: black --check src/
    - run: flake8 src/
```

### Template 2: Data Analysis Project

```yaml
name: Analysis Pipeline

on:
  push:
    branches: [ main ]
    paths:
      - 'scripts/**'
      - 'notebooks/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
    - run: pip install -r requirements.txt
    - run: python scripts/validate_data.py
    - run: jupyter nbconvert --execute notebooks/*.ipynb
```

### Template 3: Documentation Site

```yaml
name: Deploy Docs

on:
  push:
    branches: [ main ]

jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
    - run: pip install sphinx sphinx-rtd-theme
    - run: cd docs && make html
    - uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./docs/_build/html
```

---

## Checklist for Setting Up CI/CD

- [ ] Create `.github/workflows/` directory
- [ ] Add test workflow file
- [ ] Ensure tests run locally first
- [ ] Add `requirements.txt` or dependencies file
- [ ] Push to GitHub and verify workflow runs
- [ ] Add status badge to README
- [ ] Set up branch protection (require tests)
- [ ] Add secrets for deployments (if needed)
- [ ] Cache dependencies for speed
- [ ] Add code coverage reporting
- [ ] Document workflow in README
- [ ] Test pull request workflow

---

## Resources

### Documentation
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [GitLab CI/CD Docs](https://docs.gitlab.com/ee/ci/)
- [Awesome Actions](https://github.com/sdras/awesome-actions) - Curated list

### Learning
- [GitHub Actions Tutorial](https://docs.github.com/en/actions/quickstart)
- [GitLab CI/CD Tutorial](https://docs.gitlab.com/ee/ci/quick_start/)

### Testing Tools
- [pytest](https://docs.pytest.org/)
- [coverage.py](https://coverage.readthedocs.io/)
- [Codecov](https://codecov.io/)

---

## Summary

**For most research projects, start with**:
1. GitHub Actions (easiest, free, powerful)
2. Simple test workflow on every push
3. Add complexity as needed
4. Require tests to pass before merging

**Benefits you'll see**:
- Fewer bugs in published code
- Confidence when accepting contributions
- Automated documentation updates
- Professional, maintainable projects
- Better reproducibility

**Remember**: CI/CD is about automating boring tasks so you can focus on research. Start simple and add features as you need them!
