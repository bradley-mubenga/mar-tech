## 1. Create a Custom HTML Tag to Set the Transaction ID
### This tag will generate and store the transactionID and push it to the data layer.

- Tag Name: Set Transaction ID
- Tag Type: Custom HTML
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

## 2. Create a Data Layer Variable for the Transaction ID
- Variable Name: DLV - Transaction ID
- Variable Type: Data Layer Variable
- Data Layer Variable Name: transactionID

## 3. Create a Trigger for the Custom HTML Tag
- Trigger Name: All Pages (or a specific page where the conversion occurs)
- Trigger Type: Page View > All Pages

## 4. Modify the Conversion Tag to Use the Transaction ID
- Tag Type: Google Ads Conversion Tracking (or your custom conversion tag)
If you are using a custom HTML conversion tag, it should look like this:

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

- Edit your conversion tag.
- Scroll to the Advanced Settings section.
- Click Tag Sequencing.
- Check the box for Fire a Tag before...
- Select the Set Transaction ID tag.
- Save your changes.

## 6. Test Your Setup
- Use GTM's Preview mode to test and debug.
- Ensure the Set Transaction ID tag fires before the conversion tag.
- Verify that the transactionID is available in the data layer and used in the conversion tag.
