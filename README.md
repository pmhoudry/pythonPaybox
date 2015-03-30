# python-Paybox

Simple Python class to help you with the integration of the Paybox payment system

## Requirements

Only the standard library if you intend not to verify the Paybox response

M2Crypo otherwise (recommended)

## Usage

Calling Paybox from a Django view

    from Paybox import Transaction

    transaction = Transaction(
		 PBX_TOTAL=PBX_TOTAL,
		 PBX_PORTEUR=PBX_PORTEUR,
		 PBX_TIME=PBX_TIME,
		 PBX_CMD=PBX_CMD
		)
		
	if production:
	 action = 'https://tpeweb.paybox.com/cgi/MYchoix_pagepaiement.cgi'
	else:
	 action = 'https://preprod-tpeweb.paybox.com/cgi/MYchoix_pagepaiement.cgi'

	form_values = transaction.post_to_paybox()

	return render(request, 'payment.html', {
			'action': action,
			'mandatory': form_values['mandatory'],
			'accessory': form_values['accessory']
		})

How to organise the variables in a template

    <form method="POST" action="{{ action }}">
	<input type="hidden" name="PBX_SITE" value="{{ mandatory.PBX_SITE }}">
	<input type="hidden" name="PBX_RANG" value="{{ mandatory.PBX_RANG }}">
	<input type="hidden" name="PBX_IDENTIFIANT" value="{{ mandatory.PBX_IDENTIFIANT }}">
	<input type="hidden" name="PBX_TOTAL" value="{{ mandatory.PBX_TOTAL }}">
	<input type="hidden" name="PBX_DEVISE" value="{{ mandatory.PBX_DEVISE }}">
	<input type="hidden" name="PBX_CMD" value="{{ mandatory.PBX_CMD }}">
	<input type="hidden" name="PBX_PORTEUR" value="{{ mandatory.PBX_PORTEUR }}">
	<input type="hidden" name="PBX_RETOUR" value="{{ mandatory.PBX_RETOUR }}">
	<input type="hidden" name="PBX_HASH" value="{{ mandatory.PBX_HASH }}">
	<input type="hidden" name="PBX_TIME" value="{{ mandatory.PBX_TIME }}">
	<input type="hidden" name="PBX_HMAC" value="{{ mandatory.hmac }}">
	{% for name, value in accessory.items %}
		{% if value %}
			<input type="hidden" name="{{ name }}" value="{{ value }}">
		{% endif %}
	{% endfor %}
	<input type="submit" value="Proceed to payment">
</form>

Receiving an IPN in a Django view

    from Paybox import Transaction

    transaction = Transaction()
    notification = transaction.verify_notification(response=request.get_full_path(), reference='', total='')

    reference = notification['reference']
    authorization = notification['authorization']
    
    # Paybox Requires a blank 200 response
    return HttpResponse('')

## Methods

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
