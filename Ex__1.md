# Unit I -- Foundations of Prompt Engineering

## Lab Programs using Groq API

### Common setup

Install the required packages:

``` bash
pip install openai python-dotenv
```

Create a `.env` file in the project folder:

``` env
GROQ_API_KEY=your_actual_groq_api_key
```

The four programs below use the same Groq API connection. Save each
program in a separate Python file.

------------------------------------------------------------------------

## Experiment 1: Environment and Connectivity

**Aim:** Connect Python to the Groq API and execute a simple "Hello,
world!" prompt.

**File:** `hello.py`

``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Connect to Groq
client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

# Send prompt
response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": "Hello, world!"}
    ]
)

# Display output
print(response.choices[0].message.content)
```

**Run:**

``` bash
python hello.py
```

------------------------------------------------------------------------

## Experiment 2: Baseline vs. Enhanced Prompts

**Aim:** Execute a naïve prompt and an enhanced prompt, then compare
their outputs for relevance, completeness, and style.

**File:** `compare.py`

``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

prompts = [
    "Write a one-paragraph bio of Ada Lovelace.",

    """Act as a history teacher.
    Write a one-paragraph biography of Ada Lovelace.
    Include her birth, background, achievements and
    contributions to computer science.
    Use simple English and clear sentences."""
]

for i, prompt in enumerate(prompts, 1):

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[{"role": "user", "content": prompt}]
    )

    print("\nPrompt", i)
    print(response.choices[0].message.content)
```

**Run:**

``` bash
python compare.py
```

**Comparison criteria:**

-   Relevance
-   Completeness
-   Style

------------------------------------------------------------------------

## Experiment 3: Iterative Refinement

**Aim:** Summarize Shakespeare's *Romeo and Juliet* through three rounds
of prompt refinement.

**File:** `iterative.py`

``` python
from openai import OpenAI
from dotenv import load_dotenv
import os

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

prompts = [
    "Summarize Romeo and Juliet.",

    """Summarize Romeo and Juliet in exactly
    two sentences. Use simple English.""",

    """Summarize Romeo and Juliet in exactly
    two sentences. Use simple English.
    Include the setting of Verona, the love
    between Romeo and Juliet, family conflict,
    and the theme of tragic love."""
]

for i, prompt in enumerate(prompts, 1):

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[{"role": "user", "content": prompt}]
    )

    print("\nROUND", i)
    print(response.choices[0].message.content)
```

**Run:**

``` bash
python iterative.py
```

**Iterations:**

1.  Round 1: Minimal instruction.
2.  Round 2: Adds length and style constraints.
3.  Round 3: Specifies the setting, characters' love, family conflict,
    and theme.

**Document the improvement:** Compare how each round changes the
relevance, structure, and completeness of the summary.

------------------------------------------------------------------------

## Experiment 4: Diagnosing Prompt Failures and Edge Cases

**Aim:** Identify a vague or contradictory prompt and refine it using
clear instructions.

**File:** `failure.py`

``` python
from openai import OpenAI
from dotenv import load_dotenv
import os

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

prompts = [
    "Explain Java in detail in just 5 words.",

    """Act as a Java teacher.
    Explain Java programming in simple English.
    Include its definition, three features,
    and one real-world application.
    Use bullet points."""
]

for i, prompt in enumerate(prompts, 1):

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[{"role": "user", "content": prompt}]
    )

    print("\nPROMPT", i)
    print(response.choices[0].message.content)
```

**Run:**

``` bash
python failure.py
```

**Failure analysis:**

  -----------------------------------------------------------------------
  Failure type            Example                 Remediation
  ----------------------- ----------------------- -----------------------
  Ambiguity               Explain Java            Specify Java
                                                  programming language

  Missing context         Explain this topic      Provide the topic and
                                                  background

  Contradictory           Explain in detail in    Use realistic
  instructions            five words              constraints

  Format error            Explain Java            Specify bullets or
                                                  paragraphs
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Project folder structure

``` text
PromptEngineering/
│
├── .env
├── hello.py
├── compare.py
├── iterative.py
└── failure.py
```

**Security note:** Keep the `.env` file private. Do not upload your API
key to GitHub or share it publicly.
