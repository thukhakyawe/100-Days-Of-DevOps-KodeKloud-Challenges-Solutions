# Lab Information

The devops AI DevOps Team is experimenting with how artificial intelligence can automatically build infrastructure configuration files. In this project, you will explore how AI can generate and validate Kubernetes YAML through automation.

Your task is to build a two-stage AI pipeline in config_builder.py. A user prompt will be turned into a Kubernetes Deployment YAML file, and then validated for correctness.

    Initialize Client: Create the OpenAI client using api_key and base_url provided under /root/.bash_profile.

    Function 1: generate_yaml(prompt: str, output_path: str) (The Config Generator)

    Accept a natural-language prompt.

    Call the OpenAI API with instructions: "Generate a valid Kubernetes Deployment YAML file for deployment for nginx. Respond ONLY with raw YAML."

    Save the YAML text to the file path provided (/root/openaiproject/deployment.yaml).

    Function 2: validate_yaml(file_path: str) -> bool (The Validator)

    Read the YAML file created at /root/openaiproject/deployment.yaml

    Use Python's yaml module to check whether the file is syntactically valid.

    Main Execution:

    Call generate_yaml() using a simple prompt ("create a deployment for nginx").

    Call validate_yaml().

    Print the AI's response output "VALID" if YAML is valid, otherwise print "INVALID".


Notes:

    Ensure you are working in /root/openaiproject.

    Your API credentials will be available under /root/.bash_profile.

    The final output printed must be exactly either VALID or INVALID.

    Use temperature=0.2 and max_tokens=500 for YAML generation.
    Use model=openai/gpt-4.1-mini.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_API_BASE').

    Before running config_builder.py, create and activate a virtual environment, then install dependencies using:

python3 -m venv venv && source venv/bin/activate && pip install openai pyyaml

    You are allowed a maximum of 10 total requests. After this, you may hit a rate limiter.

# Lab Solutions

Implementation Code (/root/openaiproject/config_builder.py)pythonimport os

```
import os
import yaml
from openai import OpenAI

# Initialize the OpenAI Client using environmental credentials
api_key = os.environ.get('OPENAI_API_KEY')
base_url = os.environ.get('OPENAI_API_BASE')

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)

# Configuration constants
MODEL_NAME = "openai/gpt-4.1-mini"

def generate_yaml(prompt: str, output_path: str):
    """
    Stage 1: Generates a Kubernetes Deployment YAML file based on a prompt.
    """
    system_instruction = "Generate a valid Kubernetes Deployment YAML file for deployment for nginx. Respond ONLY with raw YAML."
    
    # Execute call to OpenAI API using specific constraint parameters
    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.2,
        max_tokens=500,
        messages=[
            {"role": "system", "content": system_instruction},
            {"role": "user", "content": prompt}
        ]
    )
    
    # FIX: Explicitly index the first item [0] from the choices list
    generated_content = response.choices[0].message.content.strip()
    
    # Strip Markdown fences if the model wraps the output despite constraints
    if generated_content.startswith("```yaml"):
        generated_content = generated_content.split("```yaml", 1)[1].rsplit("```", 1)[0].strip()
    elif generated_content.startswith("```"):
        generated_content = generated_content.split("```", 1)[1].rsplit("```", 1)[0].strip()
        
    # Persist raw YAML output to the specified filesystem path
    with open(output_path, "w", encoding="utf-8") as yaml_file:
        yaml_file.write(generated_content)


def validate_yaml(file_path: str) -> bool:
    """
    Stage 2: Leverages PyYAML to structurally parse and validate syntax.
    """
    try:
        with open(file_path, "r", encoding="utf-8") as yaml_file:
            # safe_load handles standard YAML without executing arbitrary python code blocks
            yaml.safe_load(yaml_file)
        return True
    except (yaml.YAMLError, FileNotFoundError):
        return False


if __name__ == "__main__":
    # Standardize working directory target
    os.chdir('/root/openaiproject')
    
    target_output = 'deployment.yaml'
    user_prompt = "create a deployment for nginx"
    
    # Execute Pipeline
    generate_yaml(user_prompt, target_output)
    is_valid = validate_yaml(target_output)
    
    # Print strict final evaluation output
    if is_valid:
        print("VALID")
    else:
        print("INVALID")

