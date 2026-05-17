# Lab Information

The nautilus AI Innovation Team is building a smart Resume Analyzer that automatically extracts the most relevant job keywords from candidate profiles.

You are required to build a Python-based AI module that extracts exactly five comma-separated job-relevant keywords from a resume paragraph.

Inside /root/openaiproject/resume_extractor.py, create an OpenAI client using the provided API key and base URL. Then define a function:

extract_keywords(text: str) -> str

This function must construct a parameterized prompt that asks the AI to extract exactly 5 comma-separated keywords from the provided resume text.

Next, send the prompt to the OpenAI chat model using:

    model: openai/gpt-4.1-mini
    messages: user → prompt
    max_tokens: 40
    temperature: 0

Finally, print the extracted keywords.

Notes:

    Function must be named extract_keywords.

    Use the OpenAI API key and base_url from /root/.bash_profile.

    The prompt MUST demand exactly five comma-separated keywords.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL')

    Before running the script, create & activate a virtual environment and install OpenAI.

python3 -m venv venv&&source venv/bin/activate &&pip install openai

    You will use the following resume text:

Experienced DevOps engineer skilled in Python, Kubernetes, Docker, CI/CD pipelines, and cloud automation.

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

Step 4: Create the resume_extractor.py file

```
vi resume_extractor.py
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

# Function to extract keywords
def extract_keywords(text: str) -> str:

    prompt = f"""
Extract exactly 5 job-relevant keywords from the following resume text.

Return only five comma-separated keywords.

Resume:
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
        max_tokens=40,
        temperature=0
    )

    return response.choices[0].message.content


# Resume text
text = """
Experienced DevOps engineer skilled in Python, Kubernetes,
Docker, CI/CD pipelines, and cloud automation.
"""

# Store AI response
response = extract_keywords(text)

# Print extracted keywords
print(response)
```


Step 5: Save and exit vi editor


Step 6: Load environment variables

```
source /root/.bash_profile
```

Step 7: Run the script

```
python resume_extractor.py
```

Expected Output Example
DevOps, Python, Kubernetes, Docker, CI/CD

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab does

You are building an AI-powered resume keyword extractor.

The script:

Takes resume text
Sends it to an AI model
Requests exactly 5 important job keywords
Prints the extracted keywords
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

This connects your Python script to the AI service.

Credentials are stored in /root/.bash_profile.

3. Creating the extract_keywords function
def extract_keywords(text: str) -> str:

This function accepts resume text as input.

Example:

extract_keywords("Python developer with AWS experience")

4. Building the AI prompt
prompt = f"""
Extract exactly 5 job-relevant keywords...
"""

The prompt clearly instructs the AI:

extract keywords
exactly 5
comma-separated only

This helps satisfy the lab validator.

5. Sending the request to the AI
response = client.chat.completions.create(

This sends the request to the AI model.

Important settings:

model="openai/gpt-4.1-mini" → supported model
max_tokens=40 → short output
temperature=0 → deterministic and consistent responses

6. Extracting the AI response
response.choices[0].message.content

This extracts only the generated keywords from the API response.

7. Printing the result
print(response)

Displays the extracted keywords in the terminal.

---