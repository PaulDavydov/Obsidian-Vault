- what is an agent?
	- AI Model capable of reasoning, planning, and interacting with its environment
	- We call it agent because it has agenecy, ability to interacty with the environment
	- A more formal definition: a system that leverages an AI model to interact with its environment in order to achieve a user-defined objective. It combines reasoning, planning, and the execution of actions (often via external tools) to fulfill tasks
- Agents have two main parts
	- The Brain(AI Model):
		- Where all the thinking happens. AI Model handles reasoning and planning. It decides which Actions to take based on the situation
	- The Body(Capabilities and Tools):
		- Part represents everything the Agent is equipped to do
- Scope of possible actions a agent can take, is dependent on what the agent has been equipped with
- Most common AI model found in agents is an LLM (Large Language Model)
	- Takes text as input and outputs text as well
	- EX: GPT4, OpenAI, LLama, Gemini, etc
		- These models are trained on a vast amount of text and are able to generalize well
- Another example is the VLM (Visual Lanuage Model)
	- Similar to LLM but also understands images as input
- How do LLM, that output only text, generate images?
	- ChatGPT and HuggingChat implement additional functionality (called Tools), that the LLM can use to create images
- An agent can perform any task we implement via tools to complete Actions
	- If we wanted it to write and send an email, we can give it some code to sned emails
	- the design of the tools is important and has a great impact on the quality of your agent
- Summary:
	- Ai Agents use the core reasoning engine to:
		- Understand natural language: interpret and respond to human instructions in a meaningful way
		- Reason and plan: analyze info, make decisions, and devise strategies to solve problems
		- Interact with its environment: gather info, take actions, and observe the results of those actions
### What are LLMs
- LLM is a type of AI model that excels at understanding and generating human language
	- trained on vast amounts of text data, allowing them to learn patterns, structure, and even nuance in language
- LLMs built on the Transformer architecture - deep learning architecture based on the "Attention" algo, that has gained significant interest since the release of BERT from Google in 2018
- 3 Types of Transformers

| Name     | Desc                                                                                                                                                           | Example          | Use Case                                                       | Size                   |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | -------------------------------------------------------------- | ---------------------- |
| Encoders | takes text (or other data) as input and outputs a dense representation (or embedding) of that text                                                             | BERT from Google | Text classification, semantic search, named entity recognition | Millions of parameters |
| Decoders | focuses on generating new tokens to complete a sequence, one token at a time                                                                                   | Llama from Meta  | Text generation, chatbots, code generation                     | Billions of parameters |
| Seq2Seq  | combines an encoder and a decoder. The encoder first processes the input sequence into a context representation, then the decoder generates an output sequence | T5, Bart         | Translation, Summarization, Paraphrasing                       | Millions of parameters |
- LLMs are typically decoder-based models with billions of parameters

Most well-known LLMs

| Model       | Provider                    |
| ----------- | --------------------------- |
| Deepseek-R1 | DeepSeek                    |
| GPT4        | OpenAI                      |
| Llama 3     | Meta (Facebook AI Research) |
| SmolLM2     | Hugging Face                |
| Gemma       | Google                      |
| Mistral     | Mistral                     |
- Principle for LLMs: predict the next token, given a sequence of previous tokens
	- A token is the unit of info an LLM works with
	- Token can be viewed as a "word", but LLMs don't use whole words
- Every LLM has their own special tokesn specific to the model
	- Used to open and close the structured components of its generation
	- Start and end of a sequence, message or response
	- End of Sequence token (EOS)

| Model       | Provider     | EOS Token             | Functionality                  |
| ----------- | ------------ | --------------------- | ------------------------------ |
| GPT4        | OpenAI       | <\|endoftext\|>       | End of message text            |
| Llama 3     | Meta         | <\|eot_id\|>          | End of sequence                |
| Deepseek-R1 | DeepSeek     | <\|end_of_sentence\|> | End of message text            |
| SmolLM2     | Hugging Face | <\|im_end\|>          | End of instructions or message |
| Gemma       | Google       | <end_of_turn>         | End of conversation turn       |
- LLMs are autoregressive, meaning that the ouput from one pass becomes the input for the next one
	- Loop continues until the model predicts the next to be the EOS token, at which point the model can stop
- LLM will decode text until it reaches the EOS
- Single decoding loop
	- Input text is tokenized, model computes a representation of the sequence that cpatures info about the meaning and the position of each token in the input sequence
	- Representation goes into the model, which outputs scores that rank the likelihood of each token in its vocabulary as being the next one in the sequence
- Easiest decoding strategy would be to always take the token with the maximum score
- A more advanced decoding strategy, beam search
	- explores multiple candidate sequences to find the one with the maximum total score-even if some individual tokens have lower scores
- Key aspect of the Transformer architecture is Attention
	- When predicting a word in a sentence, not every word in a sentence is equally important
- Process of identifying the most relevant words to predict the next token has proven to be incredibly effective
- Context length, refers to the maximum number of tokens the LLM can process, and the maximum attention span it has
- Prompting the LLM is important
	- To predict the best next token, it is important for the input sequence to be very important
- Input sequence you provide an LLM is called a prompt
	- Careful design of the prompt makes it easier to guide the generation of the LLM toward the desired output
- How are LLMs trained?
	- LLMs are trained on large datasets of text, where they learn to predict the enxt word in a sequence through a self-supervised or masked language modeling objective
	- from this unsupervised learning, the model learns the structure of the language and underlying patterns in text, allowing the model to generalize to unseen data
	- After pre-training, LLMs can be fine-tuned on a supervised learning objective to perform specific tasks
