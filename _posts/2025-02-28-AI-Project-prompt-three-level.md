---
tags: AI
---

There are three levels in authoring propts:

# First.  Direct, simple instructions 
For Example    

    data = {
        'prompt': """You are a cartoon artist. Please substitute the girl and cat in the cartoon image with the girl and cat from their respective images, following these requirements:
                    1. Artfully substitute, freely adjust the size and position of the girl and cat.
                    2. Keep the tone and feeling of the cartoon image.""",
        'n': 1,
        'size': '1024x1024'
    }

Another Example:
    
    logging.info("Sending request to OpenAI API.")
    response = requests.post(api_url, headers=headers, files=files, data=data)
       
        response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "Translate the following text from English to Chinese."},
            {"role": "user", "content": text_to_translate}
        ],
        max_tokens=8000,
        temperature=0.3,
        top_p=1.0,
        n=1,
        stop=None
    )  




# Second. Applying Prompt Engineering Principles

content are from this page:
<https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview>



Prompt generator <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prompt-generator>

Be clear and direct <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct>

Use examples (multishot) <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting>

Let Claude think (chain of thought) <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/chain-of-thought>

Use XML tags <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/use-xml-tags>

Give Claude a role (system prompts) <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/system-prompts>

Prefill Claude’s response <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prefill-claudes-response>

Chain complex prompts <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/chain-prompts>

Long context tips <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/long-context-tips>

        

# Third. The content of the prompt could change in each situation programtically

 example: 
 
<https://github.com/anthropics/anthropic-cookbook/blob/main/skills/classification/guide.ipynb>


    prompt = textwrap.dedent("""
    You will classify a customer support ticket into one of the following categories:
    <categories>
        {{categories}}
    </categories>

    Here is the customer support ticket:
    <ticket>
        {{ticket}}
    </ticket>

    Use the following examples to help you classify the query:
    <examples>
        {{examples}}
    </examples>

    First you will think step-by-step about the problem in scratchpad tags.
    You should consider all the information provided and create a concrete argument for your classification.
    
    Respond using this format:
    <response>
        <scratchpad>Your thoughts and analysis go here</scratchpad>
        <category>The category label you chose goes here</category>
    </response>
    """).replace("{{categories}}", categories).replace("{{ticket}}", X).replace("{{examples}}", rag_string)
    response = client.messages.create( 
        messages=[{"role":"user", "content": prompt}, {"role":"assistant", "content": "<response><scratchpad>"}],
        stop_sequences=["</category>"], 
        max_tokens=4096, 
        temperature=0.0,
        model="claude-3-haiku-20240307"
