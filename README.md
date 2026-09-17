# Telegram AI Agent with Google Workspace and Amazon Search

## Overview

This n8n workflow creates a Telegram-based AI assistant. Users send
messages through Telegram, the AI Agent interprets the request, and the
agent can use connected tools to perform actions across Google
Workspace, Amazon search, and calculations.

The workflow includes:

-   Telegram message intake
-   OpenAI Chat Model integration
-   Conversation memory
-   Gmail messaging
-   Google Sheets row insertion
-   Google Calendar event creation
-   Google Docs document creation
-   Google Docs document updates
-   Amazon product search through SerpApi
-   Telegram text responses

The AI Agent serves as the central orchestration layer and decides when
a connected tool is needed to fulfill the user's request.

------------------------------------------------------------------------

## Workflow Architecture

``` text
Telegram Trigger
       |
       v
   AI Agent
       |
       +----------------------> Send a text message
       |
       +----------------------> Gmail
       |
       +----------------------> Google Sheets
       |
       +----------------------> Google Calendar
       |
       +----------------------> Google Docs - Create document
       |
       +----------------------> SerpApi - Amazon search
       |
       +----------------------> Google Docs - Update document

AI Agent connections:
- OpenAI Chat Model
- Simple Memory
- Gmail
- Google Sheets
- Google Calendar
- Google Docs
- SerpApi
```

------------------------------------------------------------------------

## Workflow Components

### 1. Telegram Trigger

**Node:** `Telegram Trigger`

Receives incoming Telegram messages and starts the workflow.

The trigger is configured to listen for message updates. The incoming
message is passed directly to the AI Agent for processing.

Typical use cases include:

-   Asking questions
-   Requesting product searches
-   Sending email instructions
-   Creating calendar events
-   Creating or updating documents
-   Recording information in a spreadsheet

### 2. AI Agent

**Node:** `AI Agent`

The AI Agent is the central decision-making and orchestration component.

It:

1.  Receives the user's Telegram message.
2.  Interprets the intent of the request.
3.  Uses the connected OpenAI Chat Model to generate or plan a response.
4.  Uses memory to maintain conversational context.
5.  Selects and invokes tools when an external action is required.
6.  Returns the result to the Telegram response node.

The AI Agent is connected to multiple tools, allowing it to function as
a general-purpose productivity assistant.

### 3. OpenAI Chat Model

**Node:** `OpenAI Chat Model`

Provides the language model used by the AI Agent.

Responsibilities include:

-   Understanding natural-language requests
-   Generating responses
-   Interpreting user intent
-   Deciding when tools may be required
-   Summarizing tool results

The model, credentials, system instructions, and token settings should
be maintained in the n8n node configuration.

### 4. Simple Memory

**Node:** `Simple Memory`

Maintains conversational context between messages.

Memory can help the assistant:

-   Understand follow-up questions
-   Maintain context across a conversation
-   Refer to earlier user instructions
-   Provide more consistent responses

> Persistence, session identification, and retention depend on the
> memory configuration used in n8n.

### 5. Send a Text Message

**Node:** `Send a text message`

Sends the AI Agent's final response back to the user through Telegram.

The response may contain:

-   A direct answer
-   Results returned by a connected tool
-   Confirmation of an action
-   An error or clarification request

------------------------------------------------------------------------

## Connected Tools

### 6. Send a Message in Gmail

**Node:** `Send a message in Gmail`

Allows the AI Agent to send email messages through Gmail.

Possible use cases:

-   Sending an email based on a user instruction
-   Communicating information to a recipient
-   Sending summaries or generated content

Recommended safeguards:

-   Confirm the recipient before sending.
-   Confirm the subject and message body for sensitive communications.
-   Use least-privilege Gmail permissions.
-   Avoid sending email solely on the basis of ambiguous instructions.

### 7. Append Row in Google Sheets

**Node:** `Append row in sheet`

Adds a new row to a configured Google Sheets spreadsheet.

Possible use cases:

-   Logging user requests
-   Recording leads or tasks
-   Tracking expenses or activities
-   Storing structured information extracted from a message
-   Maintaining an operational log

