# Snippets Simple Contact Center
This snippet will show you how to implement basic contact center functionality
## About Simple Contact Center
Easily create and demonstrate how to implement a basic contact center
## Getting Started
You will need a machine with Python installed, the SignalWire SDK, a provisioned SignalWire phone number, and optionally Docker if you decide to run it in a container.

For this demo we will be using Python, but more languages may become available.
- [x] Python
- [x] SignalWire SDK
- [x] SignalWire Phone Number
- [x] Docker (Optional)
----
## Running Simple Contact Center - How It Works
We are going to build off of our dynamic IVR menu snippet, and enhance it with queues, conferences, and magic.

## Methods and Endpoints

```
Endpoint: /get_menu
Methods: GET OR POST
Requests generate a menu, it will default to main menu if no menu is specified.  The get_menu, will look for dtmf entries to select action for routing and moving along a menu tree.
```
```
Endpoint: /get_survey
Methods: GET OR POST
Requests generate a survey.
```
```
Endpoint: /conference_event
Methods: GET OR POST
Accepts conference events from GET or POST
```
```
Endpoint: /wait_music_queue
Methods: GET OR POST
Generates wait music, and queue stat updates
```
```
Endpoint: /enter_queue
Methods: GET OR POST
Handles entering a queue
```
```
Endpoint: /enqueue_event
Methods: GET OR POST
Accepts enqueue events from GET or POST
```
```
Endpoint: /inbound_voice
Methods: GET OR POST
Web entry for inbound voice interactions.
```
```
Endpoint: /inbound_sms
Methods: GET OR POST
Web entry for inbound text interactions.
```
```
Endpoint: /connect_agent
Methods: GET OR POST
Connects the agent to the conference with caller
```
```
Endpoint: /connect_caller
Methods: GET OR POST
Connects the caller to the conference with agent
```
```
Endpoint: /voice_entry
Methods: GET OR POST
The voice application entry point
```
```
Endpoint: /voice_event
Methods: GET OR POST
Handles voice status events
```
```
Endpoint: /get_voicemail
Methods: GET OR POST
Mock voicemail rendering for demo
```
```
Endpoint: /make_connection
Methods: GET OR POST
We are assuming an agent is ready for this snippet, by hitting this url it will connect the caller and agent.
```
```
Endpoint: /api/logs/{Log Resource Name}
Methods: GET
Returns JSON of entries, for reporting and front-end web.
```

## Setup Your Configuration
1. Edit the config.json file

