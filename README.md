_Please note that this is an SDK for webhooks integration, and_ **_not_** _the FormSG system._

# FormSG Python3 SDK

This SDK provides convenient utilities for verifying FormSG webhooks and decrypting submissions in JavaScript and Node.js.

## Installation
Install the package with

```bash
pip install formsg-sdk
```

## Usage
Quickstart usage with Flask:

```python
import os
import formsg
from flask import Flask, request

#get your FormSG's secret key from environment
FORM_SECRET_KEY = os.getenv('FORM_SECRET_KEY')

@app.route('/formsgtest',methods=['POST'])
def formsgtest():
    data = formsg.decrypt_responses(request, FORM_SECRET_KEY,securehttp=True)
    print(data)
	#display the decrypted data received
	
	#do your processing here
	for response in data['data']['decryptedContent']:
        print(response['question']+': '+response['answer'])
        
    return data

if __name__ == '__main__':
    app.run(debug=True)
```

### Webhook Authentication and Decrypting form responses

## End-to-end Encryption

FormSG uses _end-to-end encryption_ with _elliptic curve cryptography_ to protect submission data and ensure only intended recipients are able to view form submissions. As such, FormSG servers are unable to access the data.

The underlying cryptosystem is `x25519-xsalsa20-poly1305`. This codebase is the python implementation of the NaCl library. Official webhook SDK by the FormSG team only supports javascript.

### Format of Submission Response

| Key                    | Type                   | Description                                                                                              |
| ---------------------- | ---------------------- | -------------------------------------------------------------------------------------------------------- |
| formId                 | string                 | Unique form identifier.                                                                                  |
| submissionId           | string                 | Unique response identifier, displayed as 'Response ID' to form respondents                               |
| encryptedContent       | string                 | The encrypted submission in base64.                                                                      |
| created                | string                 | Creation timestamp.                                                                                      |
| attachmentDownloadUrls | Record<string, string> | (Optional) Records containing field IDs and URLs where encrypted uploaded attachments can be downloaded. |

### Format of Decrypted Submissions

`decrypt_responses(request, formSecretKey, securehttp=True)`
takes in a http request, formsecretkey (b64 encoded) and returns a dictionary with the following structure

<pre>
{
'data': 
    {
    'formId': str
    'submissionId': str
    'version': str
    'created': str
    'attachmentDownloadUrls': dict
    'decryptedContent': [   
                        {
                        '_id': str
                        'question': str
                        'answer': str
                        'fieldtype': str
                            },
                            {
                        '_id': str
                        'question': str
                        'answer': str
                        'fieldtype': str
                        },
                            ...
                        ]
    }
}
</pre>

### Signature verification

Under the hood, signatures are verified upon decryption, using steps found here - [Verifying Signatures Manually](https://github.com/opengovsg/formsg-javascript-sdk#verifying-signatures-manually)

### TODO
Attachment decryption.
