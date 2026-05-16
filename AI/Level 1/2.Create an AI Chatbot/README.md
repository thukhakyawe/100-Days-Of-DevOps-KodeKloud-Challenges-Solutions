# Lab Information

The AI development team at Xfusion is tasked with building a role-play chatbot using OpenAI's API.

Task Requirements:

    Navigate to the /root/openaiproject/chatbot.py directory.

    Create a client instance using api_key and base_url.

    Use openai model=openai/gpt-4.1-mini

    Define a variable prompt with the following content:

You are a friendly travel guide. Greet the user and ask where they want to go.

    Send this prompt to the OpenAI chat model and store the result in variable nameresponse.

    Extract and print the generated text reply from the response

    Run the file after installing OpenAI in a virtual environment.


Notes:

    Ensure you are working inside /root/openaiproject.

    api_key&base_url are in /root/.bash_profile (typically OPENAI_API_KEY and OPENAI_API_BASE_URL).

    Install OpenAI inside a venv before running the script.

python3 -m venv venv && source venv/bin/activate && pip install openai

    Use temperature=0.7&max_tokens=100.

    Use hardcoded values for api_key&base_url when initializing the OpenAI client, or read them from environment variables via os.environ.get('API_KEY') and os.environ.get('BASE_URL').

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.

---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Step 1: Navigate to the project directory

```
cd /root/openaiproject
```

Step 2: Create and activate the virtual environment\

```
python3 -m venv venv
source venv/bin/activate
```

Step 3: Install OpenAI package

```
pip install openai
```

Step 4: Create the chatbot.py file

```
vi chatbot.py
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

# Prompt for the chatbot
prompt = "You are a friendly travel guide. Greet the user and ask where they want to go."

# Send request to OpenAI model
response = client.chat.completions.create(
    model="openai/gpt-4.1-mini",
    messages=[
        {
            "role": "user",
            "content": prompt
        }
    ],
    temperature=0.7,
    max_tokens=100
)

# Extract and print AI response
print(response.choices[0].message.content)
```

Step 5: Save and exit Vi


Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the chatbot script

```
python chatbot.py
```

Expected Output Example

Hello there! Ready for an adventure? Where would you like to travel to today?

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is doing

You are building a very simple AI chatbot.

The chatbot behaves like a travel guide.

It sends instructions to the AI model and receives a generated reply.

Understanding the Code

1. Importing libraries
import os
from openai import OpenAI
os lets Python read environment variables
OpenAI is the library used to connect to the AI model

2. Creating the AI client
client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)

This connects your Python script to the AI API.

The credentials are stored securely in Linux environment variables.

3. Creating the prompt
prompt = "You are a friendly travel guide..."

This tells the AI how to behave.

The model will follow these instructions when generating a response.

4. Sending the request
response = client.chat.completions.create(

This sends your prompt to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → specifies the AI model
temperature=0.7 → makes responses more natural and creative
max_tokens=100 → limits reply length

5. Extracting the generated text
response.choices[0].message.content

The API response contains lots of data.

This line extracts only the chatbot’s message.

6. Printing the chatbot reply
print(response.choices[0].message.content)

Displays the AI-generated travel guide greeting on the screen.

---

