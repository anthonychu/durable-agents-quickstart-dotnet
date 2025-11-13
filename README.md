### Deploy the Infrastructure and Application

1. Run `azd up` to provision the infrastructure in your Azure account.

### Interact with the Deployed Agent

1. Get the function key for the deployed Azure Function:
   ```bash
   API_KEY=`az functionapp function keys list --name $(azd env get-value AZURE_FUNCTION_NAME) --resource-group $(azd env get-value AZURE_RESOURCE_GROUP) --function-name http-MyDurableAgent --query default -o tsv`
   ```

1. Ask the deployed agent a question:
   ```bash
   curl -i -X POST "https://$(azd env get-value AZURE_FUNCTION_NAME).azurewebsites.net/api/agents/MyDurableAgent/run?code=$API_KEY" \
     -H "Content-Type: text/plain" \
     -d "What are three popular programming languages?"
   ``` 

   Note the thread ID returned in the response headers.

1. Set the thread id in a variable:
   ```bash
   THREAD_ID=<thread id from previous response>
   ```

1. Ask a follow-up question in the same thread:
   ```bash
   curl -i -X POST "https://$(azd env get-value AZURE_FUNCTION_NAME).azurewebsites.net/api/agents/MyDurableAgent/run?code=$API_KEY&threadId=$THREAD_ID" \
     -H "Content-Type: text/plain" \
     -d "Which is easiest to learn?"
   ```

   