# Implementation Plan: Agri-Sarthi

## Overview

This implementation plan breaks down the Agri-Sarthi system into discrete, manageable tasks. The approach follows a layered implementation strategy: infrastructure setup, core services, external integrations, and finally end-to-end workflows. Each task builds incrementally on previous work, with checkpoints to ensure quality and correctness.

The implementation uses TypeScript for AWS Lambda functions, with AWS CDK or SAM for infrastructure as code.

## Tasks

- [ ] 1. Set up AWS infrastructure and project structure
  - Create AWS CDK/SAM project with TypeScript
  - Define DynamoDB tables (Sessions, Conversations, Pending Actions, Carts)
  - Define S3 buckets (Farmer Data, Crop Images, Content Cache) with encryption and lifecycle policies
  - Set up API Gateway with REST and WebSocket APIs
  - Configure Amazon Cognito user pool for farmer authentication
  - Set up IAM roles and policies for Lambda functions
  - Configure VPC endpoints for private service communication (optional)
  - _Requirements: 5.1, 5.2, 5.4, 10.6_

- [ ] 2. Implement Authentication Service (Auth Lambda)
  - [ ] 2.1 Create Auth Lambda function with Cognito integration
    - Implement farmer registration with mobile number
    - Implement OTP generation and verification
    - Implement token generation (access and refresh tokens)
    - _Requirements: 5.1, 5.3_

  - [ ]* 2.2 Write property test for authentication flow
    - **Property 15: Authentication for Sensitive Operations**
    - **Validates: Requirements 5.3, 5.5**

  - [ ] 2.3 Implement AgriStack farmer verification integration
    - Call AgriStack API to verify farmer details
    - Store farmer profile in S3
    - Handle verification failures gracefully
    - _Requirements: 3.1, 8.3_

  - [ ]* 2.4 Write unit tests for Auth Lambda
    - Test OTP generation and validation
    - Test token expiry handling
    - Test AgriStack integration error cases
    - _Requirements: 5.1, 3.1_

- [ ] 3. Implement Voice Interface Service (Voice Lambda)
  - [ ] 3.1 Create Voice Lambda function with Bhashini integration
    - Implement speech-to-text (STT) using Bhashini API
    - Implement text-to-speech (TTS) using Bhashini API
    - Handle all 22 supported Indian languages
    - Store audio files in S3 for logging (optional)
    - _Requirements: 1.1, 1.2, 1.3, 8.1_

  - [ ]* 3.2 Write property test for multilingual voice support
    - **Property 1: Multilingual Voice Support**
    - **Validates: Requirements 1.1, 1.3, 8.1**

  - [ ] 3.3 Implement audio feedback system
    - Provide feedback for listening, processing, error, and success states
    - Cache common audio responses in S3
    - _Requirements: 1.5_

  - [ ]* 3.4 Write property test for voice processing feedback
    - **Property 2: Voice Processing Feedback**
    - **Validates: Requirements 1.5**

  - [ ] 3.4 Implement language selection and switching
    - Support initial language selection
    - Allow mid-conversation language changes
    - _Requirements: 1.4_

  - [ ]* 3.5 Write unit tests for Voice Lambda
    - Test Bhashini API error handling
    - Test audio caching logic
    - Test language switching
    - _Requirements: 1.1, 1.3, 1.4, 1.5_

- [ ] 4. Checkpoint - Ensure authentication and voice services work
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 5. Implement Conversation Orchestrator (Orchestrator Lambda + Bedrock)
  - [ ] 5.1 Create Orchestrator Lambda with Bedrock integration
    - Set up Amazon Bedrock client for Claude 3.5 Sonnet
    - Implement conversation context management using DynamoDB
    - Implement intent classification using Bedrock
    - Implement response generation using Bedrock
    - Create system prompt templates for different intents
    - _Requirements: 1.1, 7.1, 7.2_

  - [ ] 5.2 Implement conversation state management
    - Store and retrieve conversation history from DynamoDB
    - Maintain context across multi-turn conversations
    - Handle session timeouts and cleanup
    - _Requirements: 1.1_

  - [ ]* 5.3 Write property test for option presentation limits
    - **Property 21: Option Presentation Limits**
    - **Validates: Requirements 7.2**

  - [ ] 5.4 Implement routing to specialized services
    - Route disease detection requests to Disease Lambda
    - Route insurance requests to Insurance Lambda
    - Route purchase requests to Purchase Lambda
    - Handle routing errors gracefully
    - _Requirements: 2.1, 3.1, 4.1_

  - [ ]* 5.5 Write unit tests for Orchestrator Lambda
    - Test intent classification accuracy
    - Test context management
    - Test routing logic
    - Test Bedrock error handling
    - _Requirements: 1.1, 7.2_

