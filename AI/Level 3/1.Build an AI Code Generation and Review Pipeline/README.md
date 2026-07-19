# Lab Information

The datacenter AI Development Team is investigating how artificial intelligence can enhance software engineering workflows. You will leverage AI to generate code, perform code reviews, analyze implementation quality, and support automated development processes.

Your task is to build a two-stage AI pipeline in pipeline.py. The first stage generates code, and the second stage reviews it for security flaws. The requirements.txt file has been created under /root/openaiproject.

    Initialize Client: Create the OpenAI client using api_key& base_urlprovided under /root/.bash_profile.

    Function1:generate_code(requirements_path: str, output_path: str) (The Developer)

        Read the content of /root/openaiproject/requirements.txtfile

        Call the OpenAI API with a prompt: "Based on these requirements, write a Python script that scrapes all <h2> headlines from 'example.com' and prints them. Include imports and a main execution block. Respond ONLY with the raw Python code."

        Save the AI's code response to the output_path (web_scraper.py) file under
        /root/openaiproject.

    Function 2: review_code(code_path: str) -> str (The Reviewer)

        Read the entire content of the file generated inCall 1 (web_scraper.py) file.

        Call the OpenAI API a second time with a different system prompt: "You are a senior security architect. Review the attached Python code for any security or best-practice vulnerabilities (e.g., missing 'User-Agent' headers, no error handling for HTTP 4xx/5xx requests). Output a JSON object with a single key 'findings', which is a list of strings, each string describing one vulnerability. If no issues, return an empty list."

        Crucially, the prompt must also instruct the AI to only return the raw JSON, with no Markdown fences.

        Return the AI JSON string response.

    Main Execution:

        Call generate_code('requirements.txt', 'web_scraper.py').

        Call review_code('web_scraper.py').

        print() the final JSON string from the review.


Notes:

    Ensure you are working in the /root/openaiproject directory.

    Your API credentials will be provided under /root/.bash_profile.

    The script must create a new file web_scraper.py under /root/openaiproject & then read from it.

    The final output must be only the raw JSON string from the second API call.

    Use temperature=0.0 for the code generation (Call 1).
    Use model=openai/gpt-4.1-mini for the call.

    Use temperature=0.5 for the review (Call 2).
    Use model=openai/gpt-4.1-mini for the call.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_API_BASE').

    Before running pipeline.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.





---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1. Go to the project directory

```
cd /root/openaiproject
```

Step 2. Create and activate a virtual environment

```
python3 -m venv venv
source venv/bin/activate
```

Step 3. Install OpenAI SDK

```
pip install openai
```

Step 4. Verify API credentials

The lab states the credentials are stored in:

```
cat /root/.bash_profile
```

You should see something similar to:

export OPENAI_API_KEY=xxxx
export OPENAI_API_BASE=https://...

Load them if necessary:

```
source /root/.bash_profile
```
Verify:

```
echo $OPENAI_API_KEY
echo $OPENAI_API_BASE
```

Step 5. Create pipeline.py

```
vi pipeline.py
```

Paste the following solution.

```
import os
import json
from openai import OpenAI

# Initialize the OpenAI Client using credentials from the environment variables
# defined in /root/.bash_profile (or fallback defaults if necessary)
api_key = os.environ.get('OPENAI_API_KEY')
base_url = os.environ.get('OPENAI_API_BASE')

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)

# Configuration constants
MODEL_NAME = "openai/gpt-4.1-mini"

def generate_code(requirements_path: str, output_path: str):
    """
    Stage 1 (The Developer): Reads the requirements and instructs the AI to generate
    a secure, functional Python web scraper script.
    """
    # 1. Read the requirements document
    with open(requirements_path, "r", encoding="utf-8") as req_file:
        requirements_content = req_file.read()

    # 2. Frame the prompt for code generation
    prompt = (
        f"Based on these requirements:\n{requirements_content}\n\n"
        "Write a Python script that scrapes all <h2> headlines from 'example.com' and prints them. "
        "Include imports and a main execution block. Respond ONLY with the raw Python code."
    )

    # 3. Request code generation from OpenAI with a strict temperature of 0.0
    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.0,
        messages=[
            {"role": "user", "content": prompt}
        ]
    )

    generated_code = response.choices[0].message.content.strip()

    # Clean up accidental Markdown wrapping if the model includes them despite instructions
    if generated_code.startswith("```python"):
        generated_code = generated_code.split("```python", 1)[1].rsplit("```", 1)[0].strip()
    elif generated_code.startswith("```"):
        generated_code = generated_code.split("```", 1)[1].rsplit("```", 1)[0].strip()

    # 4. Save the AI's code output to the target path (web_scraper.py)
    with open(output_path, "w", encoding="utf-8") as out_file:
        out_file.write(generated_code)


