# Running local models with LM Studio

- [Running local models with LM Studio](#running-local-models-with-lm-studio)
  - [What is LM Studio?](#what-is-lm-studio)
  - [Downloading and chatting with a model in LM Studio](#downloading-and-chatting-with-a-model-in-lm-studio)


## What is LM Studio?

An alternative to Ollama is **LM Studio**, with which you can:
- Run LLMs on your laptop, entirely offline
- Use models through the in-app Chat UI or an OpenAI-compatible local server
- Download any compatible model files from Hugging Face repositories
- Discover new and noteworthy LLMs on the app's home page

## Downloading and chatting with a model in LM Studio

To get started with LM Studio:
1.	In your preferred browser, navigate to: https://lmstudio.ai/.
2.	Click the button to download LM Studio for your OS.
3.	Once it’s downloaded, start the desktop app.
4.	Navigate to the model search page and pick a suitable model. Click the model’s **Download** button, as shown in *Figure 2:*

![The LM Studio model download page for DeepSeek’s R1 7B model](local-models-02.png)  

*Figure 2: The LM Studio model download page for DeepSeek’s R1 7B model*

5.	Wait for the model to download.
6.	Navigate to Chats, and at the top of the window, select the model you downloaded.
7.	In the USER box, enter a prompt, for example: What is Django in one paragraph?. Note the response, which should be reasonably accurate, as shown in *Figure 3*:

![LM Studio chat session](local-models-03.png)

*Figure 3: LM Studio chat session*

8.	In the **USER** box, enter a prompt, for example: `Who says, "Why is Gamora?", and why`?
9.	Note that the response may include hallucinations, as shown in the following response:
```
The phrase "Why is Gamora?" doesn’t appear to be widely recognized as an actual quote or line spoken by the character Gamora in any media, including Marvel's Iron Man or other related works. If you encountered this phrasing elsewhere, it might be a misremembered reference to something specific from a particular source or context (like a fan fiction, movie reference, or joke). Could you provide more context? I’d be happy to help further!
```

10.	With advanced models like DeepSeek’s R1, it can be useful to expand the **Thinking…** section, as shown in the following output and in *Figure 11.5*:
```
Thought for 12.84 seconds

Alright, so the user asked about who said "Why is Gamora?" and why. Looking back at our conversation history, I had just explained what .NET was in a detailed response. Now, they're asking about a completely different topic—Gamora.

Hmm, "Why is Gamora?" That sounds familiar, but I'm not sure where I heard it before. Let me think... It might be from a movie or a show. Maybe Iron Man 3? Gamora is a character from the Avengers, created by Marvel. She's an astrophysicist who has a strong connection with technology.

Wait, maybe they're referring to something specific like a line spoken in a particular scene or context. I should check if that phrase exists anywhere. If it's not widely known, perhaps it's from fan fiction or a lesser-known source.

Also, considering the user might be confused between different characters or misremembering something. Maybe they saw the character Gamora and associated her with a specific quote without knowing its origin.

I should make sure to explain that "Why is Gamora?" isn't a widely recognized line and suggest it's possible they're thinking of a particular source. Offering further help could be useful if they need more information or clarification.
```

![What was the model thinking?](local-models-04.png)

*Figure 4: What was the model thinking?*

11.	Now, if I ask the same question to ChatGPT-5.5, I get a much better response:
```
The line "Why is Gamora?" is said by Drax, a character from the movie Avengers: Infinity War. This moment occurs during a scene where the Avengers confront the Guardians of the Galaxy for the first time. 
[cont.]
```
