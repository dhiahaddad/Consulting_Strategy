# Software Licensing Guide for Research Code

*A practical guide for helping PhD students and postdocs license their research software*

---

## Why Licensing Matters for Research Code

### Without a License
- **Code is copyrighted by default** - others cannot legally use it
- **Cannot be cited or reused** - limits impact of your research
- **Violates many funder requirements** - NIH, NSF, EU often require open code
- **Cannot be published in some journals** - increasing requirements for code availability
- **Limits reproducibility** - others can't verify or build upon your work

### With a License
- ✅ Enables reproducible research
- ✅ Increases citations and impact
- ✅ Meets funder and journal requirements
- ✅ Builds your reputation in the community
- ✅ Enables collaboration and reuse

---

## Quick Decision Tree

```
Is this research/academic code?
│
├─ YES → Will others use/modify it?
│        │
│        ├─ YES, I want attribution → MIT or Apache 2.0
│        │
│        ├─ YES, derivative work must be open → GPL-3.0
│        │
│        └─ Unsure → MIT (most flexible)
│
├─ Contains sensitive data/methods? → Consult with advisor/tech transfer
│
└─ Commercial potential? → Consult with university tech transfer office
```

---

## Common Licenses for Research Code

### 1. MIT License ⭐ Most Popular for Research

**Best for**: Most academic code, maximum reuse

**Pros**:
- Very simple and permissive
- Allows commercial use
- Only requires attribution
- Compatible with almost everything
- Most widely understood

**Cons**:
- Derivative works can be closed-source
- No patent protection

**Use when**: You want maximum adoption and don't care if companies use it commercially

**Example projects**: NumPy, scikit-learn, many Python packages

```markdown
## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Citation

If you use this code in your research, please cite:
```
Author, A. et al. (2026). Paper Title. Journal Name.
```
```

---

### 2. Apache License 2.0

**Best for**: Code with patent concerns, larger projects

**Pros**:
- Permissive like MIT
- Includes explicit patent grant
- Better for larger organizations
- Good trademark protection

**Cons**:
- More complex than MIT
- Longer license text

**Use when**: Your research involves potential patents or you want explicit patent protection

**Example projects**: TensorFlow, Apache projects

---

### 3. GNU GPL v3.0 (General Public License)

**Best for**: When you want derivatives to remain open-source

**Pros**:
- Ensures derivative works stay open
- Strong copyleft protection
- Prevents proprietary forks

**Cons**:
- Cannot be mixed with proprietary code
- May limit commercial adoption
- Complex legal requirements

**Use when**: You strongly believe derivative works should be open-source

**Example projects**: Linux, R statistical software

---

### 4. BSD 3-Clause

**Best for**: Academic code, similar to MIT

**Pros**:
- Permissive
- Prevents name misuse
- Traditional academic license

**Cons**:
- Slightly more complex than MIT
- Less common than it used to be

**Use when**: Your institution has BSD tradition or requirements

**Example projects**: Many academic projects, BSD Unix

---

### 5. Creative Commons (CC BY 4.0)

**Best for**: Documentation, datasets, tutorials (NOT code)

**Pros**:
- Designed for creative works
- Easy to understand
- Widely recognized

**Cons**:
- Not designed for software
- May cause issues with code dependencies

**Use when**: Licensing documentation, papers, or datasets (use software licenses for code)

---

### 6. Unlicense or CC0 (Public Domain)

**Best for**: Small utilities, examples, when you want no restrictions

**Pros**:
- No restrictions whatsoever
- No attribution required

**Cons**:
- You get no credit
- May not be valid in some jurisdictions

**Use when**: Tiny scripts where attribution doesn't matter

---

## Choosing a License: Consultation Questions

### Questions to Ask Your Client

1. **"Do you want credit when others use your code?"**
   - Yes → MIT, Apache, BSD
   - No → Unlicense/CC0

2. **"Should derivative works also be open-source?"**
   - Yes, strongly → GPL-3.0
   - Don't care → MIT, Apache, BSD

3. **"Is there commercial potential?"**
   - Yes → Talk to tech transfer office first!
   - No → Any permissive license (MIT recommended)

4. **"What do your collaborators/lab use?"**
   - Use the same license for consistency

5. **"What do similar projects in your field use?"**
   - Check GitHub repos in your domain

6. **"What does your funder require?"**
   - Check grant requirements

7. **"What does your target journal require?"**
   - Many journals now require code sharing

### Institutional Considerations

**Always check**:
- [ ] University intellectual property policy
- [ ] Grant/funder requirements (NSF, NIH, ERC, etc.)
- [ ] Advisor's preference
- [ ] Lab/department norms
- [ ] If work was done "in scope of employment"

**When to consult tech transfer office**:
- Commercial applications possible
- Patents involved
- Industry partnerships
- Uncertain about IP ownership
- Concerns about liability

---

## How to Apply a License

