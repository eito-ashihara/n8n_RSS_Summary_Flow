# n8n_RSS_Summary_Flow
This GitHub repository is based on my article originally published on Zenn.
このGitHubリポジトリは私自身のZennの記事をもとに移載しています。\
Zenn URL: https://zenn.dev/eito_assy/articles/4d93d582a920c8

# Overview
Using the automation tool n8n, I created an n8n flow that uses GeminiAPI to summarize the contents of the RSS feeds I subscribe to daily and send them via email.

# About this file
This file is the Json file of the n8n flow I created. You can use this flow by loading the Json file with n8n.

# About this workflow
The workflow I created with N8N is
"receive periodic updates from an RSS feed, summarize them using AI, and send them by email."
<img width="1400" height="258" alt="image" src="https://github.com/user-attachments/assets/aa3b8706-bbda-4e9f-ad64-8c380ee4157c" />\

The workflow I created is shown below.

## Node 1; Schedule Trigger
<img width="186" height="184" alt="image" src="https://github.com/user-attachments/assets/7796188d-bc63-4803-9e74-9ab0437bcb65" />\
<img width="400" height="526" alt="image" src="https://github.com/user-attachments/assets/b22df00f-0f0b-4d97-8e03-4437dbb23d7f" />\
First, use the Schedule Trigger node to determine when to run the flow. In the Hours Between Triggers field on the settings screen, set how often you want the flow to run. In this example, we set Gemini to summarize the update contents every three hours.

## Node 2; RSS Read
<img width="170" height="184" alt="image" src="https://github.com/user-attachments/assets/e0f30bf6-213f-4d42-9af9-e5bf62862cb7" />\
<img width="411" height="281" alt="image" src="https://github.com/user-attachments/assets/f6cd7dbf-a868-4860-9dfc-acf2975a8d17" />\
The node connected next to ScheduleTrigger is called RSS Read. This is a node that reads the specified RSS, and in this case, the Input will retrieve the RSS content at the time ScheduleTrigger is activated. Enter the URL of the RSS you want to retrieve in the parameter URL.

## Node 3: IF
<img width="314" height="153" alt="image" src="https://github.com/user-attachments/assets/b027ec93-91f6-4410-a02d-561c9718fb4a" />\
<img width="785" height="413" alt="image" src="https://github.com/user-attachments/assets/24580807-88b4-48d2-a37a-b1e864ebc091" />\
The IF node allows you to create conditional branches. This is the same as the if statement in Python. In this example, I set the RSS feed retrieved by this IF node to output updates within the last three hours as True, and any other updates (more than three hours ago) as False.

## Node 4: Message a model
<img width="439" height="158" alt="image" src="https://github.com/user-attachments/assets/5f9b367d-f808-42df-9106-dbf8e81e8fb1" />\
<img width="406" height="825" alt="image" src="https://github.com/user-attachments/assets/5ef34639-5371-48f0-94a7-dc99715c0b9d" />\
Use Gemini AI to summarize the content of the RSS article selected in the IF node.

Prompt
```Json
Please summarize the following article in approximately 7 lines.

Title: {{ $json["title"] }}
Body: {{ $json["content"] || $json["description"] }}
URL: {{ $json["link"] }}

Please return the output in the following JSON format:
{
"title": "{{ $json["title"] }}",
"link": "{{ $json["link"] }}",
"summary": "Summary text"
}
```

## Node 5: Code
<img width="228" height="168" alt="image" src="https://github.com/user-attachments/assets/33e2a786-95bc-474c-b8de-0ceced5dc09d" />\
<img width="779" height="777" alt="image" src="https://github.com/user-attachments/assets/6a04b1b9-6f83-4786-9c98-d7e120b880b9" />\
The Code node converts the format of the data created in Message a model so that it can be output in email format.

## Node 6: Send a Message
<img width="192" height="138" alt="image" src="https://github.com/user-attachments/assets/dbba4816-b3aa-457d-b803-9f0a44e4ee58" />\
<img width="401" height="550" alt="image" src="https://github.com/user-attachments/assets/70a03f17-66c8-4367-b784-7285d10a365d" />\
Send the email template obtained using the Send a message node.
Select the linked Gmail account in Credential to connect with. Specify the recipient in To.
Specify the text you want to use in the subject line in Subject.



