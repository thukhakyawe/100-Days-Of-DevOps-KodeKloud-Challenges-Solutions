# Lab Information

The devops AI Reliability team is designing intelligent tools that analyze Kubernetes workloads, detect resource bottlenecks, and automatically recommend optimized configuration changes. Your task is to build a Python-based AI Kubernetes Resource Optimizer that reads deployment YAMLs alongside production alerts, identifies CPU and memory pressure, and generates an improved resource configuration, helping engineers proactively prevent throttling and production outages.

Your task is to build a two-stage AI pipeline in k8s_optimizer.py that reads a K8s YAML and a metrics file, calculates an optimized resource block, and generates a commit message for the change. The files nginx-deployment.yaml (low resources) and prometheus-alert.txt (high usage) are created.

    Initialize Client: Create the OpenAI client using the api_key &base_url.

    Function 1: get_optimized_resources(yaml_content: str, alert_content: str) -> str

        This function takes the content of both files.

        Call the OpenAI API (as an "SRE") with a prompt: "Read the attached deployment YAML and the Prometheus alert. The app is throttling. Calculate new resources: limits.cpu = P99 + 100m, limits.memory = P99 + 50Mi. requests should match limits. Respond ONLY with the 4-line resources: YAML block (cpu/memory for requests/limits) and nothing else."

        Return the AI's YAML block string.

    Function 2: generate_commit_message(original_yaml: str, new_resources: str) -> str

        This function takes the original YAML content and the string output from Function 1.

        Call the OpenAI API a second time (as a "Release Manager"): "Here is the original K8s YAML and the proposed new 'resources:' block. Write a one-line Git commit message that summarizes the change.

        Return the AI's single-line commit message.

    Main Execution:

        Read the content of both nginx-deployment.yaml and prometheus-alert.txt.

        Call get_optimized_resources().

        Call generate_commit_message() using the output from the first call.

        print() the final one-line commit message with (Conventional Commit format) perf: ...


Notes:

    Ensure you are working in the /root/openaiproject folder.

    You will be provided with an OpenAI api_key &base_url for this session under /root/.bash_profile

    This is a two-call chain. The output of Call 1 is the input for Call 2.

    The final output must be only the raw, single-line commit message.

    Use temperature=0.0 & max_tokens=100 for function 1 and temperature=0.5 & max_tokens=50 for function 2.
    Use model="openai/gpt-4.1-mini" for both the calls.

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_API_BASE').

    Before running k8s_optimizer.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    You are allowed a maximum of 10 requests. After this, you may encounter a rate limiter error. Therefore, use your requests judiciously."



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

echo $OPENAI_API_KEY
echo $OPENAI_API_BASE

Step 5: Create k8s_optimizer.py

```
import os
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

def get_optimized_resources(yaml_content: str, alert_content: str) -> str:
    """
    Stage 1 (The SRE): Reads deployment details and production metrics alerts 
    to calculate a new, right-sized Kubernetes resources YAML block.
    """
    system_prompt = "You are an expert SRE."
    user_prompt = (
        f"Read the attached deployment YAML and the Prometheus alert.\n\n"
        f"Deployment YAML:\n{yaml_content}\n\n"
        f"Prometheus Alert:\n{alert_content}\n\n"
        "The app is throttling. Calculate new resources: limits.cpu = P99 + 100m, "
        "limits.memory = P99 + 50Mi. requests should match limits. "
        "Respond ONLY with the 4-line resources: YAML block (cpu/memory for requests/limits) and nothing else."
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.0,
        max_tokens=100,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ]
    )
    
    # Safely index into choices list to obtain content
    optimized_block = response.choices[0].message.content.strip()
    
    # Strip any accidental Markdown fences if generated by the model
    if optimized_block.startswith("```yaml"):
        optimized_block = optimized_block.split("```yaml", 1).rsplit("```", 1)[0].strip()
    elif optimized_block.startswith("```"):
        optimized_block = optimized_block.split("```", 1).rsplit("```", 1)[0].strip()
        
    return optimized_block


def generate_commit_message(original_yaml: str, new_resources: str) -> str:
    """
    Stage 2 (The Release Manager): Takes the original configuration along with 
    the calculated block to draft a clean git commit message.
    """
    system_prompt = "You are a professional Release Manager."
    user_prompt = (
        f"Here is the original K8s YAML and the proposed new 'resources:' block.\n\n"
        f"Original YAML:\n{original_yaml}\n\n"
        f"New Resources Block:\n{new_resources}\n\n"
        "Write a one-line Git commit message that summarizes the change. "
        "Format it strictly as a Conventional Commit starting with 'perf: '."
    )

    response = client.chat.completions.create(
        model=MODEL_NAME,
        temperature=0.5,
        max_tokens=50,
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ]
    )
    
    # Safely index into choices list to obtain content
    commit_msg = response.choices[0].message.content.strip()
    return commit_msg


if __name__ == "__main__":
    # Ensure working within the correct directory
    os.chdir('/root/openaiproject')
    
    # Read the required source input files
    with open('nginx-deployment.yaml', 'r', encoding='utf-8') as f:
        yaml_data = f.read()
        
    with open('prometheus-alert.txt', 'r', encoding='utf-8') as f:
        alert_data = f.read()

    # Run Stage 1: Resource Calculation
    new_resources_yaml = get_optimized_resources(yaml_data, alert_data)
    
    # Run Stage 2: Conventional Commit Message Generation
    final_commit_message = generate_commit_message(yaml_data, new_resources_yaml)
    
    # Print only the raw, single-line commit message
    print(final_commit_message)