### Step 1: Choose the License
Use [choosealicense.com](https://choosealicense.com/) for interactive guidance

### Step 2: Add LICENSE File

**Create `LICENSE` file in project root:**

```bash
# Download license text
# For MIT:
wget https://raw.githubusercontent.com/licenses/license-templates/master/templates/mit.txt -O LICENSE

# Or manually create LICENSE file with license text
```

**MIT License Template:**
```
MIT License

Copyright (c) 2026 [Your Name or Institution]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### Step 3: Add License Headers (Optional but Recommended)

**For Python files, add header comment:**
```python
# Copyright (c) 2026 [Your Name]
# Licensed under the MIT License - see LICENSE file for details

"""
Module for analyzing experimental data.
"""
```

**Shorter version:**
```python
# SPDX-License-Identifier: MIT
# Copyright (c) 2026 [Your Name]
```

### Step 4: Update README

**Add to README.md:**
```markdown
## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Citation

If you use this software in your research, please cite:

```bibtex
@article{yourname2026,
  title={Your Paper Title},
  author={Your Name and Others},
  journal={Journal Name},
  year={2026},
  doi={10.xxxx/xxxxx}
}
```

Or cite the software directly:

```bibtex
@software{yourname2026software,
  author={Your Name},
  title={Software Name},
  year={2026},
  url={https://github.com/yourusername/yourproject},
  version={1.0.0}
}
```
```

### Step 5: Add to Repository Metadata

**If using GitHub:**
- Go to repository settings
- Add license from dropdown
- Add citation file (see below)

**Create `CITATION.cff` file:**
```yaml
cff-version: 1.2.0
message: "If you use this software, please cite it as below."
authors:
  - family-names: "Your Last Name"
    given-names: "Your First Name"
    orcid: "https://orcid.org/0000-0000-0000-0000"
title: "Your Software Name"
version: 1.0.0
doi: 10.5281/zenodo.1234567
date-released: 2026-01-17
url: "https://github.com/yourusername/yourproject"
```

---

## Special Cases in Academic Research

### Mixed Licensing
If your code uses different components:

```markdown
## License

- Core analysis code: MIT License
- Data preprocessing scripts: Apache 2.0 License  
- Example notebooks: CC BY 4.0
- Documentation: CC BY 4.0

See individual file headers for details.
```

### Code with Datasets
```markdown
## License

- **Code**: MIT License (see LICENSE-CODE)
- **Data**: CC BY 4.0 (see LICENSE-DATA)
- **Documentation**: CC BY 4.0

Please cite both the code and dataset if using in publications.
```

### Dependency Compatibility

**Check license compatibility**:
- MIT, BSD, Apache → Can use together
- GPL → Cannot mix with non-GPL compatible licenses
- AGPL → Very restrictive, avoid unless intentional

**Tool to check**: [License Compatibility Checker](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-compatibility-checker)

---

## Common Scenarios & Advice

### Scenario 1: "I just want people to use my code"
**Recommendation**: MIT License
- Simple, well-understood
- Maximum adoption
- Only requires attribution

### Scenario 2: "I want credit but don't care about money"
**Recommendation**: MIT License
- Requires attribution in license notices
- Allows commercial use (increases adoption)
- Should also include CITATION file

### Scenario 3: "I might commercialize this later"
**Recommendation**: Talk to tech transfer FIRST
- May need to delay publishing
- University may own the IP
- May use dual licensing

### Scenario 4: "My advisor says use GPL"
**Recommendation**: Use GPL-3.0
- Follow advisor's guidance
- Understand implications for users
- Ensure compatibility with dependencies

### Scenario 5: "I used code from another project"
**Recommendation**: Check their license!
- MIT/BSD/Apache → Can relicense
- GPL → Must use GPL
- No license → Cannot legally use!
- When in doubt, ask the author

### Scenario 6: "I'm publishing in a journal requiring code"
**Recommendation**: Check journal requirements
- Some require specific licenses
- Some just need "available on request"
- Archive on Zenodo/Figshare for DOI
- MIT is usually safe choice

---

## Archiving & DOI Assignment

### Why Archive Your Code

- **Permanent record** - GitHub may not last forever
- **DOI** - Makes code citable
- **Versioning** - Specific version tied to publication
- **Funder requirements** - Many require data/code archiving

### Where to Archive

**Zenodo** (Recommended)
- Free, permanent DOI
- GitHub integration
- Unlimited public storage
- Part of CERN infrastructure

**How to use Zenodo:**
1. Create Zenodo account
2. Connect GitHub repository
3. Create release on GitHub
4. Zenodo automatically archives and creates DOI
5. Add DOI badge to README

**Alternatives**:
- **Figshare** - Good for datasets + code
- **OSF** (Open Science Framework) - Entire project
- **Software Heritage** - Long-term archival
- **Institutional repository** - Check your university

---

## Checklist for Licensing Research Code

Before publication/sharing:
- [ ] Choose appropriate license
- [ ] Create LICENSE file in repository root
- [ ] Update README with license information
- [ ] Add citation information (CITATION.cff)
- [ ] Check all dependencies' licenses for compatibility
- [ ] Verify with advisor/collaborators
- [ ] Check university IP policy if applicable
- [ ] Check funder requirements
- [ ] Add license to repository metadata (GitHub settings)
- [ ] Consider archiving with DOI (Zenodo)
- [ ] Add license headers to source files (optional)
- [ ] Remove any sensitive information
- [ ] Ensure no passwords/API keys in code

---

## Red Flags & When to Get Help

**Stop and consult tech transfer/legal if**:
- ⚠️ Code has commercial potential
- ⚠️ Code developed under industry partnership
- ⚠️ Code involves patents
- ⚠️ Unsure about IP ownership
- ⚠️ Using restrictively licensed dependencies
- ⚠️ Government/military funded work
- ⚠️ Contains export-controlled information
- ⚠️ Health/medical data involved

---

## Resources to Share with Clients

### Interactive Tools
- [Choose a License](https://choosealicense.com/) - Simple decision tool
- [TL;DR Legal](https://tldrlegal.com/) - Plain English license summaries
- [License Compatibility](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-compatibility-checker)

### Reading
- [GitHub Licensing Guide](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository)
- [Software Sustainability Institute - Choosing a License](https://www.software.ac.uk/resources/guides/adopting-open-source-licence)
- [PLOS Computational Biology - Ten Simple Rules for Reproducible Computational Research](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1003285)

### Citation Tools
- [CITATION.cff Generator](https://citation-file-format.github.io/cff-initializer-javascript/)
- [Zenodo GitHub Integration](https://guides.github.com/activities/citable-code/)
- [ORCID](https://orcid.org/) - Get researcher ID

---

## Quick Reference Table

| License | Permissions | Conditions | Limitations | Best For |
|---------|-------------|------------|-------------|----------|
| **MIT** | ✓ Commercial use<br>✓ Modification<br>✓ Distribution<br>✓ Private use | • License & copyright notice | ✗ Liability<br>✗ Warranty | Most research code |
| **Apache 2.0** | ✓ Commercial use<br>✓ Modification<br>✓ Distribution<br>✓ Patent use<br>✓ Private use | • License & copyright notice<br>• State changes | ✗ Liability<br>✗ Warranty<br>✗ Trademark use | Code with patent concerns |
| **GPL-3.0** | ✓ Commercial use<br>✓ Modification<br>✓ Distribution<br>✓ Patent use<br>✓ Private use | • Disclose source<br>• License & copyright notice<br>• State changes<br>• Same license | ✗ Liability<br>✗ Warranty | When derivatives must be open |
| **BSD-3** | ✓ Commercial use<br>✓ Modification<br>✓ Distribution<br>✓ Private use | • License & copyright notice | ✗ Liability<br>✗ Warranty<br>✗ Use of name | Traditional academic code |
| **Unlicense** | ✓ Everything | None | ✗ Liability<br>✗ Warranty | Tiny utilities, examples |

---

## Sample Consultation Dialogue

**Client**: "I need to share my code for a paper. What should I do?"

**You**: "Great! A few questions:
1. Do you want people to be able to use and modify your code?"
   - *Client: Yes*

**You**: "Good! Do you want to get credit when they use it?"
   - *Client: Yes, definitely*

**You**: "Perfect. Do you care if someone uses it commercially, like in a company?"
   - *Client: No, I just want my research to have impact*

**You**: "Then I recommend the MIT License. It's:
- Simple to understand
- Requires attribution (you get credit)
- Allows anyone to use it (maximum impact)
- The most popular license for research code

Here's what we'll do:
1. Add a LICENSE file to your GitHub repo
2. Update your README with license and citation info
3. Create a CITATION.cff file so people know how to cite you
4. Archive on Zenodo to get a DOI for your code

Before we do that, let me ask: was this funded by a grant?"
   - *Client: Yes, NSF*

**You**: "Perfect, NSF requires code sharing, so this checks that box. Have you checked with your advisor?"
   - *Client: Not yet*

**You**: "Please confirm with them first, then we'll set this up together. I'll send you a template."

---

## Template Email for Clients

```
Subject: Resources for Licensing Your Research Code

Hi [Name],

As we discussed, here are the steps to license your code:

1. **Choose License**: I recommend MIT License based on your needs
   - Simple and permissive
   - Requires attribution
   - Allows maximum reuse

2. **Add LICENSE file**: [Attached template] - replace [Your Name] with your name

3. **Update README**: Add license section and citation information [see attached template]

4. **Create CITATION.cff**: So people know how to cite your work [attached template]

5. **Archive on Zenodo**: Get a permanent DOI
   - Create account at zenodo.org
   - Link your GitHub repo
   - Create a release
   - Zenodo will automatically archive and assign DOI

6. **Before publishing**:
   - [ ] Verify with advisor
   - [ ] Check grant requirements
   - [ ] Remove any sensitive data
   - [ ] Ensure no passwords in code

Resources:
- Choose a License: https://choosealicense.com/
- Zenodo Guide: https://guides.github.com/activities/citable-code/

Let me know if you have questions!

Best,
[Your Name]
```

---

**Remember**: For most academic research code, MIT License + Zenodo archival is the simplest, most effective solution that meets most requirements and maximizes impact.
