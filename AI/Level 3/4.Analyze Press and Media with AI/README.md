# Lab Information

The devops AI Development Team is experimenting with how artificial intelligence can create expressive short poetry through automation. In this project, you will explore how AI can also simulate a real-world public-relations workflow using a two-stage reasoning pipeline.

Your task is to build a two-stage AI pipeline in pr_pipeline.pythat simulates a public relations workflow. Aproduct_brief.txtfile has been created for you.

    Initialize Client: Create theOpenAIclient using api_key&base_url.

    Function 1:generate_press_release(brief_path: str, output_path: str) (The PR Specialist)
        Read the content of product_brief.txtpresent under/root/openaiproject.
        Call the OpenAI API with a prompt: "You are a PR specialist. Based on this product brief, write an exciting, 150-word professional press release."
        Save the AI's text response to the output_path (/root/openaiproject/press_release.txt).

    Function 2:analyze_press_release(pr_path: str) -> str (The Journalist)
        Read the entire content of the file generated in Step 2 (press_release.txt).
        Call the OpenAI API a second time with a different system prompt: "You are a cynical, skeptical tech journalist. Read the following press release. Identify the 'Top 3 Toughest Questions' a journalist would ask about this product. Return a JSON object with a single key tough_questions, which is a list of strings."
        Your prompt must instruct the AI to return only the raw JSON object, with no additional text, explanations, or Markdown code fences.
        Return the AI's JSON string response.

    Main Execution:
        Call generate_press_release('product_brief.txt', 'press_release.txt').
        Call analyze_press_release('press_release.txt').
        print() the final JSON string from the analyst.


Notes:

    Ensure you are working in the /root/openaiproject directory.

    Your API credentials will be provided under /root/.bash_profile.

    The script must create a new file press_release.txt and then read from it.

    The final output must be only the raw JSON string from the second API call.

    Use temperature=0.7&max_tokens=200 for the generate press release (Call 1) and temperature=0.5& max_tokens=200 for the analysis (Call 2).
    Use model="openai/gpt-4.1-mini"for both the Calls.

    Use hardcoded values for api_keyandbase_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_API_BASE').

    Correct Final output example:

  {
   " tough_questions": [
     "Question 1",
     "Question 2",
     "Question 3"
  ]
   }

    Before running pr_pipeline.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously.


---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines


Step 1: Navigate to the project directory

```
cd /root/openaiproject
```

Step 2: Create and activate a virtual environment

```
python3 -m venv venv
source venv/bin/activate
```
Step 3: Install the OpenAI SDK

```
pip install openai
```

Step 4: Load the API credentials

```
source /root/.bash_profile
```
Verify:

```
echo $OPENAI_API_KEY
echo $OPENAI_API_BASE
```

Step 5: Create pr_pipeline.py

```
import os
from openai import OpenAI

# Initialize the OpenAI Client using environment credentials
api_key = os.environ.get("OPENAI_API_KEY")
base_url = os.environ.get("OPENAI_API_BASE")

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)

# Configuration constant
MODEL_NAME = "openai/gpt-4.1-mini"


def generate_press_release(brief_path: str, output_path: str):
    """
    Stage 1: Generate a professional press release from the product brief.
    """
    with open(brief_path, "r", encoding="utf-8") as brief_file:
        brief_content = brief_file.read()

    system_prompt = "You are a PR specialist."

    user_prompt = (
        "Based on this product brief, write an exciting, "
        "150-word professional press release.\n\n"
        f"{brief_content}"
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.7,
        max_tokens=200,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ]
    )

    press_release = response.choices[0].message.content.strip()

    if press_release.startswith("```"):
        press_release = press_release.split("```", 1)[1].rsplit("```", 1)[0].strip()

    with open(output_path, "w", encoding="utf-8") as output_file:
        output_file.write(press_release)


def analyze_press_release(pr_path: str) -> str:
    """
    Stage 2: Analyze the press release and generate tough journalist questions.
    """
    with open(pr_path, "r", encoding="utf-8") as pr_file:
        press_release = pr_file.read()

    system_prompt = (
        "You are a cynical, skeptical tech journalist."
    )

    user_prompt = (
        "Read the following press release. "
        "Identify the Top 3 Toughest Questions a journalist would ask "
        "about this product.\n\n"
        "Return a JSON object with a single key "
        "'tough_questions', which is a list of strings.\n"
        "Return ONLY the raw JSON object. "
        "Do not include explanations, markdown, or code fences.\n\n"
        f"{press_release}"
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.5,
        max_tokens=200,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ]
    )

    json_response = response.choices[0].message.content.strip()

    if json_response.startswith("```json"):
        json_response = json_response.split("```json", 1)[1].rsplit("```", 1)[0].strip()
    elif json_response.startswith("```"):
        json_response = json_response.split("```", 1)[1].rsplit("```", 1)[0].strip()

    return json_response


