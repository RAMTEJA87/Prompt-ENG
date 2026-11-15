# Prompt Engineering Lab Experiments

## Requirements

Install the required Python packages:

``` bash
pip install openai python-dotenv pyyaml
```

Create a `.env` file in the same directory as your Python script:

``` env
GROQ_API_KEY=your_groq_api_key
```

Keep your API key private. Do not upload the `.env` file to GitHub.

------------------------------------------------------------------------

# Experiment 1: Chain-of-Thought and Task Decomposition

## Aim

To solve a multi-step logical reasoning problem using: 1. Direct
prompting 2. Zero-shot Chain-of-Thought (CoT) prompting 3. Task
decomposition

## Code

Save as `experiment1_reasoning.py`.

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
Four friends A, B, C and D are standing in a line.

1. A is left of B.
2. C is right of B.
3. D is left of A.

Find their order.
"""

# 1. Direct Prompting
p1 = "Solve this problem:\n" + problem

r1 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": p1}]
)

print("Direct Answer:")
print(r1.choices[0].message.content)


# 2. Zero-shot Chain-of-Thought Prompting
p2 = (
    "Solve the problem carefully. Explain the key steps "
    "and give the final order:\n" + problem
)

r2 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[{"role": "user", "content": p2}]
)

print("\nZero-shot CoT:")
print(r2.choices[0].message.content)


# 3. Task Decomposition
steps = [
    "According to the conditions, who must be to the left of B?",
    "Who must be to the right of B?",
    "Where must D be positioned relative to A?",
    "Combine all the conditions and give the complete order."
]

print("\nTask Decomposition:")

for step in steps:
    prompt = (
        "Use these original conditions:\n"
        + problem
        + "\nAnswer this sub-question: "
        + step
    )

    response = client.chat.completions.create(
        model="openai/gpt-oss-20b",
        messages=[{"role": "user", "content": prompt}]
    )

    print("\nQuestion:", step)
    print(response.choices[0].message.content)
```

## Expected logical order

The conditions imply:

-   D is to the left of A.
-   A is to the left of B.
-   B is to the left of C.

Therefore, the order is:

``` text
D -> A -> B -> C
```

------------------------------------------------------------------------

# Experiment 2: JSON and YAML Generation

## Aim

To instruct the language model to generate structured data in JSON
format, validate the JSON using Python's `json` module, and convert the
validated data into YAML.

## Code

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

# Remove Markdown code fences if the model includes them
if output.startswith("```"):
    output = output.replace("```json", "", 1)
    output = output.replace("```", "").strip()

print("JSON Output:")
print(output)

# Validate JSON and convert it to YAML
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
    print("\nInvalid output:", error)
```

## Explanation

1.  The model is prompted to generate details of three books.
2.  The response is stored in the `output` variable.
3.  `json.loads()` checks whether the response is valid JSON.
4.  The number of generated books is displayed.
5.  `yaml.safe_dump()` converts the Python data into YAML format.

------------------------------------------------------------------------

# Experiment 3: Structured Format Prompting

## Aim

To instruct the language model to present information using a Markdown
table and bullet points, and verify that the response follows the
requested structure.

## Code

Save as `experiment3_structured_output.py`.

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

print(response.choices[0].message.content)
```

## Expected output format

The model should return a Markdown table similar to this:

  -----------------------------------------------------------------------
  Benefit                             Description
  ----------------------------------- -----------------------------------
  Better heart health                 Supports cardiovascular fitness.

  Weight management                   Helps burn calories and maintain a
                                      healthy weight.

  Improved mood                       Can reduce stress and improve mood.
  -----------------------------------------------------------------------

It should also include three bullet points describing the benefits.

------------------------------------------------------------------------

# Result

The three prompt engineering experiments demonstrate:

1.  **Chain-of-Thought and Task Decomposition:** Applying different
    prompting strategies to solve a logical reasoning problem.
2.  **JSON/YAML Generation:** Generating structured data, validating
    JSON syntax, and converting the data into YAML.
3.  **Structured Format Prompting:** Generating information in the
    requested Markdown table and bullet-list formats.
