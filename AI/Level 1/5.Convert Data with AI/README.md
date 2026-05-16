# Lab Information

The nautilus AI Development Team is now exploring how artificial intelligence can assist in improving productivity and communication by transforming lengthy text into clear, concise bullet points for faster understanding.

You are tasked to build a Python-based AI module that converts a given paragraph into concise, easy-to-read bullet points.

Inside /root/openaiproject/converter.py, create an OpenAI client using the api_key and base_url provided for this session. Then, define a function named convert_to_bullets(text: str) -> str that constructs a parameterized prompt asking the AI to convert the given paragraph into short, meaningful bullet points.

After creating the function, send the constructed prompt to the OpenAI chat model with the following parameters:

    model: openai/gpt-4.1
    messages: user → prompt
    max_tokens: 150
    temperature: 0.1

Store the result in a variable named response and print the bullet points to the console.

Use this paragraph for conversion:

Artificial Intelligence is transforming industries by automating tasks, improving decision-making, and enabling new innovations across healthcare, finance, and education.

Notes:

    Function must accept one parameter: text.

    Use the provided OpenAI api_key and base_url from /root/.bash_profile.

    The prompt must be parameterized with the paragraph.

    Use hardcoded values for api_key&base_url when initializing the OpenAI client, or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running converter.py, create and activate a virtual environment:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 API requests due to rate limits.



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

Step 4: Create the converter.py file

```
vi converter.py
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

# Function to convert paragraph into bullet points
def convert_to_bullets(text: str) -> str:

    prompt = f"""
Convert the following paragraph into concise and meaningful bullet points.

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
        max_tokens=150,
        temperature=0.1
    )

    return response.choices[0].message.content


# Paragraph for conversion
text = """
Artificial Intelligence is transforming industries by automating tasks,
improving decision-making, and enabling new innovations across healthcare,
finance, and education.
"""

# Store AI response
response = convert_to_bullets(text)

# Print bullet points
print(response)
```

Step 5: Save and exit vi editor

Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python converter.py
```

Expected Output Example

```
- Artificial Intelligence is transforming industries by automating tasks.  
- It improves decision-making processes.  
- AI enables new innovations in healthcare, finance, and education.
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered text summarizer.

The script:

Takes a paragraph as input
Sends it to an AI model
Requests concise bullet points
Prints the bullet-point summary
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

This connects your script to the AI service using credentials stored in /root/.bash_profile.

3. Creating the function
def convert_to_bullets(text: str) -> str:

This function accepts any paragraph as input.

Example:

convert_to_bullets("AI improves productivity.")

4. Building the AI prompt
prompt = f"""
Convert the following paragraph...
"""

The f makes it an f-string.

Python inserts the paragraph automatically into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the request to the AI model.

Important settings:

model="openai/gpt-4.1" → specifies the AI model
max_tokens=150 → limits output length
temperature=0.1 → makes responses focused and consistent

6. Extracting the AI response
response.choices[0].message.content

This extracts only the generated bullet points from the API response.

7. Printing the bullet points
print(response)

Displays the AI-generated summary in the terminal.

---