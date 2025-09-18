---
layout: post
title:  Telegram | Push Notification | Bot
categories: [Telegram]
tags: [ecFlow, micromamba, WRF, MPAS, NWP, Bot]
author: wpsze
date: 2025-05-16 17:24:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/PzjJEJ8.png
banner_img: https://i.imgur.com/PzjJEJ8.png
---

# push notifications with a Telegram bot

To **set up push notifications with a Telegram bot**, you'll need to create a bot using `BotFather`, obtain its token, and then use the `Telegram Bot API` to send messages. 


The guide to set up push notifications with a Telegram bot:

1. **Create a Bot with BotFather**:
   - Open Telegram and search for `@BotFather`.
   - Start a conversation with BotFather chat and send `/newbot`.
   - Follow the prompts to name your bot and choose a unique username (must end with "_bot"). 
   - BotFather will provide you with an API token, which is your bot's unique identifier. 
   - get its **API token** (e.g., `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`).

2. **Get Your Chat ID**:
   - Message your bot (e.g., send `/start`).
   - Use the `getUpdates` API to find your chat ID:
     ```
     https://api.telegram.org/bot<YourBotToken>/getUpdates
     ```
     - Open this URL in a browser or use a tool like `wget` or `curl`. Look for `"chat":{"id":<YourChatID>` in the response.
     - `cat getUpdates | grep -i chat` to look for chat ID.

3. **Send Push Notifications**:
   - Use the `sendMessage` API to send notifications. Make a POST request or use a simple GET request like:
     ```
     https://api.telegram.org/bot{bot_token}/sendMessage?chat_id={chat_id}&text={notification_text}
     https://api.telegram.org/bot<YourBotToken>/sendMessage?chat_id=<YourChatID>&text=Hello,%20this%20is%20a%20notification!
     ```
   - You can test this in a browser, with `curl`, or in code.

4. **Implement in Code** (e.g., Python with `requests`): 
   - `conda install anaconda::requests`
   ```python
   import requests

   def send_notification(bot_token, chat_id, message):
       url = f"https://api.telegram.org/bot{bot_token}/sendMessage"
       params = {"chat_id": chat_id, "text": message}
       response = requests.get(url, params=params)
       return response.json()

   # Example usage
   token = "your_bot_token"
   chat_id = "your_chat_id"
   send_notification(token, chat_id, "Test notification!")
   ```

5. **Optional Enhancements**:
   - **Formatting**: Use `parse_mode=Markdown` or `HTML` in the API for styled messages.
     - You can also include other parameters like `parse_mode` for formatting (e.g., **Markdown**) and `disable_web_page_preview`
   - **Error Handling**: Check the API response for errors (e.g., `response.ok` in Python).
   - **Automation**: Integrate with your app or server to trigger notifications on events.

# Important Considerations:

- **Bot Token Security**:
  - **Keep your bot's API token safe and secure**, as anyone with the token can use it to send messages. 
- **Chat ID Management**:
  - If you're sending notifications to multiple users or groups, you'll need a way to store and manage their chat IDs. 
- **Error Handling**:
  - Implement error handling to gracefully handle issues like invalid chat IDs or network problems. 
- **Telegram Bot API Documentation**:
  - Refer to the official Telegram Bot API documentation for detailed information on available methods and parameters. 
  
# For sending Files

```sh
curl -v -F "chat_id=xxxxxxx" -F document=@/home/wpsze/mpas/Telegram-bot/test.pdf https://api.telegram.org/bot<TOKEN>/sendDocument
```
