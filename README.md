# Beyond Basic Chatbots: Enhancing Website Search with LLMs

This repository contains resources and code examples related to the presentation "Beyond Basic Chatbots: Enhancing Website Search with LLMs" by Mark Lee, presented at HighEdWeb 2024.

## Table of Contents

1. [Introduction](#introduction)
2. [Key Concepts](#key-concepts)
3. [LLM Basics](#llm-basics)
4. [Advanced Prompting Techniques](#advanced-prompting-techniques)
5. [RAG (Retrieval-Augmented Generation)](#rag-retrieval-augmented-generation)
6. [Code Examples](#code-examples)
7. [Resources](#resources)
8. [Future Directions](#future-directions)

## Introduction

This presentation explores how Large Language Models (LLMs) can be used to enhance website search functionality, particularly for college websites. It covers the challenges of traditional search methods and introduces advanced techniques for leveraging LLMs to improve information discovery.

## Key Concepts

- Challenges with web search and information discovery
- LLM chatbots and their limitations
- Basic to advanced prompting techniques
- Advanced LLM usage
- RAG (Retrieval-Augmented Generation) for enhanced search

## LLM Basics
- Definition and capabilities of Large Language Models
  - LLMs are AI models trained on vast amounts of text data
  - They can understand and generate human-like text across various domains
  - Capabilities include language translation, summarization, and question-answering

- Advantages and limitations of LLM chatbots
  - Advantages: 24/7 availability, consistent responses, handling multiple queries simultaneously
  - Limitations: Potential for hallucinations, lack of real-time information, ethical concerns

- Choosing the right model (open vs. closed, cost vs. intelligence, etc.)
  - Open-source models: Customizable, potentially lower cost, but may require more resources
  - Closed models: Often more advanced, easier to implement, but can be expensive
  - Consider factors like accuracy, speed, and specific use case requirements

- Understanding parameters like temperature and top-p
  - Temperature: Controls randomness in output; lower values for more focused responses
  - Top-p (nucleus sampling): Limits token selection to a cumulative probability threshold
  - These parameters help balance creativity and coherence in generated text

## Advanced Prompting Techniques

- Chain-of-thought prompting
  Example: "Let's approach this step-by-step:
  1. First, we'll analyze the problem.
  2. Then, we'll break it down into smaller parts.
  3. Next, we'll solve each part individually.
  4. Finally, we'll combine the solutions.
  Now, let's begin with step 1..."

- Few-shot and many-shot learning
  Example: "Here are three examples of how to write a haiku:
  1. 'An old silent pond... / A frog jumps into the pond, / splash! Silence again.'
  2. 'The first cold shower / Even the monkey seems to want / A little coat of straw'
  3. 'Over the wintry / Forest, winds howl in rage / With no leaves to blow'
  Now, using this pattern, write a haiku about summer."

- Pre-fill/partial response technique
  Example: "Complete this sentence in a creative way:
  The old man looked out the window and saw..."

- Scratchpad technique
  Example: "Let's solve this math problem step by step. We'll use <scratchpad></scratchpad> XML tags like this to show our work:
  Problem: What is 15% of 80?
  <scratchpad>
  1. Convert 15% to a decimal: 15% = 0.15
  2. Multiply 80 by 0.15: 80 * 0.15 = 12
  </scratchpad>
  Therefore, 15% of 80 is 12."

- Using reflection, for self-improvement
  Example: "After coming up with your answer, ask yourself:
  1. Is this response clear and concise?
  2. Have I addressed all aspects of the question?
  3. Are there any potential misunderstandings in my explanation?
  4. How could I improve this answer for better clarity or accuracy?"

## RAG (Retrieval-Augmented Generation)

- Overview of the RAG methodology
  - RAG combines traditional information retrieval with AI-powered text generation
  - It retrieves relevant documents from an index of information pulled from the weband other sources and uses them to inform AI responses
  - This approach enhances accuracy and grounds the AI outputs in factual up-to-date information

- Benefits of using RAG for web search
  - Improved relevance: Responses are based on up-to-date, site-specific information
  - Enhanced user experience: Provides direct answers instead of just a list of links
  - Reduced hallucinations: AI responses are anchored in retrieved facts
  - Customizability: Can be tailored to specific content and user needs

- Components of a RAG pipeline
  - Document ingestion: Collecting and processing website content
  - Indexing: Organizing content for efficient retrieval
  - Query processing: Understanding and refining user queries
  - Retrieval: Finding relevant documents based on the query
  - Context integration: Combining retrieved information with the query
  - Generation: Producing a final response using an AI model

- Embedding models and vector databases
  - Embedding models: Convert text into numerical vectors that capture semantic meaning
  - Vector databases: Efficiently store and search these embeddings
  - Popular options: OpenAI's text-embedding-ada-002, Sentence Transformers for embedding
  - Vector DB examples: Pinecone, Weaviate, or Chroma for storing and querying vectors

## Code Examples

This section includes example Python code snippets demonstrating how to build a rudimentary RAG-based search system. Key steps include:

1. Rephrasing user queries
    This snippet shows the prompt used to rephrase the user's question into a good search query.

    ```
    {"role": "system", "content": "You are a search assistant. Rephrase the following as a concise search query expertly crafted to retrieve relevant results from a search engine. Return only the query, no explanation:"},
    {"role": "user", "content": user_query_goes_here}
    ```

2. Searching for relevant URLs
    The following code uses the Brave Search API to find relevant URLs based on the query.

    ```
    url = f"https://api.search.brave.com/res/v1/web/search?q={query}"
    headers = {"X-Subscription-Token": BRAVE_API_KEY}
    response = requests.get(url, headers=headers)
    results = response.json()
    ```
3. Scraping and parsing content
    This snippet shows the code used to scrape and parse the content from the URLs.
        
    ```
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.content, 'html.parser')
    contents.append(soup.get_text())
    sources.append(url)
    ```
4. Embedding content into a vector database
    The following code embeds the content into a vector database.
    ```
    client = chromadb.PersistentClient()
    collection = client.get_or_create_collection(collection_name)

    for content, source in zip(contents, sources):
        collection.add(
            documents=[content],
            metadatas=[{"source": source}],
            ids=[str(uuid.uuid4())]
        )
    ```
5. Querying the database for relevant information
    The following code queries the database for relevant information.
    ```
    results = collection.query(
        query_texts=[query],
        n_results=top_k,
        include=["documents", "metadatas"]
    )
    ```
6. Generating final responses
    The following code generates a final response using the AI model.
    ```
    response = model.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are a helpful assistant. Use the provided context to answer the user's query accurately and concisely."},
            {"role": "user", "content": f"Context:\n{context}\n\nQuery: {query}\n\nResponse:"}
        ]
    )   
    ```

## Resources

### Demo and Tools
- [Ask Colorado College](https://ai-enhanced-search.streamlit.app/) - Demo RAG-based search app
- [OpenAI Cookbook](https://cookbook.openai.com/examples/evaluation/evaluate_rag_with_llamaindex)
- [Google AI Studio](https://aistudio.google.com/)
- [Anthropic Claude](https://www.anthropic.com/)
- [Perplexity AI](https://www.perplexity.ai/)

### Prompting Guides and Tutorials
- [Working with AI: Two Paths to Prompting](https://www.oneusefulthing.org/p/working-with-ai-two-paths-to-prompting)
- [How to Write Effective ChatGPT Prompts](https://www.trustinsights.ai/wp-content/uploads/2023/04/instant-insights-how-to-write-effective-chat-gpt-prompt.pdf)
- [4 Generative AI Power Questions](https://www.trustinsights.ai/wp-content/uploads/2024/04/instant_insights_4_generative_ai_power_questions.pdf)
- [Prompting Guide](https://www.promptingguide.ai/)
- [Coursera: Prompt Engineering for ChatGPT](https://www.coursera.org/learn/prompt-engineering/home/)
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [Anthropic Claude Prompt Library](https://docs.anthropic.com/claude/prompt-library)

### RAG and Answer Engine Tutorials
- [Build a Perplexity-Inspired Answer Engine Using Groq, Mixtral, Langchain, Brave & OpenAI in 10 Min](https://medium.com/@nbertagnolli/build-a-perplexity-inspired-answer-engine-using-groq-mixtral-langchain-brave-openai-in-10-min-642de8c95666)
- [RAG From Scratch: Part 1 (Overview)](https://www.youtube.com/watch?v=TRjq7t2Ms5I)
- [Build an AI-powered search engine with Langchain](https://www.youtube.com/watch?v=43ZCeBTcsS8)
- [Build a RAG AI Assistant with LangChain, OpenAI, and Pinecone](https://www.youtube.com/watch?v=wd7TZ4w1mSw&list=PLfaIDFEXuae2LXbO1_PKyVJiQ23ZztA0x)

### AI in Higher Education
- [AI For U Podcast](https://www.enrollify.org/podcasts/ai-for-u) - A podcast series exploring practical AI applications in higher education marketing, enrollment, and alumni relations
- [EDUCAUSE: Artificial Intelligence (AI)](https://library.educause.edu/topics/infrastructure-and-research-technologies/artificial-intelligence-ai) - Comprehensive resource on AI in higher education, including basics, campus use cases, classroom policies, and key resources

---

For questions or further information, please contact me here or on LinkedIn [https://www.linkedin.com/in/themarklee/](https://www.linkedin.com/in/themarklee/).
