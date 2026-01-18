+++
date = '2026-01-18T10:37:12-03:00'
draft = false
title = 'This week I built my own Discord Bot'
+++

So I had this idea with my friends from Discord the other day of making a custom Discord bot for our server and, since I've got a 
VPC in [Vultr](https://www.vultr.com/) and the bot itself barely consumes any memory to run, I decided to go and make it myself.

## Idea and API

I decided to use the discord.py API, since it is very well documented and has tons of examples. My ideas for this bot for now is fairly simple:

- Integrate my OpenAI API key to talk to it with a custom prompt to make it sound like Jarvis (yes, the one from Iron Man)
- React to messages with specific keywords with custom emojis from the server
- Count how many times @everyone were mentioned by one specific user (he is known for doing that a lot)
- Some slash commands, like:
  - Store and list funny quotes some members have said
  - List games that we have played and refunded (we do that a lot)

I implemented some other things along the way, but these were the ones that first came to my mind.

## Setup 

First I had to create my API key and set OAuth2 for my server at the [Dev Dashboard](https://discord.com/developers/). After that, set the both discord
and OpenAI keys in an `.env` file and install `discord.py` and OpenAI libs and we're good to go. Here we initialize everything:


```python
import discord
from dotenv import load_dotenv
from openai import OpenAI
import os

load_dotenv()

TOKEN = os.getenv("TOKEN")
OPENAI_KEY = os.getenv("OPEN_AI_KEY")

intents = discord.Intents.default()
intents.message_content = True
GUILD = discord.Object(id=GUILD_ID)

bot = commands.Bot(command_prefix="!", intents=intents, help_command=None)
openai_client = OpenAI(api_key=OPENAI_KEY)
```

The premise here is set your `intents`, which is basically what are the permissions of the bot, what it can do. I'm defaulting it here. Next
we create out guild object, which has my guild id on a constant set before. That's basically it. Now we are set to implementing our stuff.

## Slash commands

Let's do something simple, the add quote function I talked about earlier. We must use the decorator `@bot.tree.command` to add a slash command.
Then we add the command itself `add_quote` along with its description and the guild object.

```python
@bot.tree.command(
    name="add_quote", description="Adiciona uma citação de alguém", guild=GUILD
)
async def add_quote(
    interaction: discord.Interaction, member: discord.Member, quote: str
):
    with open("data/quotes.json", "r") as file:
        quotes = json.load(file)
    quotes.append(
        {
            "member_id": member.id,
            "member_name": member.display_name,
            "quote": quote,
            "date": datetime.now().strftime("%d/%m/%Y"),
        }
    )
    with open("data/quotes.json", "w") as file:
        json.dump(quotes, file)
    await interaction.response.send_message(
        f'Citação adicionada: "{quote}" - {member.display_name}'
    )

```

We then define the function (with async) that gets the member and the quote as input. I then opens a json file with all the quotes and add it. Here, I 
am using the `json` lib as well. 

Let's now implement some reactions to messages with certain keywords. First we set a json that we will call `KEYWORDS` with our set of keywords and respective emojis. Should look 
something like this:

```json
{
  "other": 123456789,
  "something": 1459574930294444124,
}
```

Now we implement the listener:

```python
@bot.event
async def on_message(message: Message):
    if message.author == bot.user:
        return

    content = message.content.lower()

    for keyword, emojis in KEYWORDS.items():
        if keyword in content or keyword in message.content:
            if not isinstance(emojis, list):
                emojis = [emojis]
            for emoji in emojis:
                if isinstance(emoji, int):
                    emoji = bot.get_emoji(emoji)
                if emoji:
                    await message.add_reaction(emoji)
```

This will listen to every message and react accordingly. We can also add here our LLM integration, which will be called when the bot has been 
mentioned. I defined a `SYSTEM_PROMPT` beforehand with some information about the server, its members and a custom funny Jarvis prompt.
```python
    if bot.user and bot.user.mentioned_in(message) and not message.mention_everyone:
        user_message = message.content.replace(f"<@{bot.user.id}>", "").strip()
        if user_message:
            try:
                response = openai_client.chat.completions.create(
                    model="gpt-4o-mini",
                    messages=[
                        {"role": "system", "content": SYSTEM_PROMPT},
                        {"role": "user", "content": user_message},
                    ],
                    max_tokens=300,
                )
                reply = response.choices[0].message.content
                finish_reason = response.choices[0].finish_reason
                print(f"Reply received: {reply}")
                print(f"Finish reason: {finish_reason}")
                print(f"Full response: {response.choices[0]}")

                if reply:
                    await message.reply(reply)
            except Exception as e:
                await message.reply("Desculpe, não consegui processar sua mensagem.")
                print(f"OpenAI error: {e}")

```

With that, I intend to implement some cooler stuff in the future with custom voice and stuff, but this is ok for now. Checkout the [repo](https://github.com/viniciusbregoli/discord-bot#) if you'd like. 
