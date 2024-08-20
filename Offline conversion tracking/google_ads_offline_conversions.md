# Instructions
Follow the steps below to setup the transaction ID and prevent duplicate conversions. You will need a [Google Tag Manager](https://tagmanager.google.com) account and [ActivePieces](https://www.activepieces.com/)

## 1. Create active pieces flow
- Setup a webhook listner in Activepies
- When the webhook is triggered initiate a update row piece.
- Follow the next steps

## 2. Create the Custom HTML Tag and Variables
Before you create the tag below in Google Tag Manager, make sure your contact form(s) has an ID. Replace the IDs in the JavaScript with your own form ids, if you have one form then just edit the first one, if you have more than two add as many if statements as you need.

Replace the webhook url as well as the token with your own from ActivePieces.

- Tag Name: OC - Activepices Hook
- Tag Type: Custom HTML
- Tag Content:
```html
<script>
// Store GCLID as cookie
function storeGCLIDAsCookie() {
    // Function to get a query parameter by name
    function getParameterByName(name) {
        var match = RegExp('[?&]' + name + '=([^&]*)').exec(window.location.search);
        return match && decodeURIComponent(match[1].replace(/\+/g, ' '));
    }

    // Extract the GCLID from the URL
    var gclid = getParameterByName('gclid');

    // If GCLID exists, store it as a cookie
    if (gclid) {
        var expiryDate = new Date();
        expiryDate.setTime(expiryDate.getTime() + (30 * 24 * 60 * 60 * 1000)); // Cookie expires in 30 days
        document.cookie = "gclid=" + gclid + "; expires=" + expiryDate.toUTCString() + "; path=/";
    }
}

storeGCLIDAsCookie();

// Setting form variable depending on landing page
var form = "";
if (document.getElementById("replaceWithYourFormID") == null) {
  form = document.getElementById("replaceWithYourFormID");
}
  
else if (document.getElementById("replaceWithYourFormID2") == null) {
  form = document.getElementById("replaceWithYourFormID2");
}
  
// Date of conversion
function getFormattedDateTime() {
    var now = new Date();

    // Format date and time
    var month = String(now.getMonth() + 1);
    var day = String(now.getDate());
    var year = now.getFullYear();
    
    var hours = now.getHours();
    var minutes = String(now.getMinutes());
    var seconds = String(now.getSeconds());
    
    // Pad with leading zeros if necessary
    month = month.length === 1 ? '0' + month : month;
    day = day.length === 1 ? '0' + day : day;
    minutes = minutes.length === 1 ? '0' + minutes : minutes;
    seconds = seconds.length === 1 ? '0' + seconds : seconds;
    hours = hours.length === 1 ? '0' + hours : hours;

    // Format dateString
    var dateString = month + '/' + day + '/' + year + ' ' + hours + ':' + minutes + ':' + seconds;

    return dateString;
}

// Check for GCLID
function checkGCLIDCookie() {
    // Function to get the value of a specific cookie by name
    function getCookie(name) {
        var nameEQ = name + "=";
        var ca = document.cookie.split(';');
        for (var i = 0; i < ca.length; i++) {
            var c = ca[i];
            while (c.charAt(0) === ' ') c = c.substring(1, c.length);
            if (c.indexOf(nameEQ) === 0) return c.substring(nameEQ.length, c.length);
        }
        return null;
    }

    // Check if the GCLID cookie exists
    var gclidCookie = getCookie('gclid');

    // Return true if the GCLID cookie exists, false otherwise
    return gclidCookie !== null;
}

// Webhook URL
var webhookUrl = "YourWebHookURL";

// Authorization token
var authToken = "YourToken";

// Function to send the POST request
function sendPostRequest() {
  var xhr = new XMLHttpRequest();
  xhr.open("POST", webhookUrl, true);
  xhr.setRequestHeader("Content-Type", "application/json");
  xhr.setRequestHeader("Authorization", "Token " + authToken);

  xhr.onreadystatechange = function() {
    if (xhr.readyState === 4 && xhr.status === 200) {
      console.log("Request successful");
    }
  };

  xhr.send(JSON.stringify({
    email: document.getElementsByName("Email")[0].value,
    phone: document.getElementsByName("Phone")[0].value,
    conversion_date: getFormattedDateTime()
  }));
}

// Event listner for webhook
form.addEventListener("submit", function(){
  if (checkGCLIDCookie()) {
    sendPostRequest();
  }
});
</script>
```

## 3. Create Google 2 Google Sheets (Client Copy & Scheduled Upload)
- [Download the Template Here](https://docs.google.com/spreadsheets/u/4/d/1g0-rFycZvezi8lr9lroysKx2qqAawWiRnr6F1IKwMh8/copy)
- Then create a copy of the sheet above and add the suffix ``Client Copy`` to the second spreedsheet.

## 4. Connect Activepices to Google Sheets and test
- Connect your Google Sheets piece and select the sheet with the suffix ``Client Copy``.
- Update the rows (using the row number) with the data provided in the webhook.
- Test the flow

## 5. Replace URL with production URL
- Replace the test url with the production URL and save the changes in Google Tag Manager and Activate Active Pieces.