Prompt Engineering Lab Experiments
Beginner-Friendly Practical Record
This document contains three lab experiments in the exact order given in
the lab syllabus. Each experiment explains the aim, concept,
procedure, Python code, code explanation, expected output, verification,
and result.
---
Setup: Prepare Your Python Environment
1. Install the required packages
Open the VS Code terminal or PowerShell and run:
``` bash
pip install openai python-dotenv pyyaml
```
2. Create a project folder
Example folder structure:
``` text
PromptEngineeringLab/
│
├── .env
├── experiment1_structured_format.py
├── experiment2_json_yaml.py
└── experiment3_reasoning.py
```
3. Add your Groq API key
Create a file named `.env` in the project folder:
``` env
GROQ_API_KEY=your_groq_api_key
```
Replace the example value with your actual API key. Do not share the key
or upload the `.env` file to GitHub.
4. How the API connection works
The following code is used in each experiment:
``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)
```
Statement                     Purpose
---
`import os`                   Reads environment variables.
`OpenAI`                      Provides the API client.
`load_dotenv()`               Loads values from the `.env` file.
`os.getenv("GROQ_API_KEY")`   Reads the API key securely.
`base_url`                    Connects the OpenAI-compatible client to Groq.
The examples use the `openai/gpt-oss-20b` model through Groq.
---
Experiment 1: Structured Format Prompting
1. Aim
To instruct a language model to generate information in a specific
structure, such as bullet lists and Markdown tables, and verify whether
the output follows the requested format.
2. Concept
Structured Format Prompting means telling the AI exactly how its
answer should be organized.
Instead of asking:
``` text
Tell me the benefits of daily exercise.
```
We give a more specific instruction:
``` text
List three benefits of daily exercise in a Markdown table
with the columns Benefit and Description.
Also provide three benefits as bullet points.
```
This makes the response easier to read, understand, and reuse.
Common output formats
Bullet list: Useful for short points.
Numbered list: Useful for ordered steps.
Markdown table: Useful for comparing information.
JSON: Useful for structured data and applications.
3. Procedure
Import the required Python libraries.
Load the Groq API key from the `.env` file.
Create the API client.
Write a prompt specifying the required output format.
Send the prompt to the language model.
Display the generated response.
Check whether the response contains a table and bullet points.
4. Python Program
Save the file as `experiment1_structured_format.py`.
``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

# Load API key
load_dotenv()

# Connect to Groq
client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

# Define the prompt
prompt = """
List three benefits of daily exercise.

Present the answer as a Markdown table
with the following columns:
Benefit and Description.

Also provide three benefits as bullet points.
"""

# Send prompt to the model
response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": prompt}
    ]
)

# Get model output
output = response.choices[0].message.content

# Display output
print("STRUCTURED FORMAT OUTPUT")
print(output)

# Basic format verification
if "|" in output and "---" in output:
    print("\nMarkdown table detected.")
else:
    print("\nMarkdown table not detected.")

if "- " in output or "* " in output:
    print("Bullet list detected.")
else:
    print("Bullet list not detected.")
```
5. Understanding the Important Code
Code                                 Explanation
---
`prompt = """..."""`                 Stores the instructions given to the AI.
`client.chat.completions.create()`   Sends the prompt to the model.
`model="openai/gpt-oss-20b"`         Selects the model.
`messages`                           Contains the conversation input.
`role="user"`                        Identifies the prompt as a user message.
`message.content`                    Retrieves the model's answer.
`print(output)`                      Displays the answer.
How does format verification work?
`|` is commonly used to separate Markdown table columns.
`---` is commonly used in the Markdown table header separator.
`-` or `*` commonly indicates a Markdown bullet item.
This is a basic format check, not a complete Markdown validator.
6. Example Expected Output
The model may generate an answer similar to this:
Markdown table
Benefit               Description
---
Better heart health   Supports cardiovascular fitness.
Weight management     Helps maintain a healthy weight.
Improved mood         Can reduce stress and improve mood.
Bullet points
Exercise supports heart health.
Exercise helps manage body weight.
Exercise can improve mood.
The exact wording may differ because the model generates the response
dynamically.
7. Result
The language model generated information using the requested Markdown
table and bullet-list structure. The output was checked for basic
formatting indicators.
---
Experiment 2: JSON/YAML Generation
1. Aim
To provide a dataset description to a language model, generate
structured information in JSON format, validate the JSON syntax using
Python, and convert the validated data into YAML.
2. Concept
What is JSON?
JSON stands for JavaScript Object Notation. It is a text format used
to store and exchange structured data.
Example:
``` json
[
  {
    "title": "The Alchemist",
    "author": "Paulo Coelho",
    "publication_year": 1988
  }
]
```
What is YAML?
YAML is a human-readable format for representing structured data.
Example:
``` yaml
- title: The Alchemist
  author: Paulo Coelho
  publication_year: 1988
```
JSON vs YAML
---
JSON                                YAML
---
Uses braces and brackets.           Uses indentation.
Uses double quotes for strings.     Usually does not require quotes
around simple strings.
Commonly used in APIs.              Commonly used in configuration
files.
Parsed using Python's `json`        Parsed using Python's `yaml`
module.                             module.
What is parsing?
Parsing means checking and converting text into a data structure that a
program can understand.
For example:
``` python
data = json.loads(output)
```
This converts valid JSON text into Python objects such as lists and
dictionaries. If the JSON is invalid, Python raises a `JSONDecodeError`.
3. Procedure
Import `os`, `json`, `yaml`, and the API libraries.
Load the Groq API key.
Create the API client.
Ask the model to generate details of three books.
Instruct the model to return only a valid JSON array.
Retrieve the generated response.
Validate the response using `json.loads()`.
Convert the validated Python data into YAML using
`yaml.safe_dump()`.
Display both JSON and YAML outputs.
4. Python Program
Save the file as `experiment2_json_yaml.py`.
``` python
import os
import json
import yaml
from openai import OpenAI
from dotenv import load_dotenv

# Load API key
load_dotenv()

# Connect to Groq
client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

# Generate JSON using the model
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

# Get model output
output = response.choices[0].message.content.strip()

print("JSON OUTPUT:")
print(output)

# Validate JSON and convert to YAML
try:
    data = json.loads(output)

    # Check the expected data structure
    if not isinstance(data, list):
        raise ValueError("Expected a JSON array.")

    if len(data) != 3:
        raise ValueError("Expected exactly 3 books.")

    required_fields = {
        "title",
        "author",
        "publication_year"
    }

    for book in data:
        if not isinstance(book, dict):
            raise ValueError("Each book must be a JSON object.")

        if not required_fields.issubset(book.keys()):
            raise ValueError("A book is missing a required field.")

    print("\nValid JSON!")
    print("Number of books:", len(data))

    # Convert Python data into YAML
    yaml_data = yaml.safe_dump(
        data,
        sort_keys=False,
        allow_unicode=True
    )

    print("\nYAML OUTPUT:")
    print(yaml_data)

except (json.JSONDecodeError, ValueError) as error:
    print("\nInvalid JSON or data structure:", error)
```
5. Understanding the Important Code
---
Code                                      Explanation
---
`import json`                             Imports Python's JSON library.
`import yaml`                             Imports the PyYAML library.
`json.loads(output)`                      Parses JSON text into Python data.
`isinstance(data, list)`                  Checks whether the result is a
list.
`len(data)`                               Checks the number of books.
`isinstance(book, dict)`                  Checks whether each book is a
dictionary.
`required_fields.issubset(book.keys())`   Checks whether all required fields
exist.
`yaml.safe_dump()`                        Converts Python data into YAML
text.
Understand the conversion process
`<text color="secondary" weight="medium" size="xs">`{=html}DATA
FLOW`</text>`{=html}
`<box border radius="lg" padding={3} align="center" gap={2}>`{=html}
`<box background="rgba(59,130,246,0.10)" radius="md" padding={3} width="100%" align="center">`{=html}
Prompt to AI
    <text size="sm" color="secondary">Generate details of three books</text>

