SHELL = bash

OUTPUT := pizza-crd.yaml serving-cert-secret.yaml mutatingadmissionregistration.yaml validatingadmissionregistration.yaml

all: tls.key tls.crt $(OUTPUT)

tls.key tls.crt:
		# Generate the CA cert and private key
		openssl req -nodes -new -x509 -keyout ca.key -out ca.crt -subj "/CN=Admission Controller Webhook Demo CA"
		# Generate the private key for the webhook server
		openssl genrsa -out tls.key 2048
		# Generate a Certificate Signing Request (CSR) for the private key, and sign it with the private key of the CA.
		openssl req -new -key tls.key -subj "/CN=webhook.pizza-crd.svc" -config server.conf \
		| openssl x509 -req -CA ca.crt -CAkey ca.key -CAcreateserial -out tls.crt -extensions v3_req -days 365 -extfile server.conf

%.yaml: tls.key tls.crt %.yaml.template
		sed 's,CERT,$(shell base64 tls.crt),;s,KEY,$(shell base64 tls.key),' $@.template > $@

clean:
		rm -f tls.key tls.crt $(OUTPUT)
