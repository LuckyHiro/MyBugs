# WaxWeaver Bugs
- Godot 4.2
- Custom built windows only plugin
- Free on steam & Itch io

- WaxWeaver is just a fun side project that I happen to really enjoy, this report isn't meant to cause harm, just show a slight issue that doesn't even really need fixing assuming you only play with people you trust.

## Weaved Packets (Custom Packet Injection)
Waxweaver's packet system has no validation on 8 out of their 11 types of packets, allowing bad actors to craft custom packets/spam packets to cheat in the game, and or harm the server/server host.

- **In Game Cheating:** This is a low risk exploit in terms of cheating, as the game's online feature only work with friends on steam, and the *main* effect of sending custom packets in a server will do things like spawn items or water. Certain packets though, will either only appear for the host like the dropitem packet, which is where the actual risk comes in with this exploit

- **Risk:** The real risk of this exploit comes from a bad actor crafting custom packets to over load the server. This can be done by either spamming a large amount of packets every frame and or crafting single packets that do a lot in game, like dropping a 1,000 of a single item using the dropitem packet. This starts to desync the game between the host and other players, while also adding a large amount of lag to a host, which won't directly crash the game, but can cause the host's computer to freeze up and or fully crash with the right packet setup.

- **FIX:** There are various ways to fix this, but a good option would be to add a rate limiting system of sorts that the server can accept in a given time frame, break down certain packet type like dropitem into smaller packets that focus on one thing, like drops from breaking tiles, and adding deeper auth systems to certain packet types like dropitem to ensure the player sending it actually has that item.
