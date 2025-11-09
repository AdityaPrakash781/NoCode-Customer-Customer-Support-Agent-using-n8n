# NoCode-Customer-Customer-Support-Agent-using-n8n
This project is an AI-powered Telegram support agent built in n8n that automatically triages customer issues and fully automates complex actions like seat transfers with no refund policy.
AI-Powered Telegram Customer Support Agent (n8n)
This project is an AI-powered Telegram support agent built in n8n that automatically triages customer issues and fully automates complex actions like seat transfers.

This project is a sophisticated, no-code customer support agent built with n8n.io. It uses Telegram as its chat interface, Google Sheets as a database for chat history and customer info, and Groq (AI) to power its understanding and decision-making.

The agent can handle multiple types of customer inquiries, maintain a persistent memory of conversations, and perform complex, multi-step actions like an automated "Seat Transfer" process complete with email confirmations.

🚀 Key Features
Persistent Chat Memory: The agent retrieves all previous chat history from a Google Sheet to understand the full context of a user's problem.

AI-Powered Intent Triage: A "main" AI agent analyzes the user's query and classifies it into three distinct categories: Refund, Seat Transfer, and Miscellaneous.

Automated Ticket Creation: For Miscellaneous issues, a second AI agent (a "Summarizer") creates a concise support ticket, notifies an admin via Telegram, and informs the user their ticket has been generated.

Complex Multi-Step Action: The Seat Transfer path triggers a complex workflow that:

Uses an AI data extractor to get user and transferee details.

Sends a confirmation email to the original user via Gmail.

If confirmed, sends a second confirmation email to the transferee.

Atomically updates the "Customer Sheet" (deletes the old user, adds the new one).

Confirms the successful transfer with the user on Telegram.

Fallback & Error Handling: The workflow uses multiple AI models (main and fallback) to ensure continuous operation.

Notification System: Sends automated messages to both the user (on Telegram) and an admin (on Telegram) based on the action taken.

⚙️ Core Technologies Used
Orchestration: n8n.io

Chat Interface: Telegram

AI Models: Groq (using Langchain agent nodes)

Database: Google Sheets (for both chat history and customer records)

Notifications & Approvals: Gmail (for multi-step confirmation)

Data Manipulation: Code (JavaScript) nodes to parse AI output

🗺️ Workflow Explained
This system is built around a central Switch node that routes the user based on the AI's initial classification.

1. Trigger & Context
A user sends a message to the Telegram Bot.

n8n triggers and immediately fetches the entire chat history for that user from the "Chat History" Google Sheet.

2. AI Triage (Main AI Agent)
The chat history and the new message are sent to a Groq AI model.

The AI's job is to classify the intent (Refund, Seat Transfer, Miscellaneous) or provide a direct conversational reply.

The AI's literal output is used to route the workflow.

3. Routing (The Switch)
PATH 1: General Chat / Refund Request

If the AI's output is a simple conversational response (e.g., asking for details, or stating the "no refund" policy), this response is sent directly back to the user on Telegram.

PATH 2: miscellaneous (Ticket Creation)

The workflow hits the Summarizer AI agent.

This AI's prompt is to summarize the entire chat history into a ticket_summary, phone_number, and email JSON format.

A Code node converts the AI's string output into a valid JSON object.

A Telegram message is sent to the admin with the ticket details.

A Telegram message is sent to the user confirming their ticket is logged.

PATH 3: initiate_seat_transfer (Automated Action)

The workflow hits the AI Agent1 (Data Extractor).

This AI's prompt is to extract 5 key details: user_email, user_phone_number, transferee_name, etc.

A Code node converts this into JSON.

The agent looks up the user_email in the "Customer Sheet".

Gmail Confirmation 1 (User): A "Send and wait for response" email is sent to the original user.

Gmail Confirmation 2 (Transferee): If the user clicks "Confirm," a second email is sent to the transferee.

Database Update: If the transferee clicks "Confirm," the agent appends the transferee's info to the "Customer Sheet" and then deletes the original user's row.

Final Confirmation: A Telegram message is sent to the original user saying the transfer is complete.

4. Logging
At the end of every path, the AI's final response or a summary of the action is appended to the "Chat History" Google Sheet, so the bot's memory is always up-to-date for the next interaction.

🛠️ Setup & Installation
Clone the repository:

Bash

git clone https://github.com/YourUsername/Your-Repository-Name.git
Import the n8n Workflow:

Open your n8n instance.

Go to Import > From File and upload the My workflow.json file included in this repository.

Install Node Collections:

This workflow requires the @n8n/n8n-nodes-langchain node collection. Ensure it is installed in your n8n instance.

🔧 Configuration
This workflow requires several credentials to be set up in n8n before it can run.

Telegram:

Telegram Trigger & Telegram (Send Message): Create a new bot with @BotFather on Telegram to get your API key.

Google Sheets:

Google Sheets (Read/Write): You must set up OAuth2 credentials for the Google Sheets API.

Share your Google Sheets (for "Chat History" and "Customer Sheet") with the service account email associated with your credentials.

Groq:

Groq Chat Model: Add your free Groq API key.

Gmail:

Gmail: You must set up OAuth2 credentials for the Gmail API to allow sending messages and waiting for replies.

🤝 Contributing
Contributions are welcome! Please feel free to open an issue or submit a pull request.

Fork the Project

Create your Feature Branch (git checkout -b feature/AmazingFeature)

Commit your Changes (git commit -m 'Add some AmazingFeature')

Push to the Branch (git push origin feature/AmazingFeature)

Open a Pull Request
