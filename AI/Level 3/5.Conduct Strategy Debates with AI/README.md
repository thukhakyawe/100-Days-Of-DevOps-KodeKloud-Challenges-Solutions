# Lab Information

The xfusion AI Development Team is investigating how artificial intelligence can support executive decision-making through role-based analysis. In this project, you will leverage AI to think like a business leader, evaluate opportunities and challenges, and generate strategic recommendations based on the provided business context.

Your task is to build a three-stage AI pipeline in strategy_debate.py to simulate a high-level business strategy meeting. A business_problem.txt file has been created for you.

    Initialize Client: Create the OpenAI client using api_key& base_url.

    Read Problem: Read the content of business_problem.txt provided under /root/openaiproject.

    Call 1 (The "CMO"): — Define the function:
    def get_cmo_args(problem: str) -> str:
    """Generates the CMO's (pro–e-commerce)  arguments."""

        Call the OpenAI API with a system prompt: "You are a Chief Marketing Officer. You are ambitious and focused on growth. Argue for the e-commerce platform in three concise bullet points."

        Use the business problem as the user prompt. Store the AI's response (the 3 bullets) in a variable.

    Call 2 (The "CFO"): — Define the function:
    def get_cfo_args(problem: str) -> str:
    """Generates the CFO's (pro–physical stores) arguments."""

        Call the OpenAI API a second time with a different system prompt: "You are a Chief Financial Officer. You are cautious and risk-averse. Argue for modernizing the physical stores in three concise bullet points."

        Use the business problem as the user prompt. Store the AI's response in a second variable.

    Call 3 (The "CEO"): — Define the function:

    def get_ceo_decision(problem: str, cmo_args: str, cfo_args: str) -> str:
    """Synthesizes arguments from both sides and produces the CEO's final decision."""

        Construct a new, large prompt that includes:

        1) The original business problem,
        2) The CMO's arguments, and
        3) The CFO's arguments.

        Call the OpenAI API a third time with a final system prompt: "You are the CEO. Review the business problem and the conflicting advice from your CMO and CFO. Make a final decision and write a one-paragraph executive summary explaining your reasoning and the chosen path."

    Final Output: print() only the final one-paragraph executive summary from the CEO.


Notes:

    Ensure you are working in the /root/openaiproject directory.

    Your API credentials will be provided under /root/.bash_profile.

    This script will make three separate calls to the OpenAI API.

    The input for Call 3 must contain the outputs from Call 1 and Call 2.

    The final printed output should be only the CEO's summary.

    Use temperature=0.5 & max_tokens=150 for CMO, CFO and temperature=0.7& max_tokens=200for CEO.
    Use model="openai/gpt-4.1-mini" for all the calls.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_API_BASE').

    Before running strategy_debate.py, create and activate a virtual environment, then install OpenAI using:

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

Verify:

echo $OPENAI_API_KEY
echo $OPENAI_API_BASE
```

Step 5: Create strategy_debate.py

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


def get_cmo_args(problem: str) -> str:
    """
    Stage 1: Generate the CMO's arguments.
    """
    system_prompt = (
        "You are a Chief Marketing Officer. "
        "You are ambitious and focused on growth. "
        "Argue for the e-commerce platform in three concise bullet points."
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.5,
        max_tokens=150,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": problem}
        ]
    )

    return response.choices[0].message.content.strip()


def get_cfo_args(problem: str) -> str:
    """
    Stage 2: Generate the CFO's arguments.
    """
    system_prompt = (
        "You are a Chief Financial Officer. "
        "You are cautious and risk-averse. "
        "Argue for modernizing the physical stores in three concise bullet points."
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.5,
        max_tokens=150,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": problem}
        ]
    )

    return response.choices[0].message.content.strip()


def get_ceo_decision(problem: str, cmo_args: str, cfo_args: str) -> str:
    """
    Stage 3: Generate the CEO's executive summary.
    """
    system_prompt = (
        "You are the CEO. Review the business problem and the conflicting advice "
        "from your CMO and CFO. Make a final decision and write a one-paragraph "
        "executive summary explaining your reasoning and the chosen path."
    )

    user_prompt = (
        f"Business Problem:\n{problem}\n\n"
        f"CMO Arguments:\n{cmo_args}\n\n"
        f"CFO Arguments:\n{cfo_args}"
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

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    os.chdir("/root/openaiproject")

    with open("business_problem.txt", "r", encoding="utf-8") as file:
        business_problem = file.read()

    cmo_arguments = get_cmo_args(business_problem)
    cfo_arguments = get_cfo_args(business_problem)

    ceo_summary = get_ceo_decision(
        business_problem,
        cmo_arguments,
        cfo_arguments
    )

    print(ceo_summary)
```

Step 6: Run the script

```
python strategy_debate.py
```

The output should be a single executive summary paragraph, for example:

