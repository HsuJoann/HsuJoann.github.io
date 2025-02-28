---
tags: AI
---

Prompt three levels

A.  Direct, simple instructions 
           
    data = {
        'prompt': """You are a cartoon artist. Please substitute the girl and cat in the cartoon image with the girl and cat from their respective images, following these requirements:
                    1. Artfully substitute, freely adjust the size and position of the girl and cat.
                    2. Keep the tone and feeling of the cartoon image.""",
        'n': 1,
        'size': '1024x1024'
    }




    
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




B. Prompt Engineering

https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview

C. Programtically

https://github.com/anthropics/anthropic-cookbook/blob/main/skills/classification/guide.ipynb
