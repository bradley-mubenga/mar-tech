# Instructions
Follow the steps below to setup the transaction ID and prevent duplicate conversions. You will need a [Google Tag Manager](https://tagmanager.google.com) account and [ActivePieces](https://www.activepieces.com/)

## 1. Create the Custom HTML Tag and Variables
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

## 2. Create Data Layer Variable for the Transaction ID:
- Variable Name: DLV - Transaction ID
- Variable Type: Data Layer Variable
- Data Layer Variable Name: transactionID

## 4. Modify the Conversion Tag to Use the Transaction ID
- If you are using the built in tags in GTM, just go into your tag and add the transaction Id variable in step 2 in the variable option or parameter in the tag.
- If you are using a custom HTML conversion tag, it should look something like this:

```html
<script>
  gtag('event', 'conversion', {
    'send_to': 'AW-123456789/AbC-D_efG-h12_34-567',
    'value': 1.0,
    'currency': 'USD',
    'transaction_id': '{{DLV - Transaction ID}}'
  });
</script>
```

## 5. Set Up Tag Firing Sequence
### To ensure the transaction ID is set before the conversion tag fires, use GTM's tag sequencing feature.

- Click to edit your conversion tag.
- Scroll to the Advanced Settings section.
- Click Tag Sequencing.
- Check the box for Fire a Tag before...
- Select the Set Transaction ID tag.
- Save your changes.

## 6. Test Your Setup
- Use GTM's Preview mode to test and debug.
- Ensure the Set Transaction ID tag fires before the conversion tag.
- Verify that the transactionID is available in the data layer and used in the conversion tag.