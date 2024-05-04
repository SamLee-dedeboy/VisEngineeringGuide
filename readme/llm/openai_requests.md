# OpenAI Requests

## Basics

### Applying for api key

Go to https://platform.openai.com/api-keys and apply for an api key.

### Keeping it safe

The most common mistake for exposing the api key is **accidentally pushing it to github**.

You can avoid this by:

1. Create a local file called `api_key` and put the key inside.
2. Add `api_key` to `gitignore` so you don't accidentally push the api key to github.
3. Read api key programmatically:

```python
from openai import OpenAI
api_key=open("api_key").read()
client=OpenAi(api_key=api_key)
```

**Note**: Never paste the api key as a constant in your code because you will end up pushing it to github

## Making GPT Requests

A basic code for making OpenAI GPT Requests (without error handling):

```python
def request_chatgpt(client, messages, model="gpt-3.5-turbo-0125", format=None):
    if format == "json":
        response = client.chat.completions.create(
            model = model,
            messages=messages,
            response_format={ "type": "json_object" },
            temperature=0,
        )
    else:
        response = client.chat.completions.create(
            model=model,
            messages=messages,
            temperature=0,
        )
    return response.choices[0].message.content
```

List of available models: https://platform.openai.com/docs/models

### Error handling

Error handling is not a trivial thing as many errors could happen. You'll want to checkout OpenAI's documentation for error codes: https://platform.openai.com/docs/guides/error-codes/api-errors

Here's an example that handles `RateLimitError`:

```python
def request_chatgpt(client, messages, model='gpt-3.5-turbo-0125', format=None):
    try:
        if format == "json":
            response = client.chat.completions.create(
                model=model,
                messages=messages,
                response_format={ "type": "json_object" },
                temperature=0.5,
            )
        else:
            response = client.chat.completions.create(
                model=model,
                messages=messages,
                temperature=0.5,
            )
        return response.choices[0].message.content
    except RateLimitError as e:
        print(e)
        time.sleep(5)
        return request_chatgpt(client, messages, model, format)
```

### Setting timeouts

OpenAI does a horrible job at maintaining the documentation of different versions of their APIs. This is currently the best way to set timeouts on OpenAI's requests:

<pre class="language-python"><code class="lang-python"><strong>from openai import APITimeoutError
</strong><strong># set timeout on the client direclty. 
</strong><strong># timeout=10s
</strong><strong>client=OpenAI(api_key=api_key, timeout=10)
</strong>
# catching the error:
try:
    ...
except APITimeoutError as e:
    # ... handle timeout 
</code></pre>

### Handling Context Length

LLMs all have a limit for context length. For example, `gpt-3.5-turbo-0125` has a context length of `16385 tokens`. If the prompt exceeds the context length of the model, you can either:

1. find a model with longer context lengths. For example, `gpt-4-turbo` has `128000 tokens` for context length (which is also very expensive).
2. Truncate the prompt to fit in the context length. There are many strategies for how to truncate the prompt, here's a basic strategy that finds the first user input and truncate 1000 characters at a time.

```python
import tiktoken
def request_chatgpt(client, messages, model='gpt-3.5-turbo-0125', format=None):
    model = "gpt-3.5-turbo-1106"
    enc = tiktoken.encoding_for_model(model)
    text = json.dumps(messages)
    while len(enc.encode(text)) > 16385:
        print("truncating...")
        # find the first user input
        for index, message in enumerate(messages):
            if message['role'] == 'user' and len(message['content']) > 1000:
                messages[index] = {
                    "role": "user",
                    "content": message['content'][:-1000]
                }
                break
        text = json.dumps(messages)
    return request_chatgpt(client, messages, model, format)
```

## Generating Embeddings

```python
import tiktoken
def get_embedding(text, model="text-embedding-3-small"):
    enc = tiktoken.encoding_for_model(model)
    while len(enc.encode(text)) > 8191:
        text = text[:-100]
    return client.embeddings.create(input = [text], model=model).data[0].embedding
```

**Note**: Embeddings models all have a context length of `8191 tokens`. List of available models: https://platform.openai.com/docs/guides/embeddings

## Multi-threading

For data-processing prompts, it's very time-saving to execute the prompts with multi-threading as the bottleneck is usually Network I/O.

### Code

```python
import concurrent
from tqdm import tqdm
def multithread_prompts(client, prompts, model="gpt-3.5-turbo-0125"):
    l = len(prompts)
    with tqdm(total=l) as pbar:
        executor = concurrent.futures.ThreadPoolExecutor(max_workers=100)
        futures = [executor.submit(request_chatgpt_gpt4, client, prompt, model) for prompt in prompts]
        for _ in concurrent.futures.as_completed(futures):
            pbar.update(1)
    concurrent.futures.wait(futures)
    return [future.result() for future in futures]
```

**Note**: The returned list of result is guaranteed to have the same order as `prompts`

### Usage

1. Write a template function for generating prompts:

```python
### params: 
# article: string
def summarization_prompt_template(article):
    messages = [
        {
            "role": "system",
            "content": """You are a summarization system. The user will give you an article. Summarize it with three sentences.
            """
        },
        {
            "role": "user",
            "content": "Article: {}".format(article)
        }
    ]
    return messages
```

2. Use the template to generate a list of prompts:

```python
articles = [...] # list of articles, each element is a string
summarization_prompts = [summarization_prompt_template(article) for article in articles]
```

3. Execute the prompts using multithread:

```python
summaries = multithread_promtps(client, summarization_prompts)
```

4. Combine the results with `zip()`:

```python
result = []
for article, summary in zip(articles, summaries):
    result.append({
        "article": article,
        "summary": summary
    })
... # do something with result
```

## Author

Sam, ytlee@ucdavis.edu

Date: Apr 9, 2024
