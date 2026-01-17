# Online Hosting Guide for Research Applications

*A guide for helping researchers deploy and share their computational tools and applications*

---

## Why Host Research Applications Online?

### Benefits
- ✅ **Democratize access** - Anyone can use your tool without installation
- ✅ **Increase impact** - Easier to use = more citations and adoption
- ✅ **Meet funder requirements** - Many grants require data/tool accessibility
- ✅ **Enable collaboration** - Share with collaborators easily
- ✅ **Supplement publications** - Interactive demos enhance papers
- ✅ **Reproducibility** - Fixed environment ensures consistent results
- ✅ **Community engagement** - Others can build on your work

### Common Use Cases
- Interactive data visualizations
- Analysis pipelines with web interface
- Model inference/prediction tools
- Data exploration dashboards
- Simulation tools
- Computational notebooks
- Documentation sites
- REST APIs for data/models

---

## Quick Decision Tree

```
What are you hosting?
│
├─ Static website/documentation
│  └─ GitHub Pages (FREE) ⭐
│
├─ Jupyter Notebook (interactive)
│  ├─ Just viewing → nbviewer (FREE)
│  ├─ Want execution → Binder (FREE) or Google Colab (FREE)
│  └─ Custom environment → MyBinder (FREE)
│
├─ Python web app (Streamlit/Dash/Flask)
│  ├─ Simple demo → Streamlit Community Cloud (FREE) ⭐
│  ├─ ML/AI app → Hugging Face Spaces (FREE) ⭐
│  ├─ Need custom server → Render (FREE tier) or Railway
│  └─ High traffic → Heroku, AWS, GCP (PAID)
│
├─ R Shiny app
│  └─ shinyapps.io (FREE tier available) ⭐
│
└─ Complex application/API
   ├─ Containerized → Fly.io, Railway, or cloud providers
   └─ Need institutional support → Ask IT department
```

---

## Free Hosting Platforms for Research

### 1. GitHub Pages ⭐ (Static Sites)

**Best for**: Documentation, project websites, blogs, static visualizations

**Pros**:
- Completely free
- Easy setup from repository
- Custom domains supported
- Built-in SSL
- Great for documentation (Jekyll, MkDocs, Sphinx)

**Cons**:
- Static only (no server-side processing)
- 1GB size limit
- Public repositories only (for free)

**Setup**:
```bash
# In your repository
# Create docs/ folder or use gh-pages branch
# Enable GitHub Pages in repository settings
# Your site: https://username.github.io/repo-name
```

**Perfect for**:
- Project documentation (Sphinx, MkDocs)
- Interactive visualizations (D3.js, Plotly)
- Static dashboards
- Personal research websites

