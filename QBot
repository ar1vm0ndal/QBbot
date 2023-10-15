import discord
from discord import Embed
import asyncio
from discord.ext import commands
import random
import json

channel = *****
client = discord.Client()

q_num = -2
q_asked = []
buzz = False
players = {}
redList = []
blueList = []
teamMode = False
teamEmbed = None

# Reading the JSON file - JavaScript Object
with open('quizdb-20210514024409.json', encoding="utf8") as f:
    d = json.load(f)

def getTossup(q_num):
    num = d['data']['tossups']
    text = num[q_num]['text']
    return text

def getCat(q_num):
    num = d['data']['tossups']
    return num[q_num]['category']['name']

def getTourney(q_num):
    num = d['data']['tossups']
    return num[q_num]['tournament']['name']

def getTAnswer(q_num):
    num = d['data']['tossups']
    str = num[q_num]['answer']
    index = str.find('&')

    ans = str[0:index]

    bIndex = str.find('[')
    if bIndex != -1 and bIndex < index:
        ans = str[0:bIndex]

    return ans

def getBlueTeam():
    blueTeam = None
    if len(blueList) > 0:
        blueTeam = ""
    for i in range(0, len(blueList)):
        blueTeam = blueTeam + str(i+1) + ". " + blueList[i] + "\n"
    return blueTeam

def getRedTeam():
    redTeam = None
    if len(redList) > 0:
        redTeam = ""
    for i in range(0, len(redList)):
        redTeam = redTeam + str(i+1) + ". " + redList[i] + "\n"
    return redTeam

@client.event
async def on_ready():
    await client.get_channel(*****).send("Online")

@client.event
async def on_message(message):
    global q_num
    global buzz
    global players
    global teamMode
    global teamEmbed
    channel = message.channel.id
    idcall = "<@" + str(message.author.id) + ">"

    if message.author.id == ****:
        pass

    elif message.content == "?teams":
        teamMode = True

        embed = Embed(title="MercuryOne", description="Use *?join* to join!\nUse *?start* to start the game!",
                      colour=0x9932CC)
        fields = [("Blue Team", getBlueTeam(), True),
                  ("Red Team", getRedTeam(), True)
                 ]

        for name, value, inline in fields:
            embed.add_field(name=name, value=value, inline=inline)
        teamEmbed = await message.channel.send(embed=embed)

    elif message.content.startswith('?join'):

        if teamMode == True:
            desiredTeam = message.content.replace("?join", "")
            if desiredTeam.upper().strip() == "BLUE":
                blueList.append(idcall)
                if idcall in redList:
                    redList.remove(idcall)
            elif desiredTeam.upper().strip() == "RED":
                redList.append(idcall)
                if idcall in blueList:
                    blueList.remove(idcall)
            embed = Embed(title="MercuryOne", description="Use *?join* to join!\nUse *?start* to start the game!",
                          colour=0x9932CC)
            fields = [("Blue Team", getBlueTeam(), True),
                      ("Red Team", getRedTeam(), True)
                      ]

            for name, value, inline in fields:
                embed.add_field(name=name, value=value, inline=inline)
            await teamEmbed.edit(embed=embed)

        if idcall not in players.keys():
            players[idcall] = [0, 0]
            await message.channel.send(idcall + ", you joined the game!")

        else:
            await message.channel.send("You have already joined!")

    elif message.content == "?end":
        teamMode = False

    elif message.content == '?q':

        while True:
            question_number = random.randrange(0, 750, 1)
            q_num = question_number
            if q_num not in q_asked:
                break
        q_asked.append(q_num)

        sent = ""
        tossup = getTossup(q_num)
        initialIndex = 0
        while sent[:-15] != tossup[:-15] and buzz == False:

            embed = Embed(title="MercuryOne", description="R", colour=0x2c9c91)
            fields = [("Type", "Tossup-10 Pts", True),
                      ("Category", getCat(q_num), True),
                      ("Tournament", getTourney(q_num), True),
                      ("Question", sent + tossup[initialIndex:tossup.find(".", initialIndex)] + ".", False)]

            for name, value, inline in fields:
                embed.add_field(name=name, value=value, inline=inline)
            if initialIndex == 0:
                msg = await message.channel.send(embed=embed)
            else:
                await msg.edit(embed=embed)

            sent = sent + tossup[initialIndex:tossup.find(".", initialIndex)] + "."
            initialIndex = tossup.find(".", initialIndex) + 1

            await asyncio.sleep(5)

    elif message.content == "?a":
        await message.channel.send(getTAnswer(q_num))

    elif message.content == "buzz" and buzz == False and (idcall in redList or idcall in blueList or idcall in players.keys()):
        buzz = True
        await message.channel.send("Answer?")

    elif buzz == True and ((idcall in redList) or (idcall in blueList) or (idcall in players.keys())):

        if message.content.upper().strip() == getTAnswer(q_num).upper().strip():
            await message.channel.send("You are motherfucking right")

            buzz = False
        else:
            await message.channel.send("You are wrong")
            print(getTAnswer(q_num))
            buzz = False

    elif message.content == "test":
        msg = await client.get_channel(channel).send('tested')
        msg.edit('edited')

client.run('**********************************')
