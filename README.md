# python-Paybox

Simple Python class to help you with the integration of the Paybox payment system

## Requirements :

Only the standard library if you intend not to verify the Paybox response

M2Crypo otherwise (recommended)

## Methods :

### post_to_paybox()

returns two dicts :

- mandatory, which contains all the mandatory variables unordered
- accessory, which contains all the other variables you may send to Paybox

### construct_html_form(production=False)

returns a string, which is a valid html form ready to be put inside a template for example

### verify_notification(response, reference, total, production=False, verify_certificate=True)

returns two strings in a dict :

- reference, your reference of the payment
- authorization, the number of authorization that Paybox send on success

### verify_certificate(message, signature)

return True if everything is ok, and horrible errors if the verification fails. This is automatically called by the *verify_notification* method, unless you decide otherwise.
