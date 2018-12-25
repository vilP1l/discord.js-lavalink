[![Discord](https://discordapp.com/api/guilds/412180910587379712/embed.png)](https://discord.gg/QJnGhnn)

# discord.js-lavalink
A Discord.js lavalink client that works with the newest master / internal sharding version of discord.js

## Documentation
[**mrjacz.github.io/discord.js-lavalink**](https://mrjacz.github.io/discord.js-lavalink/)

## Installation

```npm install https://github.com/vilP1l/discord.js-v12-lavalink```
## LavaLink configuration
Download from [the CI server](https://ci.fredboat.com/viewLog.html?buildId=lastSuccessful&buildTypeId=Lavalink_Build&tab=artifacts&guest=1)

Put an `application.yml` file in your working directory. [Example](https://github.com/Frederikam/Lavalink/blob/master/LavalinkServer/application.yml.example)

Run with `java -jar Lavalink.jar`

## The issue tracker is for issues only
If you're having a problem with the module contact us in the [**Discord Server**](https://discord.gg/QJnGhnn)

# Implementation
Start by creating a new `PlayerManager` passing an array of nodes and an object with `user` the client's user id and `shards` The total number of shards your bot is operating on.

```javascript
const { PlayerManager } = require("discord.js-lavalink");

const nodes = [
    { host: "localhost", port: 80, region: "asia", password: "youshallnotpass" }
];

const manager = new PlayerManager(client, nodes, {
    user: client.user.id, // Client id
    shards: shardCount // Total number of shards your bot is operating on
});
```
Resolving tracks using LavaLink REST API
```javascript
async function getSongs(string) {
    const res = await snekfetch.get(`http://localhost:2333/loadtracks?identifier=${string}`)
        .set("Authorization", "youshallnotpass")
        .catch(err => {
            console.error(err);
            return null;
        });
    if (!res) throw "There was an error, try again";
    if (!res.body.length) throw "No tracks found";
    return res.body;
}

getSongs("ytsearch:30 second song").then(songs => {
    // handle loading of the tracks somehow ¯\_(ツ)_/¯
});
```
Joining and Leaving channels
```javascript
// Join
manager.join({
    guild: guildId // Guild id
    channel: channelId // Channel id
    host: "localhost" // lavalink host, based on array of nodes
}).then(player => {
    player.play(track); // Track is a base64 string we get from Lavalink REST API

    player.once("error", error => console.error(error));
    player.once("end", data => {
        if (data.reason === "REPLACED") return; // Ignore REPLACED reason to prevent skip loops
        // Play next song
    });
});

// Leave voice channel and destory Player
manager.leave(guildId); // Player ID aka guild id
```

For a proper example look at [**Testing/app.js**](https://github.com/MrJacz/discord.js-lavalink/blob/master/testing/app.js)
