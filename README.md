# Code to call external API with OAuth 2.0 Authentication in Console App (C#)

[Call external API with OAuth 2.0 authentication using Console App (C#)](https://www.c-sharpcorner.com/forums/call-external-api-with-oauth-20-authentication-using-c-sharp-console-app)

```
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        // Replace these values with your actual OAuth 2.0 credentials
        string clientId = "your_client_id";
        string clientSecret = "your_client_secret";
        string accessToken = await GetAccessToken(clientId, clientSecret);

        if (!string.IsNullOrEmpty(accessToken))
        {
            // Replace this URL with the API endpoint you want to call
            string apiUrl = "https://api.example.com/resource";

            // Make the API request using the access token
            await CallApi(apiUrl, accessToken);
        }

        Console.ReadLine();
    }

    static async Task<string> GetAccessToken(string clientId, string clientSecret)
    {
        // Replace these values with the OAuth 2.0 token endpoint and required parameters
        string tokenEndpoint = "https://oauth.example.com/token";
        string scope = "your_api_scope";

        using (HttpClient client = new HttpClient())
        {
            // Set the required headers for token endpoint authentication
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", Convert.ToBase64String(System.Text.Encoding.ASCII.GetBytes($"{clientId}:{clientSecret}")));

            // Set the parameters for token request
            var content = new FormUrlEncodedContent(new[]
            {
                new KeyValuePair<string, string>("grant_type", "client_credentials"),
                new KeyValuePair<string, string>("scope", scope)
            });

            // Call the token endpoint to get the access token
            HttpResponseMessage response = await client.PostAsync(tokenEndpoint, content);

            if (response.IsSuccessStatusCode)
            {
                var responseContent = await response.Content.ReadAsStringAsync();
                // Parse the JSON response to get the access token
                // Note: You may need to use a JSON parsing library here
                // For simplicity, we are assuming a simple JSON response format
                // Make sure to handle errors and parse the response appropriately in a production scenario
                return responseContent.Split('"')[3];
            }
            else
            {
                Console.WriteLine($"Error getting access token: {response.StatusCode}");
                return null;
            }
        }
    }

    static async Task CallApi(string apiUrl, string accessToken)
    {
        using (HttpClient client = new HttpClient())
        {
            // Set the access token in the Authorization header
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Make the API request
            HttpResponseMessage response = await client.GetAsync(apiUrl);

            if (response.IsSuccessStatusCode)
            {
                string apiResponse = await response.Content.ReadAsStringAsync();
                Console.WriteLine($"API Response: {apiResponse}");
            }
            else
            {
                Console.WriteLine($"Error calling API: {response.StatusCode}");
            }
        }
    }
}
```
