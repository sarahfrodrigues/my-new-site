---
title: Assignment 3 - Deployment and Integration of LLM
description: This assignment focuses on deploying and using both online and local large language models (LLMs) to enhance development workflows. It involves configuring an API from an online LLM provider (like OpenAI), installing and running a local LLM using Ollama, and integrating both into an IDE such as VSCode or PyCharm. The goal is to demonstrate how these tools can assist in tasks like code generation and analysis.
date: 2025-03-24
tags: 
    - LLM
    - Ollama
    - Code Generation
categories:
    - Assignment
image: cover3.jpg
---

# AI Model API Comparison Project

## Project Overview
This project compares the performance and features of **online** and **local** AI models for code generation tasks. It provides:
- A FastAPI interface for model queries
- Performance benchmarking capabilities
- Side-by-side feature comparison

## Online vs Local Models

When selecting between online and local AI models, developers should consider trade-offs in accessibility, performance, privacy, and scalability:

### Online Models
Online models, such as Hugging Face-hosted Qwen2.5-32B, are accessed via API over the internet. These models typically offer:
- Access to powerful infrastructure and large-scale models (e.g., 32B parameters)
- Minimal local setup required
- Automatic updates and optimizations handled by the provider
- Dependence on a stable internet connection and API key
- Potential latency due to network requests
- Usage limits or costs depending on the provider's pricing plan

### Local Models
Local models like CodeLLaMA or Qwen2.5-3B run entirely on a user's machine. They offer:
- Full offline capability and independence from external services
- Greater control over execution and data privacy
- No usage quotas or API restrictions
- Hardware requirements depending on model size
- Slightly more effort for initial setup and configuration

## Online Model Setup

### 1. API Provider Selection
**Hugging Face** was selected as the API provider because:
- Offers state-of-the-art open models
- Provides generous free tier for experimentation
- Has excellent documentation and community support
- Supports the Qwen series of coding-specialized models

