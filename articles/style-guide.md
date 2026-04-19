**Style Guide**

- [Copyediting rules](#copyediting-rules)
  - [“like” versus “such as”](#like-versus-such-as)
  - [“if” versus “whether”](#if-versus-whether)
  - [“is” versus “are”](#is-versus-are)
  - [Em-dashes](#em-dashes)
  - [Code blocks](#code-blocks)
  - [Periods at the end of sentences](#periods-at-the-end-of-sentences)
  - [ASP.NET Core Minimal API](#aspnet-core-minimal-api)
- [Quotation Marks, Bold, and Italics](#quotation-marks-bold-and-italics)
  - [Quotation Marks (“ ”)](#quotation-marks--)
  - [Bold](#bold)
  - [Italics](#italics)
  - [Key Distinction](#key-distinction)
  - [Consistency Guidelines](#consistency-guidelines)

# Copyediting rules

I understand that copyeditors want to "show their work", but in some of my book chapters more than half of the copyedits were:
- deleting periods at the end of sentences (usually in bullet lists)
- adding periods to the end of links (which breaks the link if the link-creation process includes the dot)
- changing “like” to “such as”
- changing “if” to “whether”

I feel like these types of change are more to make it look like lots of work has been done rather than adding value to the book.

## “like” versus “such as”
Example:
- Author: “...online resources, **like** videos, blogs, and forums.”
- Copyedit: “...online resources, **such as** videos, blogs, and forums”

I used to use “such as” a lot myself, but previous copyeditors changed it to “like” so often that I now use “like” instead. It is shorter and my preference now.

## “if” versus “whether”

Example:
- Author: “It is not until you have written a decent amount of code with a tool that you can really judge **if** it fits your needs.”
- Copyedit: “It is not until you have written a decent amount of code with a tool that you can really judge **whether** it fits your needs.”

I accept that “whether” is the more formal and precise word in this context. But some grammar guides consider “if” acceptable as a substitute for “whether,” especially in modern English, but purists prefer to distinguish the two. So “if” is less formal, more conversational. I *want* my books to feel like a conversation with a friend or colleague, not a lecture. “whether” is also harder for non-native English speakers (and Americans 😉) to understand (and spell!).

## “is” versus “are”

Example:
- Author: “Any of the editions **are** suitable for this book.”
- Copyedit: “Any of the editions **is** suitable for this book.”

I accept that the subject of the sentence is *any*, which is singular. Since *any* is technically singular, the verb should be *is*. That makes “Any of the editions is suitable for this book.” the strictly correct form.

But in everyday English, most people treat “any of the X” as plural, because they’re thinking about the multiple items (in this case, editions). So “Any of the editions are suitable for this book.” sounds more natural (i.e. human rather than AI-generated perfect grammar) to most readers. Many modern style guides (including Garner’s Modern English Usage) note that this plural usage is widespread and acceptable.

I think the author’s voice should carry more weight than technically perfect grammar. I recommend that Packt instruct their copyeditors to allow looser rules and only override the author when it’s truly bad grammar. Generative AI may hallucinate, but it’s always perfect grammar. Perfect grammar is a red flag to modern readers. They do not trust it. Let the authors voice shine through even when it's not perfect.

## Em-dashes

Example:
- Author: “You must also mark the `.cs` file as executable in the filesystem. For example, at the command prompt or terminal: `./`”
- Copyedit: “You must also mark the `.cs` file as executable in the filesystem – for example, at the command prompt or terminal: `./`”

LLMs use em-dashes a LOT and its become a huge red flag to most people because using them makes text look AI-generated. I think this was better the way I originally wrote it. I see no benefit and potential downside to this copyedit.

## Code blocks

Example:
- Author:
```cs
int index = valueAsString.IndexOf('@');
```
- Copyedit:
```cs
int index = valueAsString.IndexOf(‘@’);
```

**NEVER** make edits to code. For example, in the past copyeditors have changed straight quotes " and ' to curly quotes “” in code blocks which breaks the code. If you think something is wrong, please leave a comment pointing to the highlighted code.

## Periods at the end of sentences

For full sentences in bullets or steps, if each bullet (or step) is a complete sentence, use a period. Example:
1. Install Python.
2. Open the terminal.
3. Run the script.

This makes the list feel like a set of instructions or statements. It’s cleaner for technical documentation.

If bullets are just fragments (not full sentences), don’t use periods. Example:
- Variables
- Functions
- Classes

## ASP.NET Core Minimal API

Safis changed all the “Minimal API” instances to “minimal API”. Please let them know that ASP.NET Core Minimal API (and ASP.NET Core Web API) are proper names and therefore should not be “minimal API” or “web API” so please do not change them in future. While copyediting *Apps and Services with .NET 7* someone even changed that on the book cover but luckily I spotted it before we went to print!

# Quotation Marks, Bold, and Italics

Mark's books use quotation marks and italics in distinct and consistent ways. Understanding the difference will help you interpret tone, emphasis, and meaning more accurately.

## Quotation Marks (“ ”)

Quotation marks are used in two situations:

**1. Referring to words or phrases as language**
Use quotation marks when discussing a word itself rather than its meaning.

Example:
The term “pointer” can be confusing at first.

**2. Signaling informal, ironic, or misleading usage (scare quotes)**
Use quotation marks to indicate that a word is being used loosely, ironically, or in a way that should not be taken at face value.

Example:
You can ask the “dumb” questions without fear.

In this case, “dumb” reflects how questions might be perceived, not their actual value.

---

## Bold

Bold is used for introducing important terms. Packt's Word template has a style named `CS - Keyword` that should be applied.

**1. Introducing new terms**
When a concept is defined for the first time, it may be bolded.

Example:
A **pointer** is a variable that stores a memory address.

After introduction, the term is written normally.

---

## Italics

Italics are used for direct quotes, emphasis, and for book, chapter, and section titles. Packt's Word template has a style named `CS - Italic` that should be applied.

**1. Direct quotations**
Use quotation marks when repeating someone’s exact words.

Example:
The compiler reports, *Segmentation fault.*

**2. Emphasis**
Use italics to draw attention to a word or phrase.

Example:
This is *not* the same as allocating memory on the heap.

**3. Titles**
Use italics for book, chapter, and section titles.

Example:
You will learn about memory allocation in *Chapter 3*, *Memory Management with C++*.

---

## Key Distinction

Quotation marks and italics serve different purposes and should not be used interchangeably.

* Quotation marks indicate distance from a term, such as irony, informality, or discussion of the word itself.
* Italics indicate emphasis or importance.

Compare:

* You can ask the “dumb” questions. → questions are not actually dumb
* You can ask the *dumb* questions. → emphasis on the idea of dumb questions

---

## Consistency Guidelines

* Do not use italics to replace quotation marks for ironic or informal usage.
* Avoid overusing italics for emphasis; use them sparingly.
* Once a technical term has been introduced, do not continue italicizing it.
* Prefer quotation marks when clarity depends on signaling how a word is being interpreted.

---

This distinction helps maintain clarity, especially in a technical context where precision of meaning is essential.
