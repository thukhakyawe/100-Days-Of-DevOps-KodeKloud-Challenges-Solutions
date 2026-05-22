# Lab Information


The devops AI team is focusing on improving their translation capabilities by using OpenAI's API. Begin by creating a new Python file named translator.py inside the /root/openaiproject directory. Import the OpenAI class in this file to enable interaction with OpenAI models.

Inside translator.py, create an OpenAI client using the API key and base URL provided for this session. Additionally, create a function named translate_to_language(text: str, language: str) -> str. This function should build a parameterized prompt that asks the AI to translate the English text into Spanish and French.

Use the following text for translation:
Good morning, how are you?

Finally, update the file to send the prompt to the OpenAI model using:

    model: openai/gpt-4.1-mini
    messages: user → prompt
    max_tokens: 100
    temperature: 0.7

Then print the translated Spanish & French output to the console.

Notes:

    Ensure you are working in the /root/openaiproject directory.

    Create and use the OpenAI client with API key + base_url from /root/.bash_profile.

    Function must accept parameters text and language.

    Prompt must be parameterized.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running, create a virtual environment and install OpenAI:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error, so use your calls wisely


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


Step 4: Create the translator.py file

```
vi translator.py
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

# Function to translate text
def translate_to_language(text: str, language: str) -> str:

    prompt = f"""
Translate the following English text into {language}.

Text:
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
        max_tokens=100,
        temperature=0.7
    )

    return response.choices[0].message.content


# Text for translation
text = "Good morning, how are you?"

# Translate to Spanish
spanish_translation = translate_to_language(text, "Spanish")

# Translate to French
french_translation = translate_to_language(text, "French")

# Print translations
print("Spanish Translation:")
print(spanish_translation)

print("\nFrench Translation:")
print(french_translation)
```

Step 5: Save and exit vi editor


Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python translator.py
```

Expected Output Example

```
Spanish Translation:
Buenos días, ¿cómo estás?

French Translation:
Bonjour, comment ça va ?
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered translator.

The script:

Takes English text
Sends it to an AI model
Requests translations into other languages
Prints the translated text
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

3. Creating the translate_to_language function
def translate_to_language(text: str, language: str) -> str:

This function accepts:

text → the English sentence
language → target translation language

Example:

translate_to_language("Hello", "Spanish")

4. Building the AI prompt
prompt = f"""
Translate the following English text into {language}.
"""

The f creates an f-string.

Python automatically inserts:

the text
the target language

into the prompt.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the translation request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → supported model
max_tokens=100 → enough space for translations
temperature=0.7 → more natural language output

6. Extracting the AI response
response.choices[0].message.content

This extracts only the translated text from the API response.

7. Printing translations
print(spanish_translation)
print(french_translation)

Displays both translated outputs in the terminal.

---