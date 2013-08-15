---
layout: post
title: "Sending emails with Node.js and Amazon SES"
date: 2013-08-14 15:16
comments: true
categories: 
---
In a nutshell: SES is a reliable way to unload the burden of sending emails. Here's how to harness Amazon's Node.js SDK to send emails. The example below is a raw Node.js app, but should be easy enough to integrate with your framework of choice - e.g. Express / Geddy.

I've posted all the code in [an example on github](https://github.com/timstermatic/node-amazon-ses-example).


### Some prerequisites

1. An [Amazon AWS account](http://aws.amazon.com) with [SES](http://aws.amazon.com/ses/) enabled.
2. Your Amazon Key and Secret.
3. [Verfiy an email address at SES](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses.html).

First, install the Amazon SDK.

    npm install aws-sdk

Or you can add it as a dependency to your `package.json`:

    {
		"dependencies": {
			"aws-sdk": "latest"
		}
	}


Then create a config file to hold your amazon credentions - e.g. config.json:

    { "accessKeyId": "YOURAMAZONKEY", "secretAccessKey": "YOURAMAZONSECRET", "region": "us-west-2" }


This example is a single script, but it would work just fine integrated into an express route etc. There's an explanation below:

    // load aws sdk
	var aws = require('aws-sdk');

	// load aws config
	aws.config.loadFromPath('config.json');

	// load AWS SES
	var ses = new aws.SES({apiVersion: '2010-12-01'});

	// send to list
	var to = ['email@example.com']

	// this must relate to a verified SES account
	var from = 'emailc@example.com'


	// this sends the email
	// @todo - add HTML version
    ses.sendEmail( { 
       Source: from, 
       Destination: { ToAddresses: to },
       Message: {
           Subject:Source {
              Data: 'A Message To You Rudy'
           },
           Body: {
               Text: {
                   Data: 'Stop your messing around',
               }
            }
       }
    }
    , function(err, data) {
        if(err) throw err
            console.log('Email sent:');
            console.log(data)console;
     });

The ses.sendEmail passes a json string with the necessary parameters required by Amazon SES. This example sends a text message, but if you look over [the docs for the sendEmail call](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SES_20101201.html#sendEmail-property) you will a HTML version can be easily passed (along with a host of other parameters). 

The callback function accepts the defacto `err` argument for collecting any errors and above use the second parameter `data` to output the information the service sends back on a successful send.

### Where to go from here.

The next logical step in this script would be to read in a text file for the body so you can send templated emails. Look to [Node.js' `fs`](http://nodejs.org/api/fs.html) to achieve something like that.

You could also wrap this functionality in a module so you can reuse it across your app with a more succint syntax..






