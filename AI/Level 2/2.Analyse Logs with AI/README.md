# Lab Information

The datacenter AI Reliability team is designing intelligent tools to monitor application health and proactively detect production issues. You are tasked to build a Python-based Log Anomaly Detector and Summarizer that reads a log file, identifies the most critical failure, and generates a concise, human-readable summary — helping engineers quickly understand the nature and impact of system errors.

Your task is to build a Python script to detect and summarize critical errors from a log file. The file log_analyzer.py has been created under /root/openaiproject.

    Initialize the OpenAI client using the api_key and base_url provided in your environment.

    Define a function named analyze_log_anomaly(log_file_path: str) -> str.

    Inside the function:
        Read the entire content of the provided log file (app.log under/root/openaiproject).
        Construct the API call using both a system message and a user message.
        The system instruction is critical: You must instruct the AI to act as an auditor, focus ONLY on the [CRITICAL] line, and provide a two-part summary: 1) The error type, 2) Immediate Impact.

    Send this prompt (including the log content in the user message) to the API using the following parameters:
        model: openai/gpt-4.1-mini
        messages: [{"role": "user", "content": prompt}]
        max_tokens: 80
        temperature: 0.1 (for factual summarization)

    In the main execution, call the function with the path '/root/openaiproject/app.log' and print only the raw summary to the console.


Notes:

    Ensure you are working in the /root/openaiproject folder.

    You will be provided with an OpenAI api_key & base_urlfor this session under /root/.bash_profile

    The prompt must include the log file content.

    The final output printed should be only the concise summary text.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running log_analyzer.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.


---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines

Step 1 — Navigate to the Project Directory

```
cd /root/openaiproject
```

Step 2 — Create and Activate Virtual Environment

```
python3 -m venv venv
source venv/bin/activate
```

Step 3 — Install OpenAI SDK

```
pip install openai
```

Step 4 — Check Environment Variables

Based on your previous lab, verify the actual variable names:

printenv | grep OPENAI

You will likely see:

OPENAI_API_KEY=...
OPENAI_API_BASE=...

If so, use OPENAI_API_BASE in your code.

Step 5 — Edit log_analyzer.py

```
vi /root/openaiproject/log_analyzer.py
```

Replace the file contents with:

```
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)


def analyze_log_anomaly(log_file_path: str) -> str:
    with open(log_file_path, "r") as file:
        log_content = file.read()

    system_message = (
        "You are an auditor. Focus ONLY on the [CRITICAL] log entry. "
        "Provide a concise two-part summary:\n"
        "1. Error Type\n"
        "2. Immediate Impact\n"
        "Ignore all non-critical log entries."
    )

    user_message = f"""
Analyze the following log file.

Log Content:
{log_content}
"""

    response = client.chat.completions.create(
        model="openai/gpt-4.1-mini",
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": user_message}
        ],
        max_tokens=80,
        temperature=0.1
    )

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    result = analyze_log_anomaly(
        "/root/openaiproject/app.log"
    )

    print(result)
```

Save:



Step 6 — Run the Script

```
python log_analyzer.py
```

Example Output:

```
~/openaiproject ✖ python log_analyzer.py
1. Error Type: OutOfMemoryError in main thread due to JVM heap exhaustion.  
2. Immediate Impact: Service is shutting down immediately, causing potential downtime and service unavailability.
```

---

Part 2: Simple Beginner-Friendly Explanation

What This Lab Does

In this lab, you are building a simple AI-powered log analyzer.

Instead of manually reading hundreds or thousands of log entries, the AI:

Reads the log file.
Examines the log entries.
Focuses on the most important [CRITICAL] event.
Generates a short, human-readable summary.
Explains the immediate impact of the problem.

This helps engineers quickly understand serious production issues.

Why We Read the Log File
with open(log_file_path, "r") as file:
    log_content = file.read()

This code opens the log file and reads all its contents into a variable called log_content.

Example log:

[INFO] Service started
[WARNING] Memory usage high
[CRITICAL] Database connection failed

The AI can only analyze information that is sent to it. Therefore, the log file contents must be included in the API request.

Why We Use a System Message
system_message = (
    "You are an auditor..."
)

The system message provides instructions that guide the AI's behavior.

In this lab, it tells the AI to:

Act as an auditor.
Focus on the [CRITICAL] log entry.
Identify the error type.
Describe the immediate impact.
Avoid summarizing the entire log file.

Think of the system message as the rules the AI must follow.

Why We Use a User Message
user_message = f"""
Analyze the following log file.

Log Content:
{log_content}
"""

The user message contains the actual data that the AI will analyze.

It includes the full contents of the log file.

Think of it this way:

System message = instructions
User message = data

The AI follows the instructions from the system message while analyzing the data in the user message.

Why We Use a Low Temperature
temperature=0.1

Temperature controls how creative the AI is.

A low temperature:

Produces more consistent responses.
Reduces randomness.
Focuses on factual information.
Helps generate reliable summaries.

This is ideal for log analysis because accuracy is more important than creativity.

How the API Processes the Logs
The script reads the log file.
The log contents are added to the user message.
The system message tells the AI what to look for.
The request is sent to the AI model.
The AI finds the critical issue and summarizes it.
The summary is returned and printed to the terminal.
Example Output

If the critical log entry is:

[CRITICAL] Database connection failed

The AI might return:

1. Error Type: Database Connection Failure
2. Immediate Impact: Application cannot access backend data and user requests may fail.

Only the summary is printed to the console, making it easy for engineers to quickly understand the problem.

---