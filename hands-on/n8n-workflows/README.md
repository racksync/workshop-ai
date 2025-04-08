# n8n Workflows - Workshop Guide

This directory contains a collection of n8n workflows created for the AI workshop. Each workflow demonstrates different functionality and integration capabilities of n8n with various services and AI models.

## Workflow Descriptions

### Basic and Chat Workflows

#### 01 - Basic n8n
A simple introduction to n8n with a chat interface that acts as a bilingual translator (Thai-English) using Google Gemini. Demonstrates the basic setup of a language model with an agent.

#### 02 - ChatBox
An interactive chatbox implementation that allows users to have conversations with an AI assistant.

#### 03 - LINE Messaging API
Integrates n8n with LINE Messaging API to create a customer service bot named "น้องจอย" that answers questions about RACKSYNC company services using Google Gemini model.

#### 04 - Telegram Basic
A Telegram bot implementation that processes messages and provides automated responses.

### Content Creation Workflows

#### 05 - Content Assistant - Google Sheets (Smart Home Content)
Generates Smart Home content based on topics stored in Google Sheets. Uses AI to create SEO-friendly content about smart home technologies for homeowners aged 30-50.

#### 06 - Content Assistant - Google Sheets (Strategic Creator)
Creates strategic planning and management content for IT professionals. Monitors a Google Sheet for new topics and generates comprehensive business strategy content using the Google Gemini model.

#### 07 - Content Assistant - Google Sheets (Course Creator)
Designs complete course curricula for IT and technology training, with a focus on reskilling and on-the-job training. The workflow reads course topics from a Google Sheet and creates structured course outlines.

#### 08 - Content Creator - Google Sheets - (SEO Assist)
SEO-focused content generation workflow that creates optimized content for specific keywords. Targets office workers aged 25-45 and updates a Google Sheet with the generated content.

### Data Collection and Processing

#### 09 - LINE Download to Google Sheets
Captures data received through LINE messages and saves it to Google Sheets for further processing or record-keeping.

#### 10 - Read Transfer Slip to Google Sheets via LINE
Processes payment slip images sent via LINE, extracts the transaction details, and records them in Google Sheets for financial tracking.

#### 11 - Email Attachments Analyzer
Monitors an email inbox for attachments, extracts and analyzes their content, and generates structured data or summaries.

#### 12 - Scrape Website
Extracts data from websites through web scraping techniques, allowing for automated data collection from online sources.

### RAG (Retrieval-Augmented Generation) Implementations

#### 13 - RAG - Qdrant (LINE Bot API)
Implements a Retrieval-Augmented Generation system using Qdrant vector database with LINE messaging integration. Uses Google Drive Trigger to process documents, Google Gemini for embeddings, and responds to queries via a webhook.

#### 14 - OpenAI + Qdrant (Movie RAG)
Creates a specialized RAG system for movie information retrieval using OpenAI models with Qdrant vector database.

#### 15 - RAG - Pinecone
Demonstrates RAG implementation using Pinecone vector database for efficient semantic search and knowledge retrieval.

#### 16 - RAG - Supabase (LINE Bot API)
Uses Supabase for vector storage in a RAG system connected to LINE Bot API for natural language querying.

### Specialized Applications

#### 17 - HR
A workflow designed for HR departments to automate processes like employee onboarding, document management, or leave request handling.

#### 18 - Home Assistant
Integrates with Home Assistant platform to automate smart home controls and routines through n8n.

## How to Use These Workflows

1. Import the workflow JSON file into your n8n instance
2. Configure credentials for any services used (Google Sheets, LINE, Telegram, etc.)
3. Activate the workflow and test its functionality

## Required Credentials

Different workflows require different credentials, including:
- Google account (for Sheets, Drive)
- LINE Messaging API tokens
- Telegram bot tokens
- OpenAI API keys
- Google Gemini (PaLM) API keys
- Database credentials (Qdrant, Pinecone, Supabase)
- Email account access

## Common Workflow Patterns

Many of these workflows follow common patterns:
- Trigger → Process → Respond
- Monitor → Extract → Transform → Store
- Query → Retrieve → Generate → Reply

## Additional Resources

For more information on n8n and how to extend these workflows, refer to:
- [n8n Documentation](https://docs.n8n.io/)
- [Google Gemini API Documentation](https://ai.google.dev/docs)
- [LINE Messaging API Documentation](https://developers.line.biz/en/docs/messaging-api/)