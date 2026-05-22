# Lab Information

The xfusion AI Engineering Team is building an intelligent Summarization Assistant designed to transform lengthy or unclear developer-reported issues into concise, easy-to-understand one-line summaries. This helps improve clarity, reduce ambiguity, and make bug reports easier to analyze and reproduce.

Inside /root/openaiproject/summarizer.py, create an OpenAI client using the api_key and base_url provided for this session. Additionally, create a function named summarize(text: str) -> str. This function should build a parameterized prompt that asks the AI to summarize the given paragraph into a single-line summary.

Use the following paragraph for summarization:

Artificial Intelligence enables machines to mimic human intelligence, performing tasks such as learning, problem-solving, and decision-making with increasing accuracy.

Then send the previously defined prompt to the OpenAI chat model, save the result in a variable named response, and print the one-line summary to the console.

Model requirements:

    model: openai/gpt-4.1-mini
    messages: role=user, content=prompt
    max_tokens: 60
    temperature: 0.5


Notes:

    Your work directory must be /root/openaiproject.

    OpenAI api_key and base_url are stored in /root/.bash_profile.

    Prompt must be parameterized with the input paragraph.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running, create and activate a virtual environment:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error, so use your calls wisely.


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

Step 4: Create the summarizer.py file

```
vi summarizer.py
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

# Function to summarize text
def summarize(text: str) -> str:

    prompt = f"""
Summarize the following paragraph into a single concise line.

Paragraph:
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
        temperature=0.5
    )

    return response.choices[0].message.content


# Paragraph for summarization
text = """
Artificial Intelligence enables machines to mimic human intelligence,
performing tasks such as learning, problem-solving, and decision-making
with increasing accuracy.
"""

# Store AI response
response = summarize(text)

# Print summary
print(response)
```

Step 5: Save and exit vi editor

CTRL + X
Y
ENTER


Step 6: Load environment variables

```
source /root/.bash_profile
```
Step 7: Run the script

```
python summarizer.py
```

Expected Output Example

```
Artificial Intelligence allows machines to replicate human intelligence by learning, problem-solving, and making decisions accurately.
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered text summarizer.

The script:

Takes a paragraph as input
Sends it to an AI model
Requests a one-line summary
Prints the summarized result
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

This connects your script to the OpenAI-compatible API server.

Credentials are stored securely in /root/.bash_profile.

3. Creating the summarize function
def summarize(text: str) -> str:

This function accepts any paragraph as input.

Example:

summarize("Python is a programming language.")

4. Building the AI prompt
prompt = f"""
Summarize the following paragraph...
"""

The f creates an f-string.

Python automatically inserts the paragraph into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the summarization request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → supported AI model
max_tokens=60 → short response limit
temperature=0.5 → balanced creativity and consistency

6. Extracting the AI response
response.choices[0].message.content

This extracts only the generated summary text.

7. Printing the summary
print(response)

Displays the one-line AI-generated summary in the terminal.

---