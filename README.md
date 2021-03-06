# broadlink-rm-http
A Web server to control IR & RF devices using Broadlink RM. Can be used for custom applications or with IFTTT to add Google Home, Alexa & HomeKit support (tutorial below).

[![npm version](https://badgen.net/npm/v/broadlink-rm-http)](https://npmjs.com/package/broadlink-rm-http)
[![npm downloads](https://badgen.net/npm/dm/broadlink-rm-http)](https://npmjs.com/package/broadlink-rm-http)

# Getting started

Install this package by running:
```
npm install broadlink-rm-http
```

Create a file named ```index.js``` and add the following code:
```js
"use strict";

const BroadlinkServer = require('broadlink-rm-http');
const commands = require('./commands');

const port = process.env.PORT || 1880;
const key = "YOUR_SECRET";

const rooms = [];
rooms["ROOM_NAME"] = {host:"MAC_OR_IP",groups:["GROUP_A", "GROUP_B"]};

let app = BroadlinkServer(commands, key, rooms);
app.listen(port);

console.log('Server running, go to http://localhost:' + port);
```

Create a file named ```commands.js``` and add:
```js
module.exports = [
{"command":"COMMAND_A","group":"GROUP_NAME","data":"IR_OR_RF_CODE"},
{"command":"COMMAND_B","group":"GROUP_NAME","data":"IR_OR_RF_CODE"}
];
```
Alternatively, you can move ```index.js``` and ```commands.js``` from ```node_modules/broadlink-rm-http/example``` to the main directory

# Adding devices

- Add your Broadlink RM devices to the Broadlink e-Control app
- Write down their Mac (recommended) or IP addresses (or copy them by running ```node index```) and create rooms in the ```index.js``` file using these informations
- Additionally, set a key/secret to prevent unwanted command executions (especially if you will open this to the Internet), but don't leave it blank

# Learning codes & making commands

- If not done already, start the server with ```node index``` and visit ```http://localhost:1880/learn/YOUR_SECRET/HOST``` replacing ```HOST``` with the Mac or IP address (not room !) of the device that will be used for learning codes
- Now, you have 15 seconds to press the desired button on your infrared or radio remote controller ; if you run out of time just refresh the page
- Once done add the result to your ```commands.js``` file and fill the placeholders accordingly

# Creating groups

- Groups allow you to restrict a set of commands to one or multiple rooms
- This is particulary useful if you have different sets of remotes used across multiple rooms
- The ```commands.js``` allows you to add commands to a group while the ```index.js``` allows you to bind rooms to one or more groups
- You can bypass this feature by setting groups according to room names or by making one single group (not recommended as you are more likely to make mistakes)

# Running commands & using IFTTT

## Executing commands
To run commands simply visit ```http://localhost:1880/execute/YOUR_SECRET/ROOM/COMMAND```

## Opening the server to the Internet:
- We will use [localtunnel](https://localtunnel.me) because it is quick and secure, but you can also use [ngrok](https://ngrok.com) or configure your modem to do so
- Install localtunnel by running ```npm install -g localtunnel``` and start it with ```lt --port 1880``` (if your server's port is ```1880```)
- You can now execute commands from the Internet via ```https://YOUR_SUBDOMAIN.localtunnel.me/execute/YOUR_SECRET/ROOM/COMMAND```
- It is also recommended to set a unique subdomain using ```lt --port 1880 --subdomain SOMETHING_UNIQUE``` to make sure it remains the same
- You can start localtunnel automatically [using its API](https://github.com/localtunnel/localtunnel#api)

## Setting up Google Assistant with IFTTT:
- Visit [IFTTT](https://ifttt.com) and create an account or log in, then go to [My Applets](https://ifttt.com/my_applets) and make a [New Applet](https://ifttt.com/create)
- Click on *+this*, search for *Google Assistant*, link your Google account and select *Say a simple phrase*
- Set *What do you want to say?* to a command like "Turn the TV on"
- Set *What do you want the Assistant to say in response?* to a response like "Turning the TV on"
- Set your language and press *Create trigger*
- Click on *+that*, search for *Webhooks*, connect and select *Make a web request*
- Fill *URL* with ```https://YOUR_SUBDOMAIN.localtunnel.me/execute/YOUR_SECRET/ROOM/COMMAND``` and press *Create action* (leave everything else as is)
- Disable notifications and press *Finish*, you can now try your new Google Assistant command !
- You can also make advanced commands with *Say a phrase with a number* or *with a text ingredient* to control your AC for example
