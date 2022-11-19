# Cronjob in Linux

## Generating Cronjobs
Head to [cronjob guru](https://crontab.guru/) to create the cronjob timing expression
In linux terminal, use the command `sudo crontab -e` to edit the cronjobs.
Add in the following to create a cronjob for every minute
```
HOME=/home/wq/Desktop/live_detection
\* \* \* \* \* /usr/bin/python3 /home/wq/Desktop/live_detection/run_me.py <argument 1> < argument 2>
```
