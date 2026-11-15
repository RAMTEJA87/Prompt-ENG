Prompt Engineering Lab Experiments
Requirements
Install the required packages:
``` bash
pip install openai python-dotenv pyyaml
```
Create a `.env` file in the same folder:
``` env
GROQ_API_KEY=your_groq_api_key
```
Keep your API key private. Do not upload `.env` to GitHub.
---
Lab Experiment 1: Structured Format Prompting
Aim
To instruct the language model to generate information in a specific
structure using bullet lists and Markdown tables, and verify that the
output follows the requested format.
Code
Save as `experiment1_structured_format.py`.
``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

prompt = """
List three benefits of daily exercise.

Present the answer as a Markdown table
with the following columns:
Benefit and Description.

Also provide three benefits as bullet points.
"""

response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": prompt}
    ]
)

output = response.choices[0].message.content

print("Structured Output:")
print(output)

# Basic verification of the requested structure
if "|" in output and "-" in output and ("- " in output or "* " in output):
    print("\nOutput contains a Markdown table and bullet points.")
else:
    print("\nCheck whether the requested table and bullet-list formats are present.")
```
Expected Output Format
Benefit               Description
---
Better heart health   Supports cardiovascular fitness.
Weight management     Helps maintain a healthy weight.
Improved mood         Can reduce stress and improve mood.
The model should also provide three bullet points.
Result
The model generates information in a Markdown table and bullet-list
format as requested.
---
Lab Experiment 2: JSON/YAML Generation
Aim
To provide a dataset description to the language model, generate
structured data in JSON format, validate it using a parser, and convert
the validated data into YAML.
Code
Save as `experiment2_json_yaml.py`.
``` python
import os
import json
import yaml
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {
            "role": "user",
            "content": """
Generate details of 3 books.

Each book must have:
- title
- author
- publication_year

Return only a valid JSON array.
Do not use Markdown code blocks.
"""
        }
    ]
)

output = response.choices[0].message.content.strip()

# Remove Markdown code fences if present
if output.startswith("```"):
    output = output.replace("```json", "", 1)
    output = output.replace("```", "").strip()

print("JSON Output:")
print(output)

# Validate JSON
try:
    data = json.loads(output)

    if not isinstance(data, list):
        raise ValueError("Expected a JSON array.")

    print("\nValid JSON!")
    print("Number of books:", len(data))

    # Convert JSON data to YAML
    yaml_data = yaml.safe_dump(
        data,
        sort_keys=False,
        allow_unicode=True
    )

    print("\nYAML Output:")
    print(yaml_data)

except (json.JSONDecodeError, ValueError) as error:
    print("\nInvalid JSON:", error)
```
Explanation
The model receives a prompt asking it to generate details of three
books.
The response is stored in the `output` variable.
`json.loads()` validates the JSON syntax.
The number of books is displayed.
`yaml.safe_dump()` converts the validated Python data into YAML
format.
Result
The model generates book details in JSON format. Python validates the
JSON data and converts it into YAML format.
---
Lab Experiment 3: Chain-of-Thought and Task Decomposition
Aim
To solve a multi-step logical reasoning problem using direct prompting,
zero-shot Chain-of-Thought (CoT) prompting, and task decomposition, and
compare their answers.
Problem
Four friends A, B, C and D are standing in a straight line.
A is left of B.
C is right of B.
D is left of A.
Find their order.
Code
Save as `experiment3_reasoning.py`.
``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

problem = """
Four friends A, B, C and D are standing in a straight line.

1. A is left of B.
2. C is right of B.
3. D is left of A.

Find their order.
"""

# 1. Direct Prompting
p1 = "Solve this problem:\n" + problem

r1 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": p1}
    ]
)

direct_answer = r1.choices[0].message.content

print("1. Direct Answer:")
print(direct_answer)


# 2. Zero-shot Chain-of-Thought Prompting
p2 = (
    "Let's think step by step. Explain the key steps "
    "before giving the final answer:\n" + problem
)

r2 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": p2}
    ]
)

cot_answer = r2.choices[0].message.content

print("\n2. Zero-shot CoT:")
print(cot_answer)


# 3. Task Decomposition
steps = [
    "According to the conditions, who must be to the left of B?",
    "Who must be to the right of B?",
    "Where must D be positioned relative to A?",
    "Combine all the conditions and find the complete order."
]

partial_answers = []

print("\n3. Task Decomposition:")

for step in steps:
    prompt = (
        "Use the following original problem:\n"
        + problem
        + "\nAnswer this sub-question: "
        + step
    )

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[
            {"role": "user", "content": prompt}
        ]
    )

    answer = response.choices[0].message.content
    partial_answers.append(answer)

    print("\nQuestion:", step)
    print("Answer:", answer)


# 4. Combine Partial Answers
combine_prompt = """
Combine the following partial answers.
Determine the final order of A, B, C and D.

Partial answers:
""" + "\n".join(partial_answers)

combined_response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": combine_prompt}
    ]
)

print("\n4. Combined Answer:")
print(combined_response.choices[0].message.content)
```
Expected Logical Order
From the given conditions:
D is left of A.
A is left of B.
C is right of B.
Therefore, the correct order is:
``` text
D -> A -> B -> C
```
Comparison
---
Technique                           Description
---
Direct Prompting                    Gives the problem directly and asks
for an answer.
Zero-shot CoT                       Requests a step-by-step explanation
before the final answer.
Task Decomposition                  Divides the problem into smaller
questions and combines the partial
answers.
Compare each model response with the expected logical order to check
correctness.
Result
The logical puzzle is solved using direct prompting, zero-shot CoT
prompting, and task decomposition. Their responses can be compared
against the expected order to evaluate accuracy.
---
Overall Result
The three lab experiments demonstrate:
Structured Format Prompting: Generating information as Markdown
tables and bullet lists.
JSON/YAML Generation: Generating structured data, validating
JSON, and converting it into YAML.
Chain-of-Thought and Task Decomposition: Solving a logical
problem using different prompting strategies and comparing the
results.