```

Step 6: Run the script

```
python k8s_optimizer.py
```

Example output:

```
~/openaiproject ➜  python k8s_optimizer.py
perf: increase CPU and memory resource requests and limits for nginx container in deployment
```

---

PPart 2: Simple Step-by-Step Explanations (Beginner Friendly)
Step 1: Import the required libraries
import os
from openai import OpenAI
What this does

This imports the libraries needed for the program:

os – Reads the API credentials from environment variables and changes the working directory.
OpenAI – Allows the Python program to communicate with the OpenAI API.
Step 2: Initialize the OpenAI client
api_key = os.environ.get('OPENAI_API_KEY')
base_url = os.environ.get('OPENAI_API_BASE')

client = OpenAI(
    api_key=api_key,
    base_url=base_url
)
What this does

The program reads the API key and base URL from the environment variables loaded from /root/.bash_profile.

These credentials are then used to create an OpenAI client, which is responsible for sending requests to the AI model.

Step 3: Store the model name
MODEL_NAME = "openai/gpt-4.1-mini"
What this does

Stores the model name in a constant so it can be reused in multiple API calls without repeating the same string.

Function 1: get_optimized_resources()
Step 4: Assign the AI the SRE role
system_prompt = "You are an expert SRE."
What this does

The system prompt tells the AI to act as an expert Site Reliability Engineer (SRE) whose responsibility is to analyze Kubernetes workloads and recommend optimized resource settings.

Step 5: Build the optimization request
user_prompt = (
    ...
)
What this does

The user prompt provides the AI with:

The Kubernetes Deployment YAML.
The Prometheus alert containing production metrics.
Instructions for calculating new CPU and memory resource values.
A requirement to return only the four-line Kubernetes resources: YAML block.

This ensures the AI focuses only on generating the optimized resource configuration.

Step 6: Send the request to the AI
response = client.chat.completions.create(
    model=MODEL_NAME,
    temperature=0.0,
    max_tokens=100,
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_prompt}
    ]
)
What this does

The program sends the deployment information and Prometheus alert to the AI.

The request uses:

Model: openai/gpt-4.1-mini
Temperature: 0.0 to produce consistent and deterministic resource recommendations.
Max Tokens: 100 because only a small YAML block is expected.
Step 7: Extract the optimized resources block
optimized_block = response.choices[0].message.content.strip()
What this does

Retrieves the AI-generated response from the first item in the choices list and removes any unnecessary whitespace before or after the YAML.

Step 8: Remove Markdown code fences

Sometimes the AI may return the YAML inside Markdown code fences, for example:

```yaml
resources:
  ...
```
What this does

This step removes the Markdown formatting so that only the raw Kubernetes YAML block is returned.

Step 9: Return the optimized resources
return optimized_block
What this does

Returns the optimized Kubernetes resources: block so it can be used by the next stage of the pipeline.

Function 2: generate_commit_message()
Step 10: Assign the AI the Release Manager role
system_prompt = "You are a professional Release Manager."
What this does

The system prompt instructs the AI to act as a professional Release Manager responsible for writing clear and meaningful Git commit messages.

Step 11: Build the commit message request
user_prompt = (
    ...
)
What this does

The user prompt provides the AI with:

The original Kubernetes Deployment YAML.
The optimized resources: block generated in the previous stage.

The AI is instructed to create a single-line Git commit message that summarizes the optimization and follows the Conventional Commits format by starting with perf:.

Step 12: Generate the commit message
response = client.chat.completions.create(
    model=MODEL_NAME,
    temperature=0.5,
    max_tokens=50,
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_prompt}
    ]
)
What this does

The program sends the request to the AI to generate a concise Git commit message.

The request uses:

Model: openai/gpt-4.1-mini
Temperature: 0.5 to allow a natural and readable commit message.
Max Tokens: 50 because only one short line is required.
Step 13: Extract and return the commit message
commit_msg = response.choices[0].message.content.strip()
return commit_msg
What this does

Retrieves the generated Git commit message from the AI response, removes any extra whitespace, and returns it to the main program.

Main Execution
Step 14: Change to the project directory
os.chdir('/root/openaiproject')
What this does

Changes the current working directory to /root/openaiproject.

This ensures the program can always locate the required input files regardless of where it is executed.

Step 15: Read the input files
with open('nginx-deployment.yaml', 'r', encoding='utf-8') as f:
    yaml_data = f.read()

with open('prometheus-alert.txt', 'r', encoding='utf-8') as f:
    alert_data = f.read()
What this does

Reads the contents of:

nginx-deployment.yaml, which contains the Kubernetes Deployment configuration.
prometheus-alert.txt, which contains the production alerts and resource usage information.

These files serve as the input for the AI optimization process.

Step 16: Generate the optimized resource configuration
new_resources_yaml = get_optimized_resources(yaml_data, alert_data)
What this does

Runs the first stage of the pipeline.

The AI analyzes the deployment configuration together with the Prometheus alert and generates an optimized Kubernetes resources: YAML block.

Step 17: Generate the Git commit message
final_commit_message = generate_commit_message(
    yaml_data,
    new_resources_yaml
)
What this does

Runs the second stage of the pipeline.

The AI uses the original deployment configuration and the optimized resource block to create a concise Git commit message in Conventional Commits format.

Step 18: Print the final result
print(final_commit_message)
What this does

Prints only the final one-line Git commit message, for example:

perf: increase nginx CPU and memory resources to reduce throttling

This satisfies the lab requirement that the output must be a single-line Conventional Commit message with no additional text.

---