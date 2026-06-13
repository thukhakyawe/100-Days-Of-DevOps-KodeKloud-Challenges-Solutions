# Lab Information

The datacenter AI Commerce team is developing tools to automatically enhance product visibility and branding through intelligent marketing assistance. You are tasked to build a Python-based Product Tag and Keyword Generator that creates creative titles and SEO-optimized keyword sets for product listings, helping businesses reach the right audience effectively.

Your task is to create a function that generates a marketing title and a fixed number of SEO keywords based on a product description and a target tone. The file tag_generator.py is ready for your code.

    Initialize the OpenAIclient using api_key and base_url provided under /root/.bash_profile

    Define a function named generate_seo(description: str, tone: str) -> str.

    Construct a parameterized prompt asking the AI to:
        Generate a short, attention-grabbing product title (4-8 words) in the specified tone.
        Generate exactly ten low-competition keywords, separated by commas, for SEO tagging.

    Send this prompt to the API, ensuring the output is strictly two lines:
        Line 1: The generated title.
        Line 2: The ten comma-separated keywords.

    Use the following parameters:
        model: openai/gpt-4.1-mini
        messages: {"role": "user", "content": constructed prompt}
        max_tokens: 60
        temperature: 0.7 (for creative tone)

    In the main execution, call the function with:
        Description: 'A waterproof, lightweight backpack designed for multi-day hikes in difficult terrain.'
        Tone: 'Rugged and Adventurous'

    Print only the raw two-line output to the console.


Notes:

    Ensure you are working in the /root/openaiproject directory.

    Your API credentials will be provided under /root/.bash_profile.

    The entire output must consist of exactly two lines separated by a newline (\n), with no extra text.

    The keywords line must contain exactly 10 comma-separated values (no spaces around commas).

    Use hardcoded values for api_key and base_url when initializing the OpenAI client or read them from environment variables via os.environ.get('OPENAI_API_KEY') and os.environ.get('OPENAI_BASE_URL').

    Before running tag_generator.py, create and activate a virtual environment, then install OpenAI using:

python3 -m venv venv && source venv/bin/activate && pip install openai

    The output should be a single, case-sensitive word extracted from the AI's response.

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

Step 4 — Verify OpenAI Variables

Since your previous labs showed:

```
printenv | grep OPENAI
```

Output similar to:

OPENAI_API_KEY=...
OPENAI_API_BASE=https://kodekey.ai.kodekloud.com/v1

Use:

os.environ.get("OPENAI_API_KEY")
os.environ.get("OPENAI_API_BASE")

when creating the client.

Step 5 — Edit tag_generator.py

```
vi /root/openaiproject/tag_generator.py
```

Replace the contents with:

```
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
    base_url=os.environ.get("OPENAI_API_BASE")
)


def generate_seo(description: str, tone: str) -> str:
    prompt = f"""
Product Description:
{description}

Tone:
{tone}

Generate:
1. A short attention-grabbing product title (4-8 words).
2. Exactly 10 low-competition SEO keywords.

Output Rules:
- Output exactly two lines.
- Line 1: title only.
- Line 2: exactly 10 keywords.
- Keywords must be comma-separated.
- No spaces around commas.
- No numbering.
- No labels.
- No extra text.
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
        temperature=0.7
    )

    return response.choices[0].message.content.strip()


if __name__ == "__main__":
    result = generate_seo(
        "A waterproof, lightweight backpack designed for multi-day hikes in difficult terrain.",
        "Rugged and Adventurous"
    )

    print(result)
```

Save and exit:


Step 6 — Run the Script

```
python tag_generator.py
```

Step 7 — Verify Output Format

Example:

```
~/openaiproject ➜  python tag_generator.py
Trailblazer Waterproof Hiking Backpack  
waterproof backpack,hiking gear,multi-day backpack,lightweight hiking bag,rugged backpack,adventure gear,off-road backpack,durable hiking pack,outdoor backpack,trekking backpack
```

---

Part 2: Simple Beginner-Friendly Explanation

What This Lab Does

In this lab, you are building an AI-powered SEO content generator.

The AI receives:

A product description
A desired tone

It then generates:

A marketing title
Ten SEO keywords

This helps products become easier to find online.

- Why We Use a Prompt
prompt = f"""
...
"""

The prompt tells the AI exactly what to create.

It includes:

Product details
Desired writing style
Output format requirements

A clear prompt helps the AI produce consistent results.

- Why We Include the Tone
Tone:
Rugged and Adventurous

The tone influences how the title sounds.

For example:

Professional Tone

Lightweight Waterproof Hiking Backpack

Rugged and Adventurous Tone

Trailblazer Pack for Wild Terrain

The same product can be presented differently depending on the target audience.

- Why We Generate Keywords

Keywords help search engines understand a product.

Example keywords:

waterproofhikingpack
multidaybackpack
adventurebackpack

These keywords improve discoverability in online searches.

- Why Temperature is 0.7
temperature=0.7

A higher temperature increases creativity.

Benefits:

More engaging titles
More varied wording
Better marketing-style content

This is useful for branding and product promotion.

- How the Program Works
The product description is provided.
The desired tone is provided.
Both are inserted into the prompt.
The prompt is sent to the AI model.
The model creates:
One title
Ten keywords
The result is returned and printed.

- Example Input
Description:
A waterproof, lightweight backpack designed for multi-day hikes in difficult terrain.

Tone:
Rugged and Adventurous

- Example Output
Trailblazer Pack for Wild Terrain
waterproofhikingpack,multidaybackpack,ruggedtrailgear,lightweighthikingbag,adventurebackpack,trekkingequipment,outdoorhikingpack,mountaintrailpack,durablehikegear,backpackingessentials

The first line is the marketing title.

The second line contains exactly ten SEO keywords separated by commas with no spaces, matching the lab requirements.

---