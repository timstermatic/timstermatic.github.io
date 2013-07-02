---
layout: post
title: "Resumable polling with jQuery"
date: 2013-07-02 12:48
comments: true
categories: 
---

Here's a very simplified version of a technique I've used recently on our startup video platform, _zumoflow_. We needed a way of updating our mongodb engagement table periodically, only when a video was being played. For this we needed to set off a periodic poll when video play started and pause the poll when the video play was paused or stopped.


The following code shows the skeleton for two simple events. One to start and one to stop the poll:

	// initialize a timer variable
	// we need to use this to reference stopping the timeout

    var timer;
		
	// this function starts the poll and binds it to a variable called timer
	// There's a configurable 2000 ms timeout on calling the function

	function doPoll() {
		$.post('http://localhost', data, function(data) {
				timer = setTimeout(doPoll, 2000);
		})
    }

Then we can attach the event to start the poll to any element:

    $('.start').click( function() {
		doPoll();
		return false;
	});
    
Finally we can attach the stop event which clears the timeout using the JavaScript `clearTimeout` function:

	$('.stop').click( function() {
		clearTimeout(timer);
		return false;
	});


References:

1. [jQuery $.post documentation](http://api.jquery.com/jQuery.post/)
2. [window.setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/window.setTimeout)
3. [window.clearTimeout](https://developer.mozilla.org/en-US/docs/Web/API/window.clearTimeout)
