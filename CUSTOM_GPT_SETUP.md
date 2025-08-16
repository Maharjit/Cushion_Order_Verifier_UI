# Custom GPT Setup Guide

This guide explains how to configure your application to use a custom GPT-4 model that you've built inside ChatGPT.

## Prerequisites

1. You must have created a custom GPT using OpenAI's GPT Builder in ChatGPT
2. You need an OpenAI API key with access to the Assistants API

## Setup Steps

### 1. Get Your Custom Assistant ID

1. Go to [OpenAI Platform](https://platform.openai.com/assistants)
2. Find your custom GPT assistant in the list
3. Copy the Assistant ID (it starts with `asst_`)

### 2. Configure Environment Variables

Create a `.env` file in your project root with the following variables:

```env
# OpenAI API Configuration
OPENAI_API_KEY=your_openai_api_key_here

# Custom GPT Configuration
# Set to "true" to use your custom GPT assistant, "false" to use standard GPT-4
USE_CUSTOM_GPT=true

# Your custom GPT assistant ID (get this from OpenAI platform)
CUSTOM_ASSISTANT_ID=asst_your_custom_assistant_id_here
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Test Your Setup

1. Start your application: `python main.py`
2. Check the logs to confirm:
   - Custom Assistant ID is configured
   - Use Custom GPT is set to true
3. Submit a test request through your UI

## How It Works

When `USE_CUSTOM_GPT=true`, the application will:

1. Create a new conversation thread
2. Send your input to your custom GPT assistant
3. Wait for the assistant to process and respond
4. Return the custom GPT's response

When `USE_CUSTOM_GPT=false`, it will use the standard GPT-4 API as before.

## Troubleshooting

### Common Issues

1. **"Custom Assistant ID not configured"**
   - Make sure you've set the `CUSTOM_ASSISTANT_ID` in your `.env` file
   - Verify the assistant ID is correct (starts with `asst_`)

2. **"Run failed"**
   - Check that your OpenAI API key has access to the Assistants API
   - Verify your custom GPT is properly configured and accessible

3. **"Run did not complete within time limit"**
   - Your custom GPT might be taking too long to respond
   - Consider increasing the `max_attempts` value in the code

### API Limits

- The Assistants API has different rate limits than the chat completions API
- Custom GPTs may take longer to respond than standard GPT-4
- Monitor your usage in the OpenAI platform dashboard

## Benefits of Using Custom GPT

1. **Specialized Knowledge**: Your custom GPT can have specific knowledge about cushion orders
2. **Consistent Responses**: Custom instructions ensure consistent analysis format
3. **Tool Integration**: Custom GPTs can use tools and functions you've configured
4. **Brand Voice**: Maintain your specific tone and style in responses

## Switching Between Models

You can easily switch between your custom GPT and standard GPT-4 by changing the `USE_CUSTOM_GPT` environment variable:

- `USE_CUSTOM_GPT=true` → Uses your custom GPT assistant
- `USE_CUSTOM_GPT=false` → Uses standard GPT-4 API