The spreadsheet ID, sheet name, column mapping, and authentication
should be configured in n8n.

### 8. Create an Event in Google Calendar

**Node:** `Create an event in Google Calendar`

Creates a calendar event based on the user's request.

Potential event details include:

-   Event title
-   Start date and time
-   End date and time
-   Description
-   Location
-   Attendees

Recommended safeguards:

-   Ask for missing date, time, or timezone information.
-   Confirm the event details before creating it.
-   Handle conflicting or ambiguous schedules explicitly.
-   Avoid assuming a default duration unless it is documented.

### 9. Create a Document in Google Docs

**Node:** `Create a document`

Creates a new Google Docs document.

Possible use cases:

-   Creating meeting notes
-   Generating reports
-   Preparing project documentation
-   Creating summaries or drafts
-   Saving AI-generated content

The workflow should define how the document title and content are
generated from the user's request.

### 10. Amazon Search in SerpApi

**Node:** `Amazon search in SerpApi`

Allows the AI Agent to search Amazon-related product information through
SerpApi.

Possible use cases:

-   Searching for products
-   Finding product listings
-   Comparing product information
-   Supporting shopping-related questions

The SerpApi credentials, search parameters, region, and result handling
should be configured in n8n.

The assistant should distinguish between search results and verified
product availability, pricing, or delivery information.

### 11. Update a Document in Google Docs

**Node:** `Update a document`

Updates an existing Google Docs document.

Possible use cases:

-   Appending new content
-   Updating project notes
-   Editing an existing report
-   Maintaining a living document
-   Adding information from a Telegram conversation

The workflow should ensure that the target document is identified
correctly before making changes.

Recommended safeguards:

-   Confirm the target document.
-   Avoid overwriting existing content unintentionally.
-   Define whether the operation appends, replaces, or modifies content.
-   Preserve important document structure where required.

------------------------------------------------------------------------

## End-to-End Execution

1.  A user sends a message to the Telegram bot.
2.  The Telegram Trigger receives the message.
3.  The message is passed to the AI Agent.
4.  The AI Agent interprets the request using the OpenAI Chat Model.
5.  The AI Agent checks conversation memory when relevant.
6.  If required, the AI Agent invokes one of the connected tools.
7.  The tool returns its result to the AI Agent.
8.  The AI Agent prepares a final response.
9.  The Telegram node sends the response to the user.

------------------------------------------------------------------------

## Example Requests

The workflow can support requests such as:

  User request                                          Potential tool
  ----------------------------------------------------- --------------------
  "Send an email to the project team."                  Gmail
  "Log this issue in the spreadsheet."                  Google Sheets
  "Schedule a meeting tomorrow at 10 AM."               Google Calendar
  "Create a document containing these meeting notes."   Google Docs
  "Search Amazon for wireless headphones."              SerpApi
  "Add this information to my project document."        Google Docs Update
  "What did we discuss earlier?"                        Simple Memory

Actual behavior depends on the prompts, node configuration, credentials,
and tool permissions.

------------------------------------------------------------------------

## Required Integrations

  Integration                 Purpose
  --------------------------- ---------------------------------------
  Telegram Bot API            Receive messages and send responses
  OpenAI                      Provide the AI Agent's language model
  n8n                         Workflow orchestration
  Gmail OAuth/API             Send email messages
  Google Sheets OAuth/API     Append rows to spreadsheets
  Google Calendar OAuth/API   Create calendar events
  Google Docs OAuth/API       Create and update documents
  SerpApi                     Amazon product search

------------------------------------------------------------------------

## Configuration Checklist

-   [ ] Configure Telegram bot credentials.
-   [ ] Confirm that the Telegram Trigger receives messages.
-   [ ] Connect the OpenAI Chat Model to the AI Agent.
-   [ ] Configure the AI Agent system prompt and instructions.
-   [ ] Configure Simple Memory and session handling.
-   [ ] Configure Gmail credentials and permissions.
-   [ ] Configure the Google Sheets spreadsheet and target sheet.
-   [ ] Configure Google Calendar credentials and event fields.
-   [ ] Configure Google Docs creation settings.
-   [ ] Configure Google Docs update settings and document
    identification.
