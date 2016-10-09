---
date: 2016-03-02T10:18:00.000Z
title: Client Side JavaScript
description: >-
  This article shows you how to embed JS in an HTML document and goes through
  some other client-side JS examples.
published: true
category:
  - js
---


This article shows you how to embed JS in an HTML document and goes through some other client-side JS examples.


{% highlight html %}
<html>
<head>
<script src="library.js"> <!-- Include a library of JS code -->
</head>
<body>
<p>This is a paragraph of HTML markup</p>
<script>
// and this is some client-side JS code
// litteraly embedded within the HTML document
</script>
<p>More HTML markup</p>
</body>
</html>

<!-- The window object, is a techique for scripting the web browser -->
<script>
function monveon() {
	// Display a modal dialog box to ask the user a question
	var answer = confirm("Ready to move on?");
	// If answer "OK", make browser load new page
	if(aswer) window.location = "http://google.com";
};
// Run the functino defined 1 minute (60,000 millliseconds) from now.
setTimeout(moveon,60000);
</script>

<!-- This function demonstrate a number of basic document searching and mod techniques -->
<script>
// Display a message in a special debugging output section of the document.
// if the document does not contain such a section. create one.
function debug(msg) {
	// Find the debugging section of the document, looking at HTML id attribute
	var log = document.getElementById("debuglog");

	// If no element with the id exist, create one.
	if (!log) {
		log = document.createElement("div");	// Crete a new <div> element
		log.id = "debuglog";			// Set the HTML attribute on it
		log.innerHTML = "<h1>Debug Log</h1>";	// Define initial content
		document.boby.appendChild(log);		// Add it at the end
	}

	// Wrap the message in its own <pre> and append it to the log.
	var pre = document.createElement("pre");	// Create a <pre> tag
	var text = document.createTextNode(msg);	// Wrap msg in a text node
	pre.appendChild(text);				// Add text to the <pre>
	log.appendChild(pre);				// Add <pre> to the log
}
</script>
<!-- How to use JS woth CSS style that define the presentation -->
<script>
function hide(e, reflow) {				// Hide the element e
	if(reflow) {					// If 2nd argument is true
		e.style.display = "none";		// hide element and use its space
	}
	else {						// Otherwise
		e.style.display = "hidden";		// make invisible, but leave its space
	}
}

function highlight(e) {					// Highlight e by setting a CSS class
	// Simply define or append to the HTML class attribute.
	// This assumes that a CSS stylesheet already defines the "hilite" class.
	if (!e.className) e.className = "hilite";
	else e.className += " hilite";
}
</scritpt>

<!-- To define behavior with JS, we register events -->
<script src="debug.js"></script>
<script src="hide.js"></script>
<button onclick="hide(this,true); debug('hide button 1');">Hide1</button>
<button onclick="hide(this); debug('hide butto 2');">Hide 2</button>
}

<!-- Some more client-side JS that uses events -->
<script>
// The load event occurs when a document is fully loaded. Usually we need to wait for this event
// before we start runnung our JS code
window.onload = Function() {				// Run this function after the document loads
	// Find all <img> tags in the document
	var images = document.getElementsByTagName("img");

	// Loop through them, adding an event handler for click events to each so that clicking on
	// images hides it
	for(var i=0; i < image.length; i++) {
		var image = images[i];
		if(image.addEventListener)		// another way of register a handler
			image.addEventListener("click",hide,false);
		else					// For compatability with IE 8 and before
			image.attachEvent("onclick",hide);
	}

	// This is the event handler function registered above
	function hide(event) {
		// srcElement needed for IE8, target for Chrome and Firefox
		var target = event.target ? event.target : event.srcElement;
		target.style.visibility = "hidden";
	}
};

<!-- Example the use of JQuary library -->
<script>
function debug(msg) {
	var log = $("#debug");				// Find the element to display msg in
	if(log.length == 0) {				// If it doesn't exist yetm create it...
		log == $("<div id='debuglog'><h1>Debug Log</h1></div>");
		log.appendTo(document.body);		// and insert it at the end of the body
	}
	log.append($("<pre/>").text(msg));		// Wrap msg in <pre> and append to log.
}
</script>
}
{% endhighlight %}