### 2. Obtaining API Key
1. Go to [Hugging Face website](https://huggingface.co)
2. Create an account or log in
3. Navigate to [Access Tokens](https://huggingface.co/settings/tokens)
4. Create a new token with `write` permission
5. Securely store your key in a `.env` file:
   ```env
   HF_API_KEY=your_api_key_here
   ```

### 3. Model Selection
Selected model: Qwen/Qwen2.5-Coder-32B-Instruct

Key features:
- 32 billion parameter model
- Specialized for coding tasks
- Supports 32k token context window
- Strong Python code generation capabilities

### 4. Python Environment Setup

Required packages:

```bash
pip install fastapi requests python-dotenv uvicorn
```
## Local Model Development

### 1. Ollama Installation
1. Download Ollama from [ollama.ai](https://ollama.ai)  
2. Install following platform-specific instructions
    ```bash
    ollama run model-name
    ```

### 2. Model Selection

| Model Name         | Size | Strengths                     | Installation Command          |
|--------------------|------|-------------------------------|-------------------------------|
| `codellama:7b`     | 7B   | General-purpose code gen      | `ollama pull codellama:7b`   |
| `qwen2.5-coder:3b` | 3B   | Better complex code reasoning | `ollama pull qwen2.5-coder`  |


### 3. Python Environment
Additional required package:
```bash
pip install ollama
```
## Model Comparison

The model comparison was implemented in Python to evaluate the behavior of different models under the same conditions.  
The following code snippet demonstrates the structure used for this comparison:

```python
from fastapi import FastAPI, Query
from fastapi.responses import JSONResponse, PlainTextResponse, HTMLResponse
from pydantic import BaseModel
import requests
import os
from dotenv import load_dotenv

# Load .env file
load_dotenv()

# Constants
HF_API_KEY = os.getenv("HF_API_KEY")
MODEL_NAME = "Qwen/Qwen2.5-Coder-32B-Instruct"
API_URL = f"https://api-inference.huggingface.co/models/{MODEL_NAME}"

# Initialize FastAPI app
app = FastAPI()


class QueryRequest(BaseModel):
    """
    Request model for text generation endpoint.
    
    Attributes:
        prompt: The input text prompt for the model.
        max_tokens: Maximum number of tokens to generate (default: 512).
    """
    prompt: str
    max_tokens: int = 512


def query_model(prompt: str, max_tokens: int = 512) -> str:
    """
    Query the Hugging Face model API with the given prompt.
    
    Args:
        prompt: Input text for the model.
        max_tokens: Maximum number of tokens to generate.
        
    Returns:
        Generated text from the model.
        
    Raises:
        requests.exceptions.RequestException: If the API request fails.
    """
    headers = {"Authorization": f"Bearer {HF_API_KEY}"}
    payload = {
        "inputs": prompt,
        "parameters": {"max_new_tokens": max_tokens}
    }
    
    response = requests.post(API_URL, headers=headers, json=payload)
    response.raise_for_status()
    
    return response.json()[0].get("generated_text", "")


def format_response(prompt: str, response: str) -> str:
    """
    Format the response with clear prompt and response sections.
    
    Args:
        prompt: The original user prompt
        response: The generated response from the model
        
    Returns:
        Formatted string with prompt and response sections
    """
    # Remove the original prompt if it's duplicated in the response
    cleaned_response = response.replace(prompt, "", 1).strip()
    return f"prompt:\n{prompt}\n\nresponse:\n{cleaned_response}"


@app.post("/generate")
async def generate_text(
    request: QueryRequest,
    format: str = Query(
        "text",
        description="Output format",
        enum=["text", "json", "html"]
    )
):
    """
    Generate text using the Hugging Face model.
    
    Args:
        request: QueryRequest containing prompt and max_tokens.
        format: Output format (text, json, or html).
        
    Returns:
        Response containing generated text in the requested format.
    """
    try:
        raw_response = query_model(request.prompt, request.max_tokens)
        formatted_response = format_response(request.prompt, raw_response)
        
        if format == "json":
            return JSONResponse(content={
                "prompt": request.prompt,
                "response": raw_response.replace(request.prompt, "", 1).strip()
            })
        elif format == "html":
            return HTMLResponse(
                content=f"""
                <html>
                    <body>
                        <h3>Prompt:</h3>
                        <pre>{request.prompt}</pre>
                        <h3>Response:</h3>
                        <pre>{raw_response.replace(request.prompt, "", 1).strip()}</pre>
                    </body>
                </html>
                """
            )
        else:
            return PlainTextResponse(content=formatted_response)
            
    except requests.exceptions.RequestException as e:
        return PlainTextResponse(
            content=f"Error: {str(e)}",
            status_code=500
        )


if __name__ == "__main__":
    # Test the model with proper formatting
    sample_query = "Write a quick sort algorithm in Python."
    response = query_model(sample_query)
    print(format_response(sample_query, response))
```


###  Benchmark Results

=== Model Comparison ===

**Model: online_qwen**
Type: Online
Time: 0.54s
Response length: 1938 chars

Response excerpt:
Write a quick sort algorithm in Python. Certainly! Quick sort is a popular and efficient sorting algorithm that uses a divide-and-conquer approach to sort elements. Here's a simple implementation of the quick sort algorithm in Python:

```python
def quick_sort(arr):
    # Base case: if the array is empty or has one element, it's already sorted
    if len(arr) <= 1:
        return arr
    else:
        # Choose a pivot element from the array
        pivot = arr[len(arr) // 2]
        
        # Partition the array into three parts:
        # - elements less than the pivot
        # - elements equal to the pivot
        # - elements greater than the pivot
        less = [x for x in arr if x < pivot]
        equal = [x for x in arr if x == pivot]
        greater = [x for x in arr if x > pivot]
        
        # Recursively apply quick_sort to the 'less' and 'greater' subarrays
        return quick_sort(less) + equal + quick_sort(greater)

# Example usage:
arr = [3, 6, 8, 10, 1, 2, 1]
sorted_arr = quick_sort(arr)
print("Sorted array:", sorted_arr)
```
Explanation:
1. Base Case: If the array has one or zero elements, it is already sorted, so we return it as is.
2. Pivot Selection: We choose the middle element of the array as the pivot. This is a simple strategy, but there are other strategies for choosing a pivot that can improve performance in certain cases.
3. Partitioning: We partition the array into three lists:
   - `less`: Elements less than the pivot.
   - `equal`: Elements equal to the pivot.
   - `greater`: Elements greater than the pivot.
4. Recursive Sorting: We recursively sort the `less` and `greater` lists and concatenate them with the `equal` list to get the sorted array.

This implementation is straightforward and works well for educational purposes. However, in practice, optimizations such as choosing a better pivot and using in-place partitioning can improve performance.

**Model: local_codellama**
Type: Local
Time: 13.07s
Response length: 756 chars

Response excerpt:

Here is an example of a quicksort algorithm in Python:
```
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[0]
    less = [x for x in arr[1:] if x <= pivot]
    greater = [x for x in arr[1:] if x > pivot]
    return quicksort(less) + [pivot] + quicksort(greater)
```
This algorithm works by selecting a pivot element from the array, and then partitioning the array into two subarrays: one containing elements less than the pivot, and one containing elements greater than the pivot. It then recursively sorts the subarrays and combines them to produce the sorted array.

Here is an example of how you can use this algorithm to sort a list of numbers:
```
arr = [3, 2, 1, 5, 4]
print(quicksort(arr))
# Output: [1, 2, 3, 4, 5]
```

**Model: local_qwen**
Type: Local
Time: 9.78s
Response length: 1340 chars

Response excerpt:
Certainly! Here's a simple implementation of the QuickSort algorithm in Python:

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    else:
        pivot = arr[len(arr) // 2]
        left = [x for x in arr if x < pivot]
        middle = [x for x in arr if x == pivot]
        right = [x for x in arr if x > pivot]
        
        return quick_sort(left) + middle + quick_sort(right)

# Example usage:
example_array = [3, 6, 8, 10, 1, 2, 1]
sorted_array = quick_sort(example_array)
print(sorted_array)
```
Explanation:
1. Base Case: If the array has 0 or 1 element, it is already sorted, so we return it as is.
2. Pivot Selection: We choose a pivot element from the array. In this implementation, we use the middle element of the array as the pivot.
3. Partitioning:
   - `left`: A list containing all elements less than the pivot.
   - `middle`: A list containing all elements equal to the pivot.
   - `right`: A list containing all elements greater than the pivot.
4. Recursive Sorting: We recursively apply the quicksort algorithm to the `left` and `right` lists and concatenate the results with the `middle` list.

This implementation of QuickSort is in-place, meaning it doesn't require extra space for another array. However, the space complexity is O(log n) due to the recursion stack.


### 1. Performance Evaluation
- **Response Time**: Measures the speed of each model in generating output.
- **Response Length**: Captures the character count of the output.
- **Quality Assessment**: Subjective evaluation based on accuracy, coherence, and code correctness.

### 2. Feature Comparison

| Feature             | Online Qwen2.5-32B | Local CodeLLaMA | Local Qwen2.5 |
|---------------------|--------------------|------------------|----------------|
| Model Parameters     | 32B                | 7B               | 3B             |
| Model Size           | Cloud-hosted       | 3.8 GB           | 1.9 GB         |
| Requires Internet    | Yes                | No               | No             |
| API Key Needed       | Yes                | No               | No             |
| Context Window       | 32K tokens         | 16K tokens       | 32K tokens     |
| Streaming Support    | No                 | Yes              | Yes            |
| Setup Complexity     | Medium             | Low              | Low            |
| Recommended Use Case | Production         | Development      | Development    |

## Conclusion

This project demonstrated how developers can effectively integrate both online and local large language models (LLMs) into their development environments. By leveraging cloud-based APIs like Hugging Face and local model runtimes such as Ollama, we explored a flexible approach to AI-assisted coding workflows.

The comparison revealed key trade-offs: online models provide access to larger, production-ready models with minimal setup, while local models offer privacy, offline capability, and greater control. Both approaches have their strengths depending on the use case—whether it's rapid prototyping, secure offline development, or cost-effective experimentation.

Ultimately, this setup empowers developers to:
- Evaluate different AI model configurations in a consistent testing framework
- Balance performance, resource usage, and deployment complexity
- Seamlessly switch between remote and local inference based on project requirements

As LLMs continue to evolve, understanding how to deploy and integrate them efficiently will be an essential skill for modern software development.

