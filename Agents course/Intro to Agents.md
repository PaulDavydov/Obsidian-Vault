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