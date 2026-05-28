# Lab Information

The devops AI Development Team is now exploring how artificial intelligence can assist in making smarter, data-driven decisions for cloud optimization. Continuing this journey, you are tasked to build a Python-based AI module that compares chipset used between two major iPhone models.

Inside compare.py, create an OpenAI client using the provided api_key and base_url under /root/.bash_profile. Then, define a function named compare(item1: str, item2: str) -> str that constructs a parameterized prompt asking the AI to compare the two given iPhone models (item1 and item2) in terms of only the chip used, ensuring the response is given in one word only.

Use the following items for comparison:

iphone 13
iphone 17

After defining the function, send the constructed prompt to the OpenAI chat model using:

    model: openai/gpt-4.1-mini
    messages: user → prompt
    max_tokens: 100
    temperature: 0.5

Store the result in a variable named response, and print the one-word comparison output to the console.

Finally, run your compare.py file to perform the chip comparison.

Notes:

    Function should accept two parameters: item1 and item2.

    Use the provided OpenAI api_key and base_url under /root/.bash_profile.

    File compare.py must be inside /root/openaiproject.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running the script run the following commands:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error, so use your calls wisely.




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

Step 4: Create the compare.py file

```
vi compare.py
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

# Function to compare iPhone chipsets
def compare(item1: str, item2: str) -> str:

    prompt = f"""
Compare the chipsets used in {item1} and {item2}.

Mention the chipset used in each iPhone model clearly and briefly.
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
        temperature=0.5
    )

    return response.choices[0].message.content


# Items to compare
item1 = "iPhone 13"
item2 = "iPhone 17"

# Store response
response = compare(item1, item2)

# Print output
print(response)
```

Step 5: Save and exit vi editor

Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python compare.py
```

Expected Output Example

```
- **iPhone 13** uses the **A15 Bionic** chipset.
- **iPhone 17** uses the **A17 Pro** chipset.
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are creating an AI-powered comparison tool.

The script:

Accepts two iPhone models
Sends them to an AI model
Asks the AI to compare only the chipsets
Prints a one-word answer
Understanding the Code

1. Importing modules
import os
from openai import OpenAI
os reads Linux environment variables
OpenAI connects Python to the AI API

2. Creating the OpenAI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

This connects your script to the AI server.

The credentials are stored securely in /root/.bash_profile.

3. Creating the compare function
def compare(item1: str, item2: str) -> str:

This function accepts two inputs.

Example:

compare("iphone 13", "iphone 17")

4. Building the AI prompt
prompt = f"""
Compare the chipset used...
"""

The f before the string means it is an f-string.

Python inserts:

{item1}
{item2}

automatically into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the comparison request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → AI model
max_tokens=100 → response length limit
temperature=0.5 → balanced creativity and consistency

6. Extracting the AI response
response.choices[0].message.content

The API response contains metadata and generated text.

This extracts only the AI answer.

7. Printing the result
print(response)

Displays the chipset comparison result in the terminal.

---