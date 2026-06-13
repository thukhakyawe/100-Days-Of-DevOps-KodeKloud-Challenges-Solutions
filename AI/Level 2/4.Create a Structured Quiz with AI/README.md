# Lab Information

The nautilus AI Learning team is developing intelligent training tools that automatically generate learning content for developers and students. You are tasked to build a Python-based Structured Quiz Generator that creates a single, well-formatted multiple-choice question (MCQ) in JSON format to support automated learning modules.

Build a function that generates a quiz on a given topic, ensuring the output is a strict JSON array containing a single question object. The file quiz_generator.py has been created.

    Initialize the OpenAI clientusing api_key andbase_urlunder /root/.bash_profile

    Define a function generate_quiz(topic: str) -> str.

    Implement the API call using a detailed prompt and system message to instruct the AI to generate exactly one multiple-choice question (MCQ) on the topic: 'Basic Linux Commands'.

    The final output must be a JSON array containing a single object with the keys: question, options (an array of 4 strings), and answer (the correct option text).

    Use the following parameters, and print the raw JSON output to the console:

    model: openai/gpt-4.1-mini
    max_tokens: 150
    temperature: 0.3

    You must output your response ONLY as a JSON array that strictly follows this schema:

 [
 {"question": "The question text.", "options": ["Option A", "Option B", "Option C", "Option D"], "answer": "The correct answer text."}
 ]


Notes:

    Ensure you are working in the /root/openaiproject folder.

    Use the system message to strictly enforce the JSON format and the constraint of one question.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running quiz_generator.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    Question should be related to topic Linux operating system.

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.

---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines

Step 1 — Navigate to the Project Directory

```
cd /root/openaiproject
```

Step 2 — Create and Activate a Virtual Environment

```
python3 -m venv venv
source venv/bin/activate
```

Step 3 — Install OpenAI SDK

```
pip install openai
```

Step 4 — Verify OpenAI Environment Variables

Check which variables are available:

```
printenv | grep OPENAI
```

From your previous labs, you likely have:

OPENAI_API_KEY=...
OPENAI_API_BASE=...

Use the actual variable names that exist in your environment.

Step 5 — Edit quiz_generator.py

```
vi /root/openaiproject/quiz_generator.py
```

Replace the contents with:

```
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)


def generate_quiz(topic: str) -> str:
    system_message = """
You are a quiz generator.

Generate EXACTLY one multiple-choice question.

Return ONLY a valid JSON array containing a single object.

Required schema:
[
  {
    "question": "Question text",
    "options": ["Option A", "Option B", "Option C", "Option D"],
    "answer": "Correct option text"
  }
]

Rules:
- Generate exactly one question.
- Include exactly 4 options.
- The answer must match one option exactly.
- Do not include markdown.
- Do not include explanations.
- Output JSON only.
"""

    user_message = (
        f"Generate one MCQ about the topic: {topic}. "
        f"The question should relate to Linux operating systems."
    )

    response = client.chat.completions.create(
        model="openai/gpt-4.1-mini",
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": user_message}
        ],
        max_tokens=150,
        temperature=0.3
    )

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    result = generate_quiz("Basic Linux Commands")
    print(result)
```

Save and exit:



Step 6 — Run the Script

```
python quiz_generator.py
```

Step 7 — Verify Output

Expected format:

```
~/openaiproject ➜  python quiz_generator.py
[
  {
    "question": "Which command is used to list the files and directories in the current directory in Linux?",
    "options": ["ls", "cd", "mkdir", "rm"],
    "answer": "ls"
  }
]
```

---

Part 2: Simple Beginner-Friendly Explanation

What This Lab Does

In this lab, you are building an AI-powered quiz generator.

The AI will:

Receive a topic.
Create one multiple-choice question.
Generate four answer choices.
Identify the correct answer.
Return everything in JSON format.

This can be used in learning platforms, training systems, or educational applications.

- Why We Use JSON

JSON (JavaScript Object Notation) is a common format for storing and exchanging structured data.

Example:

[
  {
    "question": "Which command lists files in Linux?",
    "options": ["ls", "pwd", "cd", "mkdir"],
    "answer": "ls"
  }
]

JSON is easy for both humans and programs to read.

- Why We Use a System Message
system_message = """
You are a quiz generator...
"""

The system message provides strict instructions to the AI.

It tells the model:

Generate only one question.
Use the required JSON format.
Include four options.
Provide the correct answer.
Return JSON only.

Think of the system message as the rules the AI must follow.

- Why We Use a User Message
user_message = (
    f"Generate one MCQ about the topic: {topic}"
)

The user message contains the actual task.

In this lab, the topic is:

Basic Linux Commands

Think of it this way:

System message = instructions
User message = specific request

- Why Temperature is 0.3
temperature=0.3

Temperature controls creativity.

A value of 0.3:

Allows some variation in questions.
Keeps answers reliable.
Produces consistent quiz content.

This is useful when generating educational questions.

---