# HomeLab MkI
#
# So my homelab is a humble one for the time being, the idea is to have a a microservice server within a raspberry pi for the time being until I can get an upgrade.
#
# Such home lab will be put within a raspberry pi 5 8GB  with a 128GB SD. It will be divided into respectfully to the servers, as microservices instead of a whole robust thing. As such, and from my little experience
# I will be using nginx, thanks to perfomance / consumption ratio compared to apache, also I will be trying to make a suite of light machine, instead of heavy and robust, as due to the limitation by using a raspberry pi.
#
# In the future, I will try to buy some mini pc or even a machine meant for a server, but for now this will do. Now for the interesting stuff. I will be using a mix of C and Rust for Networking. I do not control much of Rust,
# I rather be honest about it; but I am quite good at C, so will try to do my best with my skill. Also, I will be using python, and shell to automatise as efficient as possible. I will use for the database SQLite, and then
# PostgreSQL. So! The organising of the server in teams of the build up:
#
# I will borrow a Web-Server, after that I will make up my app server with Rust, which I have an engine done, and will upload my few projects, I am not gonna lie, I am not the best, but intentions are good D: 
# For (3) and (4) I will Borrow the SQLite build, and then do the PosgreSQL, as I have no clue of SQLite but I do about PostgreSQL; and then will build a Socket Server with potentially C but we'll see about it.
# (5) and (6). Both borrowed, as I am learning. Storage and Cache with Redis, and Message and event - Mosquitto an MQTT broker. 
# (7) and (8) Auth and security - will build it up with Argon2id plus JWT, and the metris and monitoring - Prometheus, Granfana or Netdata to start borrowed.
#
# As I am learning, the project will have a lot of borrowed stuff, but my aim is to make it all build up by myself. I shall see how that goes!

