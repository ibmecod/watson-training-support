Watson Training Support
===============================

This project is used to support  Watson  training sessions at various clients conductcd by IBM Developer Advocates. Because this is a public repo, it contains no client specific information. Note however that  the content may be updated over time to support different training sessions. It is  therefore recommended that you clone  the project at the time of your training session so the material will match the agenda of your session.


## Watson API Guide for Developers

The [Watson API Guide for Developers](WatsonAPIGuideForDevelopersV1.0.pdf) is a summary of the information presented at the training session. It contains links to relevant information and resources and has a one page summary of each Watson API.


## Labs

These are the instructions and all supporting files for the hands-on-lab exercises.

The hands on lab exercises are:

### Lab 1 - Watson Visual Recognition
In this lab, you will familiarize yourself with the Watson Visual Recognition service by learning how to classify, detect faces in, and recognize text in an image, and how to create, train and then use a custom classifier. **Note**: This lab requires the free [Postman](https://www.getpostman.com) REST client that is available for Mac, Windows and Linux.  

[Markdown format](labs/watsonvr/README.md)

[PDF format](labs/watsonvr/WatsonVisualRecognition.pdf)

### Lab 2 - Watson Discovery 
Use the Discovery tooling to run various queries against Discovery News - an existing dataset of English-language news sources that is updated continuously, with approximately 300,000 new articles and blogs added daily. 

[Markdown format](labs/watsondisc/README.md) 

[PDF format](labs/watsondisc/WatsonDiscoveryNewsIntro.pdf)

### Lab 3 - Watson Conversation
 In this lab, you will familiarize yourself with the Watson Conversation service tooling by starting to develop a sample restaurant chatbot that handles reservations for a restaurant. This lab utilizes the new slots feature in the Conversation service that enables you to gather multiple pieces of information in a single dialog node and allow users of the chatbot to provide required information proactively.
 
 [Markdown format](labs/watsonconv/README.md)
 
 [PDF Format](labs/watsonconv/WatsonConversationIntro.pdf)
 
 
## Code snippets
This section has various code snippets relevant to participants' interests that are  not easily found  in the Watson API document

### Java snippets

#### Calling Watson NLU via a proxy using the Watson Java SDK

**Note:** This must be compiled with the [Watson Java SDK](https://github.com/watson-developer-cloud/java-sdk/releases)

```java
import com.ibm.watson.developer_cloud.natural_language_understanding.v1.NaturalLanguageUnderstanding;
import com.ibm.watson.developer_cloud.natural_language_understanding.v1.model.AnalysisResults;
import com.ibm.watson.developer_cloud.natural_language_understanding.v1.model.AnalyzeOptions;
import com.ibm.watson.developer_cloud.natural_language_understanding.v1.model.EntitiesOptions;
import com.ibm.watson.developer_cloud.natural_language_understanding.v1.model.Features;

public class CallNLUViaProxy {

	public static void main(String[] args) {


		// Set proxy here
		System.setProperty("https.proxyHost", "nnn.nnn.nnn.nnn");
		System.setProperty("https.proxyPort", "nnnn");
		
       // Set your own NLU credentails here
		NaturalLanguageUnderstanding service = new NaturalLanguageUnderstanding(
				NaturalLanguageUnderstanding.VERSION_DATE_2017_02_27, "username", "password");
				
		Map<String, String> headers = new HashMap<String, String>();
		headers.put(HttpHeaders.X_WATSON_LEARNING_OPT_OUT, "True");
		service.setDefaultHeaders(headers);

		EntitiesOptions entities = new EntitiesOptions.Builder().sentiment(true).limit(1).build();
		Features features = new Features.Builder().entities(entities).build();
		AnalyzeOptions parameters = new AnalyzeOptions.Builder().url("www.cnn.com").features(features).build();
		AnalysisResults results = service.analyze(parameters).execute();
		System.out.println("Results\n" + results.toString());

	}

}
```
 
#### Calling Watson NLU via a proxy using the NLU REST interface

**Note:** This must be compiled with the [Jackson JSON API](https://github.com/FasterXML/jackson)

```java
package com.ibm.demo;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.Base64;

import javax.net.ssl.HttpsURLConnection;

import com.fasterxml.jackson.databind.ObjectMapper;

public class CallNLURESTViaProxy {
	
	public static void main(String[] args) throws IOException {
		
        String baseURL = "https://gateway.watsonplatform.net/natural-language-understanding/api/v1/analyze";
		
        // Set proxy here
		System.setProperty("https.proxyHost", "nnn.nnn.nnn.nnn");
		System.setProperty("https.proxyPort", "nnnn");
		
		// Set up query parameters
		StringBuilder queryParams = new StringBuilder("?version=2017-02-27");
		queryParams.append("&url=www.cnn.com");
		queryParams.append("&features=entities");
		queryParams.append("&entities.sentiment=true");
		
		// Set up GET request 
		URL url = new URL(baseURL + queryParams.toString());
		HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
		connection.setRequestMethod("GET");
		connection.setRequestProperty("X-Watson-Learning-Opt-Out", "True");
		
		// Set your NLU username and password below
		String authStr = Base64.getEncoder()
                .encodeToString("username:password".getBytes());
		
        //setting Authorization header
        connection.setRequestProperty("Authorization", "Basic " + authStr);
        
        // Send request
        int responseCode = connection.getResponseCode();
		System.out.println("Sending 'GET' request to URL : " + url);
		System.out.println("Response Code : " + responseCode);

		// Read returned JSON as a String 
		BufferedReader in = new BufferedReader(
		        new InputStreamReader(connection.getInputStream()));
		String inputLine;
		StringBuilder response = new StringBuilder();

		while ((inputLine = in.readLine()) != null) {
			response.append(inputLine);
		}
		in.close();
		
		// Use Jackson JSON lib to pretty print returned JSON
		ObjectMapper mapper = new ObjectMapper();
		Object json = mapper.readValue(response.toString(), Object.class);
		String formattedJSON = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(json);
		System.out.println("JSON returned");
		System.out.println(formattedJSON);
		
	}


}
```
 
## Python snippets

#### Calling Watson NLU via a proxy using the Watson Python SDK

```python
import json
import os
from watson_developer_cloud import NaturalLanguageUnderstandingV1
import watson_developer_cloud.natural_language_understanding.features.v1 as Features

# set proxy in env var so it can be read by the underling Requests lib used by
# the Watson SDK
os.environ["HTTPS_PROXY"] = "https://nnn.nnn.nnn.nnn:nnnn"

# Note: if proxy requires authentication, use the following format
# os.environ["HTTPS_PROXY"] = "https://user_id:pass@proxy:port"

# Substitute your NLU credentials below
natural_language_understanding = NaturalLanguageUnderstandingV1(
      version='2017-02-27',
      username='username',
      password='password')
      
response = natural_language_understanding.analyze(
           url='www.cnn.com',
           features = [Features.Entities(), Features.Sentiment(True)])
  
 # Pretty print the returned JSON          
print(json.dumps(response, indent=2))

```

#### Calling Watson NLU via a proxy using the NLU REST interface

```python
import json
import os
import requests
from requests.auth import HTTPBasicAuth

# set proxy in env var so it can be read by the underling Requests lib used by
# the Watson SDK
os.environ["HTTPS_PROXY"] = "https://nnn.nnn.nnn.nnn:nnnn"

# Note: if proxy requires authentication, use the following format
# os.environ["HTTPS_PROXY"] = "https://user_id:pass@proxy:port"

# Request parameters
requestParams = {'version': '2017-02-27', 'url': 'www.cnn.com', 'features': 'entities',
                 'entities.sentiment': 'true'}

requestURL = "https://gateway.watsonplatform.net/natural-language-understanding/api/v1/analyze"

# Substitute your NLU credentials below
username="username"
password="password"
myheader = {'X-Watson-Learning-Opt-Out': 'True'}

response = requests.get(requestURL, auth=HTTPBasicAuth(username,password), params=requestParams, headers=myheader)

# Pretty print returned JSON
print(json.dumps(response.json(), indent=2))
```