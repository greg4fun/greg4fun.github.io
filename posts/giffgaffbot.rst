.. title: giffgaffbot
.. slug: giffgaffbot
.. date: 2014/04/26 19:36:46
.. tags: bot, curl, pycurl, automatization, test
.. category: Bot
.. link: 
.. description: 
.. type: text

 Last time I wanted to buy new Samsung Galaxy S 5 I liked blue one 
 but there was no preorder option on giffgaff website (it was cheapest there) 
 so to notify me I wrote a bot in pycurl and placed job in cron to send me an email 
 when ot will be in stock.

Cut of sourcefragment from giffgaff webpage:

.. code-block:: html

   <form id="frmBuyPhone" action="/samsung/samsung-galaxy-s5/phone-plans" method="post" data-color-memory-mapping='
   {"black":{"16":{"price":"517","sku":"GG1SAS5BBN","stock":1,"isLoanAvailable":true,"minMonthlyPayment":2139,"minAPR":970}},
    "white":{"16":{"price":"517","sku":"GG1SAS5WWN","stock":1,"isLoanAvailable":true,"minMonthlyPayment":2139,"minAPR":970}},
    "blue":{"16":{"price":"517","sku":"GG1SAS5BLN","stock":1,"isLoanAvailable":true,"minMonthlyPayment":2139,"minAPR":970}},
    "gold":{"16":{"price":"517","sku":"GG1SAGS5GN","stock":1,"isLoanAvailable":true,"minMonthlyPayment":2139,"minAPR":970}}}'
   data-loan-mapping='{"GG1SAS5BBN":{"5000":{"24":{"deal_id":"7049","apr":970,"loan_amount":46700,"monthly_payment":2139,"total_payment":56336},"18":{"deal_id":"7053","apr":1140,"loan_amount":46700,"monthly_payment":2821,"total_payment":55778},"12":{"deal_id":"7057","apr":1470,"loan_amount":46700,"monthly_payment":4187,"total_payment":55244},"6":{"deal_id":"7061","apr":2580,"loan_amount":46700,"monthly_payment":8317,"total_payment":54902}}
   

The bot looks like this : 

.. code-block:: python
   :number-lines:

    #!/usr/bin/env python
    import pycurl
    import sys
    import cStringIO
    import re
    import os
    buf = cStringIO.StringIO()
    ch = pycurl.Curl()
    ch.setopt(pycurl.VERBOSE, 0)
    ch.setopt(pycurl.WRITEFUNCTION, buf.write)
    ch.setopt(pycurl.COOKIEJAR, 'Ocookie.txt')
    ch.setopt(pycurl.USERAGENT,"Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; BTRS122288; .NET CLR 1.1.4322; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; InfoPath.3)")
    def get_products(url):
        ch.setopt(pycurl.URL, url)
        ch.perform()
        buf.reset()
        newdata = buf.getvalue()
        newdata = re.search("data-color-memory-mapping.*", newdata).group()
        newdata = re.search("blue.*", newdata).group()
        newdata = re.findall("stock.:([0-9]{0,2})", newdata)
        newdata = newdata[0]
        if int(newdata) == 0:
            print "not in stock"
        else:
            os.system("echo 'in stock  %s' | /usr/sbin/sendmail mymail@gmail.com" % newdata)
            print "in stock"

    get_products('http://giffgaff.com/samsung/samsung-galaxy-s5/phone-details')

and added job to crontab to check every 5 minutes

\*/5 * * * * /workspace/projects/checkbot/readweb.py 
