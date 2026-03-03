# Agri-Sarthi-AWS-Hackathon

Agri-Sarthi: The Indic AI Agent for Bharat
Empowering 150 Million Farmers through Voice-First, "Action-Oriented" AI.

Project Overview
Agri-Sarthi is not just a chatbot; it is an Autonomous AI Agent designed to bridge the digital and linguistic divide in Indian agriculture. While existing tools only provide information (weather/prices), Agri-Sarthi takes action. It integrates with India's Digital Public Infrastructure (DPI) like AgriStack and ONDC to help farmers file insurance claims and purchase supplies using simple voice commands in 22 regional languages.

Key Features
Voice-First Native Interface: Powered by Bhashini, allowing farmers to interact in their local dialect.

Visual Crop Diagnosis: Uses Amazon Rekognition to analyze crop health from a single photo.

Autonomous Task Execution: Moves beyond "chat" to actually filling out government forms and placing orders on ONDC.

Offline-First Thinking: Designed to work in low-bandwidth rural environments.

Technical Architecture
Agri-Sarthi is built on a robust, serverless AWS foundation for maximum scalability:

The Brain: Amazon Bedrock (Claude 3.5 Sonnet) for complex reasoning.

The Senses: Amazon Rekognition (Vision) and Bhashini APIs (Voice/Translation).

Orchestration: AWS Lambda & Step Functions for secure agentic workflows.

Storage & Data: Amazon S3 (Media) and DynamoDB (User Profiles).

Note: View the full Design Document for detailed diagrams.

Zero-Form Infrastructure: Farmers shouldn't have to navigate complex apps. Agri-Sarthi handles the "Last Mile" of digital interaction.

Scalability: Built on a serverless stack, it can scale to serve all 600,000+ Indian villages.

Real Impact: Direct alignment with the Viksit Bharat 2047 vision by empowering the rural economy.
