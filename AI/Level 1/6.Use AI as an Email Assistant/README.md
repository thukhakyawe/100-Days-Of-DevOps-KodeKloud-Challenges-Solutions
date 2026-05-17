# Lab Information

The nautilus AI Development Team is now working on automating written communication tasks. As part of this initiative, you are required to build a Python-based AI Email Assistant that rewrites messages into polite and professional emails.

Inside email_assistant.py, create an OpenAI client using the provided API key and base URL under /root/.bash_profile. Then, define a function named rewrite_email(text: str) -> str that constructs a parameterized prompt asking the AI to rewrite the given email text politely and professionally.

After defining rewrite_email, send the constructed prompt to the OpenAI chat model with these parameters:

    model: openai/gpt-4.1-mini
    messages: user → prompt
    max_tokens: 60
    temperature: 0.1

Store the output in a variable named response and print the polite rewritten email to the console.

Use the following email text for rewriting:

hey send me that report asap

Notes:

    Function should accept one parameter: text.

    Use the provided OpenAI API key and base_url under /root/.bash_profile.

    File email_assistant.py must be inside /root/openaiproject.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running the script,use the following commands install OpenAI:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests before rate limiting may occur.


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

Step 4: Create the email_assistant.py file

```
vi email_assistant.py
```
Paste the following code:

```
import os
from openai import OpenAI

# Initialize OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

# Function to rewrite email politely
def rewrite_email(text: str) -> str:

    prompt = f"""
Rewrite the following email in a polite and professional tone.

Email:
{text}
"""

    response = client.chat.completions.create(
        model="openai/gpt-4.1-mini",
        messages=[
            {
                "role": "user",
                "content": prompt
            }
        ],
        max_tokens=60,
        temperature=0.1
    )

    return response.choices[0].message.content


# Original email text
text = "hey send me that report asap"

# Store AI response
response = rewrite_email(text)

# Print rewritten email
print(response)
```

Step 5: Save and exit vi editor
Press `Esc`, then type `:wq` and hit `Enter`.


Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python email_assistant.py
```

Expected Output Example

```
Subject: Request for Report

Dear [Recipient's Name],

I hope this message finds you well. Could you please send me the report at your earliest convenience? 

Thank you very much for your assistance.

Best regards,  
[Your Name]
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered email rewriting assistant.

The script:

Takes an informal email
Sends it to an AI model
Requests a polite professional rewrite
Prints the improved email
Understanding the Code

1. Importing modules
import os
from openai import OpenAI
os reads environment variables
OpenAI connects Python to the AI API

2. Creating the OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

This connects your script to the AI server using credentials stored in /root/.bash_profile.

3. Creating the rewrite function
def rewrite_email(text: str) -> str:

This function accepts any email text as input.

Example:

rewrite_email("send me the files")

4. Building the AI prompt
prompt = f"""
Rewrite the following email...
"""

The f creates an f-string.

Python inserts the input email automatically into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the rewrite request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → supported AI model
max_tokens=60 → keeps output short
temperature=0.1 → makes responses consistent and professional

6. Extracting the AI-generated email
response.choices[0].message.content

This extracts only the rewritten email text from the API response.

7. Printing the rewritten email
print(response)

Displays the professional email in the terminal.

---