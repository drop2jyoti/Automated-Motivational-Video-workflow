# Automated Motivational Video Generation and Publishing System Using Dockerized n8n

## 1. Purpose

To design and implement an automated system that generates custom motivational video content, creates AI voice-overs, assembles videos, and uploads them to YouTube using n8n. The system will be deployed via Docker for scalability and maintainability, with robust version control for workflows and configuration. The system will use advanced prompt engineering to generate structured content for each video, ready for automated production and publishing.

## 2. Scope

- Automated workflow orchestration using Dockerized n8n
- AI-powered script and metadata generation (via OpenAI)
- AI voice-over creation
- Automated video assembly
- YouTube upload automation
- Version control for workflows and configuration using Git
- Persistent storage for all workflow data and assets
- Structured content output matching business rules for easy tracking and automation

## 3. Functional Requirements

### 3.1 Workflow Automation

#### Trigger Options
- Manual
- Scheduled
- Webhook-based triggers for workflow initiation

#### Script Generation
- Use OpenAI to generate motivational scripts, captions, and metadata via a structured prompt

#### Voice-Over Generation
- Use AI text-to-speech (TTS) APIs (e.g., ElevenLabs, LOVO) to convert scripts into audio files

#### Visual Asset Generation
- Fetch or generate background images/videos using APIs like Pexels, Unsplash, or AI image generators

#### Video Assembly
- **Option A**: Use Creatomate API for templated video creation
- **Option B**: Use FFmpeg (via n8n Execute Command node) for custom assembly

#### Metadata Generation
- Use AI to generate YouTube-optimized titles, descriptions, and tags

#### YouTube Upload
- Upload videos with metadata using the YouTube Data API or n8n's YouTube node

#### Notification & Logging
- Notify stakeholders of upload status
- Log video links and metadata

### 3.2 Dockerized Deployment
- Deploy n8n using Docker and Docker Compose
- Use environment variables for configuration
- Persist data using Docker volumes

### 3.3 Version Control
- Export n8n workflows as JSON files and commit to a Git repository
- Store configuration files (.env, docker-compose.yml, scripts) in the same repository
- Automate workflow exports and Git commits via scheduled n8n workflows or CI tools
- Exclude sensitive data from version control using .gitignore

### 3.4 Structured Content Output
All generated content must follow a strict table structure for each video idea, including columns for:
- id
- idea
- caption
- channel_style_prompt
- character_style_prompt
- production_status
- final_output
- publishing_status
- error_log

Each field must follow specific business rules for consistency and automation-readiness.

## 4. Non-Functional Requirements

### Reliability
- System must recover from failures using persistent storage and versioned backups

### Security
- Credentials and secrets must not be committed to Git
- Use environment variables or secrets managers

### Scalability
- Dockerized architecture to support scaling and easy deployment

### Maintainability
- Modular workflow design
- Version-controlled configuration for easy updates and rollbacks

## 5. Technical Architecture

### 5.1 System Diagram
```
[User/Trigger/Sheet]
        |
        v
   [n8n Workflow Engine (Dockerized)]
        |
        |-- [OpenAI Script & Metadata Generation (Structured Prompt)]
        |-- [AI Voice-Over (TTS API)]
        |-- [Visual Asset Fetch/Gen (Pexels/Unsplash/AI)]
        |-- [Video Assembly (Creatomate API or FFmpeg)]
        |-- [YouTube Upload (YouTube API)]
        |-- [Notification/Logging]
        |
   [Persistent Storage (Docker Volume)]
        |
   [Version Control (Git Repo for JSON exports & config)]
```

### 5.2 Docker Deployment
- Use official n8n Docker image
- Store data in ~/.n8n (or mapped volume)
- Use .env and docker-compose.yml for configuration

### 5.3 Version Control
- All workflow exports and config files tracked in Git
- Automated export and commit process for workflow changes

## 6. Implementation Steps

### 6.1 Dockerized n8n Setup
1. Install Docker and Docker Compose
2. Create .env file for environment variables
3. Set up Docker volumes for persistent data
4. Launch n8n using Docker Compose

### 6.2 Workflow Development in n8n
1. Build modular workflows for each step:
   - Script generation
   - TTS
   - Asset fetch
   - Assembly
   - Upload
2. Use the OpenAI node or HTTP Request node for AI integration
3. Use structured prompt (see below) for generating content and metadata
4. Use Execute Command node for FFmpeg if self-hosted
5. Test each module individually

### 6.3 Version Control Strategy
1. Regularly export workflows as JSON
2. Commit exports and config files to Git
3. Automate exports and commits via n8n or CI/CD
4. Use .gitignore to exclude sensitive files

## 7. AI Prompt for Structured Content Generation

This prompt is to be used with the OpenAI node or HTTP Request node in n8n for generating batch content in the required table format:

```json
{
  "model": "gpt-4",
  "messages": [
    {
      "role": "system",
      "content": "You are a YouTube motivational content strategist. You generate structured content plans for automated video production workflows. Follow the column rules strictly."
    },
    {
      "role": "user",
      "content": "Generate a table with the following exact columns, in this order:\n\nid, idea, caption, channel_style_prompt, character_style_prompt, production_status, final_output, publishing_status, error_log\n\nCOLUMN RULES:\n- id: Start from 1, increment by 1.\n- idea: A short YouTube title, 7–10 words max, matching the [TOPIC]. Each idea is unique.\n- caption: Caption for YouTube. One line about the video, one line about the channel, one line as a call to action for user to subscribe. End with 3 topic-relevant hashtags.\n- channel_style_prompt: Expound on CHANNEL KEYWORDS and give around 5 to 10 words.\n- character_style_prompt: A unique, detailed visual character that matches the idea and topic (no repeats).\n- production_status: Should always be 'for production'.\n- final_output: Leave blank or as 'pending'.\n- publishing_status: Leave blank or as 'pending'.\n- error_log: Leave blank unless there is an error.\n\nGenerate {{count}} rows based on the topic: \"{{topic}}\".\n\nReturn the result as a JSON array of objects, each object representing a row with the specified columns."
    }
  ],
  "temperature": 0.85
}
```

Replace `{{topic}}` and `{{count}}` dynamically in n8n for each batch.

The output is a JSON array ready for downstream processing, tracking, and reporting.

## 8. Risks & Mitigations

### API Rate Limits
- Use API keys with appropriate quotas
- Handle errors gracefully

### Credential Leakage
- Never commit secrets to Git
- Use environment variables

### Data Loss
- Use persistent Docker volumes
- Regular Git commits for backup

### Workflow Corruption
- Version control enables rollback to previous working states

## 9. Success Criteria

- End-to-end automated generation and upload of custom motivational videos to YouTube
- All workflows and configurations are version-controlled and easily restorable
- All generated content follows the specified table structure and business rules
- Minimal manual intervention required for content creation and deployment
- Secure handling of credentials and persistent storage of all data

## 10. References

- [n8n OpenAI Integration Documentation]
- [n8n AI Workflow Tutorial]
- [Creatomate n8n Integration]
- [YouTube Data API]
- [OpenAI API]