- [ ] 6. Implement Disease Detection Service (Disease Lambda)
  - [ ] 6.1 Create Disease Lambda with Rekognition integration
    - Implement image upload to S3
    - Implement image quality validation
    - Call Amazon Rekognition Custom Labels for disease detection
    - Map Rekognition labels to disease information
    - Generate presigned URLs for image access
    - _Requirements: 2.1, 2.2, 8.2_

  - [ ]* 6.2 Write property test for image acceptance
    - **Property 3: Image Acceptance**
    - **Validates: Requirements 2.1**

  - [ ] 6.3 Implement treatment recommendations
    - Retrieve treatment information based on detected disease
    - Provide recommendations in farmer's language
    - Cache disease information in S3
    - _Requirements: 2.3_

  - [ ]* 6.4 Write property test for disease analysis response completeness
    - **Property 4: Disease Analysis Response Completeness**
    - **Validates: Requirements 2.3**

  - [ ] 6.5 Implement product suggestion integration
    - Link detected diseases to relevant treatment products
    - Offer to connect farmer with ONDC for purchasing
    - _Requirements: 2.6_

  - [ ]* 6.6 Write property test for product suggestion after disease detection
    - **Property 5: Product Suggestion After Disease Detection**
    - **Validates: Requirements 2.6**

  - [ ]* 6.7 Write unit tests for Disease Lambda
    - Test image quality validation edge cases
    - Test Rekognition error handling
    - Test caching logic
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_

- [ ] 7. Implement Insurance Service (Insurance Lambda)
  - [ ] 7.1 Create Insurance Lambda with AgriStack integration
    - Implement farmer details retrieval from AgriStack
    - Implement policy lookup
    - Store farmer profiles in S3
    - _Requirements: 3.1, 8.3_

  - [ ]* 7.2 Write property test for farmer data retrieval
    - **Property 6: Farmer Data Retrieval for Claims**
    - **Validates: Requirements 3.1**

  - [ ] 7.3 Implement claim data collection and validation
    - Collect all required claim fields through conversation
    - Validate claim data completeness
    - Handle missing field scenarios
    - _Requirements: 3.2_

  - [ ]* 7.4 Write property test for complete claim data collection
    - **Property 7: Complete Claim Data Collection**
    - **Validates: Requirements 3.2**

  - [ ] 7.5 Implement claim submission to AgriStack
    - Submit complete claims to AgriStack API
    - Upload evidence photos to S3 and link to claim
    - Store claim records in S3
    - Return claim reference number
    - _Requirements: 3.3, 3.4_

  - [ ]* 7.6 Write property test for claim submission confirmation
    - **Property 8: Claim Submission Confirmation**
    - **Validates: Requirements 3.4**

  - [ ] 7.7 Implement claim status tracking
    - Retrieve claim status from AgriStack
    - Cache status in S3 with TTL
    - Translate status to farmer's language
    - _Requirements: 3.5_

  - [ ]* 7.8 Write property test for status retrieval with translation
    - **Property 9: Status Retrieval with Translation**
    - **Validates: Requirements 3.5, 4.6**

  - [ ]* 7.9 Write unit tests for Insurance Lambda
    - Test AgriStack API error handling
    - Test evidence upload
    - Test claim validation edge cases
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6_

- [ ] 8. Checkpoint - Ensure disease detection and insurance services work
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 9. Implement Purchase Service (Purchase Lambda)
  - [ ] 9.1 Create Purchase Lambda with ONDC integration
    - Implement product search on ONDC network
    - Implement product details retrieval
    - Cache product catalog in S3
    - _Requirements: 4.1, 8.4_

  - [ ]* 9.2 Write property test for product search execution
    - **Property 10: Product Search Execution**
    - **Validates: Requirements 4.1**

  - [ ] 9.3 Implement product presentation
    - Format product information for voice presentation
    - Include name, price, seller information
    - Limit options to 3-4 at a time
    - _Requirements: 4.2, 7.2_

  - [ ]* 9.4 Write property test for product information completeness
    - **Property 11: Product Information Completeness**
    - **Validates: Requirements 4.2**

  - [ ] 9.5 Implement shopping cart management
    - Store cart in DynamoDB with session ID
    - Add/remove items from cart
    - Calculate total amount
    - Provide voice confirmation
    - _Requirements: 4.3_

  - [ ]* 9.6 Write property test for cart update confirmation
    - **Property 12: Cart Update Confirmation**
    - **Validates: Requirements 4.3**

  - [ ] 9.7 Implement checkout and order placement
    - Collect payment method and delivery address
    - Submit order to ONDC
    - Store order record in S3
    - Return order confirmation number
    - _Requirements: 4.4, 4.5_

  - [ ]* 9.8 Write property test for checkout data collection
    - **Property 13: Checkout Data Collection**
    - **Validates: Requirements 4.4**

  - [ ]* 9.9 Write property test for order confirmation
    - **Property 14: Order Confirmation**
    - **Validates: Requirements 4.5**

  - [ ] 9.10 Implement order status tracking
    - Retrieve order status from ONDC
    - Cache status in S3 with TTL
    - Translate status to farmer's language
    - _Requirements: 4.6_

  - [ ]* 9.11 Write unit tests for Purchase Lambda
    - Test ONDC API error handling
    - Test cart operations
    - Test product unavailability handling
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7_

