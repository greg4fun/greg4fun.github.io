.:w
. title: COMODO Positive-SSL sec_error_unknown_issuer on firefox
.. slug: comodo-positive-ssl-sec_error_unknown_issuer-firefox
.. date: 2015/03/12 10:26:23
.. tags: 
.. link: 
.. description: 
.. type: text

Tempted by post on official google admins blog http://googlewebmastercentral.blogspot.co.uk/2014/08/https-as-ranking-signal.html
it's about higher ranking when having https. So I have recently bought cheap ssl certificate from known issuer COMODO
and upgraded my server with it but it caused several problems when I redirected from 80 to 443 some 3rd party apps (like
userena for django) created loops but I found solution quickly it was to put USERENA_USE_HTTPS=True in settings.py but
it still sends more forgot password mails than it should but it's different story.

Comming back to point if you are experiencing error "sec_error_unknown_issuer" in firefox 
after implementing COMODO Positive-SSL certificate this solution may help you.
Don't know exactly but I read that it has something to do with chain certificates 
they should be bundled inside you website certificate.
And remember you have to do it in right order because first one should be your servers certificate 
cause it's signed with key) and nginx or whatever you use may have problems after reloading service.

My command for bundling was:

cat exerceo_pl.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt >
exerceo_pl.bundled.crt
after that I had just to point my nginx.conf to new bundled certificate.
And after all reaload of nginx fixed my unknow issuer firefox problem.