`</box>`{=html}
`<icon name="arrow-down" color="secondary" size="lg"/>`{=html}
`<box background="rgba(59,130,246,0.10)" radius="md" padding={3} width="100%" align="center">`{=html}
JSON response
    <text color="secondary" size="sm">Structured text from the model</text>

`</box>`{=html}
`<icon name="arrow-down" color="secondary" size="lg"/>`{=html}
`<box background="rgba(16,185,129,0.10)" radius="md" padding={3} width="100%" align="center">`{=html}
JSON validation
    <text color="secondary" size="sm">json.loads()</text>

`</box>`{=html}
`<icon name="arrow-down" color="secondary" size="lg"/>`{=html}
`<box background="rgba(16,185,129,0.10)" radius="md" padding={3} width="100%" align="center">`{=html}
Python data
    <text color="secondary" size="sm">List of dictionaries</text>

`</box>`{=html}
`<icon name="arrow-down" color="secondary" size="lg"/>`{=html}
`<box background="rgba(168,85,247,0.10)" radius="md" padding={3} width="100%" align="center">`{=html}
YAML output
    <text color="secondary" size="sm">yaml.safe_dump()</text>

`</box>`{=html} `</box>`{=html}
6. Example Expected Output
JSON output
``` json
[
  {
    "title": "The Alchemist",
    "author": "Paulo Coelho",
    "publication_year": 1988
  },
  {
    "title": "1984",
    "author": "George Orwell",
    "publication_year": 1949
  },
  {
    "title": "Wings of Fire",
    "author": "A. P. J. Abdul Kalam",
    "publication_year": 1999
  }
]
```
Validation output
``` text
Valid JSON!
Number of books: 3
```
YAML output
``` yaml
- title: The Alchemist
  author: Paulo Coelho
  publication_year: 1988
- title: '1984'
  author: George Orwell
  publication_year: 1949
- title: Wings of Fire
  author: A. P. J. Abdul Kalam
  publication_year: 1999
```
The generated book details may differ. The examples above illustrate
the expected structure.
7. What if JSON is invalid?
If the model returns invalid JSON:
Read the parser error.
Make the prompt more specific.
Ask the model to return only JSON, without Markdown or additional
text.
Run the program again.
Validate the new response.
8. Result
The model generated structured book information in JSON format. Python
validated the JSON syntax and required fields, and the validated data
was converted into YAML format.
---
Experiment 3: Chain-of-Thought and Task Decomposition
1. Aim
To solve a multi-step logical reasoning problem using direct prompting,
zero-shot Chain-of-Thought (CoT) prompting, and task decomposition, and
compare the results.
2. Concept
What is Prompting?
Prompting means providing instructions or questions to an AI model to
obtain a desired response.
A. Direct Prompting
In direct prompting, we provide the problem and ask the model to solve
it directly.
Example:
``` text
Solve this problem: A is left of B. C is right of B.
Find their order.
```
B. Zero-shot Chain-of-Thought (CoT)
Zero-shot CoT asks the model to explain the key steps before giving its
final answer, without providing a worked example.
Example:
``` text
Let's think step by step.
Explain the key steps and give the final answer.
```
C. Task Decomposition
Task decomposition means dividing a complex problem into smaller and
simpler sub-questions.
Instead of solving the complete problem at once, we:
Identify individual conditions.
Ask questions about each condition.
Collect partial answers.
Combine the partial answers.
Determine the final answer.
Comparison
Technique            Working method
---
Direct Prompting     Asks the model for the answer directly.
Zero-shot CoT        Requests an explanation of the key steps.
Task Decomposition   Breaks the problem into smaller questions.
3. Problem Statement
Four friends A, B, C and D are standing in a straight line.
Conditions:
A is to the left of B.
C is to the right of B.
D is to the left of A.
Question: Find the correct order of the four friends.
4. Understand the Problem First
We need to arrange four people in a straight line.
The conditions can be represented as:
A is before B.
B is before C.
D is before A.
Therefore, the relationships are:
``` text
D -> A -> B -> C
```
We will now solve the same problem using three prompting techniques.
5. Python Program
Save the file as `experiment3_reasoning.py`.
``` python
import os
from openai import OpenAI
from dotenv import load_dotenv

# Load API key
load_dotenv()

# Connect to Groq
client = OpenAI(
    api_key=os.getenv("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)

# Original problem
problem = """
Four friends A, B, C and D are standing in a straight line.

1. A is left of B.
2. C is right of B.
3. D is left of A.

Find their order.
"""

# ------------------------------------------------
# 1. DIRECT PROMPTING
# ------------------------------------------------

p1 = "Solve this problem:\n" + problem

r1 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": p1}
    ]
)

direct_answer = r1.choices[0].message.content

print("1. DIRECT PROMPTING")
print(direct_answer)


# ------------------------------------------------
# 2. ZERO-SHOT CHAIN-OF-THOUGHT
# ------------------------------------------------

p2 = (
    "Let's think step by step. "
    "Explain the key steps and give the final answer:\n"
    + problem
)

r2 = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": p2}
    ]
)

cot_answer = r2.choices[0].message.content

print("\n2. ZERO-SHOT CoT")
print(cot_answer)


# ------------------------------------------------
# 3. TASK DECOMPOSITION
# ------------------------------------------------

steps = [
    "Who is to the left of B?",
    "Who is to the right of B?",
    "Where is D positioned relative to A?",
    "Combine all conditions and find the complete order."
]

partial_answers = []

print("\n3. TASK DECOMPOSITION")

for step in steps:

    prompt = (
        "Original problem:\n"
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


# ------------------------------------------------
# 4. COMBINE PARTIAL ANSWERS
# ------------------------------------------------

combine_prompt = """
Combine the following partial answers.
Find the correct order of A, B, C and D.

Partial answers:
""" + "\n".join(partial_answers)

final_response = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "user", "content": combine_prompt}
    ]
)

print("\n4. COMBINED ANSWER")
print(final_response.choices[0].message.content)
```
6. Understanding the Important Code
Step 1: Store the problem
``` python
problem = """
Four friends A, B, C and D are standing in a straight line.
...
"""
```
The complete problem is stored in a string so that it can be reused in
multiple prompts.
Step 2: Direct prompting
``` python
p1 = "Solve this problem:\n" + problem
```
The problem is sent directly to the model.
Step 3: Zero-shot CoT
``` python
p2 = (
    "Let's think step by step. "
    "Explain the key steps and give the final answer:\n"
    + problem
)
```
The prompt asks the model to explain the important steps before
providing the final answer.
Step 4: Create sub-questions
``` python
steps = [
    "Who is to the left of B?",
    "Who is to the right of B?",
    "Where is D positioned relative to A?",
    "Combine all conditions and find the complete order."
]
```
The original problem is divided into smaller questions.
Step 5: Collect partial answers
``` python
partial_answers.append(answer)
```
Each response is stored in a Python list for later use.
Step 6: Combine answers
``` python
combine_prompt = """
Combine the following partial answers.
""" + "\n".join(partial_answers)
```
The collected responses are joined into one prompt and sent to the model
to obtain a combined answer.
7. Expected Output and Manual Verification
Apply the conditions one by one
Condition 1: A is left of B.
``` text
A -> B
```
Condition 2: C is right of B.
``` text
A -> B -> C
```
Condition 3: D is left of A.
``` text
D -> A -> B -> C
```
`<box border radius="lg" padding={3} gap={2}>`{=html} Final
arrangement `<grid columns={4} gap={2}>`{=html} {#each
["D","A","B","C"] as person,i} `<grid-item>`{=html}
`<box background="rgba(16,185,129,0.12)" radius="md" padding={3} align="center" gap={1}>`{=html}
```{=html}
<title size="2xl" color="success">
```
{person}
```{=html}
</title>
```
          <text color="secondary" size="xs">Position {i+1}</text>
        </box>
      </grid-item>
    {/each}

