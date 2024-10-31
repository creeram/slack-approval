# slack-approval

custom action to send approval request to Slack


## trigger
<image src="img/approve_at_reply.png" width="300" height="300" />

- When action is triggered, Post or Update in Slack, a reply message appears simultaneously with "Approve" and "Reject" buttons
## approve
<image src="img/approved.png" width="300" height="300" />

- Clicking on "Approve" will execute next steps

## reject and cancel
<image src="img/rejected.png" width="300" height="300" />
<image src="img/canceled.png" width="300" height="50" />

- Clicking on "Reject" or "cancel workflow" will cause workflow to fail and update reply message





# How To Use

- First, create a Slack App and install in your workspace.
- Second. set the `App Manifest`
```json
{
    "display_information": {
        "name": "ApprveApp"
    },
    "features": {
        "bot_user": {
            "display_name": "ApproveApp",
            "always_online": false
        }
    },
    "oauth_config": {
        "scopes": {
            "bot": [
                "app_mentions:read",
                "channels:join",
                "chat:write",
                "users:read"
            ]
        }
    },
    "settings": {
        "interactivity": {
            "is_enabled": true
        },
        "org_deploy_enabled": false,
        "socket_mode_enabled": true,
        "token_rotation_enabled": false
    }
}
```

- and set workflow step
```yaml
jobs:
  approval:
    runs-on: ubuntu-latest
    steps:
      - name: send approval
        uses: tigerwest/slack-approval@v1.0.0
        env:
          SLACK_APP_TOKEN: ${{ secrets.SLACK_APP_TOKEN }}
          SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
          SLACK_SIGNING_SECRET: ${{ secrets.SLACK_SIGNING_SECRET }}
          SLACK_CHANNEL_ID: ${{ secrets.SLACK_CHANNEL_ID }}
        timeout-minutes: 10
        with:
            approvers: user1,user2
            minimumApprovalCount: 2
            baseMessageBlocks: |
              []
            successMessageBlocks: |
              []
            failMessageBlocks: |
              []
```

## Set environment variables

  - `SLACK_APP_TOKEN`

    - App-level tokens on `Basic Information page`. (starting with `xapp-` )

  - `SLACK_BOT_TOKEN`

    - Bot-level tokens on `OAuth & Permissions page`. (starting with `xoxb-` )

  - `SLACK_SIGNING_SECRET`

    - Signing Secret on `Basic Information page`.

  - `SLACK_CHANNEL_ID`

    - Channel ID for which you want to send approval.

## Set Inputs

  - `baseMessageTs`
    - If provided, updates the target message. If not provided, creates a new message
    - Optional

  - `approvers`
    - A comma-separated list of approvers' slack user ids
    - Required

  - `minimumApprovalCount`
    - The minimum number of approvals required
    - Optional (default: "1")

  - `baseMessageBlocks`
    - The base message blocks to display. If not set, will use default message from README. To customize, provide Slack message blocks JSON
    - Optional (default: "[]")

  - `successMessageBlocks`
    - The message body indicating approval is success. If not set, will use baseMessageBlocks.
    - Optional (default: "[]")

  - `failMessageBlocks`
    - The message body indicating approval is fail. If not set, will use baseMessageBlocks.
    - Optional (default: "[]")


## outputs

- `mainMessageTs`
  - Timestamp of the main message sent to Slack

- `replyMessageTs`
  - Timestamp of the reply message sent to Slack 

