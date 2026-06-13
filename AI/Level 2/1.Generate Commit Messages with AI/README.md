# Lab Information

The xfusion AI Development Team is building intelligent developer-assistance tools that enhance productivity and communication across engineering workflows. Continuing this mission, you are tasked to develop a Python-based AI Commit Generator that analyzes code change descriptions and produces clean, Conventional Commit–style messages — ensuring consistency and clarity in version control histories.

Your task is to build a function that takes a summary of code changes and generates a formal Git commit message adhering to the Conventional Commit standard (<type>: <subject>). The file /root/openaiproject/commit_generator.py is ready.

    Initialize the OpenAI client using api_key & base_url credentials provided under root/.bash_profile.

    Define a function named generate_commit(changes: str) -> str.

    Construct a detailed prompt asking the AI to:
        Analyze the input changes.
        Choose the appropriate commit type from: feat, fix, or docs.
        Generate a concise subject (under 50 characters).
        The output must be only the commit message in the exact format: <type>: <subject>.

    Send this prompt to the API using the following parameters:
        model: openai/gpt-4.1-mini
        messages: [{\"role\": \"user\", \"content\": prompt}]
        max_tokens: 30
        temperature: 0.0 (for strict formatting)

    In the main execution, call the function with the changes summary:
        Summary: 'Added a new user registration endpoint and fixed a typo in the README file.'

    Print only the generated commit message to the console.


Notes:

    The final output must be a single line of text.

    The output must contain a colon (:) exactly once, separating the type and subject.

    Ensure the commit type correctly matches the primary nature of the change (e.g., feat for new functionality, fix for a bug correction, docs for documentation updates).

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running commit_generator.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    The script should strictly output only the formatted commit message, with no additional commentary or lines.

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.

---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines

Step 1 — Navigate to the Project Directory

```
cd /root/openaiproject
```

Step 2 — Create and Activate a Virtual Environment

Run:

```
python3 -m venv venv
source venv/bin/activate
```

Step 3 — Install OpenAI SDK

```
pip install openai
```

Step 4 — Open the Python File

Edit the file:

```
vi commit_generator.py
```

Replace everything with:

```
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)


def generate_commit(changes: str) -> str:
    prompt = f"""
Analyze the following code changes and generate a Conventional Commit message.

Rules:
- Choose only one commit type from: feat, fix, docs
- Format must be exactly: <type>: <subject>
- Subject must be concise and under 50 characters
- Output only the commit message
- Do not include quotes or extra text

Changes:
{changes}
"""

    response = client.chat.completions.create(
        model="openai/gpt-4.1-mini",
        messages=[
            {
                "role": "user",
                "content": prompt
            }
        ],
        max_tokens=30,
        temperature=0.0
    )

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    changes = (
        "Added a new user registration endpoint and "
        "fixed a typo in the README file."
    )

    commit_message = generate_commit(changes)

    print(commit_message)
```

Save and exit:



Step 5 — Load Environment Variables

Run:

```
source /root/.bash_profile
```

Step 6 — Execute the Script

```
python commit_generator.py
```

Step 7 — Verify Output

Expected format example:

feat: add user registration endpoint

Requirements:

Single line only
Exactly one colon
Conventional Commit format

---

Part 2: Simple Beginner-Friendly Explanation

What This Lab Does

You are building a small AI-powered Git commit message generator.

Instead of manually writing commit messages, the AI:

Reads a summary of code changes
Understands the main purpose
Generates a clean Conventional Commit message

Why Conventional Commits Matter
Conventional Commits keep Git history organized.

Examples:

feat: add login API
fix: resolve crash on startup
docs: update installation guide

This helps teams:
Read project history easily
Automate changelogs
Understand changes quickly

What the Code Is Doing
1. Importing Libraries
from openai import OpenAI
This imports the OpenAI SDK so Python can talk to the AI model.

2. Creating the Client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_BASE_URL")
)

This connects your script to the AI API using credentials stored in .bash_profile.

3. Defining the Function
def generate_commit(changes: str) -> str:

This function:
Accepts a change summary
Sends it to the AI
Returns a commit message

4. Building the Prompt
prompt = f"""
...
"""

The prompt tells the AI:
Use only feat, fix, or docs
Follow exact formatting
Keep subject short
Output only the commit message
Good prompts produce reliable outputs.

5. Sending the API Request
response = client.chat.completions.create(

This sends your prompt to the AI model.
Important settings:
temperature=0.0
→ Makes output predictable and strict
max_tokens=30
→ Prevents long responses

6. Extracting the Response
return response.choices[0].message.content.strip()
This gets only the generated commit message text.

7. Running the Script
print(commit_message)
This prints the final commit message to the terminal.

Example:
feat: add user registration endpoint

---