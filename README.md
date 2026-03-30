# Food-Recommendation-System
 Built an advanced food recommendation system that demonstrates similarity search and conversational AI. Using a rich food dataset we created an interactive CLI search interface, implemented advanced filtering techniques, and develop a RAG chatbot that provides intelligent food recommendations using Chroma DB and  

 Install Packages
pip install numpy==2.3.1
pip install scipy==1.16.0
pip install chromadb==1.0.12
pip install sentence-transformers==4.1.0
pip install ibm-watsonx-ai==1.3.24 / or any other ai

REASONING BEHING RAG:
The system we are building is a RAG (Retrieval-Augmented Generation) chatbot because it follows the specific architecture of giving an LLM access to data it wasn't originally trained on.While a standard chatbot (like basic ChatGPT) relies only on its internal memory, our project adds a Retrieval step that "augments" the conversation with facts from our FoodDataSet.json.The Three Pillars of RAG in our Code1. Retrieval (The "R")This is what we just explored with collection.query.Without RAG: If we asked a chatbot "Tell me about the 'Midnight Ginger' dish from my specific menu," it would guess or say it doesn't know.With RAG: Our code in interactive_search.py or advanced_search.py goes into the ChromaDB vector database and pulls out the exact ingredients and description for that dish based on similarity.2. Augmentation (The "A")Once we have the search results, we don't just show them to the user in raw JSON format. In a RAG system, we "augment" the prompt being sent to the AI. Our system takes the user's question + the food data retrieved from ChromaDB and combines them into a single, massive instruction:"System: Use the following menu items [Retrieved Data] to answer this user question: [User Query]."3. Generation (The "G")Finally, the LLM (Large Language Model) reads that augmented prompt and generates a natural language response. Because it has the food data right in front of it (in its "context window"), it can provide accurate, hallucination-free answers about our specific dataset. How our Files Work Together as RAG
Component,File in our Project,RAG Role
Knowledge Base,FoodDataSet.json,The source of truth.
Vector Database,shared_functions.py (ChromaDB logic),Stores data as vectors for fast retrieval.
The Retriever,advanced_search.py,"Finds the right ""context"" using filters and similarity." (/interactive_search.py  simpler search method)
The Chatbot,enhanced_rag_chatbot.py,Combines the retrieved data with an AI model to talk to the user.

In our project, the transition from simple searching to a full RAG (Retrieval-Augmented Generation) system is completed through three distinct scripts. Each one adds a layer of complexity to how the user interacts with the data.
1. interactive_search.py (The Semantic Search)This is the "Discovery" phase. It is a pure vector search.Functionality: It takes a user query, converts it to a vector, and finds the most similar items in ChromaDB.Output: It prints the raw data (name, description, etc.) found in the database.Limitation: It cannot "reason." If you ask, "Which of these is best for a rainy day?", it just finds items with words similar to "rainy day" and shows you the database entries.
2. advanced_search.py (The Filtered Search)This adds logical constraints to the vector search.Functionality: It uses the ChromaDB where clause to combine similarity with hard filters (e.g., "Must be < 500 calories" AND "Must be Italian").Output: A list of items that meet both the mathematical similarity and the metadata filters.Limitation: The output is still just a list of database records. It doesn't "talk" to us or explain why it chose those items in a natural way.
3. enhanced_rag_chatbot.py (The Full RAG System)This is the most advanced script because it adds an LLM (IBM Granite or any other) into the loop to act as an intelligent assistant. It follows the 4-step RAG architecture

Step,How it happens in enhanced_rag_chatbot.py
1. Retrieval,The handle_enhanced_rag_query function calls ChromaDB to find the top 3 matches.
2. Context Building,"The prepare_context_for_llm function turns those 3 database results into a clean, text-based summary."
3. Augmentation,The generate_llm_rag_response function takes our original question and pastes the food data into a hidden prompt for the AI.
4. Generation,"The IBM Granite model reads the prompt and generates a natural, conversational response (e.g., ""Since you're looking for something light, I highly recommend the Spring Rolls because..."")."

Key Differences
Feature,interactive_search.py,advanced_search.py,enhanced_rag_chatbot.py
Search Engine,ChromaDB (Vectors),ChromaDB (Vectors + Filters),ChromaDB + IBM Granite LLM
Response Type,Raw Data List,Filtered Data List,Natural Language Sentences
Reasoning,None,Boolean Logic (and/or),Contextual Reasoning
Special Feature,Quick discovery,Dietary/Cuisine limits,Comparison Mode (analyzing two queries at once)

The enhanced_rag_chatbot.py is a true "Chatbot" because it uses Generation. Instead of us reading a table of ingredients, the AI "reads" the table for us and summarizes the best parts. It also handles Comparison Mode, where the LLM can look at two different sets of search results and explain the pros and cons of each—something a database cannot do on its own.

Why use RAG instead of just training a new AI?Cost: Training a model is expensive; updating a JSON file and ChromaDB is free.Accuracy: RAG significantly reduces "hallucinations" because the AI is told to only use the provided text.Real-time: If we change a calorie count in our JSON today, the chatbot knows it instantly without needing a software update.

