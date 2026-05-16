# Lab Information

The nautilus AI Development Team is exploring how artificial intelligence can improve code readability and maintainability by automatically generating meaningful comments. You are tasked with building a Python-based AI module that analyzes a code snippet and produces a clear, one-line comment or docstring describing its purpose.

Inside /root/openaiproject/commenter.py, create an OpenAI client using the API key and base URL provided in /root/.bash_profile. Define a function generate_comment(code_snippet: str) -> str that constructs a parameterized prompt instructing the AI to generate a one-line comment explaining the provided code.

After building the function, send the constructed prompt to the OpenAI chat model:

    model: openai/gpt-4.1-mini
    messages: user → prompt
    max_tokens: 30
    temperature: 0.2

Save the result in a variable named response and print the generated comment or docstring to the console.

Use the following snippet for testing inside your script:

def calculate_area(length, width):
 return length * width


Notes:

    Ensure you are working inside /root/openaiproject.

    api_key and base_url are stored in /root/.bash_profile.

    The function must be parameterized using the input snippet.

    The function must return the generated comment AND print it.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('API_KEY') and os.environ.get('BASE_URL').

    Before running, create a virtual environment and install OpenAI:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed up to 10 OpenAI requests. Past this limit you may encounter a rate limiter error, so optimize your request usage.



---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Step 1: Navigate to the project directory

```
cd /root/openaiproject
```

Step 2: Create and activate the virtual environment

```
python3 -m venv venv
source venv/bin/activate
```

Step 3: Install the OpenAI package

```
pip install openai
```

Step 4: Create the commenter.py file

```
vi commenter.py
```

Paste the following code into the file:

```
import os
from openai import OpenAI

# Initialize OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

# Function to generate a comment for code
def generate_comment(code_snippet: str) -> str:

    prompt = f"""
Generate a clear, concise, one-line comment or docstring explaining the purpose of the following Python code.

Code:
{code_snippet}
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
        temperature=0.2
    )

    generated_comment = response.choices[0].message.content

    print(generated_comment)

    return generated_comment


# Test code snippet
code_snippet = """
def calculate_area(length, width):
    return length * width
"""

# Store AI response
response = generate_comment(code_snippet)
```

Step 5: Save and exit Vi


Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python commenter.py
```

Expected Output Example

"""Calculate and return the area of a rectangle given its length and width."""

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered code commenter.

The program:

Takes Python code as input
Sends the code to an AI model
Asks the AI to explain the code
Prints a short one-line comment
Understanding the Code

1. Importing required modules
import os
from openai import OpenAI
os helps read environment variables
OpenAI connects your Python script to the AI service

2. Creating the OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

This creates the connection to the OpenAI-compatible API server.

Your credentials are already stored in Linux environment variables.

3. Creating the function
def generate_comment(code_snippet: str) -> str:

This function accepts any Python code snippet as input.

Example:

generate_comment("print('Hello')")

4. Building the AI prompt
prompt = f"""
Generate a clear, concise...
"""

This tells the AI exactly what to do.

The {code_snippet} part dynamically inserts the code into the request.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the prompt to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → the AI model
max_tokens=30 → keeps the answer short
temperature=0.2 → makes responses more focused and consistent

6. Extracting the AI-generated comment
response.choices[0].message.content

The API response contains metadata and text.

This extracts only the generated comment.

7. Printing the result
print(generated_comment)

Displays the AI-generated code explanation in the terminal.

---