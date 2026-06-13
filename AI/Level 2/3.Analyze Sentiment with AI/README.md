# Lab Information

The datacenter AI Engineering team is developing tools to interpret the tone of developer communications. You are tasked to build a Python-based AI Sentiment Analyzer that identifies whether a given message or feedback is Positive, Negative, or Neutral, helping teams gauge emotional context quickly.

Create a Python program named under /root/openaiproject/sentiment_analyzer.py that analyzes the sentiment (Positive, Negative, or Neutral) of a given text using the OpenAI API.

    Initialize the OpenAI client with the provided api_key and base_url.

    Define a function named analyze_sentiment(text: str) -> str that:
        Constructs a parameterized prompt asking the AI to analyze the sentiment of the given text and respond with Sentiment and Reason.
        Sends this prompt to the API using the following parameters:
        model: openai/gpt-4.1-mini
        messages:{"role": "user", "content": constructed prompt}
        max_tokens: 60
        temperature: 0.0
        Extracts and returns both the Sentiment label (Positive, Negative, or Neutral) and a short Reason explaining the classification.

    In the main execution block, analyze the following text:
    I am really happy with the new update!

    Print the output strictly in the following format:
    Sentiment:<Positive/Negative/Neutral>
    Reason:<short explanation>


Notes:

    Ensure you are working in the /root/openaiproject directory.

    Your API credentials will be provided under /root/.bash_profile.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running sentiment_analyzer.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    The output should be a single, case-sensitive word & short reason extracted from the AI's response.

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


Step 3 — Verify Environment Variables

Based on your previous labs, check which OpenAI variables are available:

```
printenv | grep OPENAI
```

You will likely see:

OPENAI_API_KEY=...
OPENAI_API_BASE=...

Use the actual variable names that exist in your environment.

Step 4 — Edit sentiment_analyzer.py

```
vi /root/openaiproject/sentiment_analyzer.py
```

Replace the contents with:

```
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)


def analyze_sentiment(text: str) -> str:
    prompt = f"""
Analyze the sentiment of the following text.

Classify it as exactly one of:
- Positive
- Negative
- Neutral

Respond in exactly this format:

Sentiment:<Positive/Negative/Neutral>
Reason:<short explanation>

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
        max_tokens=60,
        temperature=0.0
    )

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    text = "I am really happy with the new update!"

    result = analyze_sentiment(text)

    print(result)
```

Save and exit:



Step 5 — Run the Script

```
python sentiment_analyzer.py
```

Step 6 — Verify Output

Expected format:

```
~/openaiproject ➜  python sentiment_analyzer.py
Sentiment:Positive  
Reason: The text expresses happiness and satisfaction with the new update.
```

---

Part 2: Simple Beginner-Friendly Explanation

- What This Lab Does

In this lab, you are building a simple AI-powered sentiment analyzer.

The AI reads a piece of text and determines whether the sentiment is:

Positive
Negative
Neutral

It also explains why it made that decision.

This helps teams quickly understand the emotional tone of messages, comments, or feedback.

- Why We Create a Prompt

prompt = f"""
Analyze the sentiment of the following text...
"""

A prompt is the instruction we send to the AI.

It tells the model:

What task to perform
Which sentiment labels are allowed
What format the response should follow

Good prompts help produce consistent results.

- Why We Use a User Message

messages=[
    {
        "role": "user",
        "content": prompt
    }
]

The user message contains:

The instructions
The text being analyzed

Think of it as:

Prompt = task description + data
User message = what is sent to the model

- Why Temperature is Zero

temperature=0.0

Temperature controls randomness.

A value of 0.0 means:

Very consistent results
Minimal creativity
More predictable classifications

This is useful for sentiment analysis because we want reliable answers.

How the Analysis Works
The script receives some text.
The text is inserted into the prompt.
The prompt is sent to the AI model.
The model determines the sentiment.
The model provides a short reason.
The result is printed to the terminal.

Example Input
I am really happy with the new update!

The phrase:

really happy

shows satisfaction and enthusiasm.

Example Output
Sentiment:Positive
Reason:Expresses happiness and satisfaction with the update.

Only the sentiment and reason are printed, making the output easy to read and process.

---