```

To prepare your environment and execute this configuration workflow, run the following commands sequentially inside your Linux environment terminal:

# Ensure target workspace exists and enter it

```
cd /root/openaiproject
```

# Create and switch context to the python environment

```
python3 -m venv venv
source venv/bin/activate
```

# Install the correct dependencies required for the script

```
pip install openai pyyaml
```

# Import security credentials into current terminal session environment

```
source /root/.bash_profile
```

# Run validation routine

```
python3 config_builder.py
```

Expected output will be either "VALID" or "INVALID" based on the syntactic correctness of the generated YAML file.

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1: Import the required libraries
import os
import yaml
from openai import OpenAI

What this does

This imports the libraries needed by the program:

os – Reads environment variables and changes the working directory.
yaml – Validates that the generated YAML file has correct syntax.
OpenAI – Connects the Python program to the OpenAI API.

Step 2: Initialize the OpenAI client
api_key = os.environ.get('OPENAI_API_KEY')
base_url = os.environ.get('OPENAI_API_BASE')

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)
What this does

The program retrieves the API credentials from the environment variables loaded from /root/.bash_profile.

These credentials are then used to create an OpenAI client, which will be responsible for sending requests to the AI model.

Step 3: Store the model name
MODEL_NAME = "openai/gpt-4.1-mini"
What this does

Instead of writing the model name multiple times, it is stored in a constant.

This makes the code easier to update if the model changes in the future.

Function 1: generate_yaml()

Step 4: Create the system instruction
system_instruction = (
    "Generate a valid Kubernetes Deployment YAML file for deployment for nginx. "
    "Respond ONLY with raw YAML."
)
What this does

This instruction tells the AI:

Generate a Kubernetes Deployment.
Use nginx.
Return only raw YAML.
Do not include explanations or Markdown formatting.

Step 5: Send the request to the AI
response = client.chat.completions.create(
    model=MODEL_NAME,
    temperature=0.2,
    max_tokens=500,
    messages=[
        {"role": "system", "content": system_instruction},
        {"role": "user", "content": prompt}
    ]
)
What this does

The program sends two messages to the AI:

A system message that defines the AI's role and instructions.
A user message containing the natural-language request.

The parameters are:

Model: openai/gpt-4.1-mini
Temperature: 0.2 for consistent YAML generation.
Max Tokens: 500 to limit the response length.

Step 6: Extract the generated YAML
generated_content = response.choices[0].message.content.strip()
What this does

The AI response contains a list of choices.

This line retrieves the first response and removes any unnecessary whitespace before or after the YAML.

Step 7: Remove Markdown code fences

Sometimes the AI may return:

```yaml
apiVersion: apps/v1
...
```
What this does

This section removes Markdown code fences if they are present.

This ensures that only valid YAML is saved to the file.

Step 8: Save the YAML file
with open(output_path, "w", encoding="utf-8") as yaml_file:
    yaml_file.write(generated_content)
What this does

Creates the file:

deployment.yaml

and writes the generated Kubernetes Deployment YAML into it.

Function 2: validate_yaml()

Step 9: Open the generated YAML file
with open(file_path, "r", encoding="utf-8") as yaml_file:
What this does

Opens the generated deployment.yaml file so it can be validated.

Step 10: Validate the YAML syntax
yaml.safe_load(yaml_file)
What this does

yaml.safe_load() parses the YAML document.

If the YAML syntax is correct, the file is successfully loaded.
If the YAML contains formatting errors, PyYAML raises an exception.

Using safe_load() is recommended because it safely parses YAML without executing arbitrary Python objects.

Step 11: Return the validation result

return True

or

return False
What this does

The function returns:

True if the YAML is valid.
False if a YAML syntax error or missing file is detected.
Main Execution

Step 12: Change to the project directory

os.chdir('/root/openaiproject')
What this does

Changes the current working directory to /root/openaiproject.

This ensures the program always reads from and writes to the correct location, even if it is started from another directory.

Step 13: Define the input prompt and output file

target_output = "deployment.yaml"
user_prompt = "create a deployment for nginx"
What this does

Stores the values needed for the pipeline:

user_prompt is the natural-language request sent to the AI.
target_output is the filename where the generated YAML will be saved.

Using variables instead of hardcoding these values makes the code easier to maintain and modify.

Step 14: Generate the Kubernetes Deployment YAML

generate_yaml(user_prompt, target_output)
What this does

Runs the first stage of the pipeline.

The AI receives the user's request and generates a Kubernetes Deployment YAML file named deployment.yaml.

Step 15: Validate the generated YAML

is_valid = validate_yaml(target_output)
What this does

Runs the second stage of the pipeline.

The program checks whether the generated YAML is syntactically valid using the PyYAML library.

Step 16: Print the final result

if is_valid:
    print("VALID")
else:
    print("INVALID")
What this does

Prints the final validation result.

The lab requires the output to be exactly one of the following:

VALID

or

INVALID

No additional text should be printed.

---