This example file has endpoints/groups that will contain list of agents. 
Each menu contains a index, which is equal to the key-press on the menu, a verbiage, and an action
```javascript
{
  "settings": {
    "name": "Kevin's Contact Center",
    "outboundPhoneNumber": "--redacted--",
    "hostname": "http://--redacted--",
    "mainMenu": "main",
    "agentHuntMode": "Longest_Idle",
    "acceptedChannels": [ "voice", "sip" ],
    "enableAnnouncment": true,
    "enableExitSurvey": false,
    "enableCallback": false,
    "enableInboundText": false,
    "enableTextSummary": false,
    "enableQueueStatsMessage": true,
    "enableExitMessage": false,
    "enableWaitingMusic": true,
    "enableWaitingAds": true,
    "enableWrapUpUrl": false,
    "wrapUpUrl": "/some/endpoint/to/handle/post/call/stuff",
    "wrapUpUrlMethod": "POST",
    "waitingMusics": [ "https://sinergyds.blob.core.windows.net/signalwire/popcorn.mp3" ],
    "waitingAds": [
      "https://sinergyds.blob.core.windows.net/signalwire/ad_sample_1.mp3",
      "https://sinergyds.blob.core.windows.net/signalwire/ad_sample_2.mp3",
      "https://sinergyds.blob.core.windows.net/signalwire/ad_sample_3.mp3",
      "https://sinergyds.blob.core.windows.net/signalwire/ad_sample_4.mp3"
    ],
    "textToSpeech": {
      "voice": "Polly.Matthew",
      "language": "en-US"
    },
    "agents": {
      "0": {
        "name": "Kevin G.",
        "channels": {
          "sip": { "endpoint": "sip:--redacted--@--redacted--" },
          "voice": { "endpoint": "--redacted--" },
          "text": { "endpoint": "/some/url/to/process/this" }
        },
        "channelsEnabled": [ "voice", "sip" ],
        "roles": [],
        "skills": [
          "english",
          {
            "desirability": 1,
            "proficiency": 1
          },
          "spanish",
          {
            "desirability": 0,
            "proficiency": 0
          }
        ]
      },
      "1": {
        "name": "Rick Sanchez",
        "channels": {
          "voice": { "endpoint": "--redacted--" }
        },
        "channelsEnabled": [],
        "roles": [],
        "skills": [
          "english",
          {
            "desirability": 1,
            "proficiency": 1
          },
          "spanish",
          {
            "desirability": 0,
            "proficiency": 0
          }
        ]
      },
      "2": {
        "name": "Marty McFly",
        "channels": {
          "voice": { "endpoint": "--redacted--" }
        },
        "channelsEnabled": [],
        "roles": [],
        "skills": [
          "english",
          {
            "desirability": 1,
            "proficiency": 1
          },
          "spanish",
          {
            "desirability": 1,
            "proficiency": 1
          }
        ]
      }
    },
    "queues": {
      "salesPartners": { "queueSid": "--redacted--" },
      "salesSupport": { "queueSid": "--redacted--" },
      "techSupport": { "queueSid": "--redacted--" }
    },
    "messages": {
      "queueStatsMessage": "You are position ... %QueuePosition% ... of ...%CurrentQueueSize% ... Callers with an average wait time of ...%AvgQueueTime% ... seconds. Thank you for holding.",
      "entryMessage": "Thank You For Calling Kevin's Excellent Call Center!!",
      "exitMessage": "Thank You For Calling! The real O Gs.. Original Geeks!  Remember you can reach us on the web at signalwire dot com"
    },
    "menus": {
      "main": {
        "1": {
          "verbiage": "For Sales, Press 1",
          "action": "/get_menu?menu=sales"
        },
        "2": {
          "verbiage": "For Tech Support, Press 2",
          "action": "/get_menu?menu=tech"
        }
      },
      "sales": {
        "1": {
          "verbiage": "For Partners, Press 1",
          "action": "/enter_queue?name=salesPartners"
        },
        "2": {
          "verbiage": "For Help With Your Purchase, Press 2",
          "action": "/enter_queue?name=salesSupport"
        }
      },
      "tech": {
        "1": {
          "verbiage": "For issues with your internet, press 1",
          "action": "/enter_queue?name=techSupport"
        },
        "2": {
          "verbiage": "For issues with your cell phone, press 2",
          "action": "/get_voicemail?group=mobileSupport"
        }
      }
    }
  },
  "signalwire": {
    "space": "",
    "project": "", 
    "token":""
  },
  "mailgun": {
    "domain": "",
    "token": ""
  }
}
```
## Setup Your Enviroment File
This snippets configuration is accomplished via config.json, no enviroment file necessary.

## Build and Run on Docker
Lets get started!
1. Use our pre-built image from Docker Hub 
```
For Python:
docker pull signalwire/snippets-simple-contact-center:python
```
(or build your own image)

1. Build your image
```
docker build -t snippets-simple-contact-center .
```
2. Run your image
```
docker run --publish 5000:5000 snippets-simple-contact-center
```
3. The application will run on port 5000

## Build and Run Natively
For Python
```
1. Edit config.json, to build out your contact center.
2. From command line run, python3 app.py
```

----
# More Documentation
You can find more documentation on LaML, Relay, and all Signalwire APIs at:
- [SignalWire Python SDK](https://github.com/signalwire/signalwire-python)
- [SignalWire API Docs](https://docs.signalwire.com)
- [SignalWire Github](https://gituhb.com/signalwire)
- [Docker - Getting Started](https://docs.docker.com/get-started/)
- [Python - Gettings Started](https://docs.python.org/3/using/index.html)

# Support
If you have any issues or want to engage further about this Signal, please [open an issue on this repo](../../issues) or join our fantastic [Slack community](https://signalwire.community) and chat with others in the SignalWire community!

If you need assistance or support with your SignalWire services please file a support ticket from your Dashboard. 