if __name__ == "__main__":
    os.chdir("/root/openaiproject")

    generate_press_release(
        "product_brief.txt",
        "press_release.txt"
    )

    result = analyze_press_release(
        "press_release.txt"
    )

    print(result)
```

Step 6: Run the script

```
python pr_pipeline.py
```

Example output:

```
~/openaiproject ➜  python pr_pipeline.py
{
  "tough_questions": [
    "How does Project-X handle user privacy and data security given that it processes potentially sensitive email content through AI?",
    "What measures are in place to prevent the AI from generating inappropriate, biased, or factually incorrect emails?",
    "How does Project-X truly differentiate itself from existing AI-powered email assistants already available in the market?"
  ]
}
```

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1: Import the required libraries

import os
from openai import OpenAI
What this does
os reads the API credentials from environment variables and manages the working directory.
OpenAI allows the Python program to communicate with the AI model.

Step 2: Initialize the OpenAI client
api_key = os.environ.get("OPENAI_API_KEY")
base_url = os.environ.get("OPENAI_API_BASE")

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)

What this does

The program reads the API key and base URL from /root/.bash_profile and creates an OpenAI client.

This client is used for all AI requests.

Step 3: Store the model name
MODEL_NAME = "openai/gpt-4.1-mini"
What this does

Stores the model name in a constant so it can be reused in both API calls.

Function 1: generate_press_release()

Step 4: Read the product brief
with open(brief_path, "r", encoding="utf-8") as brief_file:
    brief_content = brief_file.read()
What this does

Reads the contents of product_brief.txt, which contains the product information that the AI will use to write the press release.

Step 5: Assign the AI the PR Specialist role
system_prompt = "You are a PR specialist."
What this does

The system prompt tells the AI to act as a PR specialist responsible for writing a professional press release.

Step 6: Build the press release request
user_prompt = (
    ...
)
What this does

The user prompt provides the product brief and instructs the AI to write an exciting, professional press release of approximately 150 words.

Step 7: Generate the press release
response = client.chat.completions.create(
    ...
)

What this does

The program sends the request to the AI using:

Model: openai/gpt-4.1-mini
Temperature: 0.7 for creative writing.
Max Tokens: 200 to allow enough space for a 150-word press release.

Step 8: Save the press release
with open(output_path, "w", encoding="utf-8") as output_file:
    output_file.write(press_release)
What this does

Creates press_release.txt and saves the AI-generated press release into the file.

Function 2: analyze_press_release()

Step 9: Read the generated press release
with open(pr_path, "r", encoding="utf-8") as pr_file:
    press_release = pr_file.read()
What this does

Reads the press release generated in the previous stage.

Step 10: Assign the AI the Journalist role
system_prompt = (
    "You are a cynical, skeptical tech journalist."
)
What this does

The system prompt tells the AI to think like a skeptical technology journalist who asks challenging questions.

Step 11: Build the analysis request
user_prompt = (
    ...
)
What this does

The prompt instructs the AI to:

Read the press release.
Identify the Top 3 Toughest Questions.
Return only a JSON object.
Avoid explanations, Markdown, or additional text.

Step 12: Generate the JSON response
response = client.chat.completions.create(
    ...
)
What this does

The program sends the request using:

Model: openai/gpt-4.1-mini
Temperature: 0.5
Max Tokens: 200

The AI returns a JSON object containing the toughest journalist questions.

Step 13: Clean the JSON response
json_response = response.choices[0].message.content.strip()
What this does

Retrieves the AI response and removes any unnecessary whitespace.

If the AI accidentally returns Markdown code fences, they are removed so that only the raw JSON remains.

Step 14: Return the JSON string
return json_response
What this does

Returns the JSON string to the main program.

Main Execution

Step 15: Change to the project directory
os.chdir("/root/openaiproject")
What this does

Ensures the program always works inside /root/openaiproject.

Step 16: Generate the press release
generate_press_release(
    "product_brief.txt",
    "press_release.txt"
)
What this does

Runs the first stage of the pipeline.

The AI reads the product brief and generates a professional press release, which is saved as press_release.txt.

Step 17: Analyze the press release
result = analyze_press_release(
    "press_release.txt"
)
What this does

Runs the second stage of the pipeline.

The AI reads the generated press release and produces a JSON object containing the three toughest journalist questions.

Step 18: Print the final result
print(result)
What this does

Prints only the raw JSON string generated by the second AI call, matching the lab requirements.

---