- [ ] 10. Implement Content Cache Service and Offline Support
  - [ ] 10.1 Implement S3-based content caching
    - Cache disease information in S3
    - Cache product catalog in S3
    - Generate presigned URLs for cached content
    - Implement cache invalidation logic
    - _Requirements: 6.2, 6.5_

  - [ ]* 10.2 Write property test for content caching
    - **Property 18: Content Caching**
    - **Validates: Requirements 6.2**

  - [ ]* 10.3 Write property test for offline content access
    - **Property 20: Offline Content Access**
    - **Validates: Requirements 6.5**

  - [ ] 10.4 Implement offline mode detection and notification
    - Detect connectivity loss
    - Notify farmer via voice
    - Indicate unavailable features
    - _Requirements: 6.1_

  - [ ]* 10.5 Write property test for offline mode notification
    - **Property 17: Offline Mode Notification**
    - **Validates: Requirements 6.1**

  - [ ] 10.6 Implement pending action queue
    - Store pending actions in DynamoDB
    - Queue claims, orders, and image analyses
    - Track retry count and status
    - _Requirements: 6.3, 6.4_

  - [ ] 10.7 Create Sync Lambda for background synchronization
    - Triggered by EventBridge every 5 minutes
    - Process pending actions from DynamoDB
    - Retry failed actions with exponential backoff
    - Update action status
    - _Requirements: 6.3, 6.4_

  - [ ]* 10.8 Write property test for pending action synchronization
    - **Property 19: Pending Action Synchronization**
    - **Validates: Requirements 6.3, 6.4**

  - [ ]* 10.9 Write unit tests for offline support
    - Test queue operations
    - Test sync logic
    - Test retry mechanism
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5_

- [ ] 11. Implement Error Handling and Resilience
  - [ ] 11.1 Implement external service error handling
    - Handle Bhashini API failures
    - Handle Rekognition failures
    - Handle AgriStack failures
    - Handle ONDC failures
    - Provide graceful degradation
    - _Requirements: 8.5_

  - [ ]* 11.2 Write property test for external service error handling
    - **Property 23: External Service Error Handling**
    - **Validates: Requirements 8.5**

  - [ ] 11.3 Implement retry logic with exponential backoff
    - Maximum 3 retries for transient failures
    - Exponential backoff: 1s, 2s, 4s
    - Circuit breaker for repeatedly failing services
    - _Requirements: 8.6_

  - [ ]* 11.4 Write property test for retry logic with backoff
    - **Property 24: Retry Logic with Backoff**
    - **Validates: Requirements 8.6**

  - [ ] 11.5 Implement comprehensive logging
    - Log all external API calls
    - Log errors with context
    - Use CloudWatch Logs
    - Implement structured logging
    - _Requirements: 8.7_

  - [ ]* 11.6 Write property test for API interaction logging
    - **Property 25: API Interaction Logging**
    - **Validates: Requirements 8.7**

  - [ ] 11.7 Implement error response generation
    - Generate simple, non-technical error messages
    - Include suggested next steps
    - Translate errors to farmer's language
    - _Requirements: 7.3_

  - [ ]* 11.8 Write property test for error response with next steps
    - **Property 22: Error Response with Next Steps**
    - **Validates: Requirements 7.3**

  - [ ]* 11.9 Write unit tests for error handling
    - Test various error scenarios
    - Test retry logic
    - Test circuit breaker
    - _Requirements: 8.5, 8.6, 8.7, 7.3_

