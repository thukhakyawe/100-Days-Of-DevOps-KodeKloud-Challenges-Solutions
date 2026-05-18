# Lab Information

The datacenter AI Development Team is experimenting with how artificial intelligence can create expressive short poetry through automation. In this task, you are required to build a Python-based AI module that generates three-line haikus (5-7-5 syllable pattern) based on a given topic.

Inside /root/openaiproject/haiku_generator.py, create an OpenAI client using the api_key and base_url provided for this session. Additionally, define a function named generate_haiku(topic: str) -> str. This function should construct a parameterized prompt that instructs the AI to generate a haiku about the specified topic, strictly following the 5-7-5 syllable structure.

Then, send the parameterized prompt to the OpenAI chat model using:

    model: openai/gpt-4.1-mini
    prompt: parameterized_prompt
    max_tokens: 60
    temperature: 0.0

Store the output in a variable named response and print the generated haiku (three distinct lines). Use the topic:

Topic: 'sky'

Notes:

    Function should accept one parameter: topic.

    Use the provided OpenAI  api_key and base_url under /root/.bash_profile.

    The prompt must be parameterized with the topic.

    Ensure you are working inside /root/openaiproject.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running, create and activate a virtual environment and install OpenAI:

python3 -m venv venv && source venv/bin/activate && pip install openai

    Final output should display three distinct lines (the haiku).

    You are allowed a maximum of 10 requests before hitting rate limits.



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

Step 4: Create the haiku_generator.py file

```
vi haiku_generator.py
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

# Function to generate haiku
def generate_haiku(topic: str) -> str:

    prompt = f"""
Generate a haiku about {topic}.

The haiku must:
- Follow the 5-7-5 syllable structure
- Contain exactly three lines
- Be concise and poetic
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
        temperature=0.0
    )

    return response.choices[0].message.content


# Topic for haiku
topic = "sky"

# Store AI response
response = generate_haiku(topic)

# Print haiku
print(response)
```

Step 5: Save and exit vi editor
Press `Esc`, then type `:wq` and hit `Enter` to save and exit.

Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python haiku_generator.py
```

Expected Output Example

Endless blue canvas,  
Whispers of clouds drift softly,  
Dreams float with the breeze.

---
🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered poetry generator.

The script:

Takes a topic as input
Sends it to an AI model
Requests a haiku poem
Prints the generated haiku

A haiku is a short Japanese-style poem with:

Line 1 → 5 syllables
Line 2 → 7 syllables
Line 3 → 5 syllables
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

This connects your Python script to the AI server using credentials stored in /root/.bash_profile.

3. Creating the generate_haiku function
def generate_haiku(topic: str) -> str:

This function accepts a topic as input.

Example:

generate_haiku("ocean")

4. Building the AI prompt
prompt = f"""
Generate a haiku about {topic}.
"""

The f creates an f-string.

Python automatically inserts the topic into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the poetry request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → supported AI model
max_tokens=60 → enough space for 3 short lines
temperature=0.0 → more consistent structure and formatting

6. Extracting the AI response
response.choices[0].message.content

This extracts only the generated haiku text.

7. Printing the haiku
print(response)

Displays the three-line haiku in the terminal.

---