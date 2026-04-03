# Making an API request to Bedrock using lambda
We require three essential components: 
- a Bedrock Runtime Client to connect to the service,
- a Model ID to specify which model you want to run, and
- a User Message containing the text you want to feed into the model.

1. We use inference profiles for the model Id which knows the model is available in multiple regions like us-west-2 and us-east-2. 
(AWS automatically routes it to the correct region where your model exists, even if you're connecting from a different region.)
We'll use the inference profile for Global Claude Sonnet 4. 
The inference profile is found under Amazon Bedrock > Infer > Inference Profile

3. The user message content is a list because a single message can contain different types of content - text, images, or other media types. 
This structure allows you to send multimodal requests.

4. We make the API call using the converse method

5. There are two main message types we'll work with:
- User messages - Content we want to feed into the model (role: "user")
- Assistant messages - Content the model has produced (role: "assistant")

```
import boto3

def lambda_handler(event, context):
    client = boto3.client("bedrock-runtime", region_name="us-west-2")
    model_id = "global.anthropic.claude-sonnet-4-20250514-v1:0"

    user_message = {"role": "user", "content": [{"text": "what is the capital city of India?"}]}
    messages = [user_message]  # wrap it in a list

    response = client.converse(modelId=model_id, messages=messages)

    # Print the response
    print(response)
```

## Test Output
```
Status: Succeeded
Test Event Name: apiclaudetest

Response:
null

The area below shows the last 4 KB of the execution log.

Function Logs:
START RequestId: 0fdb2630-9622-46d1-bddc-85036aae752b Version: $LATEST
{'ResponseMetadata': {'RequestId': '8b77e105-57ed-4a20-ac14-830c830576aa', 'HTTPStatusCode': 200, 'HTTPHeaders': {'date': 'Fri, 03 Apr 2026 22:35:40 GMT', 'content-type': 'application/json', 'content-length': '351', 'connection': 'keep-alive', 'x-amzn-requestid': '8b77e105-57ed-4a20-ac14-830c830576aa'}, 'RetryAttempts': 0}, 'output': {'message': {'role': 'assistant', 'content': [{'text': 'The capital city of India is New Delhi.'}]}}, 'stopReason': 'end_turn', 'usage': {'inputTokens': 15, 'outputTokens': 12, 'totalTokens': 27, 'cacheReadInputTokens': 0, 'cacheWriteInputTokens': 0}, 'metrics': {'latencyMs': 715}}
END RequestId: 0fdb2630-9622-46d1-bddc-85036aae752b
REPORT RequestId: 0fdb2630-9622-46d1-bddc-85036aae752b	Duration: 2715.86 ms	Billed Duration: 3087 ms	Memory Size: 128 MB	Max Memory Used: 89 MB	Init Duration: 370.72 ms
```

<img width="981" height="293" alt="image" src="https://github.com/user-attachments/assets/87f8b68d-f69b-437d-b129-82f932e63d3d" />


# Solving Errors
### Timeout Error
Lambda has a default timeout of 3 seconds, but you can configure it up to 900 seconds (15 minutes) if it takes longer to return a response.

### Validation Exception Error
- Ensure access to the model you are working with is enabled.
- For Anthropic Claude you have to request access
- Ensure your IAM user has the necessary permissions for Amazon Bedrock resources
- Ensure your account is authorized to access the models 
