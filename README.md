# daily-health-discord-notify
A bash script that checks on the latest scrubs on zfs pools and reports them to Discord.

## Requirements
- Access to creating webhooks on the Discord server you reside in.
- A ZFS pool and access to the `zpool` command.

## Setup
This script is meant to be run with an `.env` file in the same directory. Within the `.env` file, you should set these variables:
- POOL_NAMES: A string of zpool names, separated by spaces. You can see the list with `zpool list`.
- ERROR_REPORT_WEBHOOK_URL: The webhook of the channel you wish to show errors on.
- DISK_HEALTH_WEBHOOK_URL: The webhook of the channel you want to publish all progress to.
- DEVICE_NAME: The identifying name of the device you're reporting from.
- DEVICE_DISCORD_ICON = Optional. A Discord emoji to identify this device.

You'll need to set up a webhook on the two channels you'll be reporting to (Edit channel > Integrations > View Webhooks > Add webhook). Copy the webhook url and plop the webhook for the channel that you'll be relying on all success and error messages to be spammed to into `DISK_HEALTH_WEBHOOK_URL`, meanwhile make another webhook to a channel that you would want only errors to be sent and paste into `ERROR_REPORT_WEBHOOK_URL`.

You can also customize the name and emoji of the device that will be reporting with `DEVICE_NAME` and `DEVICE_ICON`, this way it's easier to sort through multiple devices if they're all reporting to the same channel.

### Scheduling
Use `crontab` or whatever preferred scheduler. If you don't schedule scrubs, you should. There are many philosophies around how often, I do them bimonthly.

`sudo crontab -e` and add an entry for the scrub:
`0 8 1,15 * * /sbin/zpool scrub storage_pool` will schedule a scrub at 8AM every 1st and 15th of each month to scrub the zpool called "storage_pool".
`10 8 * * * /path/to/daily-health-discord-notify.sh` will run the notifier every morning at 8:10AM.

Of course the scrub only happens twice a month, but the added log helps me see if a server was down during a certain day.