-   [ ] Configure SerpApi credentials and search parameters.
-   [ ] Map the AI Agent output to the Telegram response node.
-   [ ] Add error handling for failed tool calls.
-   [ ] Test each tool independently and through the AI Agent.

------------------------------------------------------------------------

## Security and Governance

Because the AI Agent can perform external actions, the workflow should
be deployed with appropriate controls.

### Credential Management

-   Store credentials in n8n's credential manager.
-   Do not hardcode API keys, access tokens, or passwords.
-   Use separate credentials for development and production where
    possible.
-   Apply least-privilege permissions.

### Confirmation and Authorization

Consider confirmation steps before:

-   Sending emails
-   Creating calendar events
-   Updating documents
-   Writing sensitive information to spreadsheets
-   Performing actions on behalf of another user

### Data Protection

-   Avoid exposing confidential information in Telegram responses.
-   Restrict access to the Telegram bot.
-   Review what information is stored in memory, Sheets, and Docs.
-   Avoid logging credentials or sensitive user content unnecessarily.
-   Define retention and deletion procedures for stored data.

------------------------------------------------------------------------

## Error Handling Recommendations

Add handling for common failure scenarios:

-   Telegram message cannot be received
-   OpenAI request fails or times out
-   Memory session is unavailable
-   Gmail authentication fails
-   Google API quota is exceeded
-   Calendar event details are incomplete
-   Google Docs document cannot be found
-   SerpApi returns no results
-   A tool returns malformed or unexpected data

A useful error-handling strategy should:

1.  Capture the error.
2.  Log the relevant technical details securely.
3.  Return a clear user-facing message.
4.  Avoid exposing internal credentials or stack traces.
5.  Retry only when the failure is temporary and safe to retry.

------------------------------------------------------------------------

## Testing Plan

  -----------------------------------------------------------------------
  Test scenario                       Expected result
  ----------------------------------- -----------------------------------
  Basic Telegram message              AI Agent returns a response

  Follow-up question                  Memory provides relevant context

  Send Gmail message                  Email is sent only with valid
                                      details and authorization

  Append spreadsheet row              Correct row is added to the
                                      configured sheet

  Create calendar event               Event is created with correct date
                                      and time

  Create Google Doc                   New document is created with
                                      expected content

  Search Amazon                       SerpApi returns relevant search
                                      results

  Update Google Doc                   Correct document is updated without
                                      unintended overwrite

  Invalid request                     Agent asks for clarification

  API failure                         A controlled error response is
                                      returned

  Unauthorized action                 Workflow blocks or requests
                                      confirmation
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Suggested Enhancements

-   Add explicit confirmation nodes for sensitive operations.
-   Add user identity and access control.
-   Use separate workflows for high-risk actions.
-   Add structured logging and execution monitoring.
-   Store audit records in Google Sheets or a database.
-   Add rate limiting for Telegram users.
-   Add retries with backoff for temporary API failures.
-   Add document versioning or backup before updates.
-   Add tool-specific validation rules.
-   Add monitoring for API usage, latency, failures, and cost.
-   Add automated tests for critical workflow paths.
-   Add a fallback response when a tool is unavailable.

------------------------------------------------------------------------

## Metrics to Monitor

Recommended operational metrics include:

-   Number of Telegram messages received
-   AI Agent response latency
-   Tool invocation count by tool
-   Tool success and failure rate
-   Number of clarification requests
-   Gmail send success rate
-   Google Sheets append success rate
-   Calendar event creation success rate
-   Google Docs creation and update success rate
-   SerpApi search success rate
-   OpenAI token usage and cost
-   User satisfaction and repeated failure patterns

------------------------------------------------------------------------

## Summary

This n8n workflow provides a Telegram-based AI productivity assistant.
The AI Agent receives messages, uses an OpenAI Chat Model and
conversation memory, and can invoke tools for Gmail, Google Sheets,
Google Calendar, Google Docs, Amazon search, and Telegram responses.

The design combines **conversational AI, workflow automation, Google
Workspace integration, external search, and messaging** in one
extensible workflow.
