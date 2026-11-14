# Prompt Engineering – Unit I Lab Programs

This file contains the three Python programs provided for the following experiments.

## 1. Few-Shot vs. Zero-Shot Comparison

```python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

text = "The product is excellent and works perfectly."

# Zero-shot prompt
zero_prompt = f"""
Classify the sentiment of the following sentence
as Positive, Negative, or Neutral.

Sentence: {text}
Sentiment:
"""

# Few-shot prompt
few_prompt = f"""
Classify the sentiment as Positive, Negative, or Neutral.

Examples:

Sentence: I love this phone.
Sentiment: Positive

Sentence: The service was very bad.
Sentiment: Negative

Sentence: The product arrived yesterday.
Sentiment: Neutral

Now classify this sentence:

Sentence: {text}
Sentiment:
"""

# Execute zero-shot prompt
response1 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": zero_prompt}]
)

# Execute few-shot prompt
response2 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": few_prompt}]
)

print("ZERO-SHOT OUTPUT:")
print(response1.choices[0].message.content)

print("\nFEW-SHOT OUTPUT:")
print(response2.choices[0].message.content)
```

## 2. Role-Based & Negative Prompting

```python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

question = "Explain how to save money every month."

# Role-based prompt
role_prompt = """
You are a professional financial educator.

Explain how a student can save money every month.
Give five practical tips using simple English.
"""

# Negative prompt
negative_prompt = """
Explain how a student can save money every month.

Give five practical tips using simple English.

Do not mention any brand names.
Do not suggest risky investments.
Do not use complicated financial terms.
"""

# Execute role-based prompt
response1 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": role_prompt}]
)

# Execute negative prompt
response2 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": negative_prompt}]
)

print("ROLE-BASED PROMPT OUTPUT:")
print(response1.choices[0].message.content)

print("\nNEGATIVE PROMPT OUTPUT:")
print(response2.choices[0].message.content)
```

## 3. Constraint Specification & Iterative Refinement

```python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

# Three prompt versions
prompts = [

    # Basic prompt
    """
    Explain Artificial Intelligence.
    """,

    # Refinement 1
    """
    Explain Artificial Intelligence in 100 words.
    Use simple English and bullet points.
    """,

    # Refinement 2
    """
    Explain Artificial Intelligence in 100 words.
    Use simple English and exactly five bullet points.
    Include its definition, three applications,
    and one advantage.
    Avoid unnecessary information.
    """
]

# Execute all three prompts
for i, prompt in enumerate(prompts, 1):

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[
            {"role": "user", "content": prompt}
        ]
    )

    print("\nREFINEMENT CYCLE", i)
    print(response.choices[0].message.content)
```