**Resources**:
- [GitHub Pages Documentation](https://pages.github.com/)
- [Jekyll](https://jekyllrb.com/) - Static site generator
- [MkDocs](https://www.mkdocs.org/) - Documentation generator

---

### 2. Streamlit Community Cloud ⭐ (Python Apps)

**Best for**: Interactive Python data apps, dashboards, ML demos

**Pros**:
- Completely free for public repos
- Dead simple deployment
- Perfect for data science/ML
- Auto-updates from GitHub
- Built-in secrets management

**Cons**:
- Limited resources (1GB RAM)
- Apps sleep after inactivity
- Public apps only on free tier
- Limited to Streamlit framework

**Setup**:
```python
# app.py
import streamlit as st
import pandas as pd

st.title("My Research Tool")
uploaded_file = st.file_uploader("Upload data")
if uploaded_file:
    df = pd.read_csv(uploaded_file)
    st.write(df.describe())
    st.line_chart(df)
```

```bash
# requirements.txt
streamlit
pandas
numpy
matplotlib

# Deploy: streamlit.io/cloud → Connect GitHub → Select repo
# Your app: https://username-app-name.streamlit.app
```

**Perfect for**:
- Data analysis dashboards
- ML model demos
- Interactive visualizations
- Research tools with UI

**Resources**:
- [Streamlit Documentation](https://docs.streamlit.io/)
- [Streamlit Community Cloud](https://streamlit.io/cloud)
- [Example Gallery](https://streamlit.io/gallery)

---

### 3. Hugging Face Spaces ⭐ (ML/AI Apps)

**Best for**: Machine learning demos, AI models, Gradio/Streamlit apps

**Pros**:
- Free GPU options available!
- Built for ML/AI applications
- Supports multiple frameworks (Gradio, Streamlit, Docker)
- Easy model integration
- Community visibility

**Cons**:
- Focused on ML/AI use cases
- Free tier has resource limits
- Apps can be slow to wake up

**Setup**:
```python
# app.py (Gradio example)
import gradio as gr
from transformers import pipeline

classifier = pipeline("sentiment-analysis")

def analyze(text):
    return classifier(text)[0]

demo = gr.Interface(
    fn=analyze,
    inputs="text",
    outputs="label",
    title="Sentiment Analysis"
)

demo.launch()
```

**Perfect for**:
- ML model demos
- NLP applications
- Computer vision tools
- Any AI/ML research

**Resources**:
- [Hugging Face Spaces](https://huggingface.co/spaces)
- [Gradio Documentation](https://gradio.app/)

---

### 4. Binder / MyBinder ⭐ (Jupyter Notebooks)

**Best for**: Executable Jupyter notebooks, reproducible research

**Pros**:
- Completely free
- Launch notebooks from GitHub
- Reproducible environments
- No account needed
- Great for publications

**Cons**:
- Sessions are temporary
- Limited compute resources
- Can be slow to start
- No persistent storage

**Setup**:
```yaml
# environment.yml (in repo root)
name: myenv
channels:
  - conda-forge
dependencies:
  - python=3.9
  - numpy
  - pandas
  - matplotlib
  - jupyter

# Or requirements.txt
numpy
pandas
matplotlib
```

**Launch**:
- Go to [mybinder.org](https://mybinder.org)
- Enter your GitHub repo URL
- Get shareable badge for README
- Users can run your notebooks in browser

**Perfect for**:
- Reproducible analysis notebooks
- Tutorial materials
- Supplementary materials for papers
- Interactive educational content

**Resources**:
- [MyBinder](https://mybinder.org/)
- [Binder Documentation](https://mybinder.readthedocs.io/)

---

### 5. shinyapps.io (R Shiny Apps)

**Best for**: R-based interactive applications and dashboards

**Pros**:
- Free tier available
- Built for R Shiny
- Easy deployment
- Integrated with RStudio

**Cons**:
- Limited free tier (25 hours/month)
- R-specific only
- Apps sleep after inactivity

**Setup**:
```r
# app.R
library(shiny)

ui <- fluidPage(
  titlePanel("My Research App"),
  sidebarLayout(
    sidebarPanel(
      fileInput("file", "Upload CSV")
    ),
    mainPanel(
      plotOutput("plot")
    )
  )
)

server <- function(input, output) {
  output$plot <- renderPlot({
    req(input$file)
    data <- read.csv(input$file$datapath)
    plot(data)
  })
}

shinyApp(ui, server)
```

**Resources**:
- [shinyapps.io](https://www.shinyapps.io/)
- [Shiny Documentation](https://shiny.rstudio.com/)

---

### 6. Google Colab (Jupyter Notebooks)

**Best for**: GPU-intensive notebooks, collaborative coding

**Pros**:
- Free GPU/TPU access
- No setup required
- Google Drive integration
- Easy sharing
- Great for ML/deep learning

**Cons**:
- Sessions time out
- Not for production apps
- Google account required
- Limited to notebooks

**Perfect for**:
- Machine learning tutorials
- GPU-intensive computations
- Collaborative analysis
- Quick prototyping

---

### 7. Render (Full Apps)

**Best for**: Web apps, APIs, databases

**Pros**:
- Free tier for web services
- Supports Python, Node.js, Go, etc.
- Auto-deploys from GitHub
- Static sites also supported
- No credit card for free tier

**Cons**:
- Free tier spins down after inactivity
- Limited resources on free tier

**Setup**:
```python
# app.py (Flask example)
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "My Research API"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**Resources**:
- [Render](https://render.com/)

---

### 8. Railway / Fly.io (Docker Apps)

**Best for**: Dockerized applications, complex deployments

**Pros**:
- Support Docker containers
- More control over environment
- Free tiers available
- Support multiple languages

**Cons**:
- Requires Docker knowledge
- More complex setup
- Limited free tier

---

## Academic Cloud Credits

### Free Credits for Researchers

**AWS Educate / AWS Cloud Credits for Research**:
- Up to $100-$1000 in credits
- Apply through institution
- Great for large-scale deployments

**Google Cloud Platform - Research Credits**:
- Up to $5000 for research projects
- Application required
- Ideal for ML/data-intensive work

**Microsoft Azure for Research**:
- Credits available for academic research
- Apply through institution
- Good for Windows-based apps

**DigitalOcean - GitHub Student Pack**:
- $200 credit for students
- Easy-to-use platform
- Good for learning

**How to apply**:
- Check your institution's IT department
- Apply directly through cloud provider
- Mention research project and publications
- Emphasize educational use

---

## Institutional Hosting

### University IT Resources

**Check if your university provides**:
- Web hosting services
- Virtual machines
- Server space
- Database hosting
- Domain names

**Pros**:
- Free or subsidized
- Institutional support
- Long-term sustainability
- Secure infrastructure

**Cons**:
- May have restrictions
- Slower setup process
- Limited control
- May require approval

**How to access**:
1. Contact your IT department
2. Explain research need
3. Ask about available services
4. Inquire about research computing

---

## Choosing the Right Platform

### Consider These Factors

**1. Technical Complexity**
- Static site? → GitHub Pages
- Python app? → Streamlit
- Need database? → Cloud provider
- Complex stack? → Docker + cloud

**2. Resource Requirements**
- Light compute? → Free tiers
- GPU needed? → Colab or Hugging Face
- Heavy traffic? → Paid cloud
- Storage needs? → Check limits

**3. Budget**
- $0 → Free tiers, academic credits
- Small budget → Heroku, Render paid tiers
- Research grant → AWS, GCP, Azure

**4. Longevity**
- Demo for paper? → Any free tier
- Long-term tool? → Institutional or paid
- Active development? → Auto-deploy platforms
- Archive? → Zenodo + Binder

**5. Audience**
- Researchers only? → GitHub + Binder
- General public? → Streamlit, Hugging Face
- Collaborators? → Password-protected
- High traffic expected? → Paid hosting

---

## Deployment Best Practices

### 1. Containerization (Docker)

**Why Docker?**
- Reproducible environments
- Easy deployment anywhere
- Dependency management
- Version control for environment

**Example Dockerfile**:
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8501

CMD ["streamlit", "run", "app.py"]
```

### 2. Environment Management

**Always include**:
- `requirements.txt` (Python)
- `environment.yml` (Conda)
- `package.json` (Node.js)
- Runtime specifications

### 3. Configuration & Secrets

**Never commit**:
- API keys
- Passwords
- Database credentials
- Private tokens

**Use instead**:
- Environment variables
- Platform secret managers
- `.env` files (gitignored)
- Encrypted secrets

**Example**:
```python
# app.py
import os
import streamlit as st

# Secrets in Streamlit Cloud
api_key = st.secrets.get("API_KEY") or os.getenv("API_KEY")

# Local .env file
from dotenv import load_dotenv
load_dotenv()
```

### 4. Data Handling

**For large datasets**:
- Don't include in repository
- Use external storage (S3, GCS)
- Provide download scripts
- Cache processed data
- Use CDN for static assets

### 5. Performance Optimization

**Frontend**:
- Minimize loading times
- Use caching (`@st.cache_data`)
- Lazy loading for large data
- Optimize images

**Backend**:
- Efficient algorithms
- Database indexing
- Rate limiting
- Connection pooling

---

## Platform Comparison Table

| Platform | Best For | Free Tier | Setup Difficulty | Custom Domain |
|----------|----------|-----------|------------------|---------------|
| **GitHub Pages** | Static sites | ✅ Unlimited | ⭐ Easy | ✅ Yes |
| **Streamlit Cloud** | Python dashboards | ✅ Limited | ⭐ Easy | ❌ No (paid) |
| **Hugging Face** | ML demos | ✅ + GPU | ⭐⭐ Medium | ❌ No |
| **Binder** | Notebooks | ✅ Limited | ⭐ Easy | N/A |
| **Colab** | ML notebooks | ✅ + GPU | ⭐ Easy | N/A |
| **shinyapps.io** | R Shiny | ✅ 25hrs/mo | ⭐⭐ Medium | ❌ No (paid) |
| **Render** | Full apps | ✅ Limited | ⭐⭐ Medium | ✅ Yes |
| **Railway** | Docker apps | ✅ Limited | ⭐⭐⭐ Hard | ✅ Yes |
| **Heroku** | Full apps | ❌ Paid only | ⭐⭐ Medium | ✅ Yes |
| **AWS/GCP** | Enterprise | ❌ Pay-as-go | ⭐⭐⭐⭐ Hard | ✅ Yes |

---

## Common Scenarios & Recommendations

### Scenario 1: "I want to share a data visualization"

**If static (pre-generated)**:
- → GitHub Pages + Plotly/D3.js

**If interactive (user-uploaded data)**:
- → Streamlit Community Cloud

**Example**: Genomics data browser, climate data explorer

---

### Scenario 2: "I built an ML model and want others to try it"

**Recommendation**: Hugging Face Spaces with Gradio
- Free GPU
- Easy UI creation
- Discoverable by community

**Example**: Sentiment analysis, image classification, text generation

---

### Scenario 3: "I have analysis notebooks for my paper"

**Recommendation**: Binder + GitHub
- Fully reproducible
- One-click launch
- Add badge to paper

**Example**: Supplementary analysis, reproducible figures

---

### Scenario 4: "I need a dashboard for lab members"

**Recommendation**: Streamlit Cloud (if public OK) or institutional hosting
- Password protection available on paid tier
- Easy updates from GitHub
- Collaborative access

**Example**: Lab data monitoring, experiment tracker

---

### Scenario 5: "I have an R Shiny app"

**Recommendation**: shinyapps.io
- Built for Shiny
- Free tier sufficient for demos
- Easy deployment from RStudio

---

### Scenario 6: "I need an API for my model/data"

**Recommendation**: 
- Small scale → Render free tier
- Medium scale → Railway
- Large scale → Cloud provider with credits

**Example**: REST API for predictions, data query service

---

### Scenario 7: "My app needs a database"

**Recommendation**:
- Development → SQLite (file-based)
- Production → Render (PostgreSQL included) or cloud

---

## Step-by-Step: Deploy a Streamlit App

### 1. Create Your App

```python
# app.py
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

st.title("Research Data Analyzer")

uploaded_file = st.file_uploader("Upload CSV", type="csv")

if uploaded_file:
    df = pd.read_csv(uploaded_file)
    
    st.subheader("Data Preview")
    st.write(df.head())
    
    st.subheader("Summary Statistics")
    st.write(df.describe())
    
    st.subheader("Visualization")
    column = st.selectbox("Select column to plot", df.columns)
    fig, ax = plt.subplots()
    ax.hist(df[column].dropna())
    st.pyplot(fig)
```

### 2. Create Requirements

```txt
# requirements.txt
streamlit==1.29.0
pandas==2.1.0
matplotlib==3.8.0
```

### 3. Test Locally

```bash
pip install -r requirements.txt
streamlit run app.py
```

### 4. Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/yourapp.git
git push -u origin main
```

### 5. Deploy on Streamlit Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io)
2. Sign in with GitHub
3. Click "New app"
4. Select repository and branch
5. Set main file path: `app.py`
6. Click "Deploy!"

Your app: `https://yourusername-yourapp.streamlit.app`

---

## Maintenance & Sustainability

### Long-term Considerations

**For sustained availability**:
- Choose stable platforms (GitHub Pages, institutional)
- Plan for funding beyond grants
- Document maintenance requirements
- Consider archiving strategies

**Archiving research apps**:
- Zenodo + Docker image
- GitHub releases
- Software Heritage
- Institutional repository
- Include in supplementary materials

**Versioning**:
- Tag releases in Git
- Document breaking changes
- Maintain backward compatibility
- Archive old versions

---

## Checklist for Deployment

Before going live:
- [ ] Code is in version control (Git)
- [ ] Dependencies documented (requirements.txt)
- [ ] Secrets/credentials removed from code
- [ ] README with usage instructions
- [ ] LICENSE file included
- [ ] CITATION information provided
- [ ] Tested on target platform
- [ ] Error handling implemented
- [ ] Loading states/feedback for users
- [ ] Mobile-friendly (if applicable)
- [ ] Accessibility considered
- [ ] Analytics/usage tracking (optional)
- [ ] Monitoring/alerts set up (for critical apps)

---

## Resources for Clients

### Learning Materials
- [Streamlit Documentation](https://docs.streamlit.io/)
- [Full Stack Python - Deployment](https://www.fullstackpython.com/deployment.html)
- [Docker Tutorial](https://docker-curriculum.com/)
- [GitHub Pages Guide](https://pages.github.com/)

### Comparison Sites
- [Free for Developers](https://free-for.dev/) - Comprehensive list of free tiers
- [JamStack](https://jamstack.org/generators/) - Static site generators

### Tools
- [ngrok](https://ngrok.com/) - Temporary public URLs for local development
- [Localtunnel](https://localtunnel.github.io/www/) - Alternative to ngrok

---

## Summary Recommendations

**For most researchers starting out**:
1. **Documentation** → GitHub Pages
2. **Python app** → Streamlit Community Cloud
3. **ML demo** → Hugging Face Spaces
4. **Notebooks** → Binder
5. **R app** → shinyapps.io

**Best practices**:
- Start simple (free tier)
- Use version control
- Document everything
- Plan for archiving
- Consider sustainability

**When to upgrade**:
- High traffic requires it
- Need guaranteed uptime
- Research gets funded
- Tool becomes critical infrastructure

---

**Remember**: The best hosting solution is the one that makes your research accessible and reproducible while being sustainable for your situation. Start free, upgrade if needed!