def review_code(code_path: str) -> str:
    """
    Stage 2 (The Reviewer): Reads the generated Python script and performs
    a professional security evaluation, outputting raw JSON data.
    """
    # 1. Read the code generated in the previous step
    with open(code_path, "r", encoding="utf-8") as code_file:
        generated_code_content = code_file.read()

    # 2. Formulate system and user prompts to guide the JSON format output
    system_prompt = (
        "You are a senior security architect. Review the attached Python code for any security "
        "or best-practice vulnerabilities (e.g., missing 'User-Agent' headers, no error handling "
        "for HTTP 4xx/5xx requests). Output a JSON object with a single key 'findings', which is "
        "a list of strings, each string describing one vulnerability. If no issues, return an empty list. "
        "Crucially, you must only return the raw JSON, with no Markdown fences."
    )
    
    user_prompt = f"Here is the code to review:\n\n{generated_code_content}"

    # 3. Request a security review with a temperature of 0.5
    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.5,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ]
    )

    review_output = response.choices[0].message.content.strip()
    
    # Strip Markdown JSON blocks if present to ensure pure raw JSON output
    if review_output.startswith("```json"):
        review_output = review_output.split("```json", 1)[1].rsplit("```", 1)[0].strip()
    elif review_output.startswith("```"):
        review_output = review_output.split("```", 1)[1].rsplit("```", 1)[0].strip()

    return review_output


if __name__ == "__main__":
    # Ensure working within the correct directories
    os.chdir('/root/openaiproject')
    
    # Execution Workflow
    generate_code('requirements.txt', 'web_scraper.py')
    final_json_string = review_code('web_scraper.py')
    
    # Output only the pure raw JSON string
    print(final_json_string)
```

Step 6. Save the file

If using vi:

ESC
:wq

Step 7. Run the program

```
python pipeline.py
```

Step 8. Verify the generated file

```
ls
```

You should see:

pipeline.py  requirements.txt  venv  web_scraper.py

Step 9. View the generated scraper

```
ls -l web_scraper.py
```


```

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1: Import the required libraries

import os
from openai import OpenAI

What this does:

os lets us access environment variables and change directories.
OpenAI is the Python client used to communicate with the AI model.

Step 2: Create the OpenAI client
api_key = os.environ.get("OPENAI_API_KEY")
base_url = os.environ.get("OPENAI_API_BASE")

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)

What this does:

Reads the API credentials from the environment variables loaded from /root/.bash_profile.
Creates a client that will send requests to the OpenAI-compatible API.

Think of the client as the "connection" between your Python program and the AI service.

Step 3: Set the model name
MODEL_NAME = "openai/gpt-4.1-mini"

What this does:

Instead of typing the model name multiple times, we store it in one variable.

This makes the code easier to maintain if the model changes later.

Function 1: generate_code()

Step 4: Read the requirements file
with open(requirements_path, "r", encoding="utf-8") as req_file:
    requirements_content = req_file.read()

What this does:

This opens requirements.txt and reads all of its contents into a variable.

Example:

Create a Python scraper...

The AI will use these requirements to generate code.

Step 5: Build the prompt
prompt = (
    f"Based on these requirements:\n{requirements_content}\n\n"
    "Write a Python script..."
)

What this does:

Creates the instruction that will be sent to the AI.

