
import os
from dotenv import load_dotenv

from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import TokenTextSplitter
from langchain_huggingface import HuggingFaceEmbeddings
from langchain_community.vectorstores import FAISS

from langchain_groq import ChatGroq
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# Load environment variables
load_dotenv()

# ----------------------------------
# 1. LOAD DOCUMENTS
# ----------------------------------

folder_path = "documents"
documents = []

for filename in os.listdir(folder_path):

    if filename.endswith(".txt"):

        loader = TextLoader(
            os.path.join(folder_path, filename),
            encoding="utf-8"
        )

        documents.extend(loader.load())

print("Documents loaded:", len(documents))


# ----------------------------------
# 2. SPLIT DOCUMENTS
# ----------------------------------

text_splitter = TokenTextSplitter(
    chunk_size=200,
    chunk_overlap=30
)

chunks = text_splitter.split_documents(documents)

print("Total chunks:", len(chunks))


# ----------------------------------
# 3. GENERATE EMBEDDINGS
# ----------------------------------

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)


# ----------------------------------
# 4. STORE IN FAISS
# ----------------------------------

vectorstore = FAISS.from_documents(chunks, embeddings)

print("FAISS database created")


# ----------------------------------
# 5. CREATE RETRIEVER
# ----------------------------------

retriever = vectorstore.as_retriever(
    search_kwargs={"k": 3}
)


# ----------------------------------
# 6. INITIALIZE LLM
# ----------------------------------

llm = ChatGroq(
    model="llama-3.3-70b-versatile",
    temperature=0,
    api_key=os.getenv("GROQ_API_KEY")
)


# ----------------------------------
# 7. CREATE RAG CHAIN
# ----------------------------------

rag_prompt = ChatPromptTemplate.from_template("""
Answer using only the given context.
If information is unavailable, say so.

Context: {context}

Question: {question}

Answer:
""")

rag_chain = rag_prompt | llm | StrOutputParser()


# ----------------------------------
# 8. CREATE NORMAL LLM CHAIN
# ----------------------------------

normal_prompt = ChatPromptTemplate.from_template("""
Answer using your general knowledge.

Question: {question}

Answer:
""")

normal_chain = normal_prompt | llm | StrOutputParser()


# ----------------------------------
# 9. COMPARE ANSWERS
# ----------------------------------

def compare_answers(question):

    docs = retriever.invoke(question)

    context = "\n\n".join(
        doc.page_content for doc in docs
    )

    rag_answer = rag_chain.invoke({
        "context": context,
        "question": question
    })

    normal_answer = normal_chain.invoke({
        "question": question
    })

    print("\nQuestion:", question)

    print("\nRAG Answer:\n", rag_answer)

    print("\nNormal LLM Answer:\n", normal_answer)


# ----------------------------------
# 10. TEST QUERIES
# ----------------------------------

questions = [
    "Who created Python and when was it released?",
    "Who developed Java and when was it released?",
    "What is Retrieval-Augmented Generation?",
    "What are the main stages of RAG?",
    "What is the purpose of FAISS?",
    "Who developed JavaScript?"
]

for question in questions:

    compare_answers(question)
