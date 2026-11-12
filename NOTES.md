# Author. Rod
# Date. October 2026, November 2026
# Filename
# Description. Server ran in a Raspberry Pi 5, using Nginx with C and Rust.

---- First Session ----
**To do** Organise files, and prepare the logical structure of the server, and general functions of it. 

---- Second Session ----
** Finished the To do of the First Session **
** SSH installed already **

** To do ** So Nginx may communicate with my code in C, the quickest form is using FasctCGI, or make my C
code listens within a local port, say 8080, native HTTP port. 
External peticions are done in port 80/443
If the petition is a static file, html, css.. you get the idea, we send it to `public_html`.
If its a dynamic petition example /api/data, Nginx sends (Proxy) to my binary on C being ran on segundo plano 
`To do Third session`. Organise much of the files, and prepare the server side, with Nginx. Also prepare the 
'base' for the deploy. 
