---
title: AWS Lambda - Pyhton - GMail API
date: 2020-05-18 18:56:52
categories: AWS
tags: [AWS, lambda, python, gmail API]
---

This post includes:
- use python to get labels from GMail API
- deploy python package to AWS Lambda
- setup a AWS gateway API to get labels

<!--more-->

# Get Labels from GMail API using Python

Reference: https://developers.google.com/gmail/api/quickstart/python

Create a working directory: `~/gmail-labels`.
Follow the reference for `credentials.json` and install library.
Copy and Edit the code sample to become: `~/gmail-labels/main.py`.

## main.py
```Python
import pickle
import os.path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request

# If modifying these scopes, delete the file token.pickle.
SCOPES = ['https://www.googleapis.com/auth/gmail.labels']

def get_creds():
    creds = None
    # The file token.pickle stores the user's access and refresh tokens, and is
    # created automatically when the authorization flow completes for the first time.
    if os.path.exists('token.pickle'):
        with open('token.pickle', 'rb') as token:
            creds = pickle.load(token)
    # If there are no (valid) credentials available, let the user log in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file('credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        # Save the credentials for the next run
        with open('token.pickle', 'wb') as token:
            pickle.dump(creds, token)
    return creds

def get_service():
    creds = get_creds()
    service = build('gmail', 'v1', credentials=creds)
    return service

def list(event, context):
    # Call the Gmail API
    service = get_service()
    results = service.users().labels().list(userId='me').execute()
    labels = results.get('labels', [])
    #
    # if not labels:
    #     print('No labels found.')
    # else:
    #     print('Labels:')
    #     for label in labels:
    #         print(label)
    # print("======= END of list =======")
    return labels

def get_unreads(event, context):
    service = get_service()
    labels = list(None,None)[0:5]
    labelToUnreadMessage = {}
    for label in labels:
        label_details = service.users().labels().get(userId='me', id=label['id']).execute();
        labelToUnreadMessage[label['id']] = label_details['messagesUnread']
        # print("label:", label_details)
    print('labelToUnreadMessage: ', labelToUnreadMessage)
    return labelToUnreadMessage

def get_unread(event, context):
    id = event['id']
    service = get_service()
    label_details = service.users().labels().get(userId='me', id=id).execute();
    result = {}
    result['id'] = id
    result['messagesUnread'] = label_details['messagesUnread']
    print('result: ', result)
    return result

# get_unread({'id':'INBOX'}, None)
```


# Deploy python package to AWS Lambda

Install dependencies in the working directory:
```shell
cd ~/gmail-labels
pip3 install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib -t .
```

in `~/gmail-labels`, select all files(55) and compress to a `Archive.zip` (6M).

## AWS Lambda Console
- upload `Archive.zip`
- change handler to : `main.get_unread`
- save

## test event
```
{
  "id": "INBOX"
}
```


# AWS Gateway API
- add resource
- add method
- in Method Request, change API key required to true
- in Integration Request, set mapping:  
  - When there are no templates defined (recommended)
  - application/json
    ```
    #set($inputRoot = $input.path('$'))
    {
      "id": "$input.params('id')"
    }
    ```
- test
- deploy API to test stage, get URL

## create a usage plan
API(test) <-> Usage Plan <-> API key(a customer)


# PostMan
GET: https://xxtqwmuo0i.execute-api.us-east-2.amazonaws.com/test/labels?id=INBOX

Put API key to header: `x-api-key`


# issues

## Read-only file system: 'token.pickle'
```
[Errno 30] Read-only file system: 'token.pickle'
with open('token.pickle', 'wb') as token
```
make change:
```Python
token_path = '/tmp/token.pickle'

# for offline use, token must put into tmp dir first
if not os.path.exists(token_path):
    copyfile('token.pickle',token_path)

# The file token.pickle stores the user's access and refresh tokens, and is
# created automatically when the authorization flow completes for the first time.
with open(token_path, 'rb') as token:
    creds = pickle.load(token)
```
