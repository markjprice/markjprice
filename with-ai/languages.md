**Programming languages and human languages**

- [Programming languages and human languages](#programming-languages-and-human-languages)
- [A major shift in how programmers work](#a-major-shift-in-how-programmers-work)
- [Something Big Is Happening](#something-big-is-happening)
- [Implications for software engineering careers and workflows](#implications-for-software-engineering-careers-and-workflows)
- [A warning about leaning on AI when learning and coding](#a-warning-about-leaning-on-ai-when-learning-and-coding)

# Programming languages and human languages

Let’s start this section with a January 24, 2023, tweet from Andrej Karpathy, as shown in *Figure 11.1*:
![Andrej Karpathy’s tweet from January 24, 2023](languages.png)
*Figure 11.1: Andrej Karpathy’s tweet from January 24, 2023*

Andrej Karpathy is one of the most credible voices in modern artificial intelligence, particularly in deep learning and neural networks. When he makes a technical claim about AI, it is taken seriously because he has repeatedly been at the center of building the systems everyone else later talks about.

> To learn more about Andrej Karpathy, you can read his Wikipedia page: https://en.wikipedia.org/wiki/Andrej_Karpathy. Some people don’t trust Wikipedia, so I also recommend his personal site (https://karpathy.ai), his GitHub: https://github.com/karpathy, or his Stanford profile: https://cs.stanford.edu/people/karpathy/.

Karpathy argues that large language models are better understood as a new kind of programmable computer, rather than just chatbots or text generators. The key idea is that the prompt acts like a program, and the model acts like an interpreter that executes it probabilistically.
This matches how these systems are used by skilled practitioners. People who get the most value out of models like GPT are not chatting casually. They are designing prompts carefully, layering instructions, structuring inputs, and iterating in a way that looks much more like programming than conversation. You must learn to do the same.

This framing explains both the power and the limitations of current AI. Like a programming language with no type system and fuzzy execution, prompt-based programs can do astonishing things, but they can also fail silently, hallucinate, or behave inconsistently. Seeing LLMs as computers clarifies why reliability, testing, and validation matter so much, and why blind trust is dangerous.

> **Prompt**: How does probabilistic execution in large language models compare to deterministic execution in Python?

# A major shift in how programmers work

Andrej tweeted again on January 26, 2026, and had a lot more to say about programming in the age of AI, so rather than quote it at length you can read the whole thing at the following link: https://x.com/karpathy/status/2015883857489522876.

Karpathy’s key point is we are living through a shift in how professional programmers work, driven by recent advances in AI coding tools. His personal experience serves as a real-world snapshot of that change.

His main points are:
- **Shift in workflow proportions**: Karpathy says his own coding workflow flipped dramatically in a matter of weeks. Until recently, about 80% of his work was manual coding with autocomplete and only about 20% involved AI agents. By late 2025, that had reversed. Now about 80% of the code he generates comes from AI, with him making edits and adjustments. He literally says he’s mostly programming in English now, meaning he describes desired behavior to the AI rather than writing every line himself.
- **Ego and adaptation**: He acknowledges that this shift is a bit sheepish because it can feel strange or humbling for a seasoned programmer to hand over the mechanics of coding to a model, but the productivity and capability gains are net useful enough that it’s hard to ignore once you adapt to it.
- **Biggest change in decades**: Karpathy calls this shift the biggest change to his basic coding workflow in roughly 20 years. That’s significant because he’s not a casual coder. He’s spent most of his career at the cutting edge of machine learning and software engineering. He also suggests that this trend isn’t just his experience; many engineers are already crossing this threshold, even if most developers outside leading AI labs are not yet aware of it.
- **Model fallibility and tool context**: He pushes back on extreme hype, noting that ideas like there is no need for an IDE anymore or agent swarms will replace programmers outright are premature. Language models still make errors, often subtle conceptual ones, so human review and tooling (like editors and IDEs) remain important. This makes clear that he sees AI as powerful but not infallible.
His tweet drew attention because it captures an inflection point that the work of coding is moving from writing detailed code manually to directing AI to generate it. 

> **Prompt**: What are historical parallels to this shift in programming?

# Something Big Is Happening

On February 10, 2026, an AI startup CEO named Matt Shumer posted an article he had written about AI to X. It got more than 50 million views in the first 24 hours.

A key point is when he states:
> *...on February 5th, two major AI labs released new models on the same day: GPT-5.3 Codex from OpenAI, and Opus 4.6 from Anthropic... And something clicked. I am no longer needed for the actual technical work of my job. I describe what I want built, in plain English, and it just... appears. Not a rough draft I need to fix. The finished thing. I tell the AI what I want, walk away from my computer for four hours, and come back to find the work done. Done well, done better than I would have done it myself, with no corrections needed. A couple of months ago, I was going back and forth with the AI, guiding it, making edits. Now I just describe the outcome and leave.*

That claim deserves caution, although his broader points still align with and reinforce Karpathy’s observations.

Shumer’s article takes about 10 minutes to read fully, although it took me longer because I kept stopping to reflect on the impact of what he was saying. You can read the full original article on his personal blog (without needing to visit X) at the following link: https://shumer.dev/something-big-is-happening.

# Implications for software engineering careers and workflows

Karpathy and Shumer’s point is not that programming disappears, but that the act of typing code becomes less important than deciding what should be built, how it should behave, and how to tell an AI system to produce something close to that intent. This pushes software engineering away from line-by-line construction and toward specification, review, and correction.

In practical workflow terms, this means engineers spend more time describing behavior in natural language, breaking problems into well-scoped chunks, and iterating quickly on generated code. 

Instead of writing code, running it, fixing errors, and repeating, the loop often becomes prompt, inspect, adjust, and refine. IDEs do not go away, but they increasingly act as inspection and validation tools rather than primary creation tools.

For programming careers, the uncomfortable implication is that raw coding speed matters less than it used to. What matters more is taste, judgment, and systems thinking. Engineers who understand architecture, data flow, performance trade-offs, and failure modes gain leverage, because they are the ones who can spot when AI-generated code is subtly wrong or structurally dangerous. Software engineers who only know syntax, but not semantics, are more exposed.

> **Prompt**: How does AI-assisted coding change the skill profile of junior developers? What skills remain durable even if AI writes most code?

This also changes how junior engineers grow. Learning by typing thousands of lines of boilerplate is no longer the main path to competence. Instead, learning shifts toward reading code critically, asking good questions*, and understanding why a solution is correct rather than merely functional. That raises the bar intellectually, even as it lowers the barrier to producing working software.

> *This is why I have tried to model good practice with all the prompt boxes scattered throughout this book.

The long-term implication is not fewer software engineers, but a different skillset for the profession. Karpathy and Shumer’s tweets resonate because they capture that transition early, from experts who have already lived through several previous shifts in how software gets built.

> Burke Holland’s article about AI and software development in which he writes that, Opus 4.5 feels to me like the model that we were promised - or rather the promise of AI for coding actually delivered.: https://burkeholland.github.io/posts/opus-4-5-change-everything/.

This is the thread that runs through the rest of the book. The important skill is not typing Django views, Docker files, Git commands, or test cases faster than everyone else. AI tools are already good at producing first drafts of those things. The harder skill is knowing what the system should do, where each part belongs, and how to check whether the result is correct.

For a survey website, that means thinking clearly about behavior before code: Who creates a survey? Who can answer it? What happens when a survey is closed? Where should results be stored? What should be public, private, validated, tested, or logged? Those decisions matter more than the exact syntax of a model, view, or template.

The developer’s job is shifting toward design, review, diagnosis, and judgment. You still need to understand the code. In fact, you need to understand it more, because you may be reviewing code you did not personally type.

# A warning about leaning on AI when learning and coding

A warning comes from Anthropic in their article, *How AI assistance impacts the formation of coding skills*: https://www.anthropic.com/research/AI-assistance-coding-skills.

Anthropic researchers conducted a randomized controlled trial involving 52 software developers. All participants had regular experience with Python but were unfamiliar with a specific async library called Trio. They were randomly assigned into two groups:
- **AI-assisted group**: participants could use an AI coding assistant during the task.
- **Control group**: participants did not have AI assistance but could use documentation and web search.

Each participant completed two feature-building tasks using Trio. After finishing, they all took a quiz designed to measure mastery of the concepts they had just worked with. The quiz covered debugging, code reading, code writing, and conceptual understanding.

Here is what the study found:
- On average, developers who used AI scored significantly lower on the post-task quiz (about 50 percent) compared to those who coded without AI (about 67 percent). This represented a 17 percent gap, roughly equivalent to two letter grades in a typical academic setting.
- The biggest differences were observed on debugging and conceptual understanding questions, indicating that AI use was associated with weaker mastery of those skills.
- The AI-assisted group finished the coding tasks only slightly faster, but this time difference was not statistically significant.
- Qualitative analysis showed that some participants spent substantial time composing AI prompts (up to 30 percent of total time in some cases), which ate into potential efficiency gains.
The researchers concluded that:
- AI assistance can undermine learning when people are acquiring a new, unfamiliar skill. Relying on the assistant to generate and debug code appears to reduce the cognitive effort that builds deep understanding.
- Not all AI use is the same. Some participants who used AI mainly to ask conceptual questions or request explanations alongside generated code performed much closer to the control group. Those who offloaded thinking and delegated work to the AI tended to score lowest.
- The study suggests that AI does not automatically speed learning, especially on unfamiliar tasks, and may even impede skill formation if developers use it as a shortcut rather than as a learning tool.

In other words, the research challenges the assumption that coding with AI assistance always accelerates both productivity and learning simultaneously. How developers interact with AI appears to matter as much as whether they use it at all.

> **Good practice**: Always ask the AI to explain the code it writes while you are learning a new topic. Ask conceptual questions or request explanations alongside generated code. Don’t miss valuable learning opportunities while working with AI.

> Prompt: Please explain the risks of over-relying on AI during early learning. Design a learning plan that balances AI use with deep skill formation.

Before you start building a project, there are some tools that we need to configure and learn about, including GitHub Copilot and the models it will use, Docker and its containers, and Git for version control.