- [ ] 12. Checkpoint - Ensure error handling and offline support work
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 13. Implement Security and Compliance Features
  - [ ] 13.1 Implement payment data security
    - Ensure payment data is not stored locally
    - Use secure payment gateways
    - Validate payment data handling
    - _Requirements: 5.6_

  - [ ]* 13.2 Write property test for payment data non-persistence
    - **Property 16: Payment Data Non-Persistence**
    - **Validates: Requirements 5.6**

  - [ ] 13.3 Implement consent management
    - Collect explicit consent for data operations
    - Store consent records in S3
    - Verify consent before data sharing
    - _Requirements: 10.2, 10.5_

  - [ ]* 13.4 Write property test for consent collection
    - **Property 27: Consent Collection for Data Operations**
    - **Validates: Requirements 10.2**

  - [ ]* 13.5 Write property test for data sharing consent requirement
    - **Property 29: Data Sharing Consent Requirement**
    - **Validates: Requirements 10.5**

  - [ ] 13.6 Implement data deletion functionality
    - Delete farmer data on request
    - Remove from S3 and DynamoDB
    - Comply with 30-day deletion requirement
    - _Requirements: 10.3_

  - [ ]* 13.7 Write property test for data deletion on request
    - **Property 28: Data Deletion on Request**
    - **Validates: Requirements 10.3**

  - [ ]* 13.8 Write unit tests for security features
    - Test consent validation
    - Test data deletion
    - Test payment security
    - _Requirements: 5.6, 10.2, 10.3, 10.5_

- [ ] 14. Implement User Experience Enhancements
  - [ ] 14.1 Implement progress indicators for long operations
    - Detect operations taking longer than 2 seconds
    - Provide voice progress updates
    - Handle timeout scenarios
    - _Requirements: 9.4_

  - [ ]* 14.2 Write property test for progress indicators
    - **Property 26: Progress Indicators for Long Operations**
    - **Validates: Requirements 9.4**

  - [ ] 14.3 Implement first-time user onboarding
    - Create voice-guided tutorial
    - Cover core features
    - Store tutorial completion status
    - _Requirements: 7.4_

  - [ ] 14.4 Implement help and clarification system
    - Offer to repeat information
    - Provide additional help on request
    - Handle unclear intents gracefully
    - _Requirements: 7.5_

  - [ ]* 14.5 Write unit tests for UX features
    - Test progress indicators
    - Test tutorial flow
    - Test help system
    - _Requirements: 9.4, 7.4, 7.5_

- [ ] 15. Integration Testing and End-to-End Workflows
  - [ ]* 15.1 Write integration tests for disease detection workflow
    - Test complete flow: voice input → image upload → analysis → recommendations → product suggestions
    - _Requirements: 1.1, 2.1, 2.2, 2.3, 2.6_

  - [ ]* 15.2 Write integration tests for insurance claim workflow
    - Test complete flow: voice input → farmer verification → claim collection → submission → confirmation
    - _Requirements: 1.1, 3.1, 3.2, 3.3, 3.4_

  - [ ]* 15.3 Write integration tests for purchase workflow
    - Test complete flow: voice input → product search → cart management → checkout → order placement
    - _Requirements: 1.1, 4.1, 4.2, 4.3, 4.4, 4.5_

  - [ ]* 15.4 Write integration tests for offline-to-online sync
    - Test complete flow: offline action queuing → connectivity restoration → sync → completion
    - _Requirements: 6.1, 6.3, 6.4_

- [ ] 16. Deployment and Infrastructure Finalization
  - [ ] 16.1 Configure API Gateway endpoints
    - Set up REST API routes
    - Set up WebSocket API for real-time voice
    - Configure CORS
    - Set up throttling and rate limiting
    - _Requirements: 5.1, 8.5_

  - [ ] 16.2 Configure CloudWatch monitoring and alarms
    - Set up Lambda function metrics
    - Set up API Gateway metrics
    - Create alarms for errors and latency
    - Set up log aggregation
    - _Requirements: 8.7, 9.1, 9.2, 9.3_

  - [ ] 16.3 Deploy infrastructure using CDK/SAM
    - Deploy all Lambda functions
    - Deploy DynamoDB tables
    - Deploy S3 buckets
    - Deploy API Gateway
    - Deploy Cognito user pool
    - _Requirements: All_

  - [ ] 16.4 Configure environment variables and secrets
    - Store API keys in AWS Secrets Manager
    - Configure Lambda environment variables
    - Set up parameter store for configuration
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [ ] 17. Final Checkpoint - End-to-end system validation
  - Ensure all tests pass, ask the user if questions arise.
  - Verify all AWS services are properly configured
  - Test complete user workflows
  - Validate security and compliance requirements

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties
- Unit tests validate specific examples and edge cases
- Integration tests validate end-to-end workflows
- The implementation uses TypeScript for all Lambda functions
- AWS CDK or SAM is used for infrastructure as code