- Running LLMs
	- Locally (if hardware is sufficient)
	- Cloud/API (e.g. Hugging Face Serverless inference API)
- LLMs are a key component of AI Agents, providing the foundation for understanding and generating human language
	- Interpret user instructions, maintain context in conversations, define a plan and decide which tools to use
### Messages and Special Tokens
- behind the scenes, prompts are considered a sequence of tokens, but with systems like chatgpt or huggingchat, these are exchanging messages.
	- messages are concatenated and formatted into a prompt that the model can understand
- chat templates act as the bridge between conversational mesages (user and assistant turns) and the specific formatting requirements fo your chosen LLM
	- templates structure the communication between the user and the agent
- Special tokens help delimit where the user and assistant turns start and end
- System message: also known as System Prompts, define how the model should behave
	- Serve as persistent instructions
- System Message also gives info about the available tools, provides instructions to the model on how to format the actions to take, and includes the guidelines on how the though process should be segmented
- Converstations consists of alternating messages between a Human (user) and an LLM (assistant).
- Chat templates are essential for structuring conversations between language models and users
	- they guide how messages exchanges are formatted into a single prompt
- Base Model is trained on raw text data to predict the next token
- Instruct Model is fine tuned specifically to follow instructions and engage in converstations
	- SmolLm2-135M is a base model while SmolLM2-135M-Instruct is its instruction-tuned variant
- Base Model act like an instruct mdoel, we need to format our prompts in a consistent way that the model can understand, chat templates help with this
- Each instruct model uses different conversation formats and special tokens, chat templates are implemented to ensure that we correctly format the prompt the way each model expects
- transformers - chat templates include Jinga2 code, that describes how to transform the ChatML list of JSON messages, as presented in the above examples, into a textual representation of the system-level instructions, user messages and assistant responses that the model can understand
	- help maintain consistency across interactions and ensures the model responds appropriately to different types of inputs
Simplified version of the SmolLM2-135M-Instruct chat template
```
{% for message in messages %}
{% if loop.first and messages[0]['role'] != 'system' %}
<|im_start|>system
You are a helpful AI assistant named SmolLM, trained by Hugging Face
<|im_end|>
{% endif %}
<|im_start|>{{ message['role'] }}
{{ message['content'] }}<|im_end|>
{% endfor %}
```
Using these messages:
```
messages = [
    {"role": "system", "content": "You are a helpful assistant focused on technical topics."},
    {"role": "user", "content": "Can you explain what a chat template is?"},
    {"role": "assistant", "content": "A chat template structures conversations between users and AI models..."},
    {"role": "user", "content": "How do I use it ?"},
]
```
Result combing both:
```
<|im_start|>system
You are a helpful assistant focused on technical topics.<|im_end|>
<|im_start|>user
Can you explain what a chat template is?<|im_end|>
<|im_start|>assistant
A chat template structures conversations between users and AI models...<|im_end|>
<|im_start|>user
How do I use it ?<|im_end|>
```
- Transformer library will take care of chat templates for you as part of the tokenization process

### What are tools?
- Crucial aspect of AI agents, is their ability to take actions through the usage of tools
- Tool is a function given to the LLM
	- Needs to fulfill a clear objective
- Some tools for AI agents
	- Web Search: Allows the agent to fetch up-to-date info from the internet
	- Image gen: Creates images based on text descriptions
	- Retrieval: retrieves info from an external source
	- API Interface: Interacts with an external API (Github, Youtube, Spotify, etc)
- LLM predicts the completion of a prompt baed on their training data, which means that their internal knowledge only includes events prior to their training
	- Using a tool we can provide our LLM up-to-date data
	- Without a tool, an LLM could hallucinate random data/info
- Tool should contain:
	- textual description of what the function does
	- a callable (somthing to perform an action)
	- Arguments with typings
	- (Optional) Output with typings
- LLM needs to be taught that te tool exists and can be called
- But teh Agent is the one that actually executes the tool
- To give LLMs tools, we need to use the system prompt to provide textual descriptions of available tools to the model
	- We must be prices and accurate about what the tool does and what exact inputs it expects
	- Tool descriptions are usually provided using expressive but precise structure, such as computer languages or JSON
- Creating a tool
	- A descriptive name of what it does (ex: calculator)
	- A longer description, provided by the function's docstring comment: Multiply two integers
	- The inputs and their type: the function clearly expects two ints
	- the type of the output
- Programming languages are ideal since they are expressive, concise, and precise
### Understanding AI Agents through the Thought-Action-Observation Cycle
- Agent work is a continuous cycle of thinking (Thought) -> acting (Act) - > observing (Observe)
	- Thought: LLM part of the agent decides what the next step should be
	- Action: The agent takes an action by calling the tools with the associated arguments
	- Observation: the model reflects on the response from the tool
- The three components work together in a continuous loop
- In many agent frameworks, the rules and guidelines are embedded directly into the system prompt
- System message
	- Agent's behavior
	- Tools our agent has access to
	- Thought-Action-Observation-Cycle
### Though: Internal Reasoning and the ReAct Approach
- Thoughts represent the Agent's internal reasoning and planning processes to solve the task
	- Can be viewed as using the LLM to inner monologue through the problem
- Chain of Thought (COT) is a prompting technique that guides a model to think through a problem step-by-step before producing a final answer
	- usually starts with "Lets think step by step"
	- helps the model reason internally, especially for logical or mathematical tasks without interacting with external tools
- A key method is the ReAct approach
	- combines reasoning with acting
	- allows model to think step-by-step and interleave actions (like using tools) between reasoning steps
	- Solves complex tasks by:
		- thought: internal reasoning
		- Action: tool usage
		- Observation: receiving tool output
- 