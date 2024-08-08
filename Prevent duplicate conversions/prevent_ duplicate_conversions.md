# Instructions
Follow the steps below to setup the transaction ID and prevent duplicate conversions.

## 1. Create the Custom HTML Tag and Variables
- Tag Name: Set Transaction ID
- Tag Type: Custom HTML
- Tag Content:
```html
<script>
  (function() {
    function getRandomString(length) {
      var characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
      var result = '';
      var charactersLength = characters.length;
      for (var i = 0; i < length; i++) {
        result += characters.charAt(Math.floor(Math.random() * charactersLength));
      }
      return result;
    }

    function setTransactionID() {
      var transactionID = localStorage.getItem('transactionID');
      if (!transactionID) {
        transactionID = getRandomString(16);
        localStorage.setItem('transactionID', transactionID);
      }
      return transactionID;
    }

    var transactionID = setTransactionID();

    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      'event': 'transactionIDSet',
      'transactionID': transactionID
    });
  })();
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
