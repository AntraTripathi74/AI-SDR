# AI Sales Development Representative (SDR) Workflow

This repository contains an n8n workflow designed to automate key aspects of a Sales Development Representative's (SDR) tasks, from lead generation and qualification to personalized outreach. This workflow leverages various integrations, including Google Custom Search Engine (CSE), LinkedIn scraping (via RapidAPI), Google Sheets, and the Gemini 1.5 Flash LLM for intelligent lead qualification and company profiling, culminating in email outreach via Gmail.

## ✨ Features

* Automated Lead Generation: Searches for LinkedIn profiles based on defined criteria (e.g., "SaaS founder India").
* LinkedIn Profile Scraping: Extracts detailed information from LinkedIn profiles (Name, Title, Company, Industry, Location, etc.) using a RapidAPI integration.
* Google Sheets Integration:
    * Stores scraped LinkedIn profile URLs and basic details.
    * Manages lead information (Name, Title, Company, Contact details).
    * Stores and updates qualified leads in a dedicated sheet.
* LLM-Powered Lead Qualification: Utilizes Google's Gemini 1.5 Flash to automatically qualify leads based on predefined industry and location criteria, categorizes leads as "Qualified," "Unqualified," or "Uncertain" with a reason.
* Company Profiling with LLM: Uses Gemini 1.5 Flash to research and generate a concise 1-2 line description about a company's business, industry, or recent initiatives, demonstrating research for personalized outreach.
* Automated Email Outreach: Sends personalized emails via Gmail to qualified leads.

## ⚙️ Workflow Overview

The `sdr.json` file defines the following sequence of operations:

* Manual Trigger: Initiates the workflow.
* Google Custom Search Engine (CSE): Initiates a search for LinkedIn profiles (e.g., "SaaS founder India site:linkedin.com/in/") and manages pagination.
* Extract LinkedIn URLs: Parses search results to extract valid LinkedIn profile URLs.
* Save Contact Information (Google Sheets): Appends extracted LinkedIn URLs and titles to a Google Sheet.
* Read LinkedIn URLs: Reads existing LinkedIn URLs from a Google Sheet to process them.
* Split Out LinkedIn URLs: Prepares individual LinkedIn URLs for scraping.
* HTTP Request (Scrap LinkedIn Profiles): Calls a RapidAPI endpoint to scrape detailed data from LinkedIn profiles.
* Extract Profile Data: Processes the scraped LinkedIn data, extracting relevant fields like full name, company, title, industry, and generating dummy phone numbers and emails (as real contact details are typically not available via public APIs).
* Lead Information (Google Sheets): Appends or updates detailed lead information into a Google Sheet.
* Google Sheets (Read Lead Info): Reads lead information for qualification.
* Split Out Lead Data: Prepares individual lead records for LLM processing.
* HTTP Request (Gemini for Lead Qualification): Sends lead data to Gemini 1.5 Flash for qualification based on predefined rules (industry, location).
* Extract Qualification Data: Parses the JSON output from the Gemini LLM, extracting qualification status and reason.
* Qualified Leads (Google Sheets): Appends or updates the qualified leads sheet with the qualification status and reason.
* HTTP Request (Company Profiling): Sends qualified lead data to Gemini 1.5 Flash to generate a personalized company insight.
* Extract Company Info: Parses the output from the company profiling LLM.
* Append sheet with company info (Google Sheets): Updates the qualified leads sheet with the generated company insight.
* Updated Qualified Leads (Google Sheets): Reads the final qualified leads data.
* Gmail: Sends a personalized email to the qualified lead using the extracted information and company insight.

## 🛠️ Setup and Configuration

This workflow is designed to run on n8n. To use it, you will need to:

1.  Import the Workflow:
    * Open your n8n instance.
    * Go to "Workflows" and click "New".
    * Click the "Import from JSON" button and upload the `AI_SDR.json` file.
2.  Configure Credentials:
    * Google Sheets: You will need to set up OAuth2 credentials for Google Sheets to allow n8n to read from and write to your specified Google Sheets. (The workflow uses `Google Sheets account 2`).
    * Google Custom Search API Key & CX:
        * Obtain a Google Custom Search API key and a Custom Search Engine ID (CX).
        * Update the `HTTP Request (for searching profiles)` node with your `key` and `cx` in the URL: `https://www.googleapis.com/customsearch/v1?q=SaaS+founder+India+site%3Alinkedin.com%2Fin%2F&key=YOUR_API_KEY&cx=YOUR_CX_ID&num=2&start={{$json["start"]}}`
    * RapidAPI Key:
        * Obtain a RapidAPI key for the LinkedIn data scraper used (`li-data-scraper.p.rapidapi.com`).
        * Update the `HTTP Request (scrap linkedin profiles)` node with your `X-RapidAPI-Key` in the headers.
    * Gemini API Key:
        * You'll need a Google Cloud project with access to the Gemini 1.5 Flash model and an API key.
        * Update the `HTTP Request (Gemini for lead qualification)` and `HTTP Request (Company profiling)` nodes with your `key` in the URL: `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=YOUR_GEMINI_API_KEY`
    * Gmail: Set up OAuth2 credentials for Gmail to allow n8n to send emails. (The workflow uses `Gmail account`).
3.  Update Google Sheet IDs:
    * Contact Information: Update the `documentId` in the `Contact Information` and `Read` Google Sheets nodes with the ID of your Google Sheet for storing LinkedIn URLs.
    * Lead Information: Update the `documentId` in the `Lead Information` and `Google Sheets3` nodes with the ID of your Google Sheet for storing general lead data.
    * Qualified Leads: Update the `documentId` in the `Qualified leads`, `Append sheet with company info`, and `Updated qualified leads` nodes with the ID of your Google Sheet for storing qualified leads.
4.  Customize Search Query: Modify the `q` parameter in the `HTTP Request (for searching profiles)` node to change your lead search criteria (e.g., `q=software+developer+Germany+site%3Alinkedin.com%2Fin%2F`).
5.  Adjust Qualification Criteria: Review and modify the `base_prompt` within the `HTTP Request (Gemini for lead qualification)` node to fit your specific lead qualification rules (Location Check, Industry Check, etc.).
6.  Customize Email Template: Modify the `message` and `subject` in the `Gmail` node for your outreach emails.

## ⚠️ Important Notes

* API Keys and Security: Never commit your actual API keys directly into your repository. Use environment variables or n8n's credential management system. The provided JSON currently has placeholder keys.
* LinkedIn Scraping: Be aware of LinkedIn's terms of service regarding scraping. Use such tools responsibly and ethically. RapidAPI services might have their own usage policies.
* Dummy Data: The `Function for extracting profile data` node generates random phone numbers and dummy emails as actual contact details are typically not available from public LinkedIn scraping. For real contact data, you would need integrations with dedicated B2B data providers.
* Scalability: For very large-scale operations, consider n8n's queuing options or dedicated lead enrichment platforms.

## 🤝 Contributing

Feel free to fork this repository, open issues for suggestions or bugs, and submit pull requests to enhance the workflow!

## 📄 License

This project is licensed under the MIT License.
