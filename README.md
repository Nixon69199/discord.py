# discord.py
discord bot 
import discord
from discord import client
from discord import member
from discord import user
from discord import message 
from discord.ext import commands
from discord.flags import SystemChannelFlags



bot = commands.Bot(command_prefix="+",case_insensitive=True)

@bot.command()
async def hi(ctx):
    await ctx.send(f"hello {ctx.message.author}")

@bot.command(name='server')
async def fetchServerInfo(context):
	guild = context.guild

@bot.command(
  help="allows you to type something u want" 
)  
async def say(ctx, *args):
	response = ""

	for arg in args:
		response = response + " " + arg

	await ctx.channel.send(response)


@bot.event
async def on_ready() :
    await bot.change_presence(status = discord.Status.online, activity = discord.Game("Listening to +help"))
    print("I am online")

@bot.command()
async def ping(ctx) :
    await ctx.send(f"🏓 Pong with {str(round(client.latency, 2))}")

@bot.command(name="whoami")
async def whoami(ctx) :
    await ctx.send(f"You are {ctx.message.author.name}")

@bot.command()
async def clear(ctx, amount=3) :
    await ctx.channel.purge(limit=amount)

@bot.command(
	help="Uses come crazy logic to determine if noob is actually the correct value or not.",
	brief="CALLS A PRo."
)
async def PRO(ctx):
	await ctx.channel.send("UR PRO XD")




@bot.command(
	help="Looks like you need some help.",
	brief="Prints the list of values back to the channel."
)
async def print(ctx, *args):
	response = ""

	for arg in args:
		response = response + " " + arg
	await ctx.channel.send(response)


intents = discord.Intents.default()
client = discord.Client(intents=intents)



@bot.command()
@commands.has_permissions(manage_guild=True)
async def mute(ctx, member: discord.Member = None):
  
  guild = ctx.guild
  user = member

  if member == None:
    await ctx.send(f'**{ctx.message.author},** please mention somebody to mute.')
    return 

  if member == ctx.message.author:
    await ctx.send(f'**{ctx.message.author},** you cannot mute yourself, silly.')
    return 
  
  for role in guild.roles:
    if role.name == "Muted":
      if role in user.roles:
                await ctx.send("**{}** is already muted.".format(user))
                return

  embedcheck=discord.Embed(title="Mute", colour=0xFFD166, description=f'Are you sure you want to mute **{user}?**')
  
  embeddone=discord.Embed(title="Muted", colour=0x06D6A0,description=f'The mute has been done. **{user}** cannot talk in any channels anymore.')

  embedfail=discord.Embed(title="Not Muted",colour=0xEF476F,description=f'The mute did not carry out as I did not receive a reaction in time.')

  msg = await ctx.send(embed=embedcheck)
  await msg.add_reaction('✅')
  await msg.add_reaction('❌')

  try:
      def check(rctn, user):
        return user.id == ctx.author.id and str(rctn) == '✅'
      reaction, user = await bot.wait_for('reaction_add', timeout=60.0, check=check)
  except TimeoutError:
      await msg.edit(embed=embedfail)
  else:
      for role in guild.roles:
        if role.name == "Muted":
            await member.add_roles(role)
            await msg.edit(embed=embeddone)


@bot.command(name='create-channel')
@commands.has_role('Owner 🌛')
async def create_channel(ctx, channel_name='#GENRAL CHAT'):
    guild = ctx.guild
    existing_channel = discord.utils.get(guild.channels, name=channel_name)
    if not existing_channel:
        print(f'Creating a new channel: {channel_name}')
        await guild.create_text_channel(channel_name)

@bot.event
async def on_message(message):
   if 'https://' in message.content:
      await message.delete()
      await message.channel.send(f"{message.author.mention} Don't send links!")
   else:
      await bot.process_commands(message)


bot.run('ODQxOTAzNzQ3NjMzNzc0NjUy.YJth9w.t4KU99anObWh96iS-VEe-8MPkR4')    