The prompt contains:

the requirements file
the coding task
instructions to return only raw Python code

Step 6: Ask the AI to generate code
response = client.chat.completions.create(
    model=MODEL_NAME,
    temperature=0.0,
    messages=[
        {"role": "user", "content": prompt}
    ]
)

What this does:

Sends the prompt to the AI.

Why temperature=0.0?

Produces consistent and predictable code.
Reduces randomness.

Step 7: Extract the generated code
generated_code = response.choices[0].message.content.strip()

What this does:

Retrieves only the AI-generated Python code from the response.

Step 8: Remove Markdown code fences

Sometimes AI responds like this:

```python
import requests
...
```

This section removes the Markdown formatting so that only valid Python code is saved.

Step 9: Save the generated code

with open(output_path, "w", encoding="utf-8") as out_file:
    out_file.write(generated_code)

What this does:

Creates the file:

web_scraper.py

and writes the generated Python code into it.

Function 2: review_code()

Step 10: Read the generated Python file

with open(code_path, "r", encoding="utf-8") as code_file:
    generated_code_content = code_file.read()

What this does:

Reads the entire contents of web_scraper.py.

The reviewer AI will inspect this code.

Step 11: Create the review prompts
system_prompt = (
    "You are a senior security architect..."
)
user_prompt = f"Here is the code to review:\n\n{generated_code_content}"

What this does:

There are two prompts:

System Prompt tells the AI how to behave (as a security reviewer).
User Prompt provides the generated Python code to analyze.

The AI is instructed to return only raw JSON.

Step 12: Ask the AI to review the code

response = client.chat.completions.create(
    model=MODEL_NAME,
    temperature=0.5,
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_prompt}
    ]
)

What this does:

Sends the generated code to the AI for a security review.

Why temperature=0.5?

Allows slightly more flexibility in identifying vulnerabilities and best-practice issues.
Produces varied but relevant review findings.

Step 13: Extract the JSON response
review_output = response.choices[0].message.content.strip()

What this does:

Retrieves the AI's review output.

Example:

{
  "findings": [
    "Missing User-Agent header",
    "No timeout specified"
  ]
}

Step 14: Remove Markdown JSON fences

Sometimes AI returns:

```json
{
  "findings":[]
}
```

This step removes the Markdown formatting so that only valid JSON remains.

Step 15: Return the JSON string

return review_output

What this does:

Returns the final JSON string to the main program.

Main Execution

Step 16: Change to the project directory

os.chdir("/root/openaiproject")

What this does:

Ensures the script is running inside the correct directory.

This prevents errors such as:

FileNotFoundError: requirements.txt

if the script is launched from a different working directory.

Step 17: Generate the Python scraper

generate_code(
    "requirements.txt",
    "web_scraper.py"
)

What this does:

Runs the first AI stage.

Input:

requirements.txt

Output:

web_scraper.py

Step 18: Review the generated code
final_json_string = review_code("web_scraper.py")

What this does:

Runs the second AI stage.

The reviewer AI analyzes web_scraper.py and returns security findings as JSON.

Step 19: Print the final result
print(final_json_string)

What this does:

Displays only the raw JSON string on the terminal.

Example output:

{
  "findings": [
    "Missing User-Agent header",
    "No timeout specified",
    "Using HTTP instead of HTTPS"
  ]
}

or, if no issues are found:

{
  "findings": []
}

Overall Workflow

requirements.txt
        │
        ▼
generate_code()
        │
        ▼
OpenAI (Developer)
        │
        ▼
web_scraper.py
        │
        ▼
review_code()
        │
        ▼
OpenAI (Security Reviewer)
        │
        ▼
Raw JSON Output

Summary

This lab demonstrates a simple AI-powered software development pipeline:

Read the project requirements.
Use an AI model to generate Python code.
Save the generated code to a file.
Read the generated code.
Use the AI again to perform a security and best-practice review.
Output the review results as a raw JSON string.

This mirrors a real-world development workflow where AI assists both in writing code and reviewing it for potential issues before deployment.

---