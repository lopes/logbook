+++
title = "Using GPT-3.5 for SEO Advice"
date  = 2023-03-17
description = "Using OpenAI's GPT-3 to generate SEO-friendly post metadata."

[taxonomies]
tags = ["python", "blogging", "automation"]
+++

ChatGPT is a buzzword nowadays.  Suddenly, everyone was talking about it and the internet was flooded with Artificial Intelligence experts.  But beyond the hype, some people were discovering that GPT-3.5 was not bulletproof and far from perfection in many ways.  According to the [Dunning-Kruger effect](https://www.ml4devs.com/newsletter/019-chatgpt-generative-ai-large-language-model/) that's the point where people should start realizing what GPT-3.5 is and how it can be used to produce useful results.

Cut to March 6th, 2023.  I was reading some news when I stumbled upon [this post](https://medium.com/geekculture/a-paper-summarizer-with-python-and-gpt-3-2c718bc3bc88) from 2021 teaching how to use GPT-3 (not 3.5) to summarize scientific papers and, as a blogger, I thought: Why not using it to summarize my posts with SEO techniques?  Right in my first mock-ups, I expanded the idea for all fields I need to fill in every post: Title, summary, and tags.  That's how I started the [Hakspek project](https://github.com/lopes/hakspek).

{% admonition(type="info", title="Disclaimers") %}
- Being able to use GPT does not make me an AI expert, so this text is from an amateur perspective.
- I used ChatGPT to help me write some parts of this document, of course.
{% end %}


## Coding

OpenAI provides a [Python module](https://github.com/openai/openai-python) that does the hard work, but you must at least understand a couple of things to use it.

### Roles
GPT uses three roles in a conversation:

- **System**: According to [OpenAI's documentation](https://platform.openai.com/docs/guides/chat/introduction), the system role messages help set the behavior of the assistant.  Here we are going to define a context and directives for GPT work, like in an RPG game.  For Hakspek, the GPT is an SEO Advisor with some tasks.
- **User**: User role messages will instruct GPT on what exactly to do.  In Hakspek, I pass the blog post itself to be processed by GPT.
- **Assistant**: Assistant role messages are either the user providing examples of a desired behavior or the GPT response itself.  For Hakspek, it's used by GPT to answer the user.

I did my best to instruct GPT through the system role on how I wanted it to interpret my inputs and how I wanted the output, but since the model is non-deterministic, the answers can vary.  For instance, I wanted to GPT to always answer in JSON because it eases the message handling, but even with direct instructions, ~3% of the responses were not JSON compliant in my tests.

### Prompts, Completions, and Tokens
I recommend reading [this article](https://subscription.packtpub.com/book/data/9781800563193/2/ch02lvl1sec06/understanding-prompts-completions-and-tokens) for a better understanding of the subjects, but here's a summary:

- **Prompt**: It is each message sent to GPT, where the user can program GPT in plain text.  Hakspek instructs GPT using system and user roles to give directives of behavior and content to be processed.
- **Completion**: The GPT's message sent in response to a user's request.
- **Token**: Prompts are broken into tokens which are numeric representations of words or parts of words.  According to [OpenAI's Tokenizer page](https://platform.openai.com/tokenizer), 1 token is roughly equal to 4 characters of text in common English (~3/4 of a word) --thus: 1 token ~= 0.75 characters.  GPT-3.5 can process up to 4096 tokens per prompt.

Given that, analyzing the [conversation module in Hakspek](https://github.com/lopes/hakspek/blob/main/hakspek/conversation.py), you'll find that the user interactions as prompts, the GPT response as completion, and the math to estimate the number of tokens in the prompt.  Also, regarding the token counting, I did it lazily by just estimating it by the number of characters.  There are more interesting approaches, like [this one](https://blog.devgenius.io/counting-tokens-for-openai-gpt-3-api-59c8e0812eeb) that shows two different approaches, one of them using [OpenAI's Tiktoken library](https://github.com/openai/tiktoken).  But despite the token count, this code can be improved by separating the input into chunks of paragraphs to be handled separately, then the (sub)results could be analyzed to create the final output.  This is interesting because cutting the last part of bigger texts may bias the result.

### Temperature
Temperature defines the level of randomness in the result.  As clearly stated in [this post](https://writings.stephenwolfram.com/2023/02/what-is-chatgpt-doing-and-why-does-it-work/), the lower the temperature value, the more deterministic and less "creative" the results are.  On the other hand, the higher the temperature, the more random and "creative" (and less deterministic) the answers tend to be.  The `openai.ChatCompletion.create()` function defaults the temperature value to 1 (can vary from 0 to 2), but after reading a bit about it, I decided to use 0.8 as a default value for it.

Here's an example of temperature `0.8`:

```
Processing file: /Users/lopes/Laboratory/anchor/content/query-security-services-ip-reputation.md
Suggestions:
	Title..: Querying Multiple IP Security Services with a Simple Shell Script
	Summary: Save time and get valuable data by querying VirusTotal, AbuseIPDB, and GreyNoise at once.
	Tags...: ['information security', 'IP addresses', 'shell script']
```

The same file with temperature `0` (no creativity):

```
Processing file: /Users/lopes/Laboratory/anchor/content/query-security-services-ip-reputation.md
Suggestions:
	Title..: Querying Security Services for IP Addresses
	Summary: Learn how to use a shell script to query VirusTotal, AbuseIPDB, and GreyNoise for IP address data.
	Tags...: ['VirusTotal', 'AbuseIPDB', 'GreyNoise']
```


## Using
With the code stable, I processed every post from my blog using it.  In most cases, I was not satisfied with the output, so I re-run the code passing only that specific post to get another output (GPT is non-deterministic, remember?).  It made me change my mind because at first, I thought I would blindly accept the GPT's output, but then I started to see GPT's outputs as **suggestions**, not as final versions and it completely fits the advisor role I wanted GPT to assume.  In other words: If I had hired an SEO Advisor for my blog, most likely I would not accept all of his suggestions and would ask for more options.

For one case among 33 others, GPT refused to output in JSON.  Trying to fix it, I changed the system prompt directives, but either that broke something else or had no effect.  So I decided to face the fact that I'd have to deal with an error rate and 3% was acceptable.  Fun fact: That's why there's a commented line in [completion function](https://github.com/lopes/hakspek/blob/main/hakspek/conversation.py).  Since I was tired of retrying, I just uncommented that line and got the GPT's output manually.  Maybe in further versions, I create a most elegant solution for this issue.  hehe

In the end, I had run Hakspek once over the whole content of my blog, and approximately three times for &approx;80% of posts to get other ideas for titles, summaries, or tags.  In most cases, I used the suggestion directly, but in some cases (three tops) I used the suggestions to create my version because I was tired of retrying.  In one case, as stated before, GPT was not able to deliver the results even though I ran the script again, but the problem was specifically with the JSON format because the suggestions were created.


## Results
During the development of this work, I learned more about OpenAI's GPT and how it can hallucinate sometimes.  I think that it completely validates my approach to suggestions instead of final decisions from GPT.  In other words, for Hakspek's purpose, GPT is very effective, but cannot be blindly trusted.  For a better understanding of how GPT works, I highly recommend [this article](https://writings.stephenwolfram.com/2023/02/what-is-chatgpt-doing-and-why-does-it-work/) from Stephen Wolfram, full of under-the-hood and technical details, and [this article](https://www.ml4devs.com/newsletter/019-chatgpt-generative-ai-large-language-model/) from ML4Devs more generalistic and easy to digest.

Regarding my blog visits, a week has passed since I applied the new titles, summaries, and tags.  I had to completely change the URI for many posts due to title changing and admittedly this negatively affects the results because visitors and search engines will get a 404 error page if they try to access the former URIs.  That said, I'm still collecting data and waiting for new post indexing in search engines, so I guess it'll take some time to say precisely if the changes were good or not.  The fact is that the process of creating metadata for posts became easier because now I have an assistant to give me as many suggestions as I want, so I don't have to start from scratch.
