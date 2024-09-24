# Anthropic Parallel API Processor

## Overview

This tool is designed to generate datasets faster and more cost-effectively by parallel calling the Anthropic API with optional caching for Claude. It's perfect for processing large volumes of requests while respecting rate limits.

A key feature of this processor is its approach to token estimation. Since there's no open-source tokenizer model available for the new Claude models, we use an older tokenizer to make initial estimates. Once we receive a response from the API, we update these estimates with the actual token usage. This adaptive approach allows us to maintain efficient processing while adhering to Anthropic's rate limits.

## Key Features

- **Parallel Processing**: Maximize throughput with concurrent API requests.
- **Rate Limiting**: Stay within Anthropic's API limits for requests and tokens.
- **Adaptive Token Estimation**: Use initial estimates and update with actual usage.
- **Caching Support**: Optional caching for efficient processing of repeated content.
- **Error Handling**: Retry failed requests and log issues for easy debugging.
- **Memory Efficient**: Stream requests from file to handle large datasets.

## Quick Start

### Installation
This project was developed using Python **3.10.13**. For optimal compatibility and performance, it is strongly recommended that you use the same version.

1. Clone the repository:
```bash
git clone https://github.com/your-username/anthropic-parallel-processor.git
```
```bash
cd anthropic-parallel-processor
```
2. Create virtual environment:
```bash
python -m venv .venv
```
For macOS/Linux:
```bash
source .venv/bin/activate
```
For Windows:
```bash
.venv\Scripts\activate
```

3. Install dependencies:
```
pip install -r requirements.txt
```

4. Set up your Anthropic API key:

   You have two options to set up your API key:

   a. Create a `.env` file in the root of the project and add your API key:
      ```
      ANTHROPIC_API_KEY=your-api-key-here
      ```

   b. Or, set it as an environment variable in your terminal:
      ```bash
      export ANTHROPIC_API_KEY=your-api-key-here
      ```

   Note: Replace 'your-api-key-here' with your actual Anthropic API key.

### Usage

#### Without Caching

```bash
python api_request_parallel_processor.py \
--requests_filepath examples/test_requests_to_parallel_process.jsonl \
--save_filepath examples/data/test_requests_to_parallel_process_results.jsonl \
--request_url https://api.anthropic.com/v1/messages \
--max_requests_per_minute 40 \
--max_tokens_per_minute 16000 \
--max_attempts 5 \
--logging_level INFO
```

#### With Caching

```bash
python api_request_parallel_processor.py \
--requests_filepath examples/test_caching_requests_to_parallel_process.jsonl \
--save_filepath examples/data/test_caching_requests_to_parallel_process_results.jsonl \
--request_url https://api.anthropic.com/v1/messages \
--use_caching True \
--max_requests_per_minute 40 \
--max_tokens_per_minute 16000 \
--max_attempts 5 \
--logging_level INFO
```

### Input File Format
The input file should be a JSONL file where each line is a JSON object representing a single API request. Here's an example structure:
```json
{"model": "claude-3-5-sonnet-20240620", "max_tokens": 1024, "messages": [{"role": "user", "content": "Tell me a joke"}], "metadata": {"row_id": 1}}
```

For caching, use the following structure:
```json
{
  "model": "claude-3-5-sonnet-20240620",
  "max_tokens": 1024,
  "system": [
    {
      "type": "text",
      "text": "You are an AI assistant tasked with analyzing blogs."
    },
    {
      "type": "text",
      "text": "<blog content here>",
      "cache_control": {"type": "ephemeral"}
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Analyze the main themes of this blog."
    }
  ]
}
```

### Configuration Options

- `requests_filepath`: Path to the input JSONL file.
- `save_filepath`: Path for the output JSONL file (optional).
- `request_url`: Anthropic API endpoint (default: "https://api.anthropic.com/v1/messages").
- `api_key`: Your Anthropic API key (can be set as an environment variable).
- `max_requests_per_minute`: Target requests per minute (default: 40).
- `max_tokens_per_minute`: Target tokens per minute (default: 16,000).
- `max_attempts`: Number of retries for failed requests (default: 5).
- `logging_level`: Logging verbosity (default: INFO).
- `use_caching`: Enable caching for repeated content (optional).

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.

## License
This project is licensed under the MIT License - see the LICENSE file for details.