```
~/openaiproject ➜  python strategy_debate.py
After carefully weighing the arguments, I have decided to allocate the full $30M capital to building a world-class e-commerce platform. While modernizing physical stores offers lower risk and immediate benefits, the scalability, global reach, and data-driven advantages of a robust e-commerce presence are critical for long-term growth and competitiveness in today’s retail landscape. Investing in e-commerce aligns with evolving consumer behaviors favoring online shopping and provides greater cost efficiency and agility. We will continue incremental improvements in our physical stores through operational budgets, but the major capital investment must prioritize digital transformation to future-proof the company and unlock exponential growth potential.
```

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1: Import the required libraries
import os
from openai import OpenAI

What this does

This imports:

os to read environment variables and change the working directory.
OpenAI to communicate with the AI model.

Step 2: Initialize the OpenAI client
api_key = os.environ.get("OPENAI_API_KEY")
base_url = os.environ.get("OPENAI_API_BASE")

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)
What this does

The program reads the API credentials from the environment variables and creates an OpenAI client.

This client is used for all three AI requests.

Step 3: Store the model name
MODEL_NAME = "openai/gpt-4.1-mini"
What this does

Stores the model name in a constant so it can be reused throughout the program.

Function 1: get_cmo_args()

Step 4: Assign the AI the CMO role
system_prompt = (
    "You are a Chief Marketing Officer..."
)
What this does

The system prompt tells the AI to act as a Chief Marketing Officer (CMO) who is focused on business growth.

The AI is instructed to support expanding the e-commerce platform and provide three concise bullet points.

Step 5: Generate the CMO's arguments
response = client.chat.completions.create(...)
What this does

The business problem is sent to the AI.

The request uses:

Model: openai/gpt-4.1-mini
Temperature: 0.5
Max Tokens: 150

The AI returns three bullet points supporting the e-commerce strategy.

Step 6: Return the CMO's response
return response.choices[0].message.content.strip()
What this does

Retrieves and returns the CMO's arguments from the AI response.

Function 2: get_cfo_args()

Step 7: Assign the AI the CFO role
system_prompt = (
    "You are a Chief Financial Officer..."
)
What this does

The system prompt tells the AI to act as a Chief Financial Officer (CFO) who is cautious and focused on minimizing financial risk.

The AI is instructed to argue in favor of modernizing physical stores.

Step 8: Generate the CFO's arguments
response = client.chat.completions.create(...)
What this does

The same business problem is sent to the AI, but with a different role and objective.

The AI returns three concise bullet points supporting investment in physical stores.

Step 9: Return the CFO's response
return response.choices[0].message.content.strip()
What this does

Returns the CFO's arguments for use in the final decision.

Function 3: get_ceo_decision()

Step 10: Assign the AI the CEO role
system_prompt = (
    "You are the CEO..."
)
What this does

The system prompt tells the AI to act as the Chief Executive Officer (CEO) responsible for making the final business decision.

Step 11: Build the decision-making prompt
user_prompt = (
    f"Business Problem:\n{problem}\n\n"
    f"CMO Arguments:\n{cmo_args}\n\n"
    f"CFO Arguments:\n{cfo_args}"
)
What this does

Creates a single prompt containing:

The original business problem.
The CMO's recommendations.
The CFO's recommendations.

This gives the CEO all the information needed to make an informed decision.

Step 12: Generate the CEO's executive summary
response = client.chat.completions.create(...)
What this does

The AI reviews both viewpoints and generates a one-paragraph executive summary.

The request uses:

Model: openai/gpt-4.1-mini
Temperature: 0.7 for more balanced reasoning.
Max Tokens: 200

Step 13: Return the CEO's decision
return response.choices[0].message.content.strip()
What this does

Returns the final executive summary to the main program.

Main Execution

Step 14: Change to the project directory
os.chdir("/root/openaiproject")
What this does

Ensures the program is working in the correct directory where the input file is located.

Step 15: Read the business problem
with open("business_problem.txt", "r", encoding="utf-8") as file:
    business_problem = file.read()
What this does

Reads the business scenario from business_problem.txt.

This information becomes the input for all three AI calls.

Step 16: Generate the CMO's arguments
cmo_arguments = get_cmo_args(business_problem)
What this does

Runs the first stage of the pipeline.

The AI generates three marketing-focused arguments supporting e-commerce.

Step 17: Generate the CFO's arguments
cfo_arguments = get_cfo_args(business_problem)
What this does

Runs the second stage of the pipeline.

The AI generates three finance-focused arguments supporting physical store modernization.

Step 18: Generate the CEO's decision
ceo_summary = get_ceo_decision(
    business_problem,
    cmo_arguments,
    cfo_arguments
)
What this does

Runs the final stage of the pipeline.

The AI reviews the original problem along with the CMO's and CFO's recommendations to produce the CEO's executive summary.

Step 19: Print the final result
print(ceo_summary)
What this does

Prints only the CEO's one-paragraph executive summary, which is the required output for the lab.

---