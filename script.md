# Welcome to Akamai Sandbox Online Editor


## What does this demo show you?
a) You can update the Akamai configurations within less than 10 seconds and test a new logic out.

b) You can put your Akamai configuration in front of any developer environment you have.

## Setup Instructions

### Step 1: Open up your preview website 
* click "cmd + shift + p" 
* type in "open url", then click "enter"
* type in "https://security-playground.akamai.tools/" 
Now you should see a browser preview window load up on the side. By default the page should give you a 503 error code


### Step 2: Open up Terminal 
* click on "Terminal" in the top navigation
* click on "New Terminal" 

### Step 3: Edit what origin you want to access
* click on config.json file that is open in this window. If you don't find it then you will need to click on file, click on open , navigate to /.akamaicli/cache/sandbox-cli/sandboxes/edge-demo-station/config.json
* Ensure that the port number for the sandbox client server info is set to 5050, the default is 99550 <-- we need to override it. 
* update the "to" field under OriginMappings to point to your origin. For example if you wish to point your sandbox to "www.apple.com" then update both "host" and "hostHeader" to "www.apple.com"
* click save using "cmd + S" 

### Step 4: Start the sandbox
* within the terminal window below enter "akamai sandbox start"

## Scenarios to show a customer

### Editing a local developer environment and Akamaizing it
We have an apache server running content fetched from the /travel-site folder. You can edit the contents of index.html while the sandbox is running to see how the local dev environment changes can be seen using via Sandbox
* Open index.html from under /travel-site folder
* Change something of the page like a header or a title and click save 
* refresh the page within preview to see the changes via Akamai sandbox

### Updating an Akamai rule tree and pushing it into a sandboxes
While a sandbox is running you can always update the rule tree within the sandbox. Below are the steps to do that
* Click on /akamai-property folder and select the 'rule-tree.json'
* Edit this file first (to be specific edit the outgoing response header to a different value) and then run the command "akamai sandbox update --rules ./akamai-property/rule-tree.json" 
* Once the Sandbox is updated you can go ahead and refresh the browser to see the implications of the new rule.

### Image Manager and Sandbox interaction
* Update the Origin mappings section under config.json to point to your desired website
* type in "akamai sandbox start" within the terminal 
* refresh the preview page to load up the site. NOTE, loading pages through the sandbox does take time and you will notice a yellow bar on top of the preview window while the window is trying to render the page. Be patient the page will eventually load up.
* You can also type in the URL https://security-playground.akamai.tools separately into a browser window and show Piez and another tools against the sandbox'd environment
* WHAT YOU SHOULD EXPECT : All the image will be grayscaled since we are apllying the staging policies of IM against your sandbox traffic.


## Architecture and Request Flow 

### Setup
* Akamai Sandbox CLI is running on port 5050 
* Nginx server is acting as a reverse proxy and router for "security-playground.akamai.tools" 
* * Requests to security-playground.akamai.tools is routed to Sandbox client running on port 5050
* Akamai Sandbox Client config.json file controls which origin the Sandbox needs to access, currently it's been configured to point to 127.0.0.1:5060 
* LOCAL DEV environment is running on top of Apache exposed on port 5060. 
* Online IDE running on port 5040

### Request flow when fetching content from local dev via Sandbox 
* Browser sends request to security-playground.akamai.tools which hits this a server in cloud 
* Nginx router in the cloud sees that the request is for security-playground.akamai.tools and routes the request to the port that the sandbox is running on port 5050
* Sandbox routes the request through the sandbox network and bring the request back into the server where the sandbox client routes the request to localhost:5060 (apache Server) 
* Response is served back to the browser
