---
title: GenAI Models by openai
---

# GenAI Models by openai

## Model List

### Text Models

| Model                 | Description                                                                                                    |
| --------------------- | -------------------------------------------------------------------------------------------------------------- |
| GPT-4o                | Our high-intelligence flagship model for complex, multi-step tasks                                             |
| GPT-4o mini           | Our affordable and intelligent small model for fast, lightweight tasks                                         |
| GPT-4 Turbo and GPT-4 | The previous set of high-intelligence models                                                                   |
| GPT-3.5 Turbo         | A fast, inexpensive model for simple tasks                                                                     |
| TTS                   | A set of models that can convert text into natural sounding spoken audio                                       |
| Whisper               | A model that can convert audio into text                                                                       |
| Embeddings            | A set of models that can convert text into a numerical form                                                    |
| Moderation            | A fine-tuned model that can detect whether text may be sensitive or unsafe                                     |
| GPT base              | A set of models without instruction following that can understand as well as generate natural language or code |

### Image Models

| Model  | Description                                                               |
| ------ | ------------------------------------------------------------------------- |
| DALL·E | A model that can generate and edit images given a natural language prompt |

### Other Models

| Model      | Description                                                                          |
| ---------- | ------------------------------------------------------------------------------------ |
| Whisper    | A model that can convert audio into text                                             |
| Moderation | A fine-tuned model that can detect whether text may be sensitive or unsafe           |
| Embeddings | A set of models that can convert text into a numerical form                          |
| Deprecated | A full list of models that have been deprecated along with the suggested replacement |

## Usage

### 1. Chatbot / Conversational AI

- Use Case: Creating AI-based chatbots that can hold human-like conversations.
- Example: Customer support chatbots, personal assistants like Siri or Alexa.
- Model: GPT-4, GPT-3.5 Turbo.

  Key Features:

  - Contextual understanding of user queries.
  - Multi-turn dialogue handling.
  - Personalized and human-like responses.

### 2. Text Completion

- Use Case: Automatically generating or completing text based on partial input.
- Example: Autocomplete systems, content suggestions, and creative writing.
- Model: GPT-4, GPT-3.5 Turbo, GPT base.

  Key Features:

  - Fill in the blanks or suggest continuations.
  - Can assist in drafting emails, blogs, stories, etc.

### 3. Summarization

- Use Case: Summarizing long text into concise, digestible content.
- Example: Summarizing news articles, reports, research papers.
- Model: GPT-4, GPT-3.5.

  Key Features:

  - Converts lengthy documents into shorter summaries.
  - Extractive or abstractive summarization.

### 4. Text Classification

- Use Case: Categorizing or labeling text based on its content.
- Example: Sentiment analysis, topic detection, spam detection.
- Model: GPT-4, GPT-3.5, Embeddings.

  Key Features:

  - Understands text context to assign relevant labels.
  - Can be used in customer feedback analysis or social media monitoring.

### 5. Translation

- Use Case: Translating text from one language to another.
- Example: Language translation services.
- Model: GPT-4, GPT-3.5.

  Key Features:

  - Supports translation between multiple languages.
  - Maintains context and meaning in translations.

### 6. Content Generation

- Use Case: Generating new content from a given prompt.
- Example: Writing blog posts, creating product descriptions, generating code.
- Model: GPT-4, GPT-3.5 Turbo, GPT base.

  Key Features:

  - Generates creative and coherent long-form content.
  - Ideal for marketing, creative writing, and technical documentation.

### 7. Question Answering (QA)

- Use Case: Answering questions based on a given prompt or passage.
- Example: Knowledge-based assistants, educational tools.
- Model: GPT-4, GPT-3.5.

  Key Features:

  - Provides accurate and relevant answers to questions.
  - Can handle fact-based and reasoning-based queries.

### 8. Code Generation & Completion

- Use Case: Writing or completing code based on user input.
- Example: Code assistants (e.g., GitHub Copilot), debugging tools.
- Model: Codex (based on GPT), GPT-4.

  Key Features:

  - Supports multiple programming languages.
  - Can help with code suggestions, autocompletion, and even writing functions.

### 9. Paraphrasing

- Use Case: Rewriting a sentence or paragraph while maintaining its meaning.
- Example: Content rewriting tools, improving text clarity.
- Model: GPT-4, GPT-3.5.

  Key Features:

  - Helps rephrase sentences in different styles or tones.
  - Useful for improving readability or avoiding plagiarism.

### 10. Sentiment Analysis

- Use Case: Determining the sentiment or emotion expressed in text.
- Example: Analyzing customer reviews, social media posts.
- Model: GPT-4, GPT-3.5, Embeddings.

  Key Features:

  - Detects and classifies sentiments (positive, negative, neutral).
  - Can be applied to customer feedback and market research.

### 11. Text-to-Speech (TTS)

- Use Case: Converting written text into spoken audio.
- Example: Virtual assistants, audiobook generation.
- Model: TTS.

  Key Features:

  - Converts text into natural-sounding speech.
  - Useful for accessibility, voice applications, or entertainment.

### 12. Text-based Data Extraction

- Use Case: Extracting specific data or information from a large volume of text.
- Example: Extracting entities like dates, names, and locations from documents.
- Model: GPT-4, GPT-3.5.

  Key Features:

  - Identify and extract relevant data from unstructured text.
  - Used in document processing, legal, and financial analysis.

### 13. Text Moderation

- Use Case: Detecting harmful or inappropriate content in text.
- Example: Social media moderation, content filtering.
- Model: Moderation.

  Key Features:

  - Detects hate speech, violence, or sensitive content.
  - Helps in ensuring content safety and adherence to guidelines.

---

### Example cURL for Text Completion

```bash
curl https://api.openai.com/v1/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "text-davinci-003",
    "prompt": "Write a short story about a robot learning emotions.",
    "max_tokens": 150
  }'
```

### Example cURL for Chat (Conversation)

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "gpt-4",
    "messages": [{"role": "user", "content": "What is the weather like today?"}],
    "max_tokens": 50
  }'
```