`</grid>`{=html}
`<text color="success" weight="medium" textAlign="center">`{=html}D → A
→ B → C`</text>`{=html} `</box>`{=html}
8. Compare the Three Techniques
---
Technique                           What to verify
---
Direct Prompting                    Does the model give the correct
order?
Zero-shot CoT                       Does the explanation support the
correct order?
Task Decomposition                  Do the partial answers combine into
the correct order?
For this problem, the expected answer for all three techniques is:
``` text
D -> A -> B -> C
```
A simple accuracy calculation is:
[ `\text{Accuracy}`{=tex} =
`\frac{\text{Correct answers}}{\text{Total answers}}`{=tex}
`\times 100`{=tex} ]
For a single puzzle, the result is either 100% or 0% for each technique.
To make a meaningful comparison of accuracy, repeat the experiment with
multiple logic puzzles and count the correct final orders.
9. Result
The logical reasoning problem was solved using direct prompting,
zero-shot Chain-of-Thought prompting, and task decomposition. The
responses were compared with the manually verified answer to evaluate
correctness.
---
Final Summary of All Experiments
---
Experiment              Main concept            Python libraries
---
1. Structured Format   Tables and bullet lists `openai`,
Prompting                                       `python-dotenv`
2. JSON/YAML           Structured data and     `openai`, `json`,
Generation              parsing                 `yaml`, `python-dotenv`
3. Chain-of-Thought &  Logical reasoning and   `openai`,
Task Decomposition      sub-questions           `python-dotenv`
Important Viva Questions
1. What is prompt engineering?
Prompt engineering is the process of designing instructions to guide an
AI model toward a desired output.
2. What is structured format prompting?
It is a prompting technique that specifies how the response should be
formatted, such as tables, bullet lists, or JSON.
3. What is JSON?
JSON is a lightweight data-interchange format used to store and exchange
structured data.
4. What is YAML?
YAML is a human-readable data serialization format commonly used for
configuration files.
5. What is JSON parsing?
JSON parsing is the process of checking and converting JSON text into
Python data structures.
6. What is zero-shot prompting?
Zero-shot prompting asks a model to perform a task without giving it
examples of the expected input-output behavior.
7. What is Chain-of-Thought prompting?
It encourages a model to explain the key steps used to approach a
problem before presenting the answer.
8. What is task decomposition?
Task decomposition divides a complex problem into smaller, manageable
sub-tasks.
9. What is the difference between direct prompting and task
decomposition?
Direct prompting asks for the complete answer at once, while task
decomposition solves smaller parts and combines their answers.
10. Why do we use `python-dotenv`?
It loads environment variables from a `.env` file, allowing API keys to
be kept outside the Python source code.
---
Troubleshooting
---
Error                                             Possible solution
---
`ModuleNotFoundError: No module named 'openai'`   Run `pip install openai`.
`ModuleNotFoundError: No module named 'dotenv'`   Run `pip install python-dotenv`.
`ModuleNotFoundError: No module named 'yaml'`     Run `pip install pyyaml`.
API authentication error                          Check the `GROQ_API_KEY` value in
`.env`.
JSON decoding error                               Check the model response and refine
the prompt to request only valid
JSON.
Model or API error                                Verify the model name and your Groq
API access.
How to Run the Experiments
Run each program separately from the project folder:
``` bash
python experiment1_structured_format.py
```
``` bash
python experiment2_json_yaml.py
```
``` bash
python experiment3_reasoning.py
```
End of Prompt Engineering Lab Experiments
