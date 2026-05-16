# Lab Information

The devops AI Engineering team is developing tools to improve the clarity of developer-reported bugs. Developers often report issues informally, which makes them difficult to understand or reproduce.

You are tasked to build a Python-based AI Bug Description Clarifier that transforms such informal bug reports into clear, structured, and professional issue summaries.

Inside /root/openaiproject/bug_clarifier.py:

    Initialize the OpenAI client using environment values (api_key and base_url).

    Define a function clarify_bug(description: str) -> str that builds a parameterized prompt to rewrite the raw bug description.

    Send this prompt to the OpenAI Chat Completion API.

    Use the following configuration for the API call:
        model: openai/gpt-4.1-mini
        messages: user → the constructed prompt
        max_tokens: 100
        temperature: 0.0

    Use the input bug report:

  App keeps crashing when I click save.

    Store the AI response in a variable named response and print the clarified bug summary to the console.


Notes:

    Function must use the developer's input description dynamically in the prompt.

    Ensure you are working inside /root/openaiproject.

    OpenAI credentials are available in /root/.bash_profile.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('API_KEY') and os.environ.get('BASE_URL').

    Before running bug_clarifier.py, set up a virtual environment:

python3 -m venv venv && source venv/bin/activate && pip install openai

    Maximum of 10 API requests allowed before rate limiting.



# Lab Solutions



🧭 Part 1: Lab Step-by-Step Guidelines

Step 1: Navigate to the project directory

```
cd /root/openaiproject
```

Step 2: Create and activate a virtual environment

```
python3 -m venv venv

source venv/bin/activate
```

Step 3: Install the OpenAI package

```
pip install openai
```

Step 4: Create the Python file

```
vi bug_clarifier.py
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

# Function to clarify bug descriptions
def clarify_bug(description: str) -> str:

    prompt = f"""
Rewrite the following informal bug report into a clear, structured, and professional issue summary.

Bug Report:
{description}
"""

    response = client.chat.completions.create(
        model="openai/gpt-4.1-mini",
        messages=[
            {
                "role": "user",
                "content": prompt
            }
        ],
        max_tokens=100,
        temperature=0.0
    )

    return response.choices[0].message.content


# Input bug report
bug_description = "App keeps crashing when I click save."

# Store AI response
response = clarify_bug(bug_description)

# Print clarified summary
print(response)
```

Step 5: Save and exit Vi



Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python bug_clarifier.py
```

Expected Output Example

```
**Issue Summary:**  
The application crashes consistently whenever the "Save" button is clicked. This issue prevents users from successfully saving their work and disrupts normal app functionality. Further investigation is needed to identify the root cause and implement a fix.
```
---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is doing

You are building a small AI-powered Python program that takes a messy bug report and rewrites it into something professional and easier for developers to understand.

Example:

Raw input:

App keeps crashing when I click save.

AI-improved output:

The application crashes consistently when the user clicks the "Save" button.
Understanding the important parts

1. Creating the OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

This connects your Python program to the AI service.

api_key = your authentication key
base_url = the API server address

These are already stored in /root/.bash_profile.

2. The function
def clarify_bug(description: str) -> str:

This creates a reusable function.

You can pass any bug description into it.

Example:

clarify_bug("Login button not working")

3. Building the AI prompt
prompt = f"""
Rewrite the following informal bug report...
"""

This tells the AI exactly what you want it to do.

The {description} part dynamically inserts the developer’s bug report.

4. Sending the request to the AI
response = client.chat.completions.create(

This sends the prompt to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → the AI model used
max_tokens=100 → limits response size
temperature=0.0 → makes responses consistent and precise

5. Extracting the AI answer
return response.choices[0].message.content

The API response contains lots of metadata.

This line extracts only the AI-generated text.

6. Printing the result
print(response)

Displays the clarified bug summary on the terminal screen